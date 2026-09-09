# Chapitre 1 -- Presentation de fault-proof-monitors et du langage gate

Les preuves de fraude (fault proofs) permettent a n importe qui de proposer
et de valider l etat d un rollup L2 de maniere permissionless et
decentralisee -- c est le mecanisme qui, depuis leur activation sur Base
Mainnet, remplace la confiance dans un unique proposer autorise par un
processus de litige on-chain ouvert a tous. Ce depot ne contient aucun
contrat ni service : c est une collection de dix moniteurs ecrits dans
**gate**, un langage declaratif developpe par Hexagate pour definir des
invariants surveilles en temps reel a chaque bloc.

Un fichier `.gate` a une structure commune : des `param` (les adresses ou
valeurs fournies au deploiement du moniteur), des `source` (des valeurs
calculees en interrogeant la chaine -- appels de fonction via `Call`,
evenements via `Events`/`HistoricalEvents`, ou des transformations comme
`Range`, `Zip`, `Contains`, `Unique`), et un unique bloc `invariant` final
qui declare une `condition` booleenne : tant qu elle reste vraie, rien ne se
passe ; des qu elle devient fausse, une alerte se declenche. Cette structure
rend chaque moniteur lisible comme une specification plutot que comme du
code imperatif -- proche d une preuve mathematique de ce qui doit toujours
etre vrai.

Le README classe les dix moniteurs par la nature du probleme qu ils
detectent -- liveness (le systeme progresse-t-il correctement ?), integrite
(les regles du protocole sont-elles respectees ?), correction (les
propositions soumises sont-elles valides ?) et securite (les fonds sont-ils
comptabilises correctement ?). Ce parcours n en couvre pas les dix en detail
(voir chapitre 6 pour le perimetre exact), mais examine en profondeur un
representant de chacune des categories les plus significatives :
`challenged_proposal` et `duplicate_dispute_game` (chapitre 3),
`fault_proof_detection_parent`/`child` (chapitre 4) et `eth_deficit`
(chapitre 5).
