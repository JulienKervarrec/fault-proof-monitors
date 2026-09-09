# Chapitre 3 -- Liveness et integrite : challenged_proposal et duplicate_dispute_game

`challenged_proposal.gate` verifie la liveness du systeme : il detecte le
cas ou un challenger suppose honnete (`honestChallenger`) attaque une
proposition faite par un proposer suppose honnete (`honestProposer`) -- un
signal fort de bug, puisque deux acteurs honnetes ne devraient jamais entrer
en conflit entre eux. Techniquement, le moniteur recupere tous les
evenements `Move` du dispute game, puis reconstruit la liste complete des
claims via `claimDataLen()` et des appels repetes a `claimData(idx)` pour
chaque indice (boucle `for index in Range { start: 0, stop: claimCount }`).
Le point cle du raisonnement repose sur la parite du `parentIndex` de
chaque claim : un `parentIndex` pair signifie que la claim attaque son
parent, un `parentIndex` impair signifie qu elle le defend -- une regle
geometrique propre a la structure en arbre binaire des dispute games de l OP
Stack, ou chaque profondeur alterne entre attaque et defense. L invariant
final ne se declenche que si le root claim (position 0, donc
`claimData[0][2]`) provient bien du `honestProposer`, et verifie qu aucune
claim du `honestChallenger` ne porte un `parentIndex` pair pointant vers le
root claim ou ses attaques ulterieures.

`duplicate_dispute_game.gate` verifie l integrite du systeme : la regle du
protocole veut qu il n existe qu un seul dispute game par combinaison
(`gameType`, `rootClaim`, `extraData`) -- en creer un doublon rendrait
ambigu le jeu de reference au moment de finaliser un retrait. Le moniteur
calcule un identifiant unique (UUID) pour chaque dispute game via
`getGameUUID` (une fonction de la factory qui derive un hash a partir des
trois memes champs), pour les jeux crees au bloc courant
(`newDisputeGameUUIDs`, provenant des appels `create` en cours de bloc, via
`Calls`) et pour tous les jeux crees precedemment
(`previousDisputeGameUUIDs`, reconstruits a partir de l historique complet
des evenements `DisputeGameCreated` via `HistoricalEvents`, en filtrant sur
le `respectedGameType` courant et en excluant le bloc courant lui-meme).
Les UUIDs precedents sont places dans une structure `map` pour une
recherche en O(1) (`MapContains`), et l invariant echoue si un nouvel UUID
existe deja dans cette map, ou si deux UUIDs nouvellement crees dans le meme
bloc sont identiques entre eux (verifie via `Unique`).
