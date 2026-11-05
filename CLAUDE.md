# Googly Gambit: notes for Claude Code

A 3D chess roguelike in a single self-contained file, `index.html` (about 3,000 lines). It was ported from a Claude artifact page and is still published as one:
https://claude.ai/artifact/G7xwHMRpJoYSBYCWN7vMyB

## Layout of index.html

Find sections by searching for the `// =====` banner comments:

| Section | What's there |
| --- | --- |
| `<style>` (top) | All CSS. Colour tokens on `:root`. Phone layout first, wide layout under `@media (min-width:900px) and (min-aspect-ratio:1/1)`, where `#rail` (event card + scoresheet) is the left column; at 1280px+ your card and the curio tray share a row. On phones `#rail` is `display:contents`. |
| `CHESS ENGINE` | Board as `Int8Array(64)`, index `rank*8+file`. White (+) is the player, Black (-) the AI. Move gen, `isAttacked`, negamax `bestMove`. `SHIELD`, `TRANS` and `RULES` carry ability and boss rules into the engine. |
| `GAME DATA` | Scoring tables (`CHIPS`=Material, `MULT`=Brilliance), `PERSONA` lines, `RARE` pieces, `RELICS` (curios), `BOSSES`, events and targets, army builders, shop stock generation. `CHARTERS` (one per event, permanent; helpers `hasCharter`, `price`, `relicMax`, `interestCap`, `baseMult`), `ANNOT` (annotation symbols pieces can wear: `def.ann`), `LOTS` and `genLotChoices` (open one, choose 1 of 3). |
| `SCENE` | three.js renderer, board, piece factory (`buildModel`; the shop-only pieces J/F/A/X/H each have their own turned silhouette), `BOSS_LOOK` costumes (only those with `stache:true` keep the King's moustache), inlays (`ED_MAT`), the `Toy` class (eyes, expressions). |
| `ANIMATION, FX, SOUND` | `tween`, Web Audio `SFX`, particles, speech bubbles, move and capture choreography. Camera: `frameCamera` fits the board, `VIEW` holds the player's turn/tilt/zoom/pan (`applyView`, `resetView`). Render `loop`, then the table dressing (chess clock showing moves left, candle, books and tea) right after it. |
| `ROUND LOGIC` | Save/load, HUD, `startRound`, input (pointer handlers: drag turns, right-drag/two fingers pan, wheel/pinch zoom, a short tap calls `onTap`), `playerMove`, `enemyTurn`, `scoreEvent`, win/lose. |
| `RARE PIECE ABILITIES` | Feats, Jester swap, Executioner sentence, Alchemist transmute, Herald rally. |
| `BOSS RULES` | Fog, Phantom, Necromancer, Puppeteer, Glacier, Jailer/Gorgon locks, Saboteur mines, Blacksmith, Plague Doctor, Gardener vines. |
| `THE CURIO SHOPPE` | Second three.js scene. Shelves top to bottom: Lots & Charters (`SHELF.lots`), Your Cabinet, Curiosities, The Workshop, then your army on the counter. One 3D model per curio (`CURIO`), lot props (`LOT_BUILD`), the framed charter (`buildCharter`). Buying, selling, swapping army squares; an opened lot shows `#lotSheet`, and cards that target a piece reuse the workshop's `sPending` + `applyWork` flow with `lot:true`. |
| `SCREENS` | Title, event list (blinds), prize-giving, game over, victory. |

## Rules that keep it working as a Claude page

- Keep it one file. CSS and JS stay inline.
- External scripts only from cdnjs.cloudflare.com (or jsDelivr/unpkg), pinned to an exact version. Stylesheets only from Google Fonts. Anything else is blocked on Claude pages.
- Wrap every `localStorage` call in try/catch, as `save()`/`loadSave()` do. The save key is `googly-gambit-run-v2`; bump it if the save format changes.
- No `alert`/`confirm`/`prompt`; they do nothing on Claude pages.

## Publishing the Claude page

Line 1 of `index.html` is the page skeleton (`<!doctype html>…<body>`) and the last line is `</body></html>`. The Artifact tool adds that skeleton itself, so publish a copy without those two lines:

```sh
sed '1d;$d' index.html > "$SCRATCHPAD/googly-gambit.html"
```

Then publish that file with the Artifact tool, passing `url: https://claude.ai/artifact/G7xwHMRpJoYSBYCWN7vMyB` so the same page updates instead of a new one being created.

## Testing in a cloud session

Cloud sessions may block cdnjs. To smoke-test with Playwright, fetch the same build from npm (`npm pack three@0.128.0`, file `package/build/three.min.js`) and serve it for the cdnjs URL with `page.route`. Game globals such as `RS`, `onTap` and `rc` are reachable from `page.evaluate`, e.g. `onTap(rc(1,4)); onTap(rc(3,4))` plays e2–e4 once a round has started.
