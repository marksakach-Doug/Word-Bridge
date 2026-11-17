# Word Bridge

A word game for friends, or just you.

Each round shows one big word with two very different meanings. Everyone secretly writes a **bridge**: a new word that fits both meanings. Then the words are revealed one at a time, and the table argues about which ones really bridge.

> **beat**: *noun*, the steady pulse of music / *verb*, to hit again and again
> Bridge: **drum**

This is the browser version: a static site with no accounts, no backend and no build step. The full game, with rounds that wait for your friends and no daily limit, is on [Google Play](https://play.google.com/store/apps/details?id=com.marksakach.wordbridge).

## Play

Open the site, then pick one:

- **Create room** makes a table and shows an invite link. Send it to your friends. Anyone who opens it joins your table, and you press **New game** when everyone is here.
- **Solo game** is the same cards with you as the whole table.

Your name is asked for when you create or join a room, and is remembered on your device.

### How a round works

1. A big word appears with two meanings.
2. Everyone writes one word that fits both, plus an optional one-line reason, and locks it in. You can change it until the last person locks in.
3. When everyone is in, the reasons and words are revealed one at a time. Click anywhere to skip ahead.
4. Talk it over. One player is the **arbiter** each round (the role rotates) and has the final say. They click a word to approve it as a bridge, and approved words turn black.
5. The arbiter presses **Next word**. The tally at the bottom shows how many words were bridged out of how many were played.

### Daily limit

A browser plays **three words a day**, and words can't be skipped. After the third reveal, the page points to the app for more. The day resets at local midnight.

This is a soft, client-side limit: it is stored in the browser's `localStorage` (`wb-day`), so clearing site data resets it. To change it, edit `DAY_MAX` in `index.html`. While testing, run `localStorage.removeItem('wb-day')` in the console.

## Sharing a bridge

Every locked-in word has a **Share** link next to Change. After a reveal finishes, every player's word has one too. It opens a card in a new tab with the big word, both meanings, and a sentence like *Ana's word was drum, you hit it and it keeps time.*

The card page (`share.html`) has:

- the phone's share sheet where the browser supports it
- direct buttons for X, Threads, Bluesky, WhatsApp, Facebook and Telegram
- **Copy link**
- **Save image**, a 1080x1350 PNG for Instagram, TikTok and Snapchat, which have no "post this link" URL
- a link to the Android app, or to the browser game on other devices

There is no backend: the link itself carries the card. Its text is rendered as plain text, length-limited, and the page is marked `noindex`. Link previews show the same generic image for every card, because link crawlers don't run JavaScript.

## How multiplayer works

Multiplayer uses [PeerJS](https://peerjs.com/) (WebRTC). The host's browser is the table: it holds the game state, deals the words, and sends updates to everyone. Guests send the host their moves. Because of that:

- **The host must keep the page open.** If the host closes or reloads it, the table ends and guests see that the host has left. On a phone, switching away for long (including to share) can suspend the page.
- The game is built for two to six players. The code does not enforce a cap.
- PeerJS's free public signaling server is only used to introduce browsers to each other. Game traffic goes directly between them.
- Some strict networks (certain school or corporate Wi-Fi) block direct WebRTC connections. If friends can't join, try a different network, or run your own TURN server and pass it to `new Peer(...)` in `index.html`.

## Run locally

```
python3 -m http.server 8000
```

Then open <http://localhost:8000>. Serve it over HTTP to test invite links and the share card. To test multiplayer on one machine, open the invite link in a second tab or a private window (each needs its own name; the daily count is shared by tabs in the same browser).

## Deploy to GitHub Pages

1. Push this repo to GitHub with `index.html` at the root.
2. Go to **Settings > Pages**.
3. Under **Build and deployment**, choose **Deploy from a branch**, select `main` and `/ (root)`, and save.
4. After a minute the site is live at `https://<you>.github.io/<repo>/`.
5. In `share.html`, replace `https://YOUR-SITE/og.png` with the absolute address of your deployed `og.png` (1200x630) so shared links get a preview image.

The `.nojekyll` file tells Pages to serve the files as-is.

## Customizing

- **Words:** The `POOL` array in `index.html` holds 436 words. Each entry is `["word", "n", "first meaning", "v", "second meaning"]`. Use `"n"` for noun or `"v"` for verb, and pick words whose two meanings are far apart.
- **Daily limit:** `DAY_MAX` (default 3).
- **Reveal pacing:** `TYPE_MS`, `NAME_MS`, `PAUSE_MS`, `WORD_MS` and `HOLD_MS`.
- **Limits on input:** `MAXLEN` (bridge word length) and `WHYMAX` (reason length).
- **App link:** the Google Play URL appears in `share.html` (`PLAY`) and in the daily-limit panel in `index.html`.
- **Stored in the browser:** `wb-name` (your name) and `wb-day` (today's played words).

## Files

| File | Purpose |
| --- | --- |
| `index.html` | The game: markup, styles and script |
| `share.html` | The share card page, built from the link's query string |
| `og.png` | Link preview image for shared cards (you add this, 1200x630) |
| `README.md` | This file |
| `LICENSE` | MIT license |
| `.nojekyll` | Tells GitHub Pages not to run Jekyll |
| `.gitignore` | Ignores editor and OS clutter |

## Credits

Built on [PeerJS](https://github.com/peers/peerjs) (MIT), loaded from a CDN and pinned to version 1.5.4.

## License

[MIT](LICENSE)
