# AGENTS.md

Guidance for coding agents working in this repository.

## What this is

A static, read-only web page (GitHub Pages, `kneuroth/science-communication-research`) that renders Kelly's Science Communication Research Wall, an Excalidraw drawing for Kelly's OCAD Major Research Project. The drawing is edited in Kelly's Obsidian vault, not in this repo. There is no build step, package manager, linter, or test suite.

## Where the wall lives

`source.md` is a git-ignored symlink to the drawing in the Obsidian vault. The actual vault path, and how to re-link it, are in the git-ignored `AGENTS.local.md`. Read it before touching `source.md`.

If the file moves in Obsidian, the symlink breaks silently: the page falls back to the committed `wall/` copy, and the pre-commit hook skips syncing. Check `ls -L source.md` before relying on it.

## Commands

- Preview locally: `python3 -m http.server`, then open http://localhost:8000. Locally this reads the live drawing through `source.md`.
- Hooks are enabled with `git config core.hooksPath hooks`. A fresh clone needs this set, and also needs `source.md` created.

## How the pieces fit

- **`index.html`** does everything in one module script. It loads React 18 and Excalidraw 0.18 from esm.sh, fetches `source.md`, and falls back to `wall/wall.md` when that fetch fails (as it does on GitHub Pages). It extracts the scene from the ` ```compressed-json ` or ` ```json ` block (compressed scenes use LZString base64). It loads every embedded image listed in the `<fileId>: [[link]]` lines from `wall/files/<basename>`. The wall renders in view and zen mode, zoomed to the frame named `start` if one exists.
- **`hooks/pre-commit`** is how the published copy gets updated. It deletes and rebuilds `wall/` from `source.md`. It walks up from the drawing to find the vault (the directory containing `.obsidian/`), copies each `[[link]]` image into `wall/files/` by basename, and runs `git add -A wall`. Never edit `wall/` by hand: the next commit overwrites it.
- Images are looked up by **basename only**, in both the hook and the page. Two vault images with the same filename will collide.
- The "My Website" glass button styling is copied from kneuroth.github.io (dark tone). Keep it in sync with that site rather than restyling it independently.
