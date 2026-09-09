# Chapitre 4 -- useProveWithdrawalArgs : orchestrer la preuve en hooks reactifs

`useProveWithdrawalArgs` est le hook le plus complexe du depot : il
reproduit, sous forme reactive, exactement ce que `getProveWithdrawalTransactionArgs`
accomplit de maniere imperative dans op-viem (chapitre 4 du parcours
op-viem), mais en composant plusieurs hooks wagmi et hooks internes plutot
qu une seule fonction asynchrone sequentielle. Il compose : `useConfig`
(acces a la configuration globale), `usePublicClient` (client L2),
`useBlockNumberOfLatestL2OutputProposal` et `useGetL2OutputIndexAfter`
(deux hooks internes qui interrogent l oracle de sortie L2),
`useReadContract` (lecture de la proposition d output via `getL2Output`),
`useWaitForTransactionReceipt` (attente du recu de la transaction de
retrait sur L2), et `useBlock` (recuperation du bloc L2 correspondant a
la derniere proposition).

A partir de ces donnees, une chaine de `useMemo` recalcule la preuve
complete chaque fois qu une dependance change : `getWithdrawalMessage`
(dans `util/`) parcourt les logs du recu de transaction pour trouver et
decoder l evenement `MessagePassed` emis par `L2ToL1MessagePasser`, via
`decodeEventLog` avec l ABI officielle du contrat -- une approche plus
robuste que de supposer la position du log, puisqu elle filtre par adresse
d emetteur puis verifie le nom de l evenement decode. `hashWithdrawal`
encode ensuite les six champs du message (`nonce`, `sender`, `target`,
`value`, `gasLimit`, `data`) via `encodeAbiParameters` puis les hache en
keccak256 -- une fonction pure, testable independamment, qui correspond
exactement au calcul de hash de retrait attendu par le contrat
`OptimismPortal`. `getMessageSlot` reprend ensuite ce hash pour calculer
le slot de stockage (meme logique de mapping a l index 0 que
`getWithdrawalMessageStorageSlot` dans op-viem, chapitre 4).

Le resultat final, `bedrockProof`, assemble un objet
`BedrockCrossChainMessageProof` avec `outputRootProof` (version, state
root, storage root du message passer, hash du bloc) et `withdrawalProof`
(le tableau de preuve retourne par `useMakeStateTrieProof`, un hook
utilitaire qui encapsule l appel `eth_getProof`) -- strictement les memes
champs que ceux produits par op-viem, mais recalcules automatiquement par
React chaque fois qu une des dependances (recu de transaction, proposition
d output, numero de bloc) change, sans que le composant appelant ait besoin
de re-orchestrer manuellement la sequence d appels.
