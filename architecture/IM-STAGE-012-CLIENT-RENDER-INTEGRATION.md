# IM-STAGE-012 — Contrat d'intégration Cell Trio / textures / rendu émissif

**Statut : PROPOSED / PREPARED DOCUMENTATION / NOT_SENT / NOT_EXECUTED**
**Date : 2026-10-22 (America/Toronto)**
**Périmètre : conception par MCA, sans code ni commit dans le dépôt DEV**
**Référence gouvernance avant cette écriture :** 88371c474ee49539ddede3d1663b951653065343, branche `main` du dépôt `emmanueltremblay9-stack/Immersive-Monolith-GPT`
**Dépôt DEV :** `emmanueltremblay9-stack/Immersive-Monolith-DEV-` ; vérifié vide/sans HEAD avant cette écriture.
**Cible supposée :** Minecraft Java 1.21.1, NeoForge, Java 21. Build ou numéro précis NeoForge : UNKNOWN.
**Dépendances :** aucune dépendance AE2, HEXCraft, XyCraft, shader, framework de script ou API payante pour le MVP ; les données appartiennent au nouveau mod.

Sources internes consultées :
- `SOURCE_MANIFEST.json` (index local de sources, non MCA_SOURCE_MANIFEST.json)
- `DECISION_LOG.md` (IM-001…IM-015 et conflits)
- `specifications/IM-CELL-TRIO.md` (contraintes gameplay)
- `blueprints/IM-STAGE-010-TRIO-HEX-MESH.md` (mesh hexagonal déterministe et orientations)
- `specifications/IM-STAGE-011-TEXTURES-EMISSIVE-RENDER.md` (textures 16 couleurs, rendu et limitations)
- `assets/IM-STAGE-011-PALETTE.json` (chemins des références graphiques).

## 1. Contrat minimal

**Le bloc Cell Trio** :
- occupe un seul block-position et expose un mesh à 3 pointes distinctes, SMALL / MEDIUM / TALL ;
- conserve la paire (slot, couleur) pour chaque pointe, dans le même ordre lors de toute orientation ;
- supporte les 16 couleurs de DyeColor ; 16³=4096 triples ordonnés possibles ; **un seul bloc moteur**, pas 4096 blocs enregistrés ;
- dispose de 6 faces d'attache et 4 quarts de tour (24 poses proposées, modèle serveur/client à vérifier) ;
- utilise des sprites originaux Dirty Studyo ; aucune reprise de textures/modèles HEXCraft ou XyCraft ;
- peut être placé manuellement sur la surface ; la génération **naturelle jamais en surface** est une autre fonctionnalité, actuellement hors périmètre ;
- n'utilise ni animation ni ticker obligatoire dans la BlockEntity.

**Invariants gameplay préservés, hors périmètre de ce stage** : 3 teintures + 1 Glowstone Dust → monolithe ; 1 cristal → 4 teintures ; trio multicolore récolté → exactement 3 cristaux correspondant aux 3 pointes et pas de bonus Fortune. La politique NATURAL/CRAFTED `IM-013` n'est PAS approuvée ; aucune recette/loot exploitable n'est implémentée par ce document.

## 2. Assets exacts — source vérifiée localement, non copiée dans DEV

Archive de référence : `Immersive_Monolith_Stage011_Original_Texture_References.zip`, **SHA-256** `6bf4ee97cbbc43d54dfe708d33276802d65694c83d0b2b50b5442c1d0785ad9e`.
La liste du zip contient **39 membres**, dont **34 textures PNG 32×32 RGBA** (32 colorées : 16 block, 16 item ; 2 textures partagées : neutre et masque). `SHA256SUMS.json` vérifie l'empreinte de **36 fichiers**. La génération est procédurale originale, sans extraction des ressources de tiers.
**L'archive et le script de vérification restent des pièces jointes de conversation, pas des fichiers Git.**

Pour `dye` une des 16 clés `DyeColor` :
- `assets/immersive_monolith/textures/block/cell_trio_facet_{dye}.png`
- `assets/immersive_monolith/textures/item/crystal_{dye}.png`
- `assets/immersive_monolith/textures/block/cell_trio_facet_neutral.png`
- `assets/immersive_monolith/textures/block/cell_trio_emission_mask.png`

Les identifiants de sprites côté jeu sont conceptuellement `immersive_monolith:block/cell_trio_facet_blue`, etc., **sans** le suffixe `.png` ; à mapper aux atlas réels du loader. Les 16 codes hexadécimaux du manifeste ne sont que des couleurs de design, pas des constantes officielles Java de `DyeColor`.

**Validation locale IM-STAGE-012** (calcul Python hors Minecraft) :
- **4 096** triples de couleurs ordonnés énumérés sans doublon ;
- les **34 PNG** sont présents, valides et cohérents avec les chemins du manifeste ;
- **144** assertions de correspondance (6 compositions de référence × 6 faces × 4 rotations) du **contrat de slots**, pas du renderer Minecraft ;
- masque d'émission RGBA : canal alpha dans **0..204**, preuve de présence de niveaux d'intensité, **pas preuve de fullbright** ;
- archive SHA-256 et 36 entrées de manifeste validées.
Artefacts en session : `stage012_contract_check.py` SHA-256 `d8fc76689ff2cd352410be9b8827524bb6cea6f14af937bb1113196b7c98b2a7` et `IM-STAGE-012-STATIC-CONTRACT-REPORT.json` SHA-256 `a0ffab3aa8df3fea6f62994062676ec99a4ad8ba274dd5832dae0ce01498a6b0`. Code et rapport disponibles en pièces jointes de la conversation ; ne pas déduire un commit à partir de leurs noms.

## 3. Chemin de rendu **proposé** en NeoForge 1.21.1

Ordre des responsabilités :
1. **Données serveur :** `BlockState(attachmentFace, rotation)` ; `BlockEntity.TrioComposition(small,medium,tall,origin,schemaVersion)`.
2. **Synchronisation :** la BlockEntity persiste les couleurs et transmet son état de rendu au client sur chargement du chunk/changement explicite. Pas d'envoi chaque tick.
3. **Client ModelData :** expose SMALL/MEDIUM/TALL depuis la copie client de la BlockEntity et invalide/reconstruit le rendu lorsqu'un paquet valide remplace la composition (méthode exacte à confirmer dans les classes de la version visée).
4. **Modèle client :** loader enregistré côté client, `IUnbakedGeometry` et `IDynamicBakedModel` ou wrapper officiel versionné ; bake d'un mesh original commun, texture attribuée **par identité de pointe** et non par X/Z après rotation.
5. **Sprites :** correspondance `slot → dye → sprite`, sans double teinte (PNG déjà coloré).
6. **Sortie :** quads texturés sur 24 orientations, priorité à cache de géométrie par pose et textures par couleur.
7. **Items :** la représentation du bloc **dans la main et l'inventaire** ne reçoit pas automatiquement ModelData d'une BlockEntity. Prévoir une voie d'item rendering lisant les data components de l'ItemStack ou une variante d'item modèle dédiée. Tester GUI/first-person/ground/item-frame pour toutes les compositions.

### Source officielle vérifiée pour 1.21.1

La page NeoForge 1.21.1 « Custom Model Loaders » confirme `ModelEvent.RegisterGeometryLoaders`, `IGeometryLoader`, `IUnbakedGeometry` et `IDynamicBakedModel` ; la page « Baked Models » confirme `getQuads(state, side, rand, extraData, renderType)`, la récupération de `ModelData`, l'importance du cache et le traitement de modèles d'items.

**Point de décision technique** : comparer le loader dynamique ci-dessus avec un `BlockEntityRenderer` ciblé comme fallback. Si une fonction 1.21.1 n'offre pas un canal de **fullbright** fiable, ne pas annoncer « émissif testé » en se limitant à un PNG.

## 4. Matériaux et émissivité : trois effets séparés

| Effet | Mécanisme à étudier | État |
|---|---|---|
| **Albedo coloré** | Un sprite original par couleur ; shader non requis | Référence d'assets vérifiée ; rendu Minecraft NON TESTÉ |
| **Lueur intrinsèque / facettes fullbright** | Material/quad/couche client supportant une valeur lumineuse indépendante de l'ambient | PROPOSED, **nécessite test sur l'API exacte** |
| **Lumière du monde** | `BlockBehaviour.Properties.lightLevel` 0..15, valeur initiale 10 proposée | PROPOSED, aucun test ; lumière vanilla non colorée |
| **Bloom / diffusion volumétrique** | Shader/post-process du client, facultatif | OUT_OF_SCOPE du MVP |

**Ne pas confondre le masque de texture** avec une propriété émissive native : placer simplement `cell_trio_emission_mask.png` dans `textures/block` n'active pas automatiquement une couche lumineuse.

Rendu initial à tester : géométrie avec opaque/cutout pour limiter transparence, et accent de facettes émissives distinct. Pour les cristaux BLACK/GRAY/LIGHT_GRAY/WHITE, conserver des contours contrastés lisibles. Transparence réelle et bloom sont optionnels et dépendent de comparaisons visuelles et FPS réels.

## 5. Contrats d'échec, persistance, performance

- Si un sprite manque : texture neutre explicitement identifiable + log diagnostic, pas crash silencieux.
- Si une teinte est malformée : ignorer la valeur invalide, signaler diagnostic minimal, afficher repli sans **attribuer NATURAL** par défaut.
- Si une réponse de synchronisation arrive pour un bloc supprimé : ne pas recréer la BlockEntity ou invalider des chunks non chargés ; ignorer proprement.
- Si un item Cell Trio transporte une composition de trois couleurs : ordre immuable y compris si l'item se recharge après restart.
- Éviter la construction de vertices à chaque `getQuads` et à chaque frame ; mise en cache bornée, par orientation et matériau.
- Aucun `BlockEntityTicker` requis uniquement pour trois couleurs statiques.
- Le rendu côté client ne doit jamais être chargé sur un serveur dédié.
- Pas de mesure de TPS/FPS, RAM, coût réseau à ce stade : **UNKNOWN**.

## 6. Matrice de tests d'acceptation DEV (non exécutée)

| Précondition | Action | Attendu | Preuve demandée |
|---|---|---|---|
| MDK NeoForge 1.21.1 + JDK 21 et assets autorisés | Compiler client et serveur | Pas d'erreur API/rendu | Commit DEV, JAR SHA-256, logs |
| Trio RGB (R/G/B) | Placer 24 poses | Chaque point conserve sa couleur et sa hauteur | 24 captures+états BE/BlockState |
| Bleu/Bleu/Violet et Cyan/Magenta/Jaune | Déplacer au mur, plafond puis sol | Aucun réordonnancement couleur/pointe | Valeurs avant/après + captures |
| Save/load et reconnexion client | Redémarrer, relire chunks | Trio inchangé, aucun fallback durable | Logs/sérialisation |
| GUI/inventaire avec item teinté | Déplacer/montrer modèle d'item | Pas de « triple blanc » par absence de BE | Captures inventaire et ItemStack |
| Lumière naturelle niveau zéro | Comparer avec shaders absents | Albedo et facettes lumineuses distinguables | Captures identiques d'exposition |
| Test de bloc lumineux sans shader | Placer une seule formation | Lumière monde attendue (valeur acceptée) | Capture light-level + logs |
| 16 couleurs, dont noir/gris/blanc | Observer sous différents angles | Teintes discernables sans shader | Matrice captures |
| 1 / 64 / 256 / 1024 trios dans monde isolé | Mesurer sur même client | Aucune régression majeure non expliquée ; seuil à définir par owner | FPS frametime mémoire, matériel, paramètres |
| Serveur dédié | Démarrer sans classes client | Pas de crash de chargement | Logs serveur |
| Paquet tardif / BE absente | Fermer chunk puis livrer update simulé | Aucun recréation de BE, aucune erreur | Journal tests |
| Worldgen et loot désactivés | Ouvrir monde vierge | Aucune création ou sortie gameplay non approuvée | Inventaire registres / log |

Ne pas déclarer un jeu PASS à partir de la seule QA de PNG.

## 7. Séquence d'intégration proposée, sous contrôle de l'owner DEV

**A.** Owner accepte explicitement périmètre et branche de DEV, crée HEAD initial avec MDK officiel.  
**B.** Copier les assets du ZIP vérifié, y compris provenance/empreintes.  
**C.** Enregistrer bloc et stockage de couleur, puis tests sauvegarde sans effets de gameplay.  
**D.** Implémenter loader et rendu, contrôler les 24 orientations et l'item.  
**E.** Ajouter facettes émissives et lumière vanilla, séparément.  
**F.** Tester sur client vanilla moddé + serveur dédié ; produire preuves, puis envisager intégration et perf.

**Interdictions :** pas de retexture de code tiers sans droits, pas de test sur mondes personnels, pas de publication, pas d'AE2 forcé, pas d'optimisation supposée, pas de worldgen, craft, drops sans résolution de IM-013. Aucun commit DEV n'est autorisé par la création de ce document. Handoff : **PREPARED ≠ SENT ≠ RECEIVED ≠ EXECUTED**.

## 8. Sources primaires

Documentation consultée le **22 octobre 2026**, version **Minecraft Java 1.21–1.21.1**, non documentation des 1.21.5+ :
- https://docs.neoforged.net/docs/1.21.1/resources/client/models/modelloaders/ — loader personnalisé, IGeometryLoader/IUnbakedGeometry/IDynamicBakedModel, register event
- https://docs.neoforged.net/docs/1.21.1/resources/client/models/bakedmodel/ — getQuads, ModelData, cache, item render
- https://docs.neoforged.net/docs/1.21.1/blockentities/ — données par bloc, sérialisation et sync
- https://docs.neoforged.net/docs/1.21.1/resources/client/textures/ — PNG sous namespace et tailles puissances de deux

**Sources et contrôles ne sont pas une permission d'exécuter, ni une preuve de compilation ou de réussite visuelle.**
