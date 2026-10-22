# IM-008 — DEV bootstrap / bounded handoff

**Status:** PREPARED / NOT SENT / NOT RECEIVED / NOT EXECUTED
**Prepared:** 2026-10-22
**Design owner:** MCA (design only) — code/tests/releases remain with Immersive Monolith DEV owner.
**Governance repo:** `emmanueltremblay9-stack/Immersive-Monolith-GPT`, branch `main`
**Baseline governance HEAD before handoff:** `233ea689dd5fe135bc1ff7fdc4550abb39b40e12`
**Development repo:** `emmanueltremblay9-stack/Immersive-Monolith-DEV-`
**DEV read status:** empty public repository; `main` configured as default, no commit/HEAD (GitHub 409).
**Target ASSUMED:** Minecraft Java 1.21.1 + NeoForge; JDK 21.
**Source:** `specifications/IM-CELL-TRIO.md`, `DECISION_LOG.md`, `GOVERNANCE.md`, and `SOURCE_MANIFEST.json` at governance baseline HEAD.

## 1. Bounded purpose (proposal, not execution authorization)

Once the DEV owner explicitly approves a concrete target/ref and creates or authorizes its initial branch:

1. Initialize from the **official 1.21.1 NeoForge Mod Developer Kit**, selecting its compatible Gradle plugin/toolchain rather than manufacturing a Gradle wrapper/version.
2. Establish one sandboxed MOD ID, proposed `immersive_monolith` (not canon until owner approves).
3. Create the minimal module registries required for a single `Cell Trio` block, associated BlockItem, and 16 color-specific crystal items (or data-component encoded item if owner favors that approach).
4. Define data contracts for three ordered colors (SMALL, MEDIUM, TALL), an attachment face and quarter-turn rotation, and persistence/synchronization on server/client.
5. Create original geometric model placeholders for 3 attached prisms of unequal heights, contained within one block; no HEXCraft/XyCraft code, textures or models.
6. Run isolated client and dedicated-server smoke tests, collect commit SHA, JDK/NeoForge versions, build log and failures.

**Explicitly excluded from this initial handoff:** active world generation, economy/loot outputs, full crafting, AE2 dependency, shaders, optimization, launcher/profile installation, modification to personal worlds, merge, release, monetization or publication.

### Technical decomposition (PROPOSED)

- BlockState: 6 attachment-face states × 4 local quarter-turn rotations (24 combinations, verify actual orientation and placement semantics).
- BlockEntity: immutable composition of three `DyeColor` values; `origin` must be designed before crafting/loot can be enabled, otherwise default to non-generating placeholder. Persist in safe serialized data; synchronize to client; prevent trust in client-provided loot.
- ItemStack data component: retained triple colors and origin when block is picked up. Prefer no 4096 block registrations or separate BE type per color.
- Render: original faceted geometry and emissive-looking material; any bloom is renderer/shader-dependent; choose block-light level only after verifying visible effect/performance.
- VoxelShape: multiple simple AABBs transformed for orientations; cache immutable shapes, avoid per-tick recalculation.

## 2. Recipe spike (independent, not to ship until economy resolved)

**User requirements (preserved):**
- 3 dyes + 1 Glowstone Dust → 1 Cell Trio
- 1 crystal → 4 dyes of its own color
- Multicolor natural Trio broken normally → exactly 3 crystals, one matching each point; Fortune has no bonus

**Recommended technical discrimination (PROPOSED):**
Use a **custom crafting recipe** with ordered inputs, because a normal shapeless recipe does not preserve which dye is SMALL/MEDIUM/TALL. A candidate 2×2 pattern is:
```
[SMALL_DYE] [MEDIUM_DYE]
[TALL_DYE ] [GLOWSTONE  ]
```
If mirrored/shifted patterns are accepted, the algorithm must preserve the same semantic slot mapping and reject ambiguity. The result carries the triple of colors and `CRAFTED` provenance in an ItemStack component; authoritative assembly on server. Do not register this recipe in a published artifact until the owner accepts the economics and the order-mapping rule. A static set of 4096 recipe variants is disfavored.

**BLOCKING IM-013:** If a crafted trio breaks into three crystals, `3 dyes + glowstone` → `3 crystals` → `12 dyes`; this could unintentionally duplicate dyes. A provenance-sensitive loot rule (`CRAFTED` does not drop crystals) is recommended, **not approved**, and may conflict with "trio multicolor always drops exactly 3 crystals" if that statement includes crafted forms. Do not silently narrow user scope. Submit to owner for explicit resolution before implementing loot and full crafting.

## 3. Underground world-generation plan — separate future handoff

Implement `ConfiguredFeature` / `PlacedFeature` and a NeoForge `BiomeModifier` when authorized. Propose the step `underground_decoration`. Candidate biome tags and configured dimension sampling must be validated against targeted data registries and terrain.

**Hard requirement:** NEVER naturally generate the trio on the surface. A simple `canSeeSky == false` is not a sufficient universal underground predicate (open caves/facades, Nether roofs/End islands). Design a versioned `isUndergroundEligible` predicate using local geological cover and dimension-specific conditions; include edge-case tests. No destructive worldgen replacement, no retroactive chunk modification. The proposed Y ranges/density in `IM-CELL-TRIO.md` are non-measured provisional knobs, not acceptance thresholds.

## 4. Alternatives and discriminating tests

| Candidate | Status | Reason / test |
|---|---|---|
| NeoForge Java mod | **VIABLE for full scope in principle** | New block + logic + model + worldgen; verify in runtime |
| Datapack + resource pack only | **EXCLUDED** | Cannot create arbitrary new registered engine block type or full programmable per-point data behavior |
| Existing framework scripting/config | **UNCERTAIN** | Requires specific version-compatible installed dependencies and proof |
| Server-only plugin / vanilla client | **EXCLUDED for required native custom block model** | Client asset/model contract not satisfied automatically |

Test order:
1. `gradlew build` with JDK 21 and owner-selected MDK; retain JAR SHA and build log.
2. `runClient`: place one trio on each of 6 faces, rotate 4 ways, confirm collisions and visuals.
3. `runServer`: no client classloading crash; connect compatible client, verify state sync.
4. Save/load/pick/re-place all three colors; check metadata unchanged and no duplication.
5. Unit test that non-implemented recipes/worldgen/loot stay disabled until decision.

## 5. Authority and stop conditions

- Authoritative DEV owner must explicitly accept scope, target NeoForge version, repository/branch, tooling, and write rights before implementation.
- No implementation should start from a presumed HEAD: DEV currently has no HEAD and its initial commit must be made under DEV owner authority.
- Any conflict about license, colors, serialization, anti-duplication or native asset use ⇒ STOP relevant feature, deliver findings without changing user requirements.
- No copied HexCraft assets or code. All original assets must have attributable provenance and a separately approved output license.
- PASS is only for an executed, scoped test with build/game evidence. Drafts are not releases.

## 6. Sources checked (2026-10-22)

Official NeoForge **1.21.1**:
- https://docs.neoforged.net/docs/1.21.1/gettingstarted/ (JDK 21, MDK, build and run)
- https://docs.neoforged.net/docs/1.21.1/resources/server/recipes/ (Recipe, RecipeSerializer, RecipeInput, assemble)
- https://docs.neoforged.net/docs/1.21.1/blocks/states/ (BlockState lifecycle)
- https://docs.neoforged.net/docs/1.21.1/worldgen/biomemodifier/ (BiomeModifier / PlacedFeature)

External docs are engineering references, not runtime test evidence or authorization to copy third-party code.

## 7. Required return from DEV owner (not received)

Report the branch name, exact HEAD/commit SHA, modified files, supported Minecraft/NeoForge/JDK versions, any dependency/license changes, logs and test artefacts, unresolved failures and owner acceptance. No fabricated CI PASS or FPS/TPS measurements.

**Handoff outcome here:** PREPARED as documentation in GPT only, NOT SENT to DEV owner, NOT EXECUTED.
