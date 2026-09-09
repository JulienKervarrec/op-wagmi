# Chapitre 2 -- Les hooks de depot : simuler avant d ecrire

`useWriteDepositETH` (dans `hooks/L1/`) illustre le patron commun a tous
les hooks d ecriture du depot. Sa fonction interne `writeMutation` recoit
la configuration wagmi et les parametres de depot, puis effectue quatre
etapes dans l ordre : recuperer le `walletClient` pour la chaine L1 cible
via `getWalletClient(config, { chainId: l1ChainId })`, recuperer les deux
`publicClient` (L1 et L2) necessaires, resoudre l adresse du contrat
`OptimismPortal` via `validatePortalContract`, puis -- point notable --
estimer automatiquement la limite de gas L2 si l appelant ne l a pas
fournie explicitement : `l2PublicClient.estimateGas(...)` simule l appel
sur la chaine L2 cible pour deriver un `gasLimit` raisonnable, rendant ce
parametre optionnel cote API du hook alors qu il est obligatoire cote
action bas niveau de op-viem.

Une fois ces informations reunies, le hook appelle d abord
`simulateDepositETH` (importee de `op-viem/actions`, la meme fonction que
celle documentee au chapitre 3 du parcours op-viem) pour valider que la
transaction reussirait, puis seulement ensuite `writeDepositETH` pour
l emettre reellement -- un patron simuler-puis-ecrire qui protege
l utilisateur contre l envoi d une transaction vouee a echouer et contre
la perte de gas associee.

Cote React, le hook expose ce comportement via `useMutation` de
TanStack Query : `mutationFn` encapsule la resolution de chaine (via
`validateL2Chain`, voir chapitre 3) et l appel a `writeMutation`, et le
hook retourne un objet renommant les champs generiques `mutate`/
`mutateAsync` de `useMutation` en `writeDepositETH`/`writeDepositETHAsync`,
pour que l API exposee au consommateur du hook porte un nom explicite
plutot que le nom generique de la bibliotheque de requetes sous-jacente.
Les hooks `useWriteDepositERC20`, `useWriteFinalizeWithdrawalTransaction`
et `useWriteProveWithdrawalTransaction` suivent exactement la meme
structure en trois temps (resoudre les clients, simuler, ecrire).
