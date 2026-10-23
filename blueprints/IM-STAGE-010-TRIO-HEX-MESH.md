# IM-STAGE-010 — Blueprint 3D Cell Trio : prismes hexagonaux émissifs

> **NOTE DE TRAÇABILITÉ / 2026-10-22 :** le schéma de **trois centres alignés** documenté ci-dessous demeure une ancienne proposition, avec ses contrôles statiques historiques. L'utilisateur a ensuite désigné explicitement une référence visuelle à **trois pointes en triangle et cinq silhouettes**. Lire [IM-VIS-001 — audit de référence canonique candidate](IM-VIS-001-CELL-TRIO-CANON-CANDIDATE.md) et [paramètres proposés](IM-VIS-001-PARAMETERS.json) avant tout handoff DEV. **Aucune adoption canonique** n'est déduite de cette mise à jour ; le PASS géométrique antérieur n'établit pas la conformité à la nouvelle référence.


**Statut : BLUEPRINT_3D PROPOSED / PAS D'IMPLÉMENTATION**
**Date : 2026-10-22**
**Source gouvernance avant ce document :** `bbe8ca5ee4d74ef4a637012eaea234aec6c20c6d`, dépôt `emmanueltremblay9-stack/Immersive-Monolith-GPT`, branche `main`.
**Cible ASSUMED :** Minecraft **Java 1.21.1 / NeoForge**.
**Références :** `specifications/IM-CELL-TRIO.md`, `architecture/IM-009-NEOFORGE-MVP-CONTRACT.md`, `DECISION_LOG.md` et historique `qa/IM-STAGE-009-STATIC-GEOMETRY.md`.
**Auteur/portée :** MCA — conception géométrique ; DEV garde code, tests, commits et releases.

## 1. Invariants utilisateur — ne pas réinterpréter

- **Un seul emplacement de bloc Minecraft**, trois pointes collées côte à côte de hauteurs distinctes : **SMALL**, **MEDIUM**, **TALL**.
- **16 teintes vanilla** possibles pour chacune des pointes, y compris des compositions multicolores ; composition ordonnée et persistante.
- Le bloc apparaît naturellement **UNIQUEMENT sous terre, jamais en surface**. Placement manuel possible sur sol, mur et plafond.
- Les faces d'attache sont les **six faces** ; les rotations supplémentaires 0/90/180/270° autour de l'axe d'attache sont **proposées**.
- **1 cristal → 4 teintures** de la couleur correspondante.
- **3 teintures + 1 Glowstone Dust → 1 Cell Trio** (économie non validée).
- Trio **multicolore naturel** récolté : exactement **3 cristaux, un par pointe et couleur respective**, sans bonus Fortune. Le cas `CRAFTED` est conflictuel (IM-013), interdit à résoudre implicitement.
- Art/code/textures sont **originaux Dirty Studyo**. Ne pas incorporer directement de ressources HEXCraft/XyCraft non auditées.

## 2. Repère local et dimensions (valeurs proposées, vérifiées mathématiquement)

- Unité : **1 unité = 1/16 de bloc**.
- Origine : coin local `(0,0,0)` de la cellule ; axes X vers l'est local, Y vers le haut, Z vers le sud local.
- Bloc virtuel : `0 ≤ x,y,z ≤ 16`.
- Forme par pointe : **hexagone extrudé verticalement**, terminé par un sommet ; le tout inclus dans la cellule et sans air/blocs supplémentaires posés dans les cases voisines.
- Aucun remplacement de bloc autour n'est nécessaire : le volume est un **BlockState+BlockEntity unique**.

| Pointe | Centre (X,Z) | Y épaule | Y sommet | X min..max | Z min..max |
|---|---|---:|---:|---|---|
| SMALL | (4,8) | 4 | 6 | 2..6 | 6..10 |
| MEDIUM | (8,8) | 8 | 10 | 6..10 | 6..10 |
| TALL | (12,8) | 13 | 15 | 10..14 | 6..10 |

### Algorithme déterministe exact : positions et faces du maillage

Pour une pointe de centre `cX∈{4,8,12}`, de hauteur d'épaule `s` et de hauteur `t` :

1. Définir les **six points du polygone de base** dans le plan XZ, ordonnés :
   `[(cX−2,7),(cX−2,9),(cX,10),(cX+2,9),(cX+2,7),(cX,6)]`.
2. Produire les six sommets `B_i=(x_i,0,z_i)`, puis six sommets d'épaule `S_i=(x_i,s,z_i)`.
3. Ajouter le sommet `T=(cX,t,8)`.
4. Générer pour `i=0..5` une face latérale `(B_i,B_(i+1 mod 6),S_(i+1 mod 6),S_i)`, puis un triangle de pointe `(S_i,S_(i+1 mod 6),T)`.
5. Fermer le polygone de base par une triangulation de 4 triangles. Respecter les normales/winding selon le renderer choisi.
6. Attribuer **toutes les faces d'une pointe** à une couleur par **identité** (SMALL, MEDIUM, TALL), jamais selon leur ordre spatial après rotation. Les UV et la normale exacte des quads/triangles sont à tester avec NeoForge/OBJ.

Cela donne **13 sommets par pointe, 39 au total** ; **48 polygones** si les faces inférieures sont triangulées (18 quads latéraux, 18 triangles de pointe, 12 triangles de dessous). Les cristaux voisins partagent une arête verticale de contact aux plans X=6 et X=10 ; **aucune superposition volumique**.

**Collisions/projection (PROPOSED) :** une `VoxelShape` de trois AABBs orientées : SMALL X[2,6] Y[0,6] Z[6,10], MEDIUM X[6,10] Y[0,10] Z[6,10], TALL X[10,14] Y[0,15] Z[6,10]. Les boîtes comprennent des coins vides du vrai hexagone et devront être jugées en jeu : une shape plus fine peut être retenue si gêne joueur. `AIR` extérieur au maillage dans cette case ; toutes les autres positions du monde sont `KEEP`.

## 3. Orientations du bloc et plans de support — correctif de sémantique

Les faces sont celles **sur lesquelles le monolithe est fixé** : `UP` support sous le bloc à Y=0 ; `DOWN` support au-dessus à Y=16 ; `NORTH` support côté Z=0 ; `SOUTH` support côté Z=16 ; `EAST` support côté X=16 ; `WEST` support côté X=0.

Pour une position locale initiale `(x,y,z)` :
- `R90(x,y,z)=(z,y,16−x)` (rotation de quart de tour locale autour de Y) ; appliquer `R90` `q` fois, `q∈{0,1,2,3}`.
- Puis transformer `(x,y,z)` selon la face d'attache :

| Face | Transformée (X,Y,Z) | Plan de fixation vérifié |
|---|---|---|
| UP | (x,y,z) | Y=0 |
| DOWN | (x,16−y,16−z) | Y=16 |
| NORTH | (16−x,z,y) | Z=0 |
| SOUTH | (x,z,16−y) | Z=16 |
| EAST | (16−y,x,z) | X=16 |
| WEST | (y,16−x,z) | X=0 |

**Correction documentée** : l'ancien `qa/IM-STAGE-009-STATIC-GEOMETRY.md` présentait les **NORTH/SOUTH** et **EAST/WEST** avec des transformations dont le plan de fixation ne correspondait pas au nom de face. Le nouveau tableau est la correction proposée vérifiée géométriquement, **sans effacer l'historique ni affirmer que le jeu a validé la convention**.

Pour chaque face et rotation, transformer **tous** les sommets et les AABB via leurs 8 coins avant de recalculer les min/max. Les 24 configurations ont des géométries distinctes et restent dans le cube 0..16 selon le test local IM-STAGE-010.

## 4. Variantes visuelles et protocole des couleurs

- Toutes les pointes utilisent la **même géométrie**, indépendamment de leur couleur ; seul le matériau/tint change.
- Palette : `WHITE, ORANGE, MAGENTA, LIGHT_BLUE, YELLOW, LIME, PINK, GRAY, LIGHT_GRAY, CYAN, PURPLE, BLUE, BROWN, GREEN, RED, BLACK`.
- La configuration de trois valeurs fournit théoriquement `16^3 = 4096` combinaisons ordonnées et seulement **un bloc enregistré**.
- Aucun caractère RGB arbitraire hors des 16 couleurs dans ce MVP.
- Matière conceptuelle : noyau diffus/émissif avec contour facetté plus sombre, texture originale non déposée à ce stade ; transparence et bloom **facultatifs**.
- Luminosité vanilla proposée **10/15**, non mesurée ; vanilla Minecraft ne fournit pas nativement une lumière du monde colorée par teinture. Conserver la couleur du **cristal** indépendamment de la couleur de lumière environnante.

## 5. Stratégie de rendu — comparaison et choix proposé

| Solution | Statut | Compromis |
|---|---|---|
| **A. Modèle géométrique original + `BakedModel` dynamique / `ModelData` BlockEntity** | **VIABLE À TESTER, RECOMMANDÉ** | Une géométrie partagée, tint par identité de pointe, pas besoin de 4096 fichiers ; exige développement de loader/quad caching |
| B. Modèles statiques JSON/cuboids et coloris en états de bloc | **EXCLUDED comme design complet** | Les petits cuboids peuvent dépanner pour un mockup, mais les 4096 compositions × orientations rendent la maintenance lourde ; facettes hexagonales pointues non natives aux éléments cuboids |
| C. `BlockEntityRenderer` dynamique | VIABLE, **FALLBACK** | Flexibilité d'effets, coût rendu à mesurer si nombreux monolithes ; pas de coût par tick exigé |
| D. Resource pack seul | EXCLUDED | Ne crée pas l'enregistrement et les propriétés persistantes du nouveau bloc |

**Test discriminant recommandé** : prototype avec `BakedModel` et `ModelData` (trois `tintIndex` ou matériaux séparés), comparer avec BER dans une scène de quantité de blocs fixée, mesurer CPU/FPS, pas supposer une performance. En 1.21.1, un `Custom Model Loader` peut produire un `BakedModel` et des quads fondés sur `ModelData`. Bloquer toute hypothèse exacte de signature Java non vérifiée.

### Conditions de rendu

- Le serveur ne charge **jamais** de classes de rendu client ; serveur dédié sans crash.
- `BlockEntity` sans ticker ; mise à jour `ModelData` / recalcul uniquement lors de changements de couleurs/rotation/placement/synchronisation, pas chaque frame côté gameplay.
- `getQuads` ne reconstruit pas des milliers de vertices par appel ; utiliser base mesh immuable et cache borné ou par composantes.
- Cas `UNKNOWN` (données invalides/absentes) : couleurs de repli explicites et sûre, **pas d'origine NATURAL implicite**.
- Si le modèle utilise la transparence, vérifier tri et visibilité aux frontières de chunks ; préférer rendu opaque/cutout pour le premier MVP si tri instable.
- Réglage émissif client et vraie lumière du bloc sont **deux effets distincts** ; shaders et bloom sont facultatifs, jamais obligatoires.

## 6. Contrats au propriétaire DEV — non envoyés

**Entrées** : `TrioComposition(small,medium,tall,origin,schemaVersion)`, face et quart de tour ; 16 variantes dye.
**Sorties** : modèle visible côté client dans la case 1×1×1 ; AABBs tournées ; lumière stable ; restauration sans perte de l'ordre de trois couleurs.
**Tests demandés** : 6 faces × 4 rotations, tous changements d'axes, 16 couleurs, au moins 3 compos mixtes, déconnexion/reconnexion, save-reload, pickblock/Silk Touch sans double drop, serveur dédié, quantité élevée de blocs en scène.
**Invariants** : jamais « small devient medium » par rotation ; pas de duplication; aucun remplacement de blocs voisins; aucune modification de monde utilisateur.
**Arrêt** : sans arbitrage IM-013, garder fabrication/récolte exploitables et worldgen **désactivés** ; aucune publication.

## 7. Source et preuve

- Documentation NeoForge 1.21–1.21.1, consultée le 2026-10-22 : https://docs.neoforged.net/docs/1.21.1/resources/client/models/modelloaders/
- Baked Models : https://docs.neoforged.net/docs/1.21.1/resources/client/models/bakedmodel/
- BER : https://docs.neoforged.net/docs/1.21.1/blockentities/ber/
- **Vérification locale effectuée** : 24/24 positions uniques, zéro sortie du bloc, trois empreintes hexagonales tangentes sans recouvrement d'aire, plans de support correspondant aux noms de face.
- Artefacts du calcul : `imm_monolith_stage010_geometry_check.py` et `imm_monolith_stage010_geometry_report.json`, SHA-256 JSON `6e4562c99f89c60023e231ffb46da65d9f5855651b33b517216346f73f0954ae` (session de travail, script non déployé dans DEV).
- **Limites** : pas de build, rendu, loot ou génération Minecraft ; FPS/TPS UNKNOWN ; aucun fichier de modèle Blockbench/OBJ ni texture créée ou adoptée.

**Conclusion de conception** : un maillage original hexagonal déterministe avec identité SMALL/MEDIUM/TALL stable et 24 poses géométriques mathématiquement valides. Attente d'acceptation de l'owner avant implémentation DEV.
