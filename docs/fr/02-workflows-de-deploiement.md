# Chapitre 2 -- Les trois workflows de deploiement

Le README documente explicitement trois modes de deploiement, parce que
tous les moniteurs ne ciblent pas la meme portee. **Single Instance** :
deploye une seule fois, pour un invariant qui concerne l ensemble du
systeme plutot qu un dispute game precis -- c est le cas de
`duplicate_dispute_game` (chapitre 3) et de `fault_proof_detection_parent`
(chapitre 4), qui surveillent la factory elle-meme. **Per DisputeGame** :
redeploye automatiquement pour chaque nouveau dispute game cree, puisque
chaque jeu de litige est un contrat independant avec son propre etat de
claims et de bonds -- c est le cas de `challenged_proposal` et de
`eth_deficit`. **Specific DisputeGame** : deploye ponctuellement sur un
dispute game precis, seulement lorsqu un moniteur parent a deja detecte une
anomalie sur ce jeu -- c est le cas de `fault_proof_detection_child`.

Les deux derniers modes exigent une automatisation de deploiement, car
Hexagate ne peut pas connaitre a l avance l adresse d un dispute game qui n
existe pas encore. Le mecanisme decrit est un pipeline evenementiel : un
moniteur de type `Contract Event` ecoute l evenement `DisputeGameCreated`
emis par la `DisputeGameFactory`, avec un canal de notification pointant
vers une URL webhook. A la reception de cette alerte, le workflow extrait
l adresse `disputeGameAddress` du dispute game nouvellement cree, l injecte
comme parametre dans le moniteur cible, puis le deploie via l endpoint
"Create User Monitor" de l API Hexagate -- reproduisant en boucle,
automatiquement, le meme schema pour chacun des sept moniteurs "Per
DisputeGame" (`challenged_proposal`, `challenger_loses`,
`credit_and_bond_discrepancy`, `eth_deficit`, `eth_withdrawn_early`,
`incorrect_bond_balance`, `unresolvable_dispute_game`).

Le schema "Specific DisputeGame" fonctionne en cascade a deux etages :
`fault_proof_detection_parent` (Single Instance) detecte une proposition de
sortie L2 invalide, declenche une alerte via webhook, et ce webhook
provisionne alors `fault_proof_detection_child` avec l adresse precise du
dispute game litigieux -- ce dernier n a de sens que sur un jeu deja
identifie comme suspect, d ou son mode de deploiement "a la demande" plutot
que systematique.
