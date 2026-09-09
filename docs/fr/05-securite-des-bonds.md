# Chapitre 5 -- Securite : eth_deficit et la comptabilite des bonds

Chaque participant a un dispute game depose un bond en ETH, conserve dans
un contrat `DelayedWETH` associe au jeu -- ce bond est rendu (ou perdu) selon
l issue du litige. `eth_deficit.gate` verifie qu aucun deficit n apparait
jamais dans cette comptabilite : si plus d ETH pouvait etre reclame que ce
qui est reellement detenu, des participants honnetes perdraient une partie
de leurs fonds legitimes.

Le moniteur recupere cinq valeurs cle aupres du `DelayedWETH` associe au
`disputeGame` cible : le credit du challenger en mode normal
(`claimCredit`, ce qu il peut reclamer selon la resolution du jeu), son
credit en mode remboursement (`refundModeCredit`, base sur les moves
effectues), le mode de distribution des bonds actif
(`bondDistributionMode`, qui determine lequel des deux credits precedents
s applique), le statut de deverrouillage de son credit
(`hasUnlockedCredit` -- un prealable obligatoire au retrait), et enfin son
credit total effectivement deverrouille (`totalCredit`). L invariant
combine plusieurs verifications de coherence plutot qu une seule : le
credit reclamable selon le mode de distribution actif ne doit jamais
depasser le `totalCredit` deverrouille ; le `totalCredit` cumule de tous les
participants ne doit jamais depasser le solde ETH reel du `DelayedWETH`
pour ce jeu (`ethBalanceDisputeGame`) ; et si le credit reclamable est nul,
le `totalCredit` doit l etre aussi -- toute incoherence entre ces valeurs
indique une desynchronisation entre l etat du dispute game et celui du
contrat de bonds, potentiellement le symptome d un bug d accounting.

Cette famille de verifications relie directement la logique de jeu
(resolution des claims, chapitres 3 et 4) a ses consequences financieres
concretes : un dispute game peut etre techniquement bien resolu tout en
laissant une incoherence dans les bonds si le contrat de comptabilite n est
pas mis a jour de maniere strictement synchronisee avec la resolution.
