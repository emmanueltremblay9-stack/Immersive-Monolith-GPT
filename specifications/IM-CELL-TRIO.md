# IM-CELL-TRIO — spécification de travail

Statut global : **DRAFT / SPEC**, pas de preuve de build ou d'exécution Minecraft.
Origine : exigences exprimées dans la conversation du 22 octobre 2026 ; géométries et densités issues de propositions MCA. Cible supposée : Java 1.21.1 NeoForge.

## 1. Identité et objectif

Nom du projet : **Immersive Monolith**, `mod_id` proposé : `immersive_monolith`.
Bloc principal : **Cell Trio**, cristal lumineux décoratif/récoltable.
Aucun besoin moteur ne justifie de copier le code ou les textures de HEXCraft : créer un modèle original, ne réimplémenter que le *principe* de placement six faces.
Addon AE2 futur possible, **hors MVP**.

## 2. Exigences utilisateur conservées (USER-DIRECTED)

- Chaque monolithe occupe **un bloc**, contient **trois pointes solidaires** côte à côte : petite, moyenne, grande.
- **16 couleurs correspondant aux teintures vanilla** ; un Cell Trio peut être monochrome ou multicolore.
- Cristaux **émissifs** ; distinguer rendu émissif des textures, lumière de bloc vanilla et bloom dépendant du client.
- Le joueur peut placer le bloc sur **sol, plafond ou mur** ; se fixer au support validé.
- Génération naturelle **exclusivement souterraine**, jamais sur une surface exposée ; couvrir les emplacements souterrains compatibles.
- **1 cristal d'une couleur → 4 teintures de cette couleur**.
- Fabrication voulue : **3 teintures + 1 Glowstone Dust → un Cell Trio** ; association des trois couleurs aux pointes à finaliser.
- Pour un trio à **au moins deux couleurs différentes** : destruction de récolte ordinaire → **exactement trois cristaux au total**, **un par pointe**, de sa couleur respective ; **aucun bonus Fortune** sur le trio multicolore.

### Conservation

- Une formation multicolore `{small=cyan, medium=magenta, tall=yellow}` donne exactement `1 cyan + 1 magenta + 1 yellow`.
- Une formation bicolore `{small=blue, medium=blue, tall=purple}` donne `2 blue + 1 purple`, ni plus ni moins.
- En cas de Silk Touch, **proposition** : obtenir le bloc intact qui conserve la composition et son origine ; pas de cristaux en parallèle.
- Une perte de support, une récolte et une explosion concomitantes ne doivent jamais créer de double drop. Préciser une seule voie serveur de destruction.

## 3. Paramètres géométriques (PROPOSED, non adoptés)

Espace local : `[0..16]^3` unités, modèle original entièrement contenu dans un seul block-position.
En orientation posée au sol (Y+), volumes indicatifs :

| Pointe | X | Y | Z |
|---|---|---|---|
| Petite | [2,6] | [0,6] | [6,10] |
| Moyenne | [6,10] | [0,10] | [6,10] |
| Grande | [10,14] | [0,15] | [6,10] |

Rendre les pointes facettées et solidaires de la base ; volumes listés = **boîtes simplifiées**, pas maillage final.
Orientation : six faces × rotation locale quart de tour = 24 orientations prévues, **sous réserve de validation des transformations et du voxel shape**.
Collisions : un voxel shape prédéfini selon la direction/rotation, pas une boîte pleine. Support requis sur la face associée ; perte de support : destruction maîtrisée.
Aucun modèle JSON/Blockbench validé, aucune texture validée.

## 4. Persistance, composition et provenance (PROPOSED)

- BlockState : face de support / rotation. Les 3 couleurs et la provenance (`NATURAL` / `CRAFTED`) dans une BlockEntity et dans les composants de l'item récupéré si nécessaire.
- Toute opération serveur qui place/récupère le bloc doit conserver ces propriétés ; client synchronisé sans pouvoir imposer un drop.
- Les exemples de teinture dans les recettes ne sont **pas** une adoption de palettes ou de raretés.
- La recette multicolore exige un traitement déterministe d'assignation des 3 teintures (ordre des slots ou interface de recette spécifique). Les détails de `Recipe` dépendent de l'API de version à vérifier.

## 5. Contrat de génération (USER-DIRECTED + paramètres PROPOSED)

**Invariants obligatoires :** uniquement cavités souterraines closes/admissibles, pas de monolithe exposé à la surface, pas de remplacement destructeur de structure/BlockEntity ou liquide, pas de génération dans d'anciens chunks automatiquement.
Les dimensions Overworld, Nether et End sont **candidates**, mais chaque biome/dimension doit passer la même règle d'inclusion et les vérifications propres à sa topologie. Génération nulle est acceptable là où il n'existe aucune cavité admissible.
Validation `canGenerate(position, facing)` conceptuelle :
1. Choisir un emplacement vide selon les limites de la dimension/biome.
2. Identifier un support stable sur une des six faces.
3. Vérifier le volume sans collision avec des ressources ou structures protégées.
4. Vérifier que la cavité n'est pas exposée directement à un espace extérieur/surface et dispose d'une épaisseur minimale de roche appropriée, avec algorithme à spécifier/tester.
5. Placer en une seule opération atomique et choisir composition couleur/répartition déterministe par seed.

Paramètres de départ NON MESURÉS : Overworld Y -48..48, 2 tentatives/chunk ; Nether Y 12..112, 1 tentative/chunk ; End cavités d'îles, 1 tentative/4 chunks. Profondeur minimale Overworld 12 blocs sous la surface locale proposée.
Ne pas les traiter comme approbation : ces nombres doivent être discriminés en jeu, surtout pour les dimensions sans notion de surface unique.

## 6. Économie, recette et duplication — OPEN / BLOCKING

La combinaison `3 dyes + glowstone → Cell Trio`, `1 crystal → 4 dyes` et `3 crystals par Cell Trio` crée une multiplication possible **si le Cell Trio fabriqué rend trois cristaux**.
Il faut résoudre cette asymétrie **avant l'implémentation**. Choix recommandé mais NON ADOPTÉ :
- `NATURAL` : récolte 3 cristaux, respect de chaque couleur.
- `CRAFTED` : destruction ordinaire **ne donne pas de cristaux** (option : rendre le bloc intact par Silk Touch, ou restituer matériaux de base selon décision).
- Garantir la provenance persistante lors des transformations, transferts et placements ; ne jamais permettre à un item fabriqué de devenir `NATURAL` par rechargement ou recyclage.

La restriction exacte de trois cristaux pour un **trio multicolore naturel** reste impérative. Pour une formation fabriquée multicolore, la règle utilisateur « seulement 3 cristaux » peut entrer en conflit avec l'antiduplication : **conflit explicite, à arbitrer par l'owner**. Ne pas résoudre silencieusement.

Fortune sur monocolore : barème 25/50/75 % par pointe *proposé précédemment*, **non confirmé** ; aucune valeur implémentable tant que sa compatibilité avec l'économie globale n'est pas acceptée.

## 7. Tests d'acceptation avant handoff à l'owner

| Précondition | Action | Attendu et preuve |
|---|---|---|
| Chaque couleur, nouvel objet cristal | Craft shapeless | Exactement 4 teintures correspondantes ; capture recette + assertions |
| Trio naturel bleu/bleu/violet | Détruire sans Silk Touch/Fortune | Exactement 2 bleus + 1 violet ; log/test côté serveur |
| Trio naturel rouge/vert/bleu | Détruire avec Fortune III | Exactement 1 de chaque ; aucune augmentation |
| Trio intact avec 3 couleurs | Silk Touch puis sauvegarde/redémarrage et pose sur autre face | Composition ordonnée persistée ; assertion NBT/components |
| Trio naturel sur support | Retirer support pendant la récolte | Une seule voie de drops, aucune duplication |
| Nouveau monde toutes dimensions | Générer puis inspecter surface/grottes | 0 exposition en surface ; lieux admissibles seulement |
| Cell Trio fabriqué | Casser/reposer/récupérer et convertir les produits | Aucune boucle profitable une fois l'arbitrage économie adopté |

Tests NON EXÉCUTÉS, FPS/TPS UNKNOWN, compatibilité AE2 UNKNOWN.
