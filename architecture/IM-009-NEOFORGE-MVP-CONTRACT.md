# IM-STAGE-009 — Architecture du MVP Immersive Monolith (NeoForge)

**Désambiguïsation des IDs :** `IM-STAGE-009` désigne l'étape d'architecture du workflow, tandis que `IM-009` du `DECISION_LOG.md` reste la décision proposée sur les dimensions et les 24 orientations. Le nom historique de ce fichier comporte `IM-009` ; ce nom de fichier ne réattribue pas l'ID de décision. Préserver cette distinction dans les prochains handoffs.

**Statut** : PROPOSED / ARCHITECTURE, **NON ADOPTÉ**, **NOT EXECUTED**
**Date** : 2026-10-22
**Auteur du document** : MCA (conception uniquement)
**Dépôt cible de code** : `emmanueltremblay9-stack/Immersive-Monolith-DEV-` (vide, sans HEAD au moment de la revue)
**Dépôt de gouvernance** : `emmanueltremblay9-stack/Immersive-Monolith-GPT`, `main`
**Référence gouvernance vérifiée AVANT ce document** : `9599cf77891f25bacf12443d51af1ea11e3e46e5`
**Cible ASSUMED** : Minecraft Java 1.21.1 / NeoForge / Java 21 ; numéro précis NeoForge, distribution et licence du mod : UNKNOWN.
**Sources internes** : `specifications/IM-CELL-TRIO.md`, `DECISION_LOG.md`, `GOVERNANCE.md`, `handoffs/IM-008-DEV-BOOTSTRAP-PREPARED.md`.

Ce document précise la réalisation minimale sans autoriser l'initialisation de DEV. L'owner DEV garde le droit de choisir la structure effective, d'accepter les contraintes et d'exécuter tests/builds/releases. `next` ne règle aucun conflit `LOCKED` ni ne remplace une acceptation explicite du handoff.

## 1. Choix technologique minimal

| Option | État de conception | Justification |
|---|---|---|
| Mod Java NeoForge 1.21.1 + ressources originales | VIABLE (architecture, non exécuté) | Nouveau bloc et objet, données par formation, orientation 6 faces, rendu, worldgen ultérieur |
| Datapack + resource pack sans code mod | EXCLUDED pour l'intégralité | Ne peut enregistrer arbitrairement un nouveau type moteur de bloc et gérer toutes les données/loot/rendu avec mêmes garanties |
| Plugin serveur et client vanilla | EXCLUDED pour modèle natif client complet | Un plugin ne livre pas automatiquement une logique/modélisation de bloc client |
| Framework de script existant | UNCERTAIN | Dépendances et compatibilité non fournies ; besoin de test discriminant |

Décision de conception : **un seul type de bloc** `cell_trio`, à composition dynamique, 16 variantes de cristaux en tant qu'items (ou un item avec composant de couleur si l'owner choisit cette alternative).

**L'ordre SMALL / MEDIUM / TALL est un invariant** : toutes les lectures/écritures gardent les trois couleurs dans cet ordre, même lorsque le bloc est fixé au mur ou au plafond.

## 2. Contrats de données proposés

Modèle métier immuable conceptuel :

```text
TrioComposition:
  smallColor: DyeColor (une des 16 couleurs vanilla)
  mediumColor: DyeColor
  tallColor: DyeColor
  source: NATURAL | CRAFTED | UNKNOWN
  schemaVersion: integer (commence à 1)
```

- Les valeurs UNKNOWN/invalides doivent échouer sans dupliquer d'objets ni créer une origine NATURAL implicite.
- Le `BlockState` encode seulement `ATTACHMENT_FACE` (6 valeurs) + `AXIAL_ROTATION` (0..3). La proposition produit 24 orientations, sans 4096 variantes de couleur dans l'état.
- Une `BlockEntity` conserve `TrioComposition` dans le monde ; sérialisation, synchronisation client et migrations de schéma sont à documenter avec les signatures NeoForge exactes 1.21.1.
- Un `DataComponentType<TrioComposition>` d'ItemStack conserve la composition lors de Silk Touch, pick block (si prévu), inventaire, drops et repose.
- La validation des coloris, de la provenance et des résultats de récolte appartient **au serveur**. Aucun paquet reçu du client ne peut définir des récompenses ni changer silencieusement `source`.

### Événements et transitions

| Entrée | Action | État attendu |
|---|---|---|
| Placement d'un item valide | Vérifier la face de support et l'espace ; instancier le bloc et la BlockEntity | `PLACED` avec composition identique |
| Rotation libre de la vue avant placement | Déterminer orientation des trois pointes | Face + rotation stockées, couleurs toujours affectées aux mêmes pointes |
| Redémarrage / chunk reload | Charger données persistantes | Composition ordonnée + provenance inchangées |
| Suppression du support | Détruire selon une voie serveur unique | `REMOVED`, éviter double drop |
| Silk Touch (PROPOSED) | Récupérer `cell_trio` avec composant | Un item intact, zéro cristal |
| Casse ordinaire | Exécuter politique de loot `source` | **DÉSACTIVÉE / NON EXPÉDIABLE** tant que IM-013 n'est pas arbitré |

Les hooks exacts de sauvegarde/synchronisation (`saveAdditional`, `loadAdditional`, update tags/packets) doivent être vérifiés dans NeoForge 1.21.1, et utilisés de manière à éviter les mises à jour de réseau à chaque tick pour des couleurs statiques.

## 3. Modèle visuel et collisions

- Un bloc **1×1×1**, trois pointes facettées côte à côte, de hauteur SMALL 6/16, MEDIUM 10/16, TALL 15/16 (mesures proposées, non validées).
- Le squelette doit comporter des *voxel shapes* pour collisions/sélection, et un modèle/rendu côté client indépendant des ressources propriétaires HEXCraft/XyCraft.
- Choix de rendu discriminant : a) modèle JSON statique + couche couleur/texture, ou b) rendu BlockEntity seulement si les trois couleurs et l'orientation ne peuvent pas être exprimées efficacement par la variante JSON/baked model. **Mesurer** les conséquences; ne pas imposer un renderer coûteux par réflexe.
- Éclairage réel : `BlockBehaviour.Properties#lightLevel` (valeur `10` proposée) ; ne pas confondre avec facettes émissives et bloom.
- Option shaders/bloom non obligatoire, ne doit jamais conditionner la lisibilité du bloc vanilla.
- Aucun tick de BlockEntity pour une composition purement statique.

## 4. Layout de fichiers envisageable — DEV, non écrit

```text
Immersive-Monolith-DEV-/
  README.md
  gradle/ + gradlew + gradlew.bat + build.gradle + settings.gradle + gradle.properties
  src/main/java/<owner_namespace>/immersivemonolith/
    ImmersiveMonolith.java
    registry/{ModBlocks,ModItems,ModBlockEntities,ModDataComponents}
    block/CellTrioBlock.java
    blockentity/CellTrioBlockEntity.java
    data/TrioComposition.java
    client/{CellTrioModelOrRenderer}.java
  src/main/resources/
    META-INF/neoforge.mods.toml
    assets/immersive_monolith/{blockstates,models,textures,lang}
    data/immersive_monolith/{recipe,loot_table}
  src/test/...
```

Les chemins `gradle` et ressources se basent sur le **MDK officiel à la version choisie**, pas sur des fichiers Gradle improvisés. Adapter l'arborescence précise après vérification de l'archive MDK et de ses termes ; les chemins `recipe` et `loot_table` ne doivent pas entraîner l'activation des fonctionnalités conflictuelles.

## 5. Garde-fous explicites sur les recettes et le worldgen

### Recettes

- Exigence utilisateur : `3 dyes + 1 Glowstone Dust -> 1 Cell Trio`; `1 crystal -> 4 corresponding dyes`.
- Une recette dynamique doit affecter trois teintures aux pointes selon une règle **déterministe** et définie par l'owner (ordre des slots 2x2 proposé dans IM-008) ; le vanilla `crafting_shapeless` ne permet pas de déduire une affectation ordonnée à partir de la seule liste non ordonnée d'ingrédients.
- Bloquer publication/activation des sorties qui génèrent une boucle profitable tant que IM-013 n'est pas décidé.
- User constraint IM-007 : trio multicolore récolté donne exactement 3 cristaux, un par pointe; pas de bonus Fortune. Le cas `CRAFTED` reste **CONFLIT À ARBITRER** avec la recette de fabrication.

### Worldgen

- Exigence absolue : jamais de génération naturelle en surface.
- Exclure worldgen du premier bootstrap ; plus tard `ConfiguredFeature` + `PlacedFeature` + `BiomeModifier` dans les ressources du mod.
- `no skylight` / `canSeeSky == false` ne suffit pas à définir un intérieur géologique dans les trois dimensions. Définir et tester une fonction d'éligibilité dimension-aware avant activation.
- Ne pas affecter les chunks existants par rétro-génération automatique, ni remplacer des block entities ou inventaires.

## 6. Plan d'exécution DEV proposé, dépendant d'une autorisation

1. Owner DEV confirme repo, branche à initialiser, Java 21, choix MDK, version NeoForge et politique des dépendances.
2. Importer le MDK vérifié comme premier commit isolé ; produire `gradlew build` avec un JAR identifié (SHA-256).
3. Enregistrer le bloc prototype et sa BlockEntity sans recette/loot/worldgen actifs ; pouvoir le placer en environnement de test.
4. Valider les 24 orientations (6 faces × 4 rotations), les collisions/voxel shapes et la lumière.
5. Implémenter la persistance des trois couleurs et des métadonnées d'origine ; tester save/reload/replacement y compris sauvegarde dédiée serveur.
6. Rapporter les constats, comparer aux critères d'acceptation ; arrêter AVANT les mécanismes sous IM-013.

## 7. Conditions de PASS et preuves à retourner

| Précondition | Action | Attendu | Preuve |
|---|---|---|---|
| MDK officiel, JDK 21, ref fixe | Build | Compilation terminée | log build + SHA JAR |
| Bloc prototype chargé | Poser 6×4 orientations | Aucune rotation mal associée | captures/valeurs BlockState + log |
| Trois teintes mixtes | Placer, sauvegarder, fermer, rouvrir | SMALL/MEDIUM/TALL conservés | assertions de données |
| Client/serveur isolés | Placer/récolter item non loot | Pas de classes client sur serveur | log serveur dédié |
| Source invalide ou données malformées | Charger sauvegarde instrumentée | Pas de duplication, comportement sûr | assertion/failure log |
| Sans worldgen activé | Créer monde test | Pas de monolithes générés | preuve configuration + contrôle |

- Aucun succès n'est revendiqué à ce stade : BUILD = NOT_TESTED ; CLIENT = NOT_TESTED ; SERVER = NOT_TESTED ; FPS/TPS = UNKNOWN.
- Aucune migration de monde ni profil personnel, aucun merge et aucune publication.
- Handoff IM-008 = PREPARED, pas SENT/RECEIVED/EXECUTED.

## 8. Sources primaires techniques, portée et vérification

Documentation officielle NeoForged ciblée **Minecraft 1.21–1.21.1**, consultée 2026-10-22 :
- https://docs.neoforged.net/docs/1.21.1/gettingstarted/ — MDK/JDK 21/build/client/serveur
- https://docs.neoforged.net/docs/1.21.1/blocks/states/ — états de bloc/updates
- https://docs.neoforged.net/docs/1.21.1/blockentities/ — enregistrement, sauvegarde/sync
- https://docs.neoforged.net/docs/1.21.1/items/datacomponents/ — composants d'items et codecs
- https://docs.neoforged.net/docs/1.21.1/resources/server/recipes/ — recettes, sérialisation et Recipe#assemble
- https://docs.neoforged.net/docs/1.21.1/worldgen/biomemodifier/ — injection de features en biome

Ces sources sont des **preuves d'API/documentation**, pas des autorisations de code ni des tests runtime.

## 9. État de gouvernance

**Fichier préparé dans GPT uniquement ; pas de commit en DEV ; aucune permission d'écriture code déduite.**
La création de ce document n'adopte pas les options PROPOSED, ne résout pas IM-013, n'active pas un processus de publication et ne certifie aucune performance.
