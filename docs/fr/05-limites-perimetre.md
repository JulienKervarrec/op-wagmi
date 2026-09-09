# Chapitre 5 -- Limites et perimetre de ce parcours

Ce parcours couvre la presentation generale du depot et sa relation de
dependance avec op-viem, le patron simuler-puis-ecrire commun aux hooks de
depot, le mecanisme de resolution automatique de chaine L1 depuis une
chaine L2 et la validation des contrats de pont associes, et le hook
d orchestration de preuve de retrait le plus complexe du depot,
`useProveWithdrawalArgs`, avec le detail de ses fonctions utilitaires de
hachage et de calcul de slot.

Sont volontairement laisses hors champ : le detail exhaustif de chaque
hook individuel (`useSimulateDepositERC20`, `useWriteWithdrawERC20`,
`useSimulateFinalizeWithdrawalTransaction`, et les autres, qui suivent
tous des patrons deja illustres aux chapitres 2 et 4) ; le contenu complet
de `useGetL2OutputIndexAfter` et `useBlockNumberOfLatestL2OutputProposal`
(les deux hooks internes utilises par `useProveWithdrawalArgs` mais non
exportes publiquement) au-dela de leur role mentionne au chapitre 4 ;
le dossier `example/` (une application de demonstration presente dans le
depot) ; et l outillage de build, de documentation (TypeDoc, visible via
`typedoc.json`) et de publication npm du depot.

L objectif reste le meme que pour les parcours precedents de cette
bibliotheque : comprendre precisement comment ce depot construit une
couche d integration React au-dessus des primitives bas niveau de op-viem,
sans pretendre couvrir l integralite d une bibliotheque de hooks aussi
etendue.
