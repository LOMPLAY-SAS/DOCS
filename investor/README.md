# Investor pitch deck (LOMPLAY)

## Source and output

| File | Purpose |
|------|---------|
| [LOMPLAY-pitch-deck.md](./LOMPLAY-pitch-deck.md) | Marp source — edit this for copy and slide order |
| `LOMPLAY-pitch-deck.pdf` | Generated PDF (not hand-edited; re-run the script after changes) |

## Generate the PDF

From the **repository root** (parent of `docs/`):

```bash
npm install
npm run pitch:pdf
```

The PDF is written to `docs/investor/LOMPLAY-pitch-deck.pdf`.

## Dependencies (Chromium)

The npm script passes **`--no-stdin`** so Marp reads the file path from argv (without it, the CLI may wait for stdin when run via some terminals).

`@marp-team/marp-cli` uses a bundled Chromium to render PDFs. First run may download a large browser binary. If PDF generation fails:

- Ensure network access for the first install
- On macOS, some teams install Chromium via Homebrew and set `PUPPETEER_EXECUTABLE_PATH` if you use a custom setup — see [Marp CLI](https://github.com/marp-team/marp-cli) documentation for current environment variables

## Before sending to investors

Replace every **`[Founder fill-in]`** placeholder in the Markdown source (legal name, traction numbers, team, round, contact).

Confirm **shipped vs planned** language matches how you want to present the product; the repo mixes delivered work with roadmap PRDs.

Do **not** commit RPC URLs, API keys, or non-public endpoints into the deck — keep the external deck **safe for forwarding**.

## Optional logo

To add a logo on the title slide, place an image under `docs/investor/assets/` and reference it in `LOMPLAY-pitch-deck.md` with Marp image syntax (see Marp documentation).
