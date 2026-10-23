# IM-STAGE-014 — Handoff rendu client / smoke tests Cell Trio DEFAULT

**STATUT : HANDOFF PREPARED — NOT SENT / NOT RECEIVED / NOT EXECUTED**  
**Date : 2026-10-23 (America/Toronto)**  
**Émetteur :** MCA, conception et protocole de test uniquement.  
**Destinataire prévu :** owner `Immersive-Monolith-DEV-` (aucune transmission prouvée).  
**Gouvernance :** `emmanueltremblay9-stack/Immersive-Monolith-GPT` branche `main`, baseline HEAD `7fc2dc791e091a87d1e2ad284a94beb1300727de`.  
**DEV :** `emmanueltremblay9-stack/Immersive-Monolith-DEV-`, repo vide sans commit/HEAD à la vérification ; `main` affichée comme branche par défaut de repo, **pas une ref de commit**.  
**Cible de conception ASSUMED :** Minecraft Java 1.21.1, NeoForge, JDK 21. Version exacte NeoForge, namespace Java, output license et environnement de test : UNKNOWN.

## 1. But BORNÉ — ne pas confondre avec une autorisation

Produire **un premier prototype graphique `Cell Trio DEFAULT`** qui importe l'artefact Blockbench approuvé et prouve en client Minecraft les matériaux translucides, la pierre et une première pose. Rechercher une solution 1.21.1 suffisante avec coût de rendu acceptable, **sans** implémenter le loot, les recettes, l'AE2 ou la génération du monde.

**Baseline de modèle de référence** : `Cell_Trio_DEFAULT_r01(1).bbmodel`, SHA-256 `f2c09b84432c2675213f52dc92311a883c143ab1cac6fce37c51490dfbc4fef6`, `IM-VIS-003` USER APPROVED MODEL REFERENCE. **Ne jamais régénérer le modèle depuis une image** ni substituer le package antérieur plus volumineux sans révision/approbation.

**Paquet d'assets préparé en session :** `Immersive_Monolith_IM-STAGE-013_Asset_Handoff_Prepared.zip`, SHA-256 `24b5ad25826c9c35da6e18ee46946297b12cf563a06957b6e193476297b30985` ; sources en tant que pièces jointes conversation, **pas** fichiers Git.
**Spécification source :** `architecture/IM-STAGE-013-EXACT-BBMODEL-ASSET-EXPORT.md`.

## 2. Portée autorisable après décision explicite de DEV

1. Vérifier l'identité exacte des deux fichiers SHA-256, absence de changement HEAD distant, cible JDK 21, MDK NeoForge 1.21.1 choisi (version exacte).
2. Initialiser la branche de développement via l'owner, à partir du MDK officiel ; aucun commit/réparation automatique par MCA.
3. Isoler un bloc de présentation `immersive_monolith:cell_trio` : enregistrement, BlockItem et validation du chargement sur client et serveur dédié.
4. Importer séparément le socle `solid`, les coques/noyaux `translucent`, les arêtes `cutout` ; comparer `neoforge:obj` + `neoforge:composite` et, seulement si nécessaire, modèle dynamique ou BER.
5. Vérifier réellement que les textures PNG semi-transparentes n'ont ni tri inversé, ni profondeur occluse, ni faces visibles au mauvais endroit ; tester sans shader. Ne pas présumer `MTL.d`/alpha suffisant.
6. Ajouter **rendu émissif effectif seulement si le moteur 1.21.1 permet une voie prouvée** ; séparer le fullbright de `lightLevel` et du bloom optionnel.
7. Lire trois `DyeColor` ordonnés de l'état métier. Pour ce handoff, il est acceptable que le premier aperçu utilise uniquement les trois bleus du source ; n'autoriser un `PASS_COLOR_COMPOSITIONS` qu'après traitement et test dynamique des couleurs.
8. Produire preuves de compilation/test ou anomalie reproductible ; arrêter à l'issue du smoke. Le passage au vrai worldgen, crafting/loot, CI/release nécessite une validation séparée.

### Fichiers/composants attendus (ne pas imposer une structure Java avant vérification MDK)
- Module d'enregistrement bloc + BE (si BE utilisé).
- Modèle d'objet 3D (en inventaire) et bloc (monde).
- Import des 3 OBJ/MTL sources, 5 PNG d'origine ; sans suppression de la texture inset non utilisée.
- `BlockState` attachement/roll, `VoxelShape` liée au maillage, persistance BE et item data components en étape distincte.
- Tests isolés, logs et captures. Aucun transfert sur mondes personnels.

## 3. Choix d'implémentation — comparaison

| Option | État pré-implémentation | Test discriminant |
|---|---|---|
| `neoforge:obj` + `neoforge:composite` en trois couches | **VIABLE À TESTER** pour premier bleu | Charger JSON, vérifier matériau/layers et UV dans une vraie session |
| Custom `IGeometryLoader` + `IDynamicBakedModel` / `ModelData` | **RECOMMANDÉ APRÈS SPIKE** pour 16³ couleurs ordonnées | Couleurs par instance, cache quads, aucune inversion après rotation/reload |
| `BlockEntityRenderer` | **FALLBACK**, si transparence/émissivité ne fonctionne pas en baked model | Comparer 1/64/256/1024 blocs, FPS/frametime (mesuré), absence de client classes sur serveur |
| Resource pack/datapack seul | **EXCLUDED** pour l'ensemble du besoin | Pas de type moteur custom et données de trois couleurs persistantes |
| HEXCraft code/meshes/textures copiés | **EXCLUDED sans licence explicite vérifiée** | Nouveau modèle et textures Dirty Studyo uniquement |

**Risque important :** le véritable `.bbmodel` fourni par l'utilisateur **ne possède pas les 3 coiffes rocheuses supérieures visibles dans l'image conceptuelle** ; la pierre est limitée à Y ≤ 2,4 unités source. Préserver ce fait, ne pas modifier le modèle approuvé silencieusement. Nouvelle décision de l'owner si ajout des coiffes souhaité.

## 4. Matrice de tests — PRÉCONDITION → ACTION → ATTENDU → PREUVE

| ID | Précondition | Action | Attendu | Preuve |
|---|---|---|---|---|
| IM-QA-014-01 | MDK versionnée + JDK 21 | `gradlew build` dans environnement isolé | Compilation sans erreur | HEAD DEV, version, log, SHA JAR |
| IM-QA-014-02 | JAR construit, client compatible | Démarrer nouveau monde de test | Bloc enregistré, absence de texture manquante | log client + captures |
| IM-QA-014-03 | Bloc posé, shader OFF, éclairage de grotte 0 | Voir de face, derrière et en diagonale avec grille contrastée derrière | Coque/noyau partiellement transparents, pierre opaque | captures + angle/conditions |
| IM-QA-014-04 | Modèle avec arêtes séparées | Couper `lightLevel` et shaders | Émissivité intrinsèque démontrée ou déclarée FAIL / NOT_IMPLEMENTED ; ne pas la confondre avec alpha | capture comparée + paramètres |
| IM-QA-014-05 | Modèle orientable + 6 supports solides | Placer sur 6 faces × 4 rotations | 24 poses, aucune face inversée/collision hors cellule | captures, states, shape |
| IM-QA-014-06 | BlockEntity + composition ordonnée | Poser bleu/bleu/bleu puis rouge/vert/bleu | Three-slot assignment stable ; palette 16 couleurs lorsque implémentée | log de `TrioComposition` + captures |
| IM-QA-014-07 | Nouveau client + serveur dédié | Démarrer serveur et connecter client | Aucune classe client chargée côté serveur, données synchronisées | logs des deux |
| IM-QA-014-08 | Objet Cell Trio teinté en inventaire | Montrer GUI, main, ground, item-frame | Même forme et teintes que l'objet, sans BE mondaine | quatre captures |
| IM-QA-014-09 | Plusieurs blocs en scène isolée | Mesurer 1, 64, 256, 1024 unités | FPS/frametime et GPU/CPU consignés ; seuil défini AVANT jugement | logs/mesures, setup reproductible |
| IM-QA-014-10 | Worldgen, craft, loot désactivés | Créer monde de test, briser prototype | Aucun worldgen ni drop générateur de boucle | logs/états du prototype |

**Ne jamais inscrire PASS avant exécution et preuve exacte.** Le calcul 24 poses est `PASS_STATIC_ONLY`, pas IM-QA-014-05 PASS.

## 5. Échecs et conditions d'arrêt

- Modèle ou textures absents, UV inversés, erreurs de tri `translucent` ou shader requis → FAIL du point concerné, fournir capture/log.
- Crash du serveur dédié lié à classe client, données colorées non persistantes ou race chunk update → STOP pour correction.
- Provenance `source=UNKNOWN` → ne pas inventer NATURAL ; pas de récompense basée sur une valeur invalide.
- Diff de source BBMODEL et SHA attendu → conflit ; préserver les deux versions, pas de remplacement silencieux.
- **IM-013 économie ouverte** : aucune fabrication/récolte permettant `3 teintures → 3 cristaux → 12 teintures` sans arbitrage explicite. Hors scope.
- Génération exclusivement souterraine à préparer séparément, **jamais de rétro-génération ni de modification des mondes personnels**.
- Pas de commit, merge, publication, licence finale ou installation de nouvelle dépendance au seul motif qu'un handoff existe.

## 6. Réponse demandée au propriétaire DEV

Renvoyer `DEV_REPO`, `BRANCH`, `HEAD`, Minecraft/NeoForge/JDK versions, inventaire des fichiers importés, SHA-256 JAR, liste des tests IM-QA-014 avec logs/captures, limitations de rendu, éventuels problèmes de licences, FPS/TPS **mesurés** ou UNKNOWN, décisions owner et éventuels pivots proposés.

**Status final de ce fichier : PREPARED IN GPT ONLY**. Pas `SENT`, pas `RECEIVED`, pas `EXECUTED`.

## 7. Références primaires versionnées

Documentation NeoForged pour **Minecraft Java 1.21–1.21.1** :
- https://docs.neoforged.net/docs/1.21.1/gettingstarted/
- https://docs.neoforged.net/docs/1.21.1/resources/client/models/modelloaders/ — builtin OBJ, composite, loaders
- https://docs.neoforged.net/docs/1.21.1/resources/client/models/bakedmodel/ — ModelData, RenderTypes, item perspectives, caches
- https://docs.neoforged.net/docs/1.21.1/blockentities/ — sauvegarde/synchronisation
Documentation lue = **faits d'API, pas permission d'écriture ou résultat de test**.
