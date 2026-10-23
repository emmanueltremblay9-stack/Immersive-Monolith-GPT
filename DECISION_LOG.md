# Registre des décisions — Immersive Monolith

Statut : **WORKING LOG**, initialisé le 2026-10-22 à partir des messages utilisateur de cette conversation. Le code DEV n'a pas été consulté ni modifié. `LOCKED` n'est jamais inféré d'un « next ».

| ID | Contenu | Classe | Portée |
|---|---|---|---|
| IM-001 | Nom « Immersive Monolith » choisi par l'utilisateur | USER-DIRECTED | Identité du projet |
| IM-002 | Trois cristaux solidaires (petit, moyen, grand) dans un seul bloc | USER-DIRECTED | Géométrie |
| IM-003 | Palette 16 couleurs Minecraft, mélange possible | USER-DIRECTED | Apparence |
| IM-004 | Émissivité et pose sur six faces à la manière du principe HEXCraft | USER-DIRECTED | Rendu/placement, code et textures originaux requis |
| IM-005 | Apparition naturelle sous terre seulement, jamais en surface | USER-DIRECTED | Monde |
| IM-006 | 1 cristal → 4 teintures correspondantes | USER-DIRECTED | Recette |
| IM-007 | Trio multicolore : précisément 3 cristaux, 1/pointe et même couleur ; pas de bonus Fortune | USER-DIRECTED | Drops |
| IM-008 | 3 teintures + 1 Glowstone Dust → Cell Trio | USER-DIRECTED ; attribution aux pointes OPEN | Recette |
| IM-009 | 6/16, 10/16, 15/16 de hauteur ; 24 orientations | PROPOSED | Géométrie |
| IM-010 | Bloc mono-couleur : bonus Fortune I/II/III proposé | PROPOSED | Loot |
| IM-011 | 80/15/5 % monocolore/bicolore/tricolore | PROPOSED | Generation des couleurs |
| IM-012 | Densités et plages Y Overworld/Nether/End | PROPOSED / NON MESURÉ | Worldgen |
| IM-013 | Provenance NATURAL/CRAFTED et règles anti-duplication | OPEN / BLOCKING | Économie et loot |
| IM-014 | Stocker trio de couleurs dans une BlockEntity, orientation dans BlockState | PROPOSED | Technique |
| IM-015 | NeoForge 1.21.1 Java et intégration AE2 future facultative | ASSUMED / CIBLE À CONFIRMER | Déploiement |

## Décisions visuelles et modèles (IDs distincts du registre IM-001…015)

| ID | Objet | Décision utilisateur | Portée et preuve |
|---|---|---|---|
| IM-VIS-001 | Images de cinq silhouettes, triangle compact | Référence visuelle SELECTED ; géométrie PROPOSED | Aucun PASS Minecraft ; voir `blueprints/IM-VIS-001-CELL-TRIO-CANON-CANDIDATE.md` |
| IM-VIS-002 | Apparence `Cell Trio DEFAULT` de l'image | APPROVED_VISUAL_REFERENCE | Aucun modèle moteur accepté ; voir `blueprints/IM-VIS-002-CELL-TRIO-DEFAULT-VISUAL-APPROVAL.md` |
| IM-VIS-003 | Fichier exact `Cell_Trio_DEFAULT_r01(1).bbmodel` | **APPROVED_MODEL_REFERENCE** par « That is exactly how I want it » | SHA-256 `f2c09b84432c2675213f52dc92311a883c143ab1cac6fce37c51490dfbc4fef6` ; visuel + données, non testé en jeu ; `blueprints/IM-VIS-003-DEFAULT-R01-USER-APPROVED-BBMODEL.md` |

L'approbation `IM-VIS-003` est bornée à la référence `DEFAULT r01` et n'implique ni validation de l'économie IM-013 ni adoption des quatre autres maillages. L'artefact binaire reste une pièce jointe de conversation, pas un commit Git. Aucun handoff DEV exécuté.

## Conflits non arbitrés

- **IM-VIS-003-CAPS — OPEN (écart d'assets à arbitrer)** : l'image de référence `IM-VIS-002` illustre trois coiffes rocheuses **supérieures**. Le `.bbmodel` exact que l'utilisateur a ensuite approuvé `IM-VIS-003` contient de la matière `stone` seulement dans sa partie basse (Y ≤ 2,4/16) et **aucune coiffe haute**. La priorité documentaire reste **le fichier exact approuvé**, sans ajout silencieux. Si l'owner souhaite rapprocher davantage le rendu de l'illustration, créer une révision de fichier approuvée séparément, pas une mutation du SHA déjà accepté.
- **IM-STAGE-013** : `inset_blue.png` est dans la source, mais n'est référencé par aucune des 905 faces. Préserver l'asset et documenter son absence d'usage ; aucune suppression tacite.


- `IM-007` « 3 cristaux sur un trio multicolore » versus `IM-008` « fabrication par teintures » + `IM-006` « rendement 4 » : le recyclage d'un trio fabriqué en cristaux dupliquerait des teintures. Ne pas interdire les drops des trios fabriqués sans accord explicite ; soumettre l'option `CRAFTED` sans cristaux à l'owner.
- Les 16 couleurs appartiennent au projet original, ne pas mélanger avec les **cinq couleurs** du HEXCraft original.
- Le principe de placement six faces peut être réimplémenté de façon indépendante ; **aucune autorisation de copier le modèle, code ou texture originale HEXCraft** n'est déduite.

## Procédure de changement

Changer `PROPOSED` → `APPROVED` uniquement sur décision explicite de l'owner concerné ; conserver les IDs et l'historique. Une décision technique et son intégration en code sont deux événements distincts. Un test PASS exige une preuve liée à un ref Git immuable, une version Minecraft et un scénario reproductible.

Prochain arbitrage suggéré : économie des drops pour les Cell Trio fabriqués, suivi du choix d'un format de recette déterministe et d'une stratégie de données client/serveur.
