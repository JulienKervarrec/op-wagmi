# Chapitre 1 -- Presentation de op-wagmi

Ce depot fournit des hooks React au style wagmi pour les operations de pont
OP Stack (depot L1 vers L2, retrait L2 vers L1). Contrairement a op-viem
(chapitre precedent de cette bibliotheque, marque deprecie), op-wagmi ne
duplique pas la logique bas niveau : il l importe directement depuis le
paquet `op-viem` (les imports `from 'op-viem/actions'` apparaissent dans
plusieurs hooks) et se concentre sur la couche d integration React --
gestion d etat de mutation, cache de requetes, resolution automatique de
client depuis un `Config` wagmi. Le README ne porte aucun avertissement de
depreciation, contrairement a op-viem : ce depot reste actif.

Le paquet s appelle `op-wagmi` (confirme par `package.json`) et organise
son code source sous `src/` en quatre dossiers : `hooks/L1/` (depots et
preuves de retrait, cote Ethereum mainnet), `hooks/L2/` (initiation des
retraits, cote chaine OP Stack), `types/` (types partages entre hooks,
notamment les types de base `UseWriteOPActionBaseParameters` et
`UseSimulateOPActionBaseParameters` dont heritent tous les hooks
specifiques), et `util/` (fonctions de calcul reutilisees par les hooks
d orchestration : hachage de retrait, calcul de slot de stockage, preuve
Merkle-Patricia, decodage d evenement).

Chaque hook exporte suit une convention de nommage stricte refletant le
style wagmi natif : `useWrite*` pour les mutations qui emettent une
transaction (avec un objet de retour exposant `write*` et `write*Async`),
`useSimulate*` pour les simulations en lecture seule avant ecriture. Cette
convention permet a un developpeur deja familier de wagmi de reconnaitre
immediatement le comportement attendu de chaque hook sans consulter sa
documentation individuelle.
