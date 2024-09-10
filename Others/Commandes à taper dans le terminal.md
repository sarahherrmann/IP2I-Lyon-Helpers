# Commandes à taper dans le terminal
## I. ALIEN
### 1. Aller sur alien

- Dans l'environnement O2, taper `alien.py` pour accéder au ALICE GRID
- Les commandes `ls` `cd`et `pwd`fonctionnent dans cet environnement (entre autres)
- `exit` pour sortir de alien
### 2. Récupérer un document qui est sur alien

- Dans l'environnement O2 taper `alien_cp /alice/cern.ch/user/a/aliperf/alibi_nightlies/O2DPG_pp_minbias_testbeam.sh/07-11-2021-18:00-O2a9da397-ALIDISTdd6c323/mcarchive.tar.gz mcarchive.tar.gz` c'est à dire : `alien_cp chemin_du_fichier/nom_du_fichier nom_du_fichier_dans_destination`

:::info
Ne JAMAIS faire `rm *` dans alien, ça supprime tout en local
:::

`alien_ls dir > list.txt`
`alien.py cp dir/mftclusters.root file:mftclusters.root`

`tar -xf archive.tar.gz`

## General

`Command`+`K`pour effacer les lignes d'un terminal
`Command`+`Maj`+`/` pour commenter dans atom (ajoute // à chaque ligne)

To know how many files in a directory: `ls |wc -l` 

`ctrl`+`R`pour rechercher dans l'historique du terminal


`kill %%` pour kill un process en cours, faire `ctrl`+`Z` fait passer la tâche en bckg seulement, ne la kill pas vraiment

`cd `te ramène dans to home

## lxplus

ssh sherrman@lxplus.cern.ch

scp alicecerno2:archive.tar.gz /eos/user/s/sherrman/.