# IM-VIS-001 — Cell Trio : référence visuelle sélectionnée et modèle canonique candidat

**Référence visuelle : SELECTED par l'utilisateur.** L'image avec le panneau VARIATIONS cerclé sert de référence prioritaire.  
**Coordonnées, 5 maillages et intégration : PROPOSED, NON ADOPTÉS.** Ne pas confondre référence choisie avec acceptation d'un blueprint.  
**Audit : 2026-10-22 (America/Toronto). Minecraft : Java 1.21.1 NeoForge supposé. Tests jeu : NOT_TESTED.**  
**Repo :** `emmanueltremblay9-stack/Immersive-Monolith-GPT`, HEAD de base `274834ccd6da57cc15a0bffc1f5701ac7a2e2785`.  
**DEV :** `emmanueltremblay9-stack/Immersive-Monolith-DEV-`, vérifié vide/sans HEAD avant cette rédaction ; **aucun code, modèle ou asset n'y est ajouté**.  
**Auteurs/autorités :** MCA = analyse et spécification, owner du mod = adoption, DEV = code, QA, builds, releases.

## 1. Ordre des références

1. **Visuel sélectionné par l'utilisateur** `Cell Trio Crystal Build Guide(1).png` (1671×941, SHA-256 `36aa174b4cf2306986234c7545178b0164b0165c59312cde6c883fc4d8840563`) : volumes, arrangement spatial, matériaux et cinq styles.
2. **Annotation encadrant les styles** `image-markup-preview-file_000000004b6881f6a30490342c83aa6b.png` (1671×941, SHA-256 `c347c518a24c5510764e77304c281eb15537f281e3fc5326dbb633e3e29ac145`) : sélection explicite `DEFAULT / TAPERED / SMOOTH / ANGLED / SPIKE`.
3. **Capture HEXCraft fournie par l'utilisateur** `image.png` (151×197, SHA-256 `ef77b011c2496345583bf55cd9f7b907c1aabcfc9ded83d1bd6ba1642394c`) : référence d'idée (prisme hexagonal, cristal/roche, six faces de placement), **pas un artefact source pouvant être copié**.
4. Rendus IA suivants : inspiration secondaire, pas une source de dimensions.
Ces visuels sont des **attachments conversation**, PAS des binaires GitHub ni des plans 3D métriques. Les empreintes identifient les images effectivement inspectées.

## 2. Constat précis sur l'image retenue

- Un seul volume de bloc Minecraft **1×1×1** contient 3 cristaux attachés : **TALL arrière-centre ; MEDIUM avant-gauche ; SMALL avant-droite**. Le plan au sol est un **triangle**, PAS 3 colonnes alignées en ligne.
- Les cristaux ont des **hauteurs relatives** croissantes. Le panneau graphique « 1/2/3 blocks high » est un effet de présentation, incompatible avec la consigne supérieure **un seul block-position** : ne jamais reproduire littéralement 3 blocs en hauteur.
- Socle rocheux **continu**, trois bagues/colliers de pied ; pierre sombre distincte de l'émission, couronnes minérales variables aux sommets.
- Facettes polygonales et arêtes lumineuses, profondeur lumineuse interne, deux tons par facette ; le rendu doit fonctionner **sans shader** pour la lisibilité. La lumière colorée de la caverne est **illustrative**, pas une lumière RGB vanilla.
- Couleur définie **par pointe**, pas par colonne spatiale après rotation.
- Les 5 noms désignent **5 silhouettes différentes**, et pas 5 PNG ou niveaux de rareté.

### Morphologie des 5 styles (proposition lisible à l'image)

| Style | Forme | Coiffes rocheuses proposées |
|---|---|---|
| `DEFAULT` | 3 prismes hexagonaux plats, facettes droites, pierre | SMALL, MEDIUM, TALL |
| `TAPERED` | SMALL/TALL pointes longues qui se resserrent ; MEDIUM droite | MEDIUM |
| `SMOOTH` | SMALL/TALL épaules progressives et extrémités adoucies ; MEDIUM droite | MEDIUM |
| `ANGLED` | TALL sommet biseauté asymétrique ; autres droites | SMALL, MEDIUM |
| `SPIKE` | TALL grande aiguille et sous-aiguilles faisant partie **du même cristal**, autres droites | SMALL, MEDIUM |

**Les sous-aiguilles SPIKE ne deviennent jamais un 4e cristal** : le loot demeure 3 pointes logiques SMALL/MEDIUM/TALL.

### Anomalies de l'infographie — non canoniques

- `NETHERITE` n'est **pas** une des 16 teintures vanilla ; remplacer la palette graphique par les 16 `DyeColor` exactes : WHITE, ORANGE, MAGENTA, LIGHT_BLUE, YELLOW, LIME, PINK, GRAY, LIGHT_GRAY, CYAN, PURPLE, BLUE, BROWN, GREEN, RED, BLACK.
- Les mots COMMON/RARE/EPIC/LEGENDARY ne constituent **pas** un système de rareté approuvé ; aucune probabilité couleur/forme ne doit en être déduite.
- Les recettes illustrées dans l'image sont secondaires : préserver les **règles exprimées en texte par l'utilisateur** (3 teintures + glowstone; 1 cristal → 4 teintures; 3 drops colorés sur un trio multicolore), sous réserve du conflit économique IM-013 non résolu.

## 3. Redline : ancienne géométrie vs cible

Le blueprint `blueprints/IM-STAGE-010-TRIO-HEX-MESH.md` propose un alignement de centres `X=4,8,12` avec `Z=8` et trois profils identiques : c'est une **approche antérieure mathématiquement testée**, pas la meilleure traduction visuelle du choix récent. Ne pas effacer ni réinterpréter son PASS statique comme une preuve du nouveau triangle.

Correction proposée, priorité **BLOCKING POUR CANON MODEL** :
1. Passer d'une **ligne** à un **triangle**.
2. Passer d'une unique géométrie à **5 morphologies**, en conservant 3 identités de pointe.
3. Ajouter base de pierre continue + bagues de pied + coiffes spécifiques.
4. Recalculer colliders/UV/normal maps par style et les 24 poses ; l'ancienne `VoxelShape` n'est plus représentative.
5. Persister `shapeVariant` dans les données par bloc et l'item, au même titre que les 3 couleurs.
6. Stage-011 sprites 32×32 : **palette exploratoire**, pas matériaux visuels canoniques finalisés.

## 4. Blueprint spatial déterministe CANDIDATE

**Unités : 1/16 bloc ; enveloppe locale : `[0,16]^3`.**  
Orientation posée au sol : +X = droite, +Y = haut, +Z = arrière (loin de l'observateur face au groupe). L'extérieur du modèle dans son block-position reste vide visuellement ; toutes les autres positions de monde = **KEEP**.

| Identité | Centre X | Centre Z | Hauteur totale Y | Position |
|---|---:|---:|---:|---|
| `SMALL` | 11.2 | 5.5 | 7.0 | avant-droite |
| `MEDIUM` | 4.8 | 5.5 | 11.0 | avant-gauche |
| `TALL` | 8.0 | 11.0 | 15.2 | arrière-centre |

- Chaque cristal : hexagone de rayon circonscrit **2.55**, à `(cx,cz)` ; points `P_k=(cx + 2.55 cos(kπ/3), cz + 2.55 sin(kπ/3))`, `k=0..5` ; corps depuis `Y=1.70`, profil supérieur selon `shapeVariant`.
- Base unique : enveloppe convexe des **trois** hexagones de rayon 3.16 extrudée de `Y=0` à `1.55`, complétée par trois colliers de rayon 3.02 entre `Y=1.20..1.87`.
- Chaque couronne: rayon **3.02**, partie supérieure à `Y=h`, épaisseur verticale ~0.76. TAPERED/SMOOTH/ANGLED/SPIKE emploient des terminaisons propres à la famille listée ci-dessus.
- Le modèle occupe un **seul BlockPos**. Les trois cristaux ne s'interpénètrent pas ; la pierre du socle reste volontairement une base commune.
- Aucun air ni bloc de monde supplémentaire ne doit être posé en dehors de cette cellule pour le rendu.
- Six faces UP/DOWN/NORTH/SOUTH/EAST/WEST et quatre rolls par face : transformer toute la géométrie rigidement et conserver l'association `SMALL / MEDIUM / TALL`.

### Contrôle mathématique exécuté en dehors de Minecraft

Un nouveau générateur procédural de **5 OBJ de référence** et un validateur Python ont été exécutés (fichiers disponibles comme package de conversation, pas sur Git).
- **5 styles × 24 poses = 120 orientations** testées, toutes **dans** `[0,16]^3` et distinctes par style.
- Rayons/gaps géométriques : SMALL/MEDIUM `1.30` unité 1/16 entre les empreintes de cristaux ; SMALL/TALL et MEDIUM/TALL `1.2633`. Les coiffes sont séparées de `0.36` et `0.3238` ; la base est commune par construction.
- Aucun dépassement de cellule. Les formes OBJ ne sont **PAS** des assets Minecraft JSON, un rendu émissif, une extraction de JAR, ni un test NeoForge.
- **Rapport local** : `static_geometry_report.json`; **script local** `check_reference_geometry.py`. Package de discussion `Immersive_Monolith_IM-VIS-001_Model_Reference_Candidate.zip`, SHA-256 `5654dd27ccc604bdb80aa3b1ceb9cf9dc72e83c99539e014df1f0f6710526561`. À importer seulement après autorisation de DEV.

## 5. Architecture de rendu (NeoForge 1.21.1 — à tester)

Un ID enregistré proposé : `immersive_monolith:cell_trio`; pas de 20 480 blocs. `BlockState` = attachment face (6) + axial roll (4). `BlockEntity` = 3 DyeColor ordonnés + `shapeVariant` (5) + `origin` + `schemaVersion`. L'item récupéré doit conserver ces données via DataComponents appropriés, sous décision Silk Touch.

- Apparences théoriques = `5×16³ = 20 480`; avec orientations : `491 520` combinaisons de rendu possibles. **Ne pas précréer autant de modèles** ; favoriser 5 bases de morphologie × 24 orientations (=120 caches géométriques candidats) et distribution des sprites par pointe.
- Favoriser un custom `IGeometryLoader` vers `IDynamicBakedModel` avec `ModelData` fournie par BlockEntity, ou un renderer BER fallback testé si nécessaire. Tout traitement de couleur doit rester associé à **l'identité** de pointe.
- Texture basique colorée ≠ émission fullbright ≠ lumière vanilla ≠ bloom. `BlockBehaviour.Properties.lightLevel` 10/15 reste **PROPOSED**, sans éclairage RGB. Tester indépendamment du shader, y compris faces noires/grises/blanches.
- Stone/cap, cristal, arête émissive et facette sombre doivent avoir des surfaces/matériaux distincts. Transparence vraie facultative (tri et surdessin à mesurer); aucun tick serveur requis pour 3 couleurs statiques.
- Modèle item : les données BlockEntity ne sont pas automatiquement présentes à l'inventaire ; prévoir rendu item à partir de ses composants pour préserver style et 3 couleurs.

Documentation NeoForge 1.21.1 :
- https://docs.neoforged.net/docs/1.21.1/resources/client/models/modelloaders/
- https://docs.neoforged.net/docs/1.21.1/resources/client/models/bakedmodel/
- https://docs.neoforged.net/docs/1.21.1/blockentities/

## 6. Référence HEXCraft : portée et droits

`BlockHexoriumMonolith.java` : monolithe d'origine attachable aux six faces, contrôle du support et drops de cristaux, initialement cinq couleurs. `HexModelRendererMonolith.java` : forme prismatique hexagonale et rendu cristal/pierre. **Ce mod ne fournit pas notre modèle Cell Trio triangulaire ni ses cinq variations**.

Sources :  
https://github.com/CelesTek-Team/HexCraft/blob/master/src/main/java/com/celestek/hexcraft/block/BlockHexoriumMonolith.java  
https://github.com/CelesTek-Team/HexCraft/blob/master/src/main/java/com/celestek/hexcraft/client/renderer/HexModelRendererMonolith.java  
https://github.com/CelesTek-Team/HexCraft/blob/master/LICENSE

Le dépôt original est annoncé CC BY-NC 4.0 ; aucun code, mesh, texture ni UV CelesTek ne doit être copié sans revue/licence autorisant l'usage concerné. Le blueprint candidate est **nouveau et procédural** ; HEXCraft est une **inspiration mécanique et esthétique générale**, pas une source de fichiers redistribuables.

## 7. Handoff, décisions et critères d'acceptation

**À faire accepter par l'owner** : (A) triangle arrière TALL / devant MEDIUM-SMALL ; (B) cinq variants de mesh ; (C) socle/couronnes ; (D) proportions X/Y/Z du candidat ; (E) stratégie de rendu dans le moteur. Séparer chaque décision de sa mise en œuvre.

| Test futur | Précondition/action | Attendu et preuve |
|---|---|---|
| Silhouettes | Isoler les 5 styles en vue de face, plan haut et profil | 5 profils distincts conformes à l'image; captures et mesh validé |
| Rotations | Chaque style sur 6 faces × 4 rolls | 120 placements sans inversion SMALL/MEDIUM/TALL, pas de bloc voisin détruit |
| 16 DyeColor | Mélange 3 couleurs, test noir/gris/blanc | 16 IDs exacts et identité des pointes persistante |
| Monde/sauvegarde | Place/reload serveur dédié/client | style et 3 couleurs conservés |
| Lumière sans shader | Grotte obscure puis lumière 0 | distinction albedo/fullbright/block light documentée |
| Performance | scène 1/64/256/1024 blocs en runtime isolé | mesures reproductibles FPS/TPS/frametime (actuellement UNKNOWN) |
| Worldgen futur | Inspecter surfaces/cavernes/dimensions | aucune génération naturelle en surface, partout où cavité admissible |
| Loot futur | Trio multicolore récolté | 3 cristaux exactement, chacun correspondant à sa pointe; pas de bonus Fortune |

**Économie IM-013 toujours ouverte** : la combinaison de recette `3 dyes + glowstone` et rendement `1 crystal→4 dyes` ne doit pas être implémentée avec un recyclage du trio fabriqué sans arbitrage explicite. Ce blocage n'empêche pas l'étude de la géométrie.

**Statut final de ce document : MODEL_CANON_CANDIDATE / GIT_DRAFT.** Ceci n'adopte pas les paramètres, ne change pas les IDs historiques, n'exécute pas de handoff DEV et ne déclare aucun test Minecraft réussi.
