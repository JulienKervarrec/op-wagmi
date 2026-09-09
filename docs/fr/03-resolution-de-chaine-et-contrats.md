# Chapitre 3 -- Resolution de chaine et validation des contrats

`util/validateChains.ts` centralise une difference architecturale
importante avec op-viem. La ou op-viem definit des tables d adresses
statiques par chaine (`baseAddresses`, `optimismAddresses`, etc., un objet
par L2), op-wagmi lit les adresses de contrats directement depuis l objet
`Chain` de viem passe dans la configuration wagmi : `l2Chain.contracts
.portal[l1ChainId]`, `l2Chain.contracts.l2OutputOracle[l1ChainId]`, etc.
Chaque champ `contracts.portal` (et les autres) est lui-meme un objet cle
par identifiant de chaine L1, ce qui permet a une seule definition de
chaine L2 (par exemple Base) de porter plusieurs adresses de portail
differentes selon qu on la pont depuis Ethereum mainnet ou depuis un
testnet -- une flexibilite que la table statique d op-viem n offrait pas
directement.

`validateL2Chain` illustre une seconde difference : plutot que d exiger
que l appelant precise explicitement l identifiant de chaine L1, elle le
derive automatiquement depuis `l2Chain.sourceId`, un champ de convention
viem pour les chaines OP Stack qui pointe vers la chaine L1 dont cette L2
descend. Si `sourceId` est absent, la fonction leve une erreur explicite
(`L2ChainMissingSourceChainMessage`) plutot que de echouer silencieusement
plus loin dans le flux -- toutes les fonctions de validation de ce fichier
suivent ce meme principe : verifier une precondition et lever un message
d erreur nomme et exporte depuis `constants/errorMessages.ts` plutot que
de laisser remonter une erreur generique de deref undefined.

Six fonctions de validation existent au total, chacune correspondant a un
contrat du protocole de pont : le portail (`validatePortalContract`),
l oracle de sortie L2 (`validateL2OutputOracleContract`), le passeur de
messages L2 vers L1 (`validatel2ToL1MessagePasserContract`), et les deux
bridges standard (`validatel1StandardBridgeContract`,
`validateL2StandardBridgeContract`). Ces fonctions sont appelees en amont
de chaque hook qui en a besoin, garantissant une erreur claire et precoce
plutot qu un echec tardif au moment de l appel RPC.
