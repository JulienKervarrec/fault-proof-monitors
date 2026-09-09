# Chapitre 6 -- Limites et perimetre de ce parcours

Ce parcours couvre le README (contexte, table des dix moniteurs, workflows
de deploiement), et cinq des dix fichiers `.gate` avec leur documentation
associee dans `docs/` : `challenged_proposal.gate`,
`duplicate_dispute_game.gate`, `fault_proof_detection_parent.gate`,
`fault_proof_detection_child.gate` et `eth_deficit.gate`.

Sont volontairement laisses hors champ : les cinq autres moniteurs
(`challenger_loses.gate`, `credit_and_bond_discrepancy.gate`,
`eth_withdrawn_early.gate`, `incorrect_bond_balance.gate`,
`unresolvable_dispute_game.gate`), qui suivent des schemas similaires a ceux
deja detailles (analyse de claims, comptabilite de bonds) sans introduire de
mecanisme fondamentalement nouveau ; le dossier `tests/` (les suites Go qui
appellent l API de mock d Hexagate pour valider chaque moniteur) ; et l
infrastructure de deploiement webhook elle-meme (en dehors du depot, cote
plateforme Hexagate).

L objectif reste le meme que pour les parcours precedents de cette
bibliotheque : comprendre precisement comment ces moniteurs traduisent des
proprietes de securite du systeme de preuves de fraude -- liveness,
integrite, correction, securite financiere -- en invariants verifiables a
chaque bloc dans un langage declaratif, sans pretendre couvrir l integralite
des dix moniteurs ni le fonctionnement interne de la plateforme Hexagate qui
les execute.
