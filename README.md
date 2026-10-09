# Word Bridge

A word game for up to six players, or just you.

Each round shows one big word with two very different meanings. Everyone secretly writes a **bridge**: a new word that fits both meanings. Then the words are revealed one at a time, and the table argues about which ones really bridge.

> **beat**: *noun*, the steady pulse of music / *verb*, to hit again and again
> Bridge: **drum**

It is a single static HTML file. No accounts, no backend, no build step.

## Play

Open the site, enter your name (it is remembered on your device), then pick one:

- **Play with friends** creates a table and copies an invite link to your clipboard. Send the link to up to five people. Anyone who opens it joins your table.
- **Solo game** is the same cards with you as the whole table.

### How a round works

1. A big word appears with two meanings.
2. Everyone writes one word that fits both, plus an optional one-line reason, and locks it in. You can change it until the last person locks in.
3. When everyone is in, the words and reasons are revealed one at a time. Click anywhere to skip ahead.
4. Talk it over. One player is the **arbiter** each round (the role rotates) and has the final say. They click a word to approve it as a bridge, and approved words turn black.
5. The arbiter presses **Next word**.

### Scoring and records

- Each approved bridge scores a point for whoever wrote it. If two players wrote the same word, both score.
- The tally shows how many words were bridged out of how many were played.
- The **host** presses **End game** when you are done. The final scores are saved to a **Records** list at the bottom of the page, one game per line, winner in bold:

  ```
  John: 3 | David: 6
  ```

Names and records are stored in your browser's `localStorage`, so they stay on that device.

## How multiplayer works

Multiplayer uses [PeerJS](https://peerjs.com/) (WebRTC). The host's browser is the table: it holds the game state, deals the words, and sends updates to everyone. Guests send the host their moves. Because of that:

- **The host must keep the page open.** If the host closes or reloads it, the table ends and guests see "The host has left." Each guest's device still saves the last scores it saw.
- Up to **6 players** total, including the host.
- PeerJS's free public signaling server is used only to introduce browsers to each other. Game traffic goes directly between them.
- Some strict networks (certain school or corporate Wi-Fi) block direct WebRTC connections. If friends can't join, try a different network, or run your own TURN server and pass it to `new Peer(...)` in `index.html`.

## Run locally

```sh
git clone https://github.com/<you>/<repo>.git
cd <repo>
python3 -m http.server 8000
```

Then open <http://localhost:8000>. Opening `index.html` directly from disk also works for solo play, but serve it over HTTP to test invite links. To test multiplayer on one machine, open the invite link in a second tab or a private window.

## Deploy to GitHub Pages

1. Push this repo to GitHub with `index.html` at the root.
2. Go to **Settings → Pages**.
3. Under **Build and deployment**, choose **Deploy from a branch**, select `main` and `/ (root)`, and save.
4. After a minute the site is live at `https://<you>.github.io/<repo>/`.

The included empty `.nojekyll` file tells Pages to serve the file as-is.

## Customizing

Everything lives in `index.html`.

- **Words:** The `POOL` array holds 338 words. Each entry is:

  ```js
  ["word", "n", "first meaning", "v", "second meaning"]
  ```

  Use `"n"` for noun or `"v"` for verb. Pick words whose two meanings are far apart.
- **Player cap:** `MAX_PLAYERS` near the top of the script.
- **Records kept:** `MAX_RECORDS` (default 30).
- **Reveal pacing:** The `TYPE_MS`, `NAME_MS`, `PAUSE_MS`, `WORD_MS`, and `HOLD_MS` constants control the slow reveal.
- **Storage keys:** `NAME_KEY` and `REC_KEY`.

## Files

| File | Purpose |
| --- | --- |
| `index.html` | The whole game: markup, styles, and script |
| `README.md` | This file |
| `LICENSE` | MIT license |
| `.nojekyll` | Tells GitHub Pages not to run Jekyll |
| `.gitignore` | Ignores editor and OS clutter |

## Credits

Built on [PeerJS](https://github.com/peers/peerjs) (MIT), loaded from a CDN and pinned to version 1.5.4.

## License

[MIT](LICENSE)
