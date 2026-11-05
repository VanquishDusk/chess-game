# Googly Gambit

A chess roguelike. Every piece has a name, a temper, and very worried eyes.

Captures score the victim's **Material × your attacker's Brilliance**. Hit the points target in the Opening and Middlegame, then checkmate a titled boss in the Endgame. Spend your purse in the Curio Shoppe between games, and win eight events to become World Champion.

## Play

- **On Claude:** https://claude.ai/artifact/G7xwHMRpJoYSBYCWN7vMyB
- **Locally:** open `index.html` in a browser, or serve the folder:

  ```sh
  python3 -m http.server 8000
  # then open http://localhost:8000
  ```

Runs in any modern browser with WebGL. The page loads three.js r128 from cdnjs and its fonts from Google Fonts, so it needs an internet connection.

## Files

- `index.html` is the whole game: HTML, CSS and JavaScript in one file.
- `CLAUDE.md` holds notes for working on the game with Claude Code, including how to republish the Claude page.
