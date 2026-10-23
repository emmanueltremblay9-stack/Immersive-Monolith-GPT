# Immersive Monolith — dossier de gouvernance

Statut : **INITIALISATION DOCUMENTAIRE / DRAFT** (2026-10-22).

Ce dépôt conserve la conception, les décisions et les conditions de transfert du projet **Immersive Monolith** (Dirty Studyo). Il n'héberge pas les sources exécutables du mod.

## Dépôts

- **Gouvernance (ce dépôt)** : [Immersive-Monolith-GPT](https://github.com/emmanueltremblay9-stack/Immersive-Monolith-GPT)
- **Développement (owner du code/tests/releases)** : [Immersive-Monolith-DEV-](https://github.com/emmanueltremblay9-stack/Immersive-Monolith-DEV-)

Cible de conception supposée : **Minecraft Java 1.21.1 / NeoForge** ; compatibilité AE2 éventuelle, non obligatoire pour la v0.1. L'owner de développement décide de l'implémentation et de la publication.

## Sources documentaires internes

- [Conception Cell Trio](specifications/IM-CELL-TRIO.md) — modèle, recettes, drops, placement et génération ; statut par règle.
- [Registre de décisions](DECISION_LOG.md) — provenance, degrés de certitude et décisions ouvertes.
- [Gouvernance et handoff](GOVERNANCE.md) — limites des autorités et critères d'acceptation.
- [IM-008 — handoff DEV préparé](handoffs/IM-008-DEV-BOOTSTRAP-PREPARED.md) — bootstrap NeoForge 1.21.1 proposé, non envoyé, non exécuté.
- [IM-STAGE-009 — architecture du MVP](architecture/IM-009-NEOFORGE-MVP-CONTRACT.md) — contrats, persistance, cible et phases du premier prototype ; distinct de la décision IM-009.
- [QA géométrique statique IM-STAGE-009](qa/IM-STAGE-009-STATIC-GEOMETRY.md) — rapport historique, **étiquettes des faces cardinales corrigées à l'étape 010** ; non testé en jeu.
- [IM-STAGE-010 — blueprint 3D du Cell Trio](blueprints/IM-STAGE-010-TRIO-HEX-MESH.md) — positions déterministes, 24 rotations, rendu dynamique et choix proposés.
- [IM-STAGE-010 — preuve mathématique](qa/IM-STAGE-010-HEX-TRANSFORMS-REPORT.json) — 24 orientations vérifiées par calcul local ; **aucun test Minecraft**.

## Principes

- **PROPOSED ≠ APPROVED ; FINAL ≠ adoption.** Rien ici ne déclare une release ou une implémentation réussie.
- Préserver les décisions utilisateur énoncées ; séparer paramètres proposés et comportement vérifié.
- Ne jamais copier les assets ou le code HEXCraft/XyCraft sans revue des licences et des droits propres aux fichiers.
- Pas de modification rétroactive des mondes existants sans accord, sauvegardes et périmètre de migration contrôlé.
- Aucun commit, build, test, installation, merge ou publication dans DEV n'est autorisé par l'existence de ces documents.

## Travaux ouverts

1. Décider de l'antiduplication entre Cell Trio fabriqué et cristal récolté ; la recette 3 teintures + 1 glowstone est la demande de base.
2. Fixer la stratégie de rendu émissif, la persistance des couleurs et les contrôles de génération jamais en surface.
3. Vérifier formats, API et outils selon les versions réelles du jeu et de NeoForge.
4. Obtenir l'acceptation explicite de l'owner avant de transférer un handoff à DEV.
