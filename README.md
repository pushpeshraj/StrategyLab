# Strategy Lab — Backtest Analyzer

A zero-server dashboard for analyzing your trading strategy backtest CSVs. Drop a
file in the browser, get equity curves, drawdowns, monthly heatmaps, P&L
breakdowns, and a sortable trade log. Adjust position size and starting capital
with sliders — all metrics and charts recompute on the fly.

Everything runs **client-side in the browser**. No data ever leaves your
machine, no backend needed.

```
index.html    → the entire app (HTML + CSS + JS in one file)
```

## How to host it on GitHub Pages

This works just like your NSE site — but even simpler because there's no
GitHub Action or Python involved.

1. **Create a new GitHub repository** (public).
2. **Upload `index.html`** to the root.
3. **Enable Pages**: Repo → **Settings → Pages** → Source: **Deploy from a
   branch** → Branch: `main` / `(root)` → **Save**.
4. After a minute it's live at `https://<your-username>.github.io/<repo-name>/`.

That's the whole setup. No workflows, no schedules, no Python — because the
analyzer parses your CSVs in the browser when you upload them.

## How to use it

1. Open the site, click **Upload CSV** (or drag CSVs onto the drop zone).
2. Each strategy file becomes a row in the sidebar.
3. With one strategy loaded → it shows full single-strategy analysis.
4. With multiple strategies → switch the **View Mode**:
   - **Single** — analyze one in detail (click in the sidebar to switch).
   - **Compare** — overlay equity curves to see them side-by-side.
   - **Combined** — sum daily P&L across all strategies to simulate a portfolio.

### Dynamic controls

- **Date range** — type dates or use the **YTD / 3M / 1M / All** quick buttons,
  or drag the slider under the equity chart.
- **Position size multiplier** — scale all P&L (e.g. 2× to see what running
  double the lots would have done). Equity, drawdown, Sharpe — everything
  recomputes.
- **Starting capital** — override the inferred starting capital to see how
  returns and CAGR change.

### Custom chart

Below the standard charts there's a tab strip with every numeric column from
your CSV (ATM levels, futures prices, premiums, DTE, etc.). Click any one to
plot it over the filtered date range. Useful for sanity-checking that your
strategy's behavior makes sense.

## Expected CSV format

The analyzer requires these columns to be present:

| Column              | Type   | Notes                                |
|---------------------|--------|--------------------------------------|
| `Date`              | string | `YYYY-MM-DD`                         |
| `Total_PnL_Rs`      | number | Daily P&L in rupees                  |
| `Cumulative_PnL_Rs` | number | Running total (used for sanity)      |
| `Equity_Rs`         | number | Equity after this day                |

Optional but used when present:

- `DD_Rs`, `DD_pct` — your stored drawdown (the analyzer also recomputes its
  own from the equity curve when you change position size / capital).
- Any column ending in `_PnL_Rs` (e.g. `CE_Short_PnL_Rs`, `Fut_PnL_Rs`,
  `Straddle_PnL_Rs`) → automatically shown as a stacked breakdown chart.
- Every other column → preserved and shown in the trade log table.

The three sample strategy files (strat19 bear call spread, strat20 straddle+BPS,
strat26 long futures + short OTM call) all work out of the box.

## Run locally (optional)

You don't strictly need a server — you can just open `index.html` directly in
a browser. But if you want a clean dev setup:

```bash
python -m http.server
# then open http://localhost:8000
```

## Notes

- Loading multiple files at once is supported — drag them all in together.
- Data is **session-only** — refresh the page and you re-upload. (This is
  intentional; your files stay on your machine.)
- The **Export** button saves the current view (metrics + filtered series) as
  JSON for downstream analysis.
- If you change a strategy's CSV format, just re-upload — the analyzer
  re-detects columns automatically.
