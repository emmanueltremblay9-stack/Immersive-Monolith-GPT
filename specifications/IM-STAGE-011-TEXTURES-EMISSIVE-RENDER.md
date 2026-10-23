# IM-STAGE-011 — Textures originales et contrat de rendu émissif

**STATUT : SPECIFICATION PROPOSED / REFERENCE_ASSETS_PREPARED / RUNTIME_NOT_TESTED**  
**Date : 2026-10-22**  
**Owner de conception : MCA ; owner du code et de la publication : DEV**  
**Gouvernance :** emmanueltremblay9-stack/Immersive-Monolith-GPT, main, baseline avant ce document c67eb28427d552b383ae2d9ea97ed6b3a6db97cb.  
**DEV :** emmanueltremblay9-stack/Immersive-Monolith-DEV-, dépôt existant mais sans HEAD, non modifié.  
**Cible ASSUMED :** Minecraft Java 1.21.1, NeoForge, Java 21 ; version NeoForge exacte UNKNOWN.

## 1. Besoin et invariants préservés

Immersive Monolith introduit un seul bloc de Cell Trio : trois pointes liées de hauteurs SMALL/MEDIUM/TALL, dans une cellule de 1×1×1 bloc, attachable aux six faces de support. Chaque pointe possède une couleur parmi les 16 teintures vanilla. Les trois couleurs sont indépendantes et leur association aux pointes **ne change pas après rotation, sauvegarde ou récupération**. Les monolithes apparaissent naturellement seulement sous terre ; aucun rendu ne doit demander de worldgen en surface.

Les règles utilisateur existantes relatives à la fabrication (3 teintures + 1 poussière de glowstone), conversion (1 cristal → 4 teintures) et récolte (trio multicolore = 3 cristaux exacts, un par pointe) **ne sont pas modifiées**. Le conflit économique IM-013 reste ouvert ; aucun loot/fabrication exploitable n'est autorisé par ce document.

## 2. Ressources de référence produites en session (non intégrées au repo DEV)

Paquet ZIP préparé dans l'espace de travail de cette conversation :

- Identité : Immersive_Monolith_Stage011_Original_Texture_References.zip
- SHA-256 de l'archive : 6bf4ee97cbbc43d54dfe708d33276802d65694c83d0b2b50b5442c1d0785ad9e
- Génération : code procédural original, générateur create_stage011_assets.py inclus dans l'archive.
- 34 PNG **32×32 RGBA**, valides en lecture PIL : 16 albedos de facette de bloc, 16 icônes d'objet cristal, une facette neutre, un masque d'émission.
- Une planche d'aperçu hors assets, manifeste de palette, contrôle SHA-256 des fichiers, README de provenance.
- **Les PNG sont des pièces jointes locales de la conversation, PAS des fichiers présents dans le dépôt GitHub.** Les importer dans DEV constituerait un transfert et une implémentation distincts, à accepter par son owner.

Catalogue nominal des ressources ; la référence Git [palette IM-STAGE-011](../assets/IM-STAGE-011-PALETTE.json) en donne les 16 chemins exacts :

~~~text
assets/immersive_monolith/textures/block/cell_trio_facet_neutral.png
assets/immersive_monolith/textures/block/cell_trio_emission_mask.png
assets/immersive_monolith/textures/block/cell_trio_facet_{dye}.png   × 16
assets/immersive_monolith/textures/item/crystal_{dye}.png          × 16
~~~

Toutes les variantes sont nouvelles et **ne copient pas** les images, UV, modèles ou le code de HEXCraft ni XyCraft. Droits d'utilisation commerciale/publication/licence de sortie : à définir par l'owner du mod ; ne pas inventer de licence.

## 3. Correspondance des 16 teintures

Identifiants de palette/ressource (exactement ceux des couleurs de teintures Minecraft) :

WHITE, ORANGE, MAGENTA, LIGHT_BLUE, YELLOW, LIME, PINK, GRAY,
LIGHT_GRAY, CYAN, PURPLE, BLUE, BROWN, GREEN, RED, BLACK.

Les hex RGB du manifeste sont des **échantillons visuels proposés**, PAS les valeurs internes officielles garanties du moteur Minecraft 1.21.1. Les 16 noms font foi pour la correspondance vers les objets vanilla de teinture ; valider les valeurs à l'aide de DyeColor / tables du moteur de la version cible, et confronter visuellement le rendu.

Ordre stable : SMALL = première teinture, MEDIUM = deuxième, TALL = troisième, selon **l'algorithme de placement des ingrédients qui reste à approuver**. La recette ne doit pas dépendre de l'ordre de recherche des ingrédients d'un recipe shapeless.

Nombre théorique de compositions : 16³ = **4096 combinaisons ordonnées**, mais **un seul type de bloc** et une géométrie fixe. Pas de création de 4096 états ou textures.

## 4. Contrat des matériaux

- Forme : référence [IM-STAGE-010 maillage hexagonal](../blueprints/IM-STAGE-010-TRIO-HEX-MESH.md), géométrie SMALL/MEDIUM/TALL et transformations 24 orientations, **PROPOSED, pas de modèle Minecraft testé**.
- Matériau fond : base/semelle en roche ou céramique technique **originale**, teinte sombre neutre. Les références de cette session ne contiennent PAS encore une texture spécifique validée de semelle.
- Facettes : au moins trois surfaces visibles, avec teinte base, face assombrie, arête lumineuse et pointe haute ; ne pas rendre l'intérieur illisible lorsque les trois pointes sont de couleurs sombres.
- Zone diffuse : albedo coloré ; utiliser les 16 PNG par couleur comme option immédiate **ou** un albedo neutre teinté par le rendu ; ne pas faire les deux simultanément (risque de double teinte).
- Zone brillante : masque alpha 32×32 fourni comme référence de zones émissives. **Un PNG de masque ne crée pas un rendu fullbright à lui seul** : traitement dans BakedModel/BER/custom pipeline nécessaire et à prouver dans le runtime exact.
- Apparence dans une grotte sans lumière : silhouette et arêtes identifiables même si bloom est absent.
- Transparence : **non imposée** dans le premier MVP ; solide/cutout privilégié pour limiter le tri, la sur-impression et les coûts. Transparence réelle à étudier séparément.
- Animation et particules : **aucun ticker ni particule obligatoire** ; les effets futurs restent opt-in et mesurés.

## 5. Trois effets visuels à ne jamais confondre

1. **Lumière vanilla propagée dans le monde** : propriété de bloc lightLevel basée sur BlockState, niveau configurable, valeur initiale **10/15 PROPOSED**, couleur de lumière vanilla non teintée par la couleur de l'objet. La lumière n'est pas automatiquement la source du matériau visuel.
2. **Émissivité apparente des facettes** : le client affiche les faces lumineuses malgré un faible éclairage ambiant via une technique de rendu réellement compatible 1.21.1, à valider (quads fullbright / couche distincte ou autre solution). Le masque n'active rien seul.
3. **Bloom** : halo post-traitement dépendant du moteur de rendu et du shader ; **jamais une dépendance** du gameplay ou de la lisibilité.

AVERTISSEMENT VERSION : ne pas emprunter sans vérification une propriété BakedQuad lightEmission apparue dans d'autres versions 1.21.x ; l'API et les interfaces de rendu varient entre 1.21.1, 1.21.2 et suivantes. Tester shaders facultatifs, rendu vanilla et compatibilité avec renderer alternatif. Les appels Java et compatibilités « fullbright » ne sont pas encore prouvés.

## 6. Route technique comparative

| Solution | État | Couverture | Coût / risque / test |
|---|---|---|---|
| Custom model loader 1.21.1 → dynamic BakedModel + ModelData de BlockEntity | VIABLE sur le principe ; RECOMMANDÉ pour prototype | Géométrie commune, 3 couleurs par instance, 24 orientations, mise en cache de quads | Implémenter loader client et requêtes ModelData ; vérifier rendu FULLBRIGHT réel |
| BakedModel avec 16 sprites par couleur et 3 canaux de teinte | VIABLE | Pas besoin de 4096 modèles ; textures colorées réutilisables | Eviter ré-application d'une teinte sur albedo déjà coloré ; vérifier indices et ItemColor |
| BlockEntityRenderer | FALLBACK VIABLE | Gestion fine de couches, animations, effet éventuel | Appels chaque frame, culling et FPS/TPS à mesurer |
| 4096 modèles statiques différents | EXCLUDED (maintenance) | Théoriquement complet | Gros catalogue, combinatoire inutile |
| Shader obligatoire | EXCLUDED | Bloom agréable seulement | Contradit le fonctionnement client sans shader |

La classe de BlockEntity contient TrioComposition immuable et expose un ModelData client synchronisé. Un changement de composition est exceptionnel : invalider/recharger le modèle lorsqu'une couleur change, non chaque tick. Calcul géométrique uniquement à la cuisson/cache, pas dans une boucle serveur.

Limiter le cache selon les phases : géométrie par orientation (24) + surfaces par teinte/partie, puis compositions combinées à la demande. Eviter une carte globale sans borne dimensionnée au nombre de structures placées.

## 7. Contrat de rendu et synchronisation

**Entrées** :
- TrioComposition(smallColor, mediumColor, tallColor, source, schemaVersion)
- BlockState attachmentFace et axialRotation
- Texture atlas des facettes, composante client et masque émissif
- Paramètre de lumière pour rendu vanilla proposé

**Sorties attendues** :
- trois prismes hexagonaux visibles avec les bonnes couleurs assignées à SMALL, MEDIUM, TALL ;
- iconographie des 16 cristaux cohérente avec les objets récoltables ;
- rendu correct des faces murs/plafonds, sans UV inversés ni disparition de morceaux ;
- lumière de bloc et matériaux visuellement séparés ;
- item intact Silk Touch (PROPOSED) conserve les 3 couleurs et la provenance après sauvegarde/reprise.

**Validations et échecs** :
- Les données manquantes/corrompues doivent utiliser un rendu de repli et **ne jamais convertir source UNKNOWN en NATURAL**.
- Si la couleur d'un BlockEntity manque côté client pendant le chargement du chunk, afficher une texture neutre temporaire puis recalculer après synchro. Ne pas afficher des drops inventés.
- La topologie des cristaux et les UV ne changent pas lorsque leurs couleurs changent.
- Ne pas charger d'objets/classes client dans le serveur dédié.

## 8. QA à retourner par l'owner DEV (futur)

| Précondition | Action | Résultat attendu | Preuve à fournir |
|---|---|---|---|
| 16 textures item et 16 block du paquet | Charger atlas via MDK ciblé | Aucune texture manquante / magenta-black checker | Screenshots et logs client |
| Trio toutes teintes identiques | Voir nuit sombre sans shaders | Cristal identifié, distinction éclairage/émissivité | Captures exposées et info shader |
| 3 couleurs différentes | Placer 6 faces × 4 rotations | Ordre SMALL/MEDIUM/TALL invariant | Captures + valeurs BlockState/BE |
| Monde sauvegardé | Déconnecter / recharger chunk / redémarrer | Couleurs inchangées | Log/état persisté |
| Nombre élevé de blocs en chunk test | Comparer BakedModel et BER | FPS/CPU comparatifs **mesurés** | Protocole reproductible |
| Serveur dédié | Charger mod sans client | Aucun crash client-only | Logs serveur |
| Tri/transparence candidat | Vue proche/lointaine, angles extrêmes | Aucune face inversée / z-fighting | Captures comparatives |
| Texture black/gray/white | Observer faible éclairage | Trois couleurs restent lisibles sans shader | Captures |

QA de ressource locale : 34 PNG 32×32 RGBA vérifiés, inventaire et archive SHA-256 contrôlés ; cela est **PASS STATIQUE**, jamais un PASS in-game.

## 9. Sources primaires techniques (version 1.21.1, consultées 2026-10-22)

- https://docs.neoforged.net/docs/1.21.1/resources/client/textures/ — PNG dans textures/, 16×16 et 32×32 compatibles.
- https://docs.neoforged.net/docs/1.21.1/resources/client/models/modelloaders/ — custom loaders, cuisson BakedModel.
- https://docs.neoforged.net/docs/1.21.1/resources/client/models/bakedmodel/ — ModelData, getQuads, cache.
- https://docs.neoforged.net/docs/1.21.1/resources/client/models/ — mode d'usage et types de rendu.
- https://docs.neoforged.net/docs/1.21.1/blocks/ — BlockBehaviour.Properties.lightLevel, de 0 à 15.
- https://docs.neoforged.net/docs/1.21.1/blockentities/ — données par bloc et synchronisation.

Sources = documentation, jamais permission d'écriture ou preuve de test Minecraft.

## 10. Autorité et arrêt

Conception/ressources **PREPARED**. Aucun transfert à DEV n'est déclaré, aucune licence de distribution n'est fixée, aucun commit DEV/CI/build/test jeu n'est réalisé. L'owner DEV doit accepter le handoff, fixer les versions et autoriser les écritures et tests isolés avant implémentation. Le conflit économique IM-013 demeure ouvert et bloque les mécaniques exploitables, pas la revue visuelle.
