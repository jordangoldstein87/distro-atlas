# Distro Atlas

An interactive world map of distributor coverage. Green = has a distributor, red = none. Hover a country to see its distributors; click to add or remove them. Covered countries are labeled on the map; uncovered ones reveal their labels as you zoom in.

## Files
- `index.html` — the app (fully self-contained, no build step)
- `data.json` — the shared, team-wide dataset everyone sees on load

## Distributors and leads
Every country can hold two kinds of entries, chosen from the **Type** dropdown in the
add form (which sits at the bottom of the panel, so the current list stays clean at the top):

- **Distributor** — an active distributor. Counts toward green map coverage and the sales totals.
- **Lead (potential)** — a prospect/contact you're working. Does **not** turn the country green;
  it shows under a separate "Leads" section, a purple `LEAD` tag, and a "Prospect" badge, and is
  counted in the header **Leads** chip and the tooltip's "N leads in pipeline" line.

Each entry has a name plus these optional fields:

| Field | Required | Notes |
| ----- | -------- | ----- |
| Name | yes | shown in the panel, tooltip, and CSV |
| Email | no | contact address; shown as a `✉` mailto link and in CSV |
| WhatsApp | no | phone in international format; shown as a `✆` wa.me link and in CSV |
| City | no | shown as a tag chip next to the name and in the tooltip |
| State / Region | no | shown as a tag chip and in the tooltip |
| Avg sales / month | no | distributors only; number, shown as a green `$/mo` chip and summed per country |
| Note | no | free text (contact, terms…), shown under the name |

The on-disk shape is `{ "<country name>": [ { "type", "name", "email", "city", "state", "sales", "note" }, … ] }`,
where `type` is `"distributor"` or `"lead"` and `sales` is a plain number. Older entries with no
`type` are treated as distributors, and entries that only have `{ "name", "note" }` (or no `sales`/`email`)
still load fine — missing fields are treated as blank/0,
so existing `data.json` files keep working.

## Sales totals
- Each country's panel shows a **Total avg sales / month** tally summed across all of that
  country's distributors, just below the list.
- The country tooltip shows a `… /mo total` line.
- The header **Sales / mo** chip shows the grand total across every country.

## Bulk add format
**Bulk add** accepts one distributor per line, comma-separated:

```
Country, Distributor, City, State, Avg Sales/Month, Note
```

`City`, `State`, `Avg Sales/Month`, and `Note` are all optional — leave a field blank
(`USA, West Coast Wholesale, , CA, 12000`) or just omit trailing fields
(`Germany, EU Smoke Distribution`). Sales is a plain number (e.g. `12000`). Anything after the
Sales column is treated as the Note (so notes may contain commas). Country names are matched
loosely — `USA`, `UK`, etc. work.

Example:

```
Jamaica, Island Vapes Ltd, Kingston, , 8000, Main warehouse
Germany, EU Smoke Distribution
USA, West Coast Wholesale, Los Angeles, CA, 12000, net-30 terms
```

## CSV export
**Export CSV** writes one row per entry (distributors and leads) with columns:
`Country, Type, Name, City, State, Email, WhatsApp, Avg Sales/Month, Note`.

## Host it on GitHub Pages
1. Create a new repository (e.g. `distro-atlas`).
2. **Add file → Upload files** → drag in `index.html` and `data.json` → Commit.
3. **Settings → Pages → Build and deployment → Source: Deploy from a branch** → pick `main` / `/ (root)` → Save.
4. Wait ~1 minute. Your link appears at the top of the Pages settings:
   `https://<your-username>.github.io/distro-atlas/`
5. Share that link with your team.

## Update the map for everyone

### Option A — Auto-publish (recommended)
Turn on **⚙ Auto-publish** in the panel footer once, and every add / delete commits
`data.json` to the repo for you — the live map updates for everyone with no manual steps.

Setup (one time, per browser):
1. Create a **fine-grained personal access token** at
   <https://github.com/settings/personal-access-tokens/new>:
   - **Resource owner:** your account · **Repository access:** *Only select repositories* → `distro-atlas`
   - **Permissions → Repository permissions → Contents:** *Read and write* (leave everything else off)
2. In the app, click **⚙ Auto-publish**, paste the token, confirm owner/repo/branch, tick
   **Enable auto-publish**, and **Save & publish now**. A green "Connected ✓" confirms it works.
3. The footer then shows a live status (`Publishing… / Published ✓`) after each edit.

The token is stored **only in your browser** (localStorage) and is never written into the repo
or the shared dataset. Because the repo is public, treat the token as sensitive — scope it to this
one repo with Contents-only access, and revoke it from GitHub settings if a browser is shared.

### Option B — Manual
1. Open the page and edit distributors (or use **Bulk add**).
2. Click **⤓ Save data.json** to download the updated dataset.
3. In the repo, upload the new `data.json` (replacing the old one) and commit.
4. Everyone sees the update on their next refresh. Teammates can click **↻ Load shared** to pull the latest without clearing browser cache.

## How saving works
- The shared `data.json` in the repo is what new viewers see.
- Edits in the browser are kept locally (per person, per browser) so a refresh won't lose them — with **Auto-publish** on they are also committed to `data.json`; otherwise they are **not** shared until you publish a new `data.json` manually.

> **Privacy note:** GitHub Pages on a free account requires a **public** repo, so anyone with the link can see your distributor data. If that list is sensitive, use a private repo with GitHub Pro/Team, or ask about a password-protected host.
