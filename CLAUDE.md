# Vantage Picks Landing

## Purpose
Static landing page linking out to the two Vantage betting-model dashboards
(sports picks, educational/research framing — not financial advice).

## Sibling projects
- `~/Desktop/vantage_cfb` — college football model, serves `dashboard.py` on :8765
- `~/Desktop/vantage_ufc` — UFC/MMA model, serves `pwa_server.py` on :8766
- `~/Desktop/vantage_pwa.py` — a separate, standalone combined mobile PWA
  dashboard for CFB+UFC picks (reads `vantage_cfb/paper_record.json` and
  `vantage_ufc/data/picks_tracker.json` directly). It is NOT part of this repo
  and is not deployed by it — it's a loose alternative UI meant for local/
  Tailscale-funnel access, unrelated to the Cloudflare tunnel setup here.

This repo only contains the marketing/link-out page — no model or picks logic.

## Stack
Plain static HTML/CSS, single file. No build tooling, no framework, no
package.json.

## Entry points
- `index.html` — the entire site (wordmark, two link-out cards, footer)

## Commands
No build/test/lint — it's a single static HTML file. Verify by opening
`index.html` directly in a browser.

## Deploy
- Root domain (`vantage.space`): Cloudflare Pages (per comment in
  `cloudflare-tunnel-config.yml`; no Pages config file found in this repo —
  likely configured directly in the Cloudflare dashboard, or deployed by
  pushing `index.html` to Pages manually/via git integration)
- Subdomains `cfb.YOURDOMAIN.space` and `ufc.YOURDOMAIN.space`: Cloudflare
  Tunnel (`cloudflare-tunnel-config.yml`) proxies to locally-running
  dashboards — `dashboard.py :8765` (vantage_cfb) and `pwa_server.py :8766`
  (vantage_ufc). These are NOT static — the tunnel requires the local
  servers to be running on the host machine.
- GitHub remote: `github.com/echang793/vantage-picks-landing` (main branch,
  no CI/CD workflows configured)

## Architecture
- Single `index.html`: inline `<style>`, two `.card` links (CFB gold accent,
  UFC red accent) pointing at the tunnel subdomains
- `cloudflare-tunnel-config.yml` is a documented template
  (`~/.cloudflared/config.yml`) — placeholder `YOURDOMAIN` and
  `<TUNNEL-UUID>` must be filled in on the actual machine running the tunnel

## Gotchas
- `index.html` currently has placeholder domains (`YOURDOMAIN.space`) in the
  two card `href`s — these must match whatever real domain is configured in
  the live `~/.cloudflared/config.yml` before the links work
- The subdomain links only resolve while both local dashboard servers
  (`:8765`, `:8766`) and the `cloudflared` tunnel are running — this is not a
  fully static/serverless deployment
- `vantage_pwa.py` is a different delivery mechanism (single combined PWA)
  and should not be confused with this landing page's two-subdomain approach

## Do NOT touch
- Do not commit real domain names, tunnel UUIDs, or credentials files into
  `cloudflare-tunnel-config.yml` — keep it as a placeholder template
