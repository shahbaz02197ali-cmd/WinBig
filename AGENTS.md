# Working on this repo in Base44

## What this app is

`index.html` is the entire application: a single self-contained page (inline
CSS + inline JS, client-side "pages" toggled by `showPage()`). There is no
backend, no bundler, no `package.json`, and no database. State (`balance`,
`isLogged`, `purchaseHistory`) lives in JS variables and resets on reload.

## Running it

```bash
docker compose -f docker-compose.base44.yml up -d --build
```

`web` is an `nginx:alpine` container that serves the working tree's
`index.html` from host port 3000. Nothing is installed or compiled, so there is
no dependency install step and no build to wait for.

- **Hot reload:** none. The file is bind-mounted, so a browser refresh shows
  edits immediately; in Base44 call `reload_preview` after changing the page.
- **Only `index.html` is mounted.** New static files (images, extra JS/CSS)
  must be added to the `volumes:` list in `docker-compose.base44.yml`, otherwise
  nginx returns 404 for them.
- nginx accepts any `Host` header by default, so the preview proxy host needs
  no allowlist entry. Don't add a `server_name` restriction.

## Verifying it works

```bash
curl -s http://localhost:3000/ | grep -i "WinBig"   # page is served
docker compose -f docker-compose.base44.yml ps       # web healthy
```

Then check the page in the preview: the product grid renders 20 cards from the
`products` array on `window.onload`, and clicking "Purchased Now" while logged
out routes to the login "page".

## Things that are intentionally fake

- Login is client-side only: any phone number "signs in" (`handleAuth`).
- Deposits credit the balance immediately after a confirmation alert
  (`submitDeposit`) — no payment verification.
- Withdrawals only show an alert.
- Product images come from Unsplash and icons from the Font Awesome CDN, so the
  page needs outbound network access to look complete.
