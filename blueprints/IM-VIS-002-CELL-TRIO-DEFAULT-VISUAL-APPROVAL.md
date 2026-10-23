# IM-VIS-002 — Validation visuelle du Cell Trio DEFAULT

**Décision : APPROVED_VISUAL_REFERENCE** (requête explicite de l'utilisateur : « Block validate this model » dans la conversation du 2026-10-22).  
**Portée approuvée : silhouette, composition, matériaux et aspect visuel de la variante DEFAULT uniquement.**  
**PAS une validation technique, un modèle Blockbench adopté, une preuve Minecraft ou un feu vert à modifier DEV.**  
**Dépôt de gouvernance :** `emmanueltremblay9-stack/Immersive-Monolith-GPT`, branche `main`, HEAD de référence avant écriture `1fd270c17f05e81d7e8b6a3789cf19d32cbff320`.  
**Cible ASSUMED :** Minecraft Java 1.21.1 NeoForge ; version précise de loader, licence de diffusion et environnement de test : UNKNOWN.  
**Owner de conception : MCA ; owner du mod/DEV garde adoption des paramètres, implémentation, QA et releases.**

## 1. Référence identifiée

- **Image de référence explicitement validée :** `glowing_crystal_block_showcase.png`, 1672 × 941, RGB, SHA-256 **`1e4401fc76220d73745bbe272c7ba0babdf561b8e8c41562c730176f5b448019`**.
- Image créée antérieurement dans la conversation (rendu conceptuel), puis remise par l'utilisateur à l'examen avec la demande de validation du bloc.
- Les trois panneaux retenus : **BLOCK MODEL / ONE BLOCK (1 × 1 × 1)**, **IN-GAME FLOOR PLACEMENT**, et **WALL PLACEMENT**.
- Le PNG de référence est **une pièce jointe de la conversation**, **non déposée comme binaire dans Git**, et ne constitue **pas** un asset Minecraft 3D/voxel/texturé ou un test du jeu. Son empreinte permet la comparaison exacte lorsque le fichier sera fourni à DEV.
- Sources visuelles précédentes conservées dans `IM-VIS-001` (cinq morphologies). `IM-VIS-002` ne les remplace pas globalement ; il précise **DEFAULT**. Ne pas modifier les IDs historiques.

## 2. Caractères DEFAULT validés par la décision utilisateur

| Élément | Référence visuelle approuvée |
|---|---|
| Position | Trois cristaux contigus dans **un seul block-position** |
| Composition | TALL à l'arrière-centre, MEDIUM à l'avant-gauche, SMALL à l'avant-droite |
| Silhouette | Trois **prismes essentiellement hexagonaux, à sommets plats**, plus long/moyen/court ; aucun pic obligatoire dans DEFAULT |
| Matière cristal | **Translucide bleu/cyan**, corps visible en profondeur avec facettes opaques/semi-transparentes et contours brillants |
| Structure rocheuse | Socle commun en pierre sombre, soubassements individuels et **trois coiffes de pierre** presque planes, chacune avec une petite face supérieure bleutée |
| Effet lumineux | Arêtes bleues lumineuses, cœur cyan clair, relief distinct en obscurité ; l'effet de halo de l'image n'est pas automatiquement fourni par Minecraft vanilla |
| Identités | Chaque pointe garde son identité SMALL/MEDIUM/TALL lors de toute rotation, même si sa couleur devient différente |
| Couleurs | L'exemple bleuté illustre **une** des 16 couleurs vanilla prévues pour chacune des trois pointes ; il ne retire aucune autre couleur |
| Look | Style voxel/pixel-art 3D original, éviter de reproduire directement des textures, maillages ou UV de HEXCraft |

L'image montre un ensemble **compact et à base commune** ; les trois colonnes ne sont **pas trois blocs verticaux** et ne doivent pas occuper trois `BlockPos`.

## 3. Traduction géométrique PROPOSED (non approuvée métriquement)

L'infographie est **non métrique** : les flèches « 1 BLOCK » et les proportions à l'écran ne démontrent pas que l'objet réel rentre dans `[0,16]^3`. Se référer aux coordonnées révisables de `blueprints/IM-VIS-001-PARAMETERS.json`, et recalculer toutes les intersections/UV pour la vraie nouvelle géométrie.

Proposition d'implémentation : bloc unique `immersive_monolith:cell_trio` avec `shapeVariant=DEFAULT` dans ses données d'instance, et 3 `DyeColor`. `BlockState` contient la face d'attache et le roulis axial, `BlockEntity` la composition/forme/provenance. Les 24 poses (6 faces × 4 rotations) restent une **proposition à tester**, non un engagement d'une géométrie déjà validée dans le jeu.

**Ne pas confondre image et asset :** l'OBJ du package `IM-VIS-001` est un maillage analytique de référence et doit être **comparé puis ajusté** aux trois coiffes épaisses, au socle en relief, aux proportions et aux teintes du nouveau visuel. Aucun bloc final n'est prêt à intégrer depuis cette seule image.

## 4. Compatibilité Minecraft 1.21.1 : audit ciblé

### Options de rendu
- **VIABLE À PROTOTYPER (recommandé) :** un custom model loader client créant un `BakedModel` dynamique et utilisant `ModelData` pour récupérer les trois couleurs depuis la `BlockEntity`.
- **Double couche de matériau à tester :** `SOLID` ou `CUTOUT` pour la roche, `TRANSLUCENT` pour les prismes de cristal.
- **Alternative viable en cas de tri incorrect :** `BlockEntityRenderer`, à comparer sur scène reproductible. Un BER peut augmenter le coût de rendu ; aucun TPS/FPS mesuré.
- **Non suffisants :** resource pack seul pour nouvel ID de bloc + état par pointe; simple PNG de masque pour rendre automatiquement les facettes fullbright; shaders imposés pour l'usage du bloc.

### Risques visuels et mécaniques identifiés
1. **Tri alpha / surfaces internes :** trois prismes transparents adjacents + pierre opaque peuvent se recouvrir visuellement selon angle, orientation, chunk et distance. Tester de face, de biais, derrière, en contact d'autres blocs.
2. **Émission ≠ lumière colorée :** `lightLevel` émet de la lumière vanilla non teintée ; l'effet de halo bleu au sol de l'image est un effet graphique, pas un résultat garanti. Une couche fullbright doit être vérifiée séparément.
3. **La scène WALL est ambiguë :** elle représente un trio vertical fixé à une paroi. Le mécanisme HEXCraft de placement six faces oriente normalement l'axe de la formation selon la normale du support. Valider **comment** les cristaux pivotent et dépassent horizontalement ; ne pas considérer le panneau comme preuve de ce comportement.
4. **Vue CEILING absente :** une pose inversée doit être testée, sans clipping dans le support ou le bloc voisin.
5. **Enveloppe 1×1×1 :** pierre/coiffes ne doivent pas dépasser `0..16`, et `VoxelShape` doit respecter les caps + socle, pas seulement l'ancien volume cristallin.
6. **Item 3D :** composition colorée visible en GUI, main et item-frame même sans `BlockEntity` d'item; préserver données via composants.
7. **Client/serveur :** aucune classe client chargée en serveur dédié, pas de ticker pour seule coloration statique.

**Aucune de ces contraintes n'est prouvée par l'image.** La qualification du rendu réel reste `NOT_TESTED`.

## 5. Limites de la validation et héritage

Ce que « APPROVED_VISUAL_REFERENCE » **ne valide pas** :
- les nombres X/Y/Z, hauteurs, rayons, UV et géométries exactes du prochain mesh ;
- les quatre autres formes `TAPERED/SMOOTH/ANGLED/SPIKE` de `IM-VIS-001` (restent candidates) ;
- textures PNG finales, licence et diffusion des ressources ;
- comportement de `TRANSLUCENT` réellement observé, luminosité ou bloom ;
- génération naturelle, drops, recettes ou économie (`IM-013` toujours OPEN/BLOCKING) ;
- un commit dans `Immersive-Monolith-DEV-`, un build NeoForge, une release ou PASS Minecraft.

Règles utilisateur inchangées : **jamais de génération naturelle à la surface**, pose manuelle possible sur six faces ; 3 cristaux logiques, 16 couleurs ; 1 cristal → 4 teintures ; recette 3 teintures + 1 Glowstone Dust ; trio multicolore naturel récolté = 3 cristaux, un par couleur respective, pas de Fortune. La question des trios fabriqués reste ouverte.

## 6. Conditions d'acceptation du VRAI modèle en jeu

| Précondition | Action | Attendu | Preuve requise |
|---|---|---|---|
| MDK 1.21.1 accepté + JDK 21 + mesh DEFAULT versionné | Build + démarrage client | Modèle enregistré et textures chargées | commit DEV + JAR SHA-256 + logs |
| Monde de test isolé, grille 1×1×1 | Afficher le bloc de six angles | Tout contenu dans le `BlockPos`, sans clipping | captures in-game et vérification bounds |
| Shader désactivé, obscurité complète | Observer cristal + pierre | Cristal translucide et lumineux, pierre opaque ; pas de textures manquantes | captures avec paramètres de jeu |
| Cristaux bleu/rouge/vert | Placer au sol puis sauvegarder | Identités/couleurs conservées | données BE/ItemStack et captures |
| Mur N/E/S/O + plafond, 4 rolls | Placer en changeant de support | Fixation et forme cohérentes selon faces, collisions vérifiées | 24 états + captures et test support |
| Forte population de trios | Comparer modèles dynamiques et BER | Performance acceptable selon budget à définir, pas de crash | FPS/frametime, GPU/CPU, monde test |
| Serveur dédié, client compatible | Démarrage + join | Aucune erreur client-only, état synchronisé | logs de session |
| Sans worldgen/loot actifs | Charger nouveau monde | Aucune génération ou boucle économique non approuvée | preuves de configuration |

**Décision enregistrable :** le **visuel du bloc DEFAULT** est approuvé par l'utilisateur comme référence. Le **modèle moteur ne peut pas être déclaré PASS** avant réalisation, test et acceptation par le propriétaire DEV.

## 7. Sources externes et licence

- Documentation technique NeoForge **Minecraft 1.21–1.21.1**, consultée le 2026-10-22 :
  - https://docs.neoforged.net/docs/1.21.1/resources/client/models/modelloaders/
  - https://docs.neoforged.net/docs/1.21.1/resources/client/models/bakedmodel/
  - https://docs.neoforged.net/docs/1.21.1/blocks/
- Référence historique : `CelesTek-Team/HexCraft` 1.7.10, sous **CC BY-NC 4.0** d'après son fichier LICENSE, et HEXCraft 1.12.2 sous MIT : le traitement des droits aux textures et au code n'est pas déduit automatiquement d'un simple nom de licence.
- **Ce document décrit un design autonome. Aucun code, modèle ou texture HEXCraft n'est repris.**

**Transmission vers DEV : NOT_SENT. Exécution DEV : NOT_EXECUTED. Décision sur les métriques : PROPOSED.**
