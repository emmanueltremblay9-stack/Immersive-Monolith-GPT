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

## Conflits non arbitrés

- `IM-007` « 3 cristaux sur un trio multicolore » versus `IM-008` « fabrication par teintures » + `IM-006` « rendement 4 » : le recyclage d'un trio fabriqué en cristaux dupliquerait des teintures. Ne pas interdire les drops des trios fabriqués sans accord explicite ; soumettre l'option `CRAFTED` sans cristaux à l'owner.
- Les 16 couleurs appartiennent au projet original, ne pas mélanger avec les **cinq couleurs** du HEXCraft original.
- Le principe de placement six faces peut être réimplémenté de façon indépendante ; **aucune autorisation de copier le modèle, code ou texture originale HEXCraft** n'est déduite.

## Procédure de changement

Changer `PROPOSED` → `APPROVED` uniquement sur décision explicite de l'owner concerné ; conserver les IDs et l'historique. Une décision technique et son intégration en code sont deux événements distincts. Un test PASS exige une preuve liée à un ref Git immuable, une version Minecraft et un scénario reproductible.

Prochain arbitrage suggéré : économie des drops pour les Cell Trio fabriqués, suivi du choix d'un format de recette déterministe et d'une stratégie de données client/serveur.
