# Choruswild Transcendent Balance Patch - QA Report

## Static validation

- JavaScript syntax: PASS (`game.js` and `sw.js`).
- Manifest JSON: PASS.
- Runtime source contains no emoji/pictograph characters: PASS.
- Runtime has no external HTTP/HTTPS dependency or CDN: PASS.
- Flat GitHub Pages package: PASS.
- Species IDs: 62 total / 62 unique.
- Base shape IDs: 62 total / 62 unique.
- Worlds: 9.
- Home-world seasonal event mappings: 8, covering every non-Seasonal world/islet.
- App icon: 512 x 512 PNG rendered from the actual in-game Epic Gourdgeist art, not generated promotional artwork.
- Service-worker cache version updated to `choruswild-v4-transcendent-patch`.

## Browser runtime method

The environment blocks normal navigation to localhost and file URLs. QA therefore used headless Chromium with an in-memory page that inlined the exact shipped `index.html`, `style.css` and `game.js`. A small localStorage shim replaced only the browser storage API so save-dependent flows could be exercised. Game logic and UI code were not replaced by the harness.

## Final browser flow tests

The following flows were exercised in Chromium with zero page errors and zero console errors:

### Daily Arcade

- Opened the rebuilt three-tier Scratch selector.
- Verified Standard advertises 0.25 percent seasonal and 2 percent Quad/top-tier.
- Verified Grand advertises 1 percent seasonal and starts ready on a fresh save.
- Claimed a ready Grand Ticket for 0 Gems, completed it, then verified the next Grand shows a 3-day cooldown and a 16-Gem skip.
- Used the Grand skip and verified exactly 16 Gems were deducted.
- Verified Supreme advertises 3 percent seasonal, 10 percent Quad/top-tier and 2x Rare/Epic form odds.
- Claimed a ready Supreme Ticket for 0 Gems, completed it, then verified the next Supreme shows a 10-day cooldown and a 35-Gem skip.
- Used the Supreme skip and verified exactly 35 Gems were deducted.
- Forced a Standard seasonal prize on Meadowreach on October 17 and received Petalbop despite the Spring breeding event being inactive, confirming Scratch seasonals are year-round.
- Verified the new cooldown timestamps persist through the normal save object.

### Fluxworks attunement

- Unlocked Fluxworks and supplied test resources through the built-in owner panel.
- Upgraded the Synthesis Chamber to Stability 3.
- Started Five-Cycle.
- Verified Five-Cycle displays as exactly one batch job rather than five jobs overflowing three slots.
- Rushed the batch using the normal scaled Gem speed-up path.
- Verified completion produced one Stone, Leaf, Gale, Ember and Glow Core.
- Verified the persistent Attunement panel remained operational after completion.

### Halloween home-world event and failed breeding

Test date was inside the Moonroot Halloween window.

- Opened Moonroot Hollow.
- Verified the Gourdgeist limited event recipe is visible.
- Selected Rootoracle + Candlewisp.
- Verified the possible-results board includes Gourdgeist limited outcomes, normal outcomes and failure-return outcomes.
- Checked every displayed probability is 100 percent or less.
- Forced a failure path.
- Verified the result stays hidden until the timer is completed.
- Verified the failed result is explicitly a Common parent return.
- Moved the returned egg into the Nursery.
- Completed Nursery incubation and hatched it cleanly.

### Seasonal Island breeding

- Verified exactly four direct-purchase Festival Ensemble starter cards.
- Verified all 14 permanent seasonal recipes are listed.
- Bought the four starters.
- Fed Lanternling and Ribbonram to breeding level.
- Verified the Petalbop Common/Rare/Epic outcomes and failure paths appear before breeding.
- Forced a successful seasonal breed.
- Completed the breeding timer, Nursery timer and hatch flow successfully.

### Responsive layout

- Chromium viewport: 390 x 844.
- `documentElement.scrollWidth == innerWidth`: PASS.
- No horizontal page overflow.
- Patched three-tier Scratch selector stacks correctly at 390 pixels with all three tiers present.

## Regression fixes verified

- Previous impossible Fluxworks Song Power target: replaced with a roster-scaled reachable target.
- Previous fragile single attunement state: replaced with persistent attunement jobs and a dedicated Five-Cycle batch state.
- Previous Five-Cycle visual slot overflow: fixed.
- Previous possible-result failure display that could show 3600 percent: fixed.
- Previous direct Seasonal form-creation studio: removed from the source; seasonals use breeding and normal rarity mechanics. Seasonal Rare/Epic breed-form odds received a small balance increase in this patch.
- Scratch tickets intentionally can award the world seasonal outside its event window; home-world breeding remains event-gated.
- Breeding failure cannot bypass the Resonance Vault because failed parents return as Common.

## Included evidence screenshots

- `screenshot-main.png` - actual Stormforge stage and nine distinct Common performers.
- `screenshot-scratch.png` - labeled wheel plus Standard, Grand and Supreme Scratch Ticket selector.
- `screenshot-fluxworks.png` - Stability 3 Fluxworks Attunement/Synthesis interface after a completed Five-Cycle.
- `screenshot-halloween-breed.png` - live Moonroot Halloween recipe and possible-result probabilities.
- `screenshot-epics.png` - Epic Collection Book showing anatomy and species-detail differences.
- `screenshot-mobile.png` - 390-pixel-wide running build.

## Rapid-tap regression

- Computed `touch-action` on interactive controls: `manipulation`.
- Native `dblclick` default is prevented without replacing ordinary click handlers.
- Purpose: prevent rapid iPad/iPhone taps from triggering browser double-tap zoom.

## Scope statement

The QA pass verifies the tested flows and static invariants above. It does not claim that a browser game of this size can never contain an undiscovered edge case on every device or browser version.
