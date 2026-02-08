# Price-Drop Notifier

Framework-free price-drop subscription widget (TS + vanilla DOM + CSS) with:
- Embeddable widget that collects an email and posts `{ email, product: { name, price, url } }`
- Tampermonkey userscript that injects on Amazon/eBay product pages (script embed → iframe fallback)
- Express API that simulates realistic latency and errors
- CSP-strict demo page (no inline scripts/styles)

## Requirements
- Node 20+
- pnpm

## Install
```sh
pnpm i
````

## Build

```sh
pnpm build
```

## Test

```sh
pnpm test
```

## Run

```sh
pnpm start
```

Open: `http://localhost:3000/demo/`

## Userscript (Amazon/eBay)

Amazon/eBay pages are HTTPS, so the userscript needs your server on a **public HTTPS origin**.

1. Start the server:

```sh
pnpm start
```

2. Create an HTTPS tunnel (pick one):

```sh
cloudflared tunnel --url http://127.0.0.1:3000
```

or

```sh
npx localtunnel --port 3000
```

3. Copy the printed `https://...` URL and set it as `ORIGIN` in:

* `userscript/price-drop-injector.user.js`

4. Install the script in Tampermonkey:

* Install Tampermonkey extension
* Import `userscript/price-drop-injector.user.js`
* Enable the script
* Ensure Tampermonkey has site access on Amazon/eBay

### Quick manual check

* Open an Amazon product page (URL contains `/dp/`) or an eBay product page (`/itm/`)
* The widget should appear near the title area
* Submit an email → verify `POST /subscribe-price-drop` in DevTools → Network

## Demo CSP

The demo route serves a page with **no inline scripts/styles** and sets a strict CSP header:

```
default-src 'self';
script-src 'self';
style-src 'self';
connect-src 'self';
img-src 'self' data:;
object-src 'none';
base-uri 'none';
```

## Bundle size proof

```sh
gzip -c widget/dist/price-drop-widget.umd.min.js | wc -c
```

## Proof / artifacts

See `./artifacts/`:

* Network waterfall/timing screenshot
* Network request payload screenshot
* Lighthouse report
* Demo video walkthrough

## Notes / trade-offs

See `NOTES.md` for Amazon/eBay CSP/DOM variance notes, the CSS collision observed, and fallback behavior.
