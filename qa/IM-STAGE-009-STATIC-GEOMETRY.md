# IM-STAGE-009 — Validation géométrique statique (MCA)

**Date de calcul** : 2026-10-22
**Statut** : STATIC_COORDINATE_CHECK / EXECUTED, **PAS UN TEST DU JEU**
**Dépôt concerné** : documentation de conception uniquement ; aucun modèle ni code Minecraft testé.

## Entrées (coordonnées locales 0..16, modèle posé au sol)

| Pointe | X | Y | Z |
|---|---|---|---|
| SMALL | [2,6] | [0,6] | [6,10] |
| MEDIUM | [6,10] | [0,10] | [6,10] |
| TALL | [10,14] | [0,15] | [6,10] |

Ces volumes sont **proposés** et rectangulaires pour le calcul ; les facettes et pivots définitifs ne sont pas approuvés.

## Transformations mathématiques vérifiées

D'abord, rotations locales autour de l'axe Y, répétées 0..3 fois : `R(x,y,z)=(z,y,16-x)`.

Puis attachement sur chacune des six faces :
- UP : `(x,y,z)`
- DOWN : `(x,16-y,16-z)`
- NORTH : `(x,z,16-y)`
- SOUTH : `(x,16-z,y)`
- EAST : `(y,16-x,z)`
- WEST : `(16-y,x,z)`

Pour chaque boîte AABB, transformer les huit sommets, puis recalculer les minima/maxima des axes. Vérifier que toutes les bornes restent dans 0..16 et que l'intersection volumique de toute paire est zéro.

**Test exécuté :** script Python local éphémère, sans Minecraft, forge/mod, fichiers utilisateur ni accès aux mondes.

## Résultat

| Assertion statique | Résultat |
|---|---|
| 6 faces × 4 rotations = 24 cas | PASS — calcul local |
| Géométries distinctes | 24 / 24 — calcul local |
| Toutes les coordonnées dans un bloc 16×16×16 | PASS — calcul local |
| Aucune superposition volumique des 3 volumes | PASS — calcul local |

Les volumes **partagent volontairement des surfaces de contact** ; l'absence de recouvrement volumique ne prouve pas la qualité visuelle d'un modèle facetté.

## Limitations et tests nécessaires

- Ne valide ni l'API NeoForge `VoxelShape`, ni le placement réel des 24 orientations, ni le comportement des murs/plafonds, ni le rendu et la transparence, ni les collisions des joueurs, ni les effets de lumière.
- Ne valide ni la génération sous terre, ni le support `canSurvive`, ni la sauvegarde `BlockEntity`, ni la teinture, ni les drops.
- Ne mesure aucune performance : FPS/TPS = UNKNOWN.
- Un passage en revue moteur 1.21.1 et des tests client/serveur isolés restent nécessaires pour le PASS en jeu.
