# IM-VIS-003 — Cell Trio DEFAULT r01 : modèle 
**Status : USER_APPROVED_MODEL_REFERENCE.** Validation explicite de l'utilisateur : « That is exactly how I want it », accompagnée du fichier `Cell_Trio_DEFAULT_r01(1).bbmodel`.

**Portée de l'approbation :** utiliser **CE FICHIER exact** comme référence de modèle 3D et d'aspect pour `DEFAULT`. Il prime sur les anciennes esquisses divergentes (notamment le blueprint linéaire IM-STAGE-010). Il ne prouve **ni** un build de mod, **ni** un rendu NeoForge, **ni** l'adoption des dimensions de toutes les variantes `TAPERED/SMOOTH/ANGLED/SPIKE`.

## Identité de l'artefact soumis

- Original attaché : `Cell_Trio_DEFAULT_r01(1).bbmodel`
- SHA-256 du fichier complet : `f2c09b84432c2675213f52dc92311a883c143ab1cac6fce37c51490dfbc4fef6`
- Taille : **144 392 octets**
- `name` : `Cell_Trio_DEFAULT_r01`
- `model_identifier` : `immersive_monolith_cell_trio_default_r01`
- `meta.format_version` : `5.0`
- `meta.model_format` : `free` (modèle Blockbench libre, **non** export Java/NeoForge prêt-à-charger)
- **Aucun fichier binaire joint à ce commit Git**. L'artefact exact doit être conservé ou transféré au propriétaire DEV par un canal autorisé, en vérifiant l'empreinte.

## Contrôle statique du fichier (lecture/extraction JSON et PNG, pas Minecraft)

| Contrôle | Observation |
|---|---|
| Document JSON | Lecture réussie |
| Maillages `elements` | **13**, `type=mesh` |
| Groupes | BASE, SMALL, MEDIUM, TALL |
| Faces | **905** (797 quads + 108 triangles) |
| Triangles après triangulation simple des quads | **1 702** (calcul, pas un build moteur) |
| Références UV | **3 512 entrées**, tous les identifiants de sommets des faces résolus |
| Textures embarquées | **5 PNG RGBA, 32×32** : `core_blue`, `edge_blue`, `inset_blue`, `shell_blue`, `stone` |
| Alpha du noyau `core_blue` | Min 92 / Max 137 (translucide) |
| Alpha de la coque `shell_blue` | Min 86 / Max 205 (translucide) |
| Alpha des autres textures | 255 (opaques) |
| Enveloppe native (unités auteur) | X [−6.2, +6.2], Y [0, 14.12], Z [−6, +6] |
| Volume après recentrage X/Z par +8 | X [1.8,14.2], Y [0,14.12], Z [2,14] → **dans 16×16×16** par calcul |
| Nommage des pièces | `FOUNDATION_SHARED`; trois ensembles `FOOT_COLLAR / CRYSTAL_SHELL / INNER_CORE / EMISSIVE_EDGES` |

**Réserve importante :** ce `.bbmodel` contient **13 maillages** et non les « 19 éléments » annoncés précédemment pour un package différent. Aucune pièce distinctement nommée `TOP_CAP` n'apparaît dans la liste ; les trois coiffes rocheuses visibles dans l'illustration historique doivent être comparées au rendu de **cet artefact** avant d'affirmer leur existence structurelle. L'approbation du fichier n'efface pas cette différence de preuve.

## Modèle ciblé (conservé)

- Un seul emplacement Minecraft `1×1×1`, trois pointes logiques SMALL / MEDIUM / TALL en triangle.
- Coque et cœur de cristal bleus translucides, arêtes lumineuses ; matériau de pierre opaque.
- Matériaux et formes originaux, **HEXCraft seulement comme inspiration**. Aucun code, mesh, modèle, texture ou UV HEXCraft repris.
- Palette future : 16 `DyeColor` pour chaque pointe (soit 4096 combinaisons) ; l'artefact r01 fourni contient **seulement les textures bleues**. L'adaptation dynamique aux couleurs n'est pas intégrée dans le fichier r01.
- Placement prévu sur 6 faces + rotation, génération naturelle **jamais en surface** et recette/drops selon décisions antérieures (le conflit `IM-013` est toujours ouvert).

## Critères nécessaires avant « jeu PASS »

1. Charger le projet dans Blockbench, vérifier maillages et aperçu — **pas encore exécuté**.
2. Importer/convertir sous contrôle DEV en modèle compatible NeoForge 1.21.1 ; texture atlas, UV, modèle d'item et couche transparente.
3. Afficher un vrai bloc dans le jeu, tester translucence et ordre du rendu alpha à 6 faces × 4 rotations, sans shader.
4. Vérifier les 16 couleurs, sauvegarde, synchro client-serveur et `VoxelShape` du maillage réel.
5. Exécuter tests de support, de luminosité, de chunks et dédiés serveur ; transmettre logs, captures et JAR SHA.

Les validations de design ne prouvent pas l'implémentation ou le fonctionnement réel. **Build = NOT_TESTED ; Render = NOT_TESTED ; DEV = NOT_MODIFIED ; Publication = NOT_DONE.**

## Provenance & autorité

Précédents : `IM-VIS-001` (candidat 5 morphologies), `IM-VIS-002` (approbation de l'image DEFAULT). La présente décision `IM-VIS-003` **approuve la référence `.bbmodel` identifiée par SHA** en tant que modèle de référence retenu. Les paramètres non vérifiés des cinq morphologies restent PROPOSED.

Gouvernance : `emmanueltremblay9-stack/Immersive-Monolith-GPT`, branche `main`, référence de base vérifiée `ce0a0834c1c37e656a73b8dec6f7018c72d78df4`. Dépôt DEV : `emmanueltremblay9-stack/Immersive-Monolith-DEV-`, vide/sans HEAD à cette étape. La création de ce document ne transmet pas un handoff au propriétaire DEV et ne constitue pas une permission de création de code ou de publication.
