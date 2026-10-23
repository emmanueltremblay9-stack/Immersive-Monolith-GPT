# IM-STAGE-013 — Export statique du modèle DEFAULT r01 approuvé

**Statut : PREPARED_ASSETS / PASS_STATIC_EXPORT / NOT_TESTED_IN_MINECRAFT / NOT_SENT_TO_DEV**  
**Projet : Immersive Monolith — Cell Trio DEFAULT**  
**Dépôt gouvernance :** `emmanueltremblay9-stack/Immersive-Monolith-GPT`, `main`, HEAD vérifié avant écriture `38874a979c8ad2fa4b19145e614ed94e3712f2ee`.  
**DEV :** `emmanueltremblay9-stack/Immersive-Monolith-DEV-` (vide et sans HEAD lors du contrôle).  
**Cible hypothétique :** Java Edition Minecraft 1.21.1 / NeoForge / JDK 21.  
**Autorité :** MCA conçoit et prépare des artefacts d'analyse ; le propriétaire DEV garde le code, l'intégration, les tests et les releases.

## 1. Référence approuvée à préserver

La décision utilisateur `IM-VIS-003` désigne **le fichier exact** `Cell_Trio_DEFAULT_r01(1).bbmodel`, SHA-256 `f2c09b84432c2675213f52dc92311a883c143ab1cac6fce37c51490dfbc4fef6`. Il est copié **octet pour octet** dans le package, puis traité en lecture seule ; ne pas le reconstruire à partir du PNG conceptuel.

Données constatées sur le JSON source : format `5.0` / `free`, 13 maillages, 905 faces (797 quadrilatères + 108 triangles), et 1 702 triangles après triangulation déterministe. Trois groupes SMALL, MEDIUM et TALL plus fondation commune. UV et textures incorporés dans le BBMODEL.

**Différence de preuve visuelle importante** : le modèle approuvé ne possède **aucune coiffe rocheuse en haut** des pointes. Les 437 faces utilisant la texture `stone.png` sont toutes situées entre **Y=0 et Y=2,4 unités**. Les coiffes visibles dans l'illustration de concept `IM-VIS-002` ne doivent pas être ajoutées tacitement au fichier exact que l'utilisateur a approuvé. Cette divergence mérite un arbitrage propre avant de déclarer qu'un rendu est identique au concept.

## 2. Artefacts produits localement

Package de conversation (aucun transfert à DEV/Git) :
`Immersive_Monolith_IM-STAGE-013_Asset_Handoff_Prepared.zip`
SHA-256 : `24b5ad25826c9c35da6e18ee46946297b12cf563a06957b6e193476297b30985`.
Nombre de fichiers internes : **24**. Contient :
- Référence BBMODEL inchangée et identifiable par SHA ;
- Cinq textures PNG sources RGBA originales : pierre, coque, noyau, arêtes et inset non exploité ;
- Trois exports Wavefront `OBJ` + `MTL` séparés : `stone`, `translucent`, `edges` ;
- Quatre exemples JSON, dont `neoforge:obj` / `neoforge:composite` comme **piste de test**, pas configuration certifiée ;
- Rapport de validation et 24 bornes géométriques.

**Remarque MTL :** les `map_Kd #stone` et autres références sont des alias de sprites proposés pour le loader NeoForge 1.21.1. Ne pas traiter les fichiers comme un rendu validé sans actualisation du modèle dans le jeu. Un OBJ brut peut paraître opaque ou sans texture dans un autre lecteur.

### Décomposition exacte calculée

| Groupe OBJ | Matériaux | Triangles | RenderType candidat |
|---|---|---:|---|
| `cell_trio_default_stone` | `stone` | 838 | `minecraft:solid` |
| `cell_trio_default_translucent` | `core_blue`, `shell_blue` | 216 | `minecraft:translucent` |
| `cell_trio_default_edges` | `edge_blue` | 648 | `minecraft:cutout` puis étude émissive |
| Total | Quatre matériaux effectivement employés | **1702** | non testé moteur |

La cinquième texture `inset_blue.png` est incorporée dans le BBMODEL mais n'est référencée **par aucune face**. Elle est préservée telle quelle, sans ajout spéculatif au maillage.

## 3. Conventions spatiales et des UV — PROPOSED

Source BBMODEL : X/Z centrés autour de zéro. Conversion déterministe : `p_OBJ=((x+8)/16, y/16, (z+8)/16)`. UV Blockbench en pixels 32×32, origine haute-gauche ; OBJ cible : `u_OBJ=u/32`, `v_OBJ=1-v/32`.

Bornes normalisées constatées dans l'export de référence :
- `X=[0.1125,0.8875]`
- `Y=[0,0.8825]`
- `Z=[0.125,0.875]`

Chaque face a été triangulée en éventail en préservant l'ordre de sommets et son matériau. **Aucune aire géométrique nulle, aucune aire UV nulle** observée. Les normales de faces ont été calculées via produit vectoriel ; aucune validation visuelle du winding en moteur.

Rotation locale quart de tour autour de Y, puis choix de la face de support parmi `UP/DOWN/NORTH/SOUTH/EAST/WEST` : **24 enveloppes** recalculées, toutes incluses dans `[0,1]^3`. Les noms et plans de support doivent correspondre aux conventions du gameplay, testées en jeu ; la validation de bounds ne prouve ni support ni collision.

## 4. Décision technique comparative

- **Piste de premier chargement (recommandée pour discriminer)** : trois `neoforge:obj` composés par `neoforge:composite`, chacun avec le `render_type` correspondant. Disponible dans la documentation officielle NeoForge 1.21.1, mais combinaison avec l'export **NOT_TESTED**.
- **Piste d'exploitation 16 couleurs** : `BakedModel` dynamique + `ModelData` et identité de pointe, cache borné. Un loader OBJ simple **ne crée pas automatiquement** 4096 compositions par données d'instance.
- **Fallback** : `BlockEntityRenderer` (BER) si le tri alpha, les noyaux et les arêtes échouent avec le modèle compilé ; performance à comparer réellement.
- **EXCLUDED** : déclarer « glow » à partir de l'alpha PNG, supposer de la lumière RGB vanilla, imposer un shader aux joueurs, ou enregistrer un type de bloc par triplet de couleurs.

Sources API consultées le 2026-10-23 (cible Minecraft 1.21.1) :
- https://docs.neoforged.net/docs/1.21.1/resources/client/models/modelloaders/
- https://docs.neoforged.net/docs/1.21.1/resources/client/models/bakedmodel/
- https://docs.neoforged.net/docs/1.21.1/blockentities/

## 5. Qualité et risques restant ouverts

**PASS STATIC EXPORT** = existence d'un export issu de l'original identifié, 1 702 triangles, 24 enveloppes et UV non dégénérés. Cela **ne signifie pas** :
- modèle réellement ouvert dans Blockbench, chargé dans Minecraft, ou visible sur client/dédié ;
- alpha trié correctement avec cristal intérieur et coque extérieure ;
- fullbright/emissivité/éclairage vanilla ou bloom ;
- rotation/placement effectifs sur les six faces ;
- composant ItemStack/BlockEntity, 16 teintures et persistance ;
- génération souterraine, recettes ou drops ; `IM-013` reste bloqué.

**Stop / transfert :** ne pas copier ces fichiers dans DEV sans accord explicite de l'owner, cible et branche vérifiées, et parcours d'écriture conforme. Ne pas passer `PREPARED` à `SENT` sur la seule présence du ZIP.

## 6. Provenance

- `IM-VIS-003` (validé par l'utilisateur), `IM-VIS-002` (concept) ;
- `IM-VIS-001` (cinq silhouettes candidates) conservé ;
- `IM-STAGE-010` (ancienne géométrie alignée) historique, non détruit ;
- `IM-STAGE-011/012` (textures exploratoires/rendu) restent distincts du nouveau source approuvé.

Les exports n'incorporent aucun code ou asset du mod HEXCraft ; les droits de publication du mod seront choisis par l'owner.
