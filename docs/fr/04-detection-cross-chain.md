# Chapitre 4 -- Verifier la correction d une proposition : fault_proof_detection_parent et child

Ce couple de moniteurs est presente par sa propre documentation comme
unique dans le depot : c est le seul a avoir besoin de donnees provenant
simultanement d Ethereum (L1) et du rollup lui-meme (L2) pour fonctionner,
puisqu il s agit de verifier qu une proposition d etat L2 soumise sur L1
est effectivement correcte.

`fault_proof_detection_parent.gate` ecoute chaque `DisputeGameCreated`
emis par la `DisputeGameFactoryProxy`. Pour chaque nouveau jeu, il
recalcule lui-meme l output root attendu, independamment de ce qui a ete
propose : il recupere le numero de bloc L2 concerne
(`l2BlockNumber()` sur le dispute game), puis va chercher sur le L2 le
state root de ce bloc, son block hash, et le storage root hash du contrat
L2CrossDomainMessenger ("message passer") a ce meme bloc. Ces quatre
elements -- un mot nul de 32 octets, le state root, le storage hash du
message passer, et le block hash -- sont concatenes puis hashes en
Keccak256, reproduisant exactement la formule canonique de calcul d un
output root de l OP Stack. Le moniteur compare ce resultat calcule a la
valeur `l2OutputProposal` effectivement soumise dans l evenement : toute
divergence signale une proposition invalide, et le moniteur verifie en
prime qu un seul `DisputeGameCreated` n apparait par bloc.

`fault_proof_detection_child.gate` prend le relais une fois qu une
proposition invalide a ete identifiee par le parent : deploye
specifiquement sur ce dispute game (parametre `disputeGame` fourni par le
workflow declenche du chapitre 2), il reutilise la meme logique de parite
de `parentIndex` que `challenged_proposal` (chapitre 3), mais dans l autre
sens -- il verifie cette fois que le `cbChallenger` attaque activement (
`parentIndex` pair, `claimant == cbChallenger`) cette proposition
identifiee comme fausse, et leve une alerte separee si un participant
quelconque tente de la defendre (`parentIndex` impair) -- un signal
possible de comportement adversarial coordonne. Le couple parent/enfant
forme ainsi une chaine de detection complete : le parent identifie l
erreur, l enfant verifie que la reponse (le challenge) suit bien.
