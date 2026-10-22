# Gouvernance et limites des autorités

Statut : **PROPOSED / DRAFT**. Ce document ne change ni les instructions du projet, ni les propriétaires de code, ni les permissions GitHub.

## Périmètre des dépôts

- `Immersive-Monolith-GPT` : gouvernance, décisions, spécifications et handoffs. Les décisions restent attribuées à leurs auteurs / owners.
- `Immersive-Monolith-DEV-` : implémentation, tests, dépendances, Git branches, builds et releases ; son owner conserve son autorité.
- MCA : conçoit, compare et prépare un handoff ; ne s'attribue pas l'exécution, la publication ou la validation d'une implémentation.
- Les dépôts ont été déclarés par l'utilisateur. Il faut vérifier leur état/HEAD au moment de toute opération dépendante ; existence d'un repo ne vaut pas acceptation d'un design.

## Cycle de validation

1. `DRAFT` / `PROPOSED` : document de travail utile, aucune adoption.
2. `APPROVED` : décision explicite de l'owner, avec ID stable et portée.
3. `HANDOFF PREPARED` : demande bornée, sources/ref, tests et actions permises/interdites documentés.
4. `HANDOFF SENT/RECEIVED` : exige une preuve de transmission/réception ; préparation seule ne suffit pas.
5. `IMPLEMENTED` : commit/build avec artefact vérifiable ; `TESTED` et `PASS` séparés.
6. `DONE` : acceptation explicite selon critères.

Aucun stade ne doit être déduit d'un simple « next » si une décision `LOCKED` ou un conflit substantiel exige l'owner.

## Restrictions de sécurité, publication et licence

- Ne pas écraser de changements distants ou fusionner dans DEV depuis la conception.
- Ne pas générer rétroactivement les nouveaux monolithes dans des mondes existants sans sauvegarde et autorisation spécifique.
- Aucune performance supposée (TPS/FPS) ou succès en jeu sans mesure.
- Modèles, code et textures Immersive Monolith indépendants ; ne pas embarquer de composants HEXCraft ou XyCraft sans audit de provenance et autorisation applicable.
- CurseForge, Modrinth et autres plateformes : publication séparée de la conception, licences/dépendances/compatibilités à vérifier avant soumission.
- La licence propre du futur mod est **UNKNOWN** et doit être choisie par l'owner ; ne pas créer un LICENSE spéculatif.

## Handoff minimal vers DEV — pas encore envoyé

**But borné** : produire une première implémentation du bloc Cell Trio et des recettes selon les règles `APPROVED` uniquement.
**Sources** : ref Git exact de ce dépôt et `specifications/IM-CELL-TRIO.md`.
**Autorisé après acceptation par l'owner DEV** : branche isolée, fichiers du nouveau mod, tests unitaires et jeu de test isolé.
**Interdit sans accord** : publication, merge, changement de mondes personnels, copie de code/assets tiers, installation de dépendances inattendues.
**Acceptation** : présence des 3 pointes, orientations/sauvegarde testées, 16 recettes 1→4, zéro spawn en surface dans les scénarios testés, absence de duplication et preuve de drops mixtes.
**Arrêt** : au moindre conflit de droits, de dépendances, d'invariants ou de gouvernance non arbitrés.

État présent : **PREPARED CONCEPT ONLY ; NOT SENT ; NOT EXECUTED**.
