# ⚾ Tournament Rules Viewer

A mobile-friendly web page for looking up baseball tournament rules. Data is pulled live from a published Google Sheet, so rules can be updated by anyone with sheet access — no code changes needed.

---

## Files

| File | Purpose |
|---|---|
| `index.html` | The main web page |
| `config.js` | Configuration — sheet URL, theme, logo, and column names |
| `mockup.html` | Standalone preview with sample data (no sheet required) |

---

## Setup

### 1. Create your Google Sheet

Set up a sheet with the following columns. **Column order doesn't matter** — the app finds each column by its header name. The first row must be a header row with these exact names (spelling and capitalization must match).

| Column | Type | Notes |
|---|---|---|
| Active | Boolean | `TRUE`, `yes`, or `1` to include in dropdown |
| Tournament | Text | Tournament name shown in the dropdown |
| Location | Text | Venue / city |
| Time Limit | Text | e.g. `1:45` |
| Innings | Integer | e.g. `6` |
| Mercy Rule | HTML | Supports `<ul>`, `<strong>`, `<p>`, etc. |
| Pitching Limits | HTML | Supports `<ul>`, `<strong>`, `<p>`, etc. |
| Balks | Text | e.g. `Enforced` or `Warning first` |
| Courtesy Runner | Text | e.g. `Pitcher & catcher only` |
| Extra Inning | HTML | Supports `<ul>`, `<strong>`, `<p>`, etc. |
| Tie Breaker | HTML | Supports `<ul>`, `<strong>`, `<p>`, etc. |
| Rules URL | URL | Optional. Link to official rules PDF or page. Leave blank to hide the button. |
| Notes | HTML | Optional. Anything that doesn't fit elsewhere. Supports `<ul>`, `<strong>`, `<p>`, etc. |
| Sort Order | Number | Optional. Controls dropdown order (1, 2, 3…). Tournaments without a value appear last. |
| Start Date | Text | Optional. Tournament start date, e.g. `June 6, 2025`. |
| End Date | Text | Optional. Tournament end date. If same as start date, only one date is shown. |
| Dropped Third Strike | HTML | Optional. Enforced or not, and any nuances. |
| Stealing Rules | HTML | Optional. What stealing is permitted, any restrictions. |
| Field Type | Text | Optional. `turf`, `grass`, or `mixed`. Displays as a badge in the header. |
| Substitution Rules | HTML | Optional. Re-entry rules, free substitution, DH rules, etc. Supports rich HTML formatting. |
| Hotel Name | Text | Optional. Team hotel name. If blank, the hotel banner is hidden. |
| Hotel Address | Text | Optional. Hotel street address. |
| Hotel URL | URL | Optional. Link to hotel website. Leave blank to hide the button. |
| Field Address | Text | Optional. Full street address of the field complex. Used to generate a one-tap directions link. |
| Guaranteed Games | Number | Optional. Minimum number of games guaranteed. Displays inline with the dates. |
| Gate Fee | Text | Optional. Admission cost, e.g. `$5 per person` or `Free admission`. Displays in the tournament header. |
| Number of Teams | Integer | Optional. Total teams in the tournament. Displays as a badge alongside guaranteed games. |
| Tournament URL | URL | Optional. Link to the tournament's website. Makes the tournament name a clickable link in the header. |

> **HTML fields:** You can use basic HTML directly in those sheet cells. For example, a Mercy Rule cell might contain:
> ```html
> <p><strong>10 runs</strong> after 4 innings<br><strong>8 runs</strong> after 5 innings</p>
> ```

### 2. Publish the sheet as CSV

1. In Google Sheets, go to **File → Share → Publish to web**
2. In the first dropdown, select your sheet tab
3. In the second dropdown, select **Comma-separated values (.csv)**
4. Click **Publish** and copy the URL

### 3. Add the URL to `config.js`

Open `config.js` and replace the placeholder:

```js
SHEET_URL: "https://docs.google.com/spreadsheets/d/YOUR_SHEET_ID/pub?gid=0&single=true&output=csv",
```

### 4. Deploy

Upload both `index.html` and `config.js` to the **same folder** on your web host. Any static hosting works — GitHub Pages, Netlify, a school or league website, etc.

---

## Customization

All visual customization is done in `config.js` — no need to edit `index.html`.

### Page Title

The text shown in the browser tab and when the page is bookmarked:

```js
PAGE_TITLE: "OV Xpress – Tournament Rules",
```

### Favicon

The icon shown in the browser tab and on the home screen when added to a mobile device:

```js
FAVICON: "apple-touch-icon.png",           // browser tab icon (.png, .ico, or .svg)
APPLE_TOUCH_ICON: "apple-touch-icon.png",  // iOS/Android home screen icon (optional)
```

Both accept a filename in the same folder as `index.html` or a full URL. If `APPLE_TOUCH_ICON` is left empty (`""`), it automatically falls back to whatever `FAVICON` is set to. Set `FAVICON` to `""` to let the browser use its default.

### Logo

Set the logo image displayed in the page header:

```js
LOGO_SRC: "MrXpress.PNG",   // relative path or full URL; set to "" to hide the logo
LOGO_ALT: "Ohio Valley Xpress Baseball",
```

`LOGO_SRC` can be a filename in the same folder as `index.html`, or a full URL to an image hosted elsewhere. Set it to an empty string (`""`) to hide the logo entirely.

### Theme

Control the color scheme of the entire page with the `THEME` setting:

```js
THEME: "original-blue",
```

**Built-in themes:**

| Value | Description |
|---|---|
| `"original-blue"` | Navy background with sky-blue accents (default) |
| `"red-black"` | Black background with red accents |
| `"light-blue"` | Deep navy with lighter blue accents |
| `"orange"` | Black background with orange accents |
| `"black-yellow"` | Black background with yellow accents |
| `"black-white"` | Black background with white accents |

**Custom theme:** You can define your own color set instead of using a built-in name. Replace the string value with an object:

```js
THEME: {
  name:           "custom",
  primary:        "#cc0000",  // main accent color (buttons, borders, header underline)
  primary2:       "#990000",  // darker accent used in hover states and gradients
  navy:           "#0a0a0a",  // darkest background (page background)
  navy2:          "#1a1a1a",  // secondary background (stat row, dropdowns)
  cardBg:         "#111111",  // rule card background
  border:         "#2a2a2a",  // border color throughout
  gold:           "#f5a623",  // highlight color for stat values and bold text
  headerGradient: "linear-gradient(135deg, #0a0a0a 0%, #1a0000 60%, #2a0000 100%)",
},
```

---

## How It Works

1. On page load, the app fetches the published CSV from Google Sheets
2. Only rows with `Active = TRUE` appear in the dropdown
3. When a user selects a tournament and clicks **View Rules**, the rule cards populate below
4. If a **Rules URL** is provided, a button linking to the official rules document appears at the bottom
5. If no Rules URL is set for a tournament, that button is hidden automatically

---

## Updating Rules

Since the page reads live from Google Sheets, keeping rules up to date is simple:

- **Edit a rule** — update the cell in the sheet. Changes appear on the next page load.
- **Add a tournament** — add a new row and set Active to `TRUE`.
- **Deactivate a tournament** — set Active to `FALSE` (or delete the row). It will no longer appear in the dropdown.
- **No code changes needed** for any of the above.

---

## Previewing Without a Sheet

Open `mockup.html` in any browser. It uses hardcoded sample data and does not require a Google Sheet or internet connection. Use it to preview layout changes or share with others before the sheet is ready.

---

## Troubleshooting

**Error says "Missing sheet columns: …"**
- The app couldn't find one or more expected header names in your sheet's first row
- Check spelling and capitalization exactly match the names in `config.js` — e.g. `Time Limit` not `time limit` or `TimeLimit`
- Make sure row 1 of your sheet is the header row, not a blank row or data row
- Check that the URL in `config.js` is correct and was published as CSV (not HTML)
- Republish the sheet — sometimes the publish step needs to be repeated after changes
- Open the URL directly in a browser to verify it returns raw CSV text

**My edits to the sheet aren't showing up**
- Google's publishing cache can take a few minutes to refresh
- Hard-refresh the page (`Ctrl+Shift+R` / `Cmd+Shift+R`) to bypass the browser cache

**The page works locally but not when hosted**
- Make sure `config.js` is in the same folder as `index.html` on your server
- Confirm your host serves `.js` files (virtually all do)

**A tournament isn't appearing in the dropdown**
- Verify the Active column for that row contains exactly `TRUE`, `yes`, or `1`
- Check that the row isn't empty in the Tournament column
