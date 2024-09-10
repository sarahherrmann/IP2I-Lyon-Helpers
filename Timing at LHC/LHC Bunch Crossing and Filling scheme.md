Useful links:

[http://cdsweb.cern.ch/record/691782/files/project-note-323_v1.pdf](http://cdsweb.cern.ch/record/691782/files/project-note-323_v1.pdf)

[https://lpc.web.cern.ch/cgi-bin/filling_schemes.py](https://lpc.web.cern.ch/cgi-bin/filling_schemes.py)

Un faisceau du LHC = constitué de bunch, un ensemble de 10^11 particules casées dans une boite, un bucket, un espace possible tous les 25ns
Pas de bunch plus proches que 25ns
bunch: paquet de faisceau
bunch beam 1, rentre en collision avec bunch de beam 2

Comment ces bunchs sont-ils organisés dans la machine ? 
Cela dépend de l'injection, de comment le PS et le SPS livrent à l'injection ces bunches
Le schéma classique (25 ns): les bunchs sont organisés par trains, un train est constitué de 72 bunch avec 25ns d'écart. 

Le train est représenté par le rectangle gris encadré en rouge ci-dessous
![[Capture d’écran 2023-08-28 à 09.55.49.png]]

Il y a un remplissage par trains, qui sont eux aussi espacés, afin de monter les kickers d'injections (kickers: aimants, qui doivent arriver à un certain courant nominal, ce qui prend du temps)

Structure écrite en dessous de filling scheme: 
A cause des radiofréquences dans le LHC, on ne peut pas mettre plus que 3564 bunches (à cause de la circonférence du LHC), ces 3564 bunches de décomposent de la façon suivante: 
Structure:  333 , 3 trains un espace de 30e, puis 3 trains et un espace de 30e, puis 3 trains à nouveau et un espace de 30e +1e

Après chaque trains il y a 8 empty, visible ci-dessous
1empty représente 25ns
![[Capture d’écran 2023-08-28 à 10.03.51.png]]

Le train complet est donc composé de 72 bunches suivi de 8 empty, donc 72b+8e

La structure 334 signifie quant à elle qu'il y a une suite de 3 trains, puis un espace, puis 3 trains et un espace, puis enfin 4 trains. 

les bunches et les empty sont ce que l'on appelle des buckets, et chaque bucket dure 25 ns

Donc pour savoir combien dure un train de structure 72b+8e, il faut additionner $72*25+8*25$



En plus des trains, il y a des injections d' *individuals* : Des bunchs isolés qui ne sont pas sous forme de trains.
Ils ne sont pas dans le schéma de remplissage maximum vu ci-dessus. 



Intéressons nous maintenant au deuxième lien: [https://lpc.web.cern.ch/cgi-bin/filling_schemes.py](https://lpc.web.cern.ch/cgi-bin/filling_schemes.py) 
On y retrouve tous les filling schemes du LHC par année.

Prenons un filling scheme au hasard, en 2022 : [25ns_1167b_1154_1022_1088_144bpi_12inj_3INDIV.csv](https://lpc.web.cern.ch/fillingSchemes/2022/candidates/25ns_1167b_1154_1022_1088_144bpi_12inj_3INDIV.csv)
![[Capture d’écran 2023-08-28 à 10.18.05.png]]

Le schéma en bas de page représente le filling scheme:
![[Capture d’écran 2023-08-28 à 10.19.14.png]]

La première ligne correspond au beam 1 (en bleu), la deuxième au beam 2 (en rouge)

On voit ici que le remplissage est très différent par rapport au remplissage maximal vu plus haut. Ici nous avons:
4 trains, 4 trains, 1 individual, ceci répêté 3 fois, puis 4 trains x2 à la fin
(Attention les traits verticaux gris sont juste des séparations, pas des bunches)

Ce schéma correspond au remplissage des faisceaux, mais tous les bunchs ne rentrent pas en collision partout. En analysant le nom du filling scheme, on peut voir combien de bunches rentrent en collision: 
25ns_1167b_1154_1022_1088_144bpi_12inj_3INDIV

C'est un filling scheme de 25ns, il y a au total 1167 bunches, et ensuite les nombres suivants correspondent au nombre de collisions:
Il y a 1154 bunches qui rentrent en collisions à ATLAS et CMS (ils sont à l'opposée donc ils ont le même nombre de collisions)
1022 bunches rentrent en collision chez ALICE, et 1088 chez LHCb


Quels bunch rentrent en collision ? Il faut pour cela regarder les couleurs sur le filling scheme, en vert ce sont les bunch qui rentrent en collisions en CMS et ATLAS, en rose chez ALICE et en marron chez LHCb

Mis à part le premier bunch (en bleu sur la 1ère ligne) qui sont des bunch non-colliding, (les 12 indiqués dans le tableau)

Sur ALICE, le 1er bunch des trains ne rentre jamais en collision (les bunchs bleus) 

Les deux faisceaux sont identiques du point de vue remplissage.

Lorsque l'on s'intéresse par exemple à l'alignement temporel entre deux détecteurs, ce n'est pas du tout pareil de regarder des collisions qui ont lieu à l'intérieur d'un train plutôt que de regarder des collisions ayant lieu sur des bunch individuels.

Lorsqu'il y a une collision sur un bunch individual (attention certain bunches individuals ne sont pas colliding pour ALICE), il y a une collision pendant 25ns au maximum, et ensuite il n'y a rien pendant plusieurs micro secondes


La précision temporelle du MFT étant mauvaise, dans une configuration de trains qui se suivent, il n'est pas possible de distinguer temporellement les collisions au sein de ce train (les 72b), ni même entre trains si ils sont proches
333 ou 334 on les voit comme un seul bloc.

Si je n'essaie de regarder que les traces produites pendant les individual bunches, il y aura sûrement très peu de statistiques, mais c'est quand même très intéressant de regarder ce que cela peut donner. Il y a de nombreux paramètres qui rentrent en compte, car en plus de la dépendance en filling scheme, les résultats du taux d'interaction (IR) dépendent de la luminosité, c'est à dire le nombre de particules mises en collision, qui dépend de $\beta *$ l'angle de croisement entre les faisceaux. 
Quand tu lis: *Fill à 500 kHz*, tu peux obtenir ces 500 kHz avec un filling scheme très dense, mais aussi avec un filling scheme moins dense mais des faisceaux plus concentrés.

Il est néanmoins évident qu'avec un filling scheme très peu rempli, il ne sera pas possible d'atteindre la luminosité nominale. Cependant, même si le nombre de bunch mis en collision dans ALICE peut être relativement élevé (mais toujours inférieur à celui de CMS), indépendamment de cette valeur là, on peut régler la luminosité via le $\beta *$. 

Deux effets se cumulent: la luminosité en elle même, ie combien on accepte de particules dans chaque bunch entrant en collision, (la focalisation du bunch, bunch très éclatés qui se frôlent où bunch très serrés)
Chaque bunch a un nombre de protons fixe, mais ils ne rentrent pas tous en collision. $\beta *$ est lié à la section efficace , surface de contact de tes bunch, comme un paramètre d'impact pour tes bunchs.

Regarder le filling scheme des runs que j'analyse, sélectionner les individuals s'il y en a, et voir si le décalage temporel évolue, pas simplement en fonction de l'IR, mais en fonction du filling scheme, en se concentrant sur certains BCids: quel est le BCid correspondant au bunch individual

Regardons le fichier csv, peut être que ça peut nous indiquer quel BC est individual ou non.

La fin du nom du fichier est 12inj_3INDIV, signifiant qu'il y a 12 injections (24 au total car 12 injections par faisceaux)
Probablement que 3INDIV signifie qu'il y a 3 individual bunches

Il faudrait demander aux gens du trigger pour savoir comment comprendre le fichier csv, par exemple Roman Lietava.
Il existe un code pour convertir ce fichier csv en bunches.


___

Regarder les filling scheme des runs que j'ai analysé: 
LHC22r run 529208 25ns_2462b_2450_1737_1735_180bpi_17inj_2INDIV, 1 INDIV ALICE
LHC22o run 527826 (500 kHz) 25ns_2462b_2450_1737_1735_180bpi_17inj_2INDIV, 1INDIV ALICE
LHC22o run 526926 (1MHz) 25ns_2461b_2448_1737_1733_180bpi_16inj_1INDIV, 1 INDIV ALICE

Essayer LHC22r run 529066: 25ns_591b_578_510_540_72bpi_12inj_3INDIV, 2 INDIV ALICE


LHC22r_529066: /alice/data/2022/LHC22q/529005/apass4_lowIR
LHC22o_527826


Maurice messages
https://github.com/AliceO2Group/AliceO2/blob/b335fa4071cc0e128a0faf18f81d59105ec75587/Detectors/Vertexing/src/VertexTrackMatcher.cxx#L139-L140

https://github.com/AliceO2Group/AliceO2/blob/dev/Detectors/ITSMFT/common/base/include/ITSMFTBase/DPLAlpideParam.h#L39


ninja install O2Physicsexe-analysis-fwdtrack-to-collision-associator