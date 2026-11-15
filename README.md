# G-CATS Auto-Judgement

Judgement support for IMDS material compliance at TMMIN. Import the daily G-CATS Error Check Log,
let the rules engine work through each error, answer the few questions it cannot decide alone, and
record a final OK/NG with its full evidence trail.

**The engine recommends. A person decides.** Nothing is marked final until an Approver confirms it,
and every recommendation keeps the reference versions and the step-by-step reasoning behind it.

Runs entirely in the browser — the G-CATS data never leaves the machine it is opened on.

## Screens

| Screen | What it is for |
| --- | --- |
| **Overview** | KPI cards, completion, bottleneck, error types, by-supplier breakdown, trend, and the Download Summary |
| **Judgment Workspace** | Internal judgment: every case with its evidence chain, status, and the decision panel |
| **Supplier Self-Check** | Everything suppliers submitted on their IMDS Material Self-Check Sheets |
| **Daily G-CATS Update** | Import today's Error Check Log; answers and decisions already made are kept |
| **Auto-Judgement History** | Append-only log of every answer, approval, override and re-open |
| **Supplier Follow-up** | Per-supplier tracker (Need follow-up → Already followed up → Supplier replied) with the Outlook e-mail template, plus the rejected-part resubmission tracker. Summarised on the Overview. |
| **Substance References** | CAS lookup: GADSL → TSZ0001G group → POPs / REACH XVII / REACH XIV |
| **Judgement Rules** | The Appendix II flows the engine follows |

### Light and dark mode

The toggle sits in the page header, next to your role. The app follows your system setting on first
visit, then remembers whatever you pick. Green still means OK and red still means NG in both themes;
the interface accents are cyan and violet so they never compete with a judgement colour.

### Typefaces

Urbanist and JetBrains Mono, loaded from Google Fonts. If your IT blocks that, the app falls back to
system fonts and keeps working — but to self-host instead:

1. Download both families (fonts.google.com) and put the `.woff2` files in `public/fonts/`.
2. In `public/index.html`, delete the two `fonts.googleapis.com` `<link>` tags and add matching
   `@font-face` rules at the top of the `<style>` block.
3. In `vercel.json`, drop `https://fonts.googleapis.com` and `https://fonts.gstatic.com` from the
   Content-Security-Policy header.

## Deploy on Vercel

1. Go to [vercel.com/new](https://vercel.com/new) and import `jasminefleury/gcats-auto-judgment-ver2`.
2. Framework preset: **Other**. Leave the build command empty. `vercel.json` already sets the output
   directory to `public` and adds the security headers.
3. Deploy. Every push to `main` redeploys automatically.

Because the data stays in the browser, the deployment is just static hosting — no server, no database.
Anyone who can open the URL can use the app, so **protect the deployment**: in Vercel, turn on
Deployment Protection (Settings → Deployment Protection) or keep it to your Vercel team.

### Run it locally

Open `public/index.html` in a browser. That is all — there is no build step.

## Daily workflow

1. Download today's G-CATS Error Check Log and save it as `.xlsx` (old `.xls` must be re-saved first).
2. **Daily G-CATS Update** → drop the file in → check the preview → *Commit to register*.
3. **Overview** → start with what needs attention.
4. **Judgement Register** → open a case, read the evidence chain, answer the engine's question.
   *Apply to identical cases* answers a whole group at once; any case that reaches a different
   result on its own data is split back out.
5. Set your role to **Approver** to confirm a final OK/NG. A comment is always required; overriding
   the engine, or deciding a case it could not judge, also requires a written justification.
6. Export:
   - **Excel (Error Log format)** — three sheets: the G-CATS layout ready to upload back, the judgement
     summary, and a "How to read" legend.
   - **Summary report (Excel)** — the judgement summary on its own, in the G-CATS Error Check Log
     column layout plus the full judgement record: engine procedure, OK/NG, the record-of-changing-to-OK
     comment, any override justification, who decided, their role and when. Status is a solid fill:
     green OK, red NG, amber needs input, violet human review.
   - **Printable summary** (PDF via your browser) and **CSV**.

### Internal judgment statuses

A case carries one status, in this order of precedence:

| Status | Set by | Meaning |
| --- | --- | --- |
| **OK / NG** | Approver, or the WARP rule | A final judgement is recorded, with comment, reviewer and date |
| **⚠️ Need confirmation to supplier** | Any reviewer | Parked until the supplier answers; the reason is recorded |
| **Working** | Any reviewer | Taken for internal judgment, with the reviewer's name |
| Needs input / Recommend OK / Recommend NG / Human review | The engine | Where the Appendix II flow stopped |

A final NG also carries an **IMDS reject comment**, kept separately from the internal comment.

### WARP part number auto-judgment

A WARP part-number case is judged **OK automatically** when all of the following hold:

1. the supplier code is present and registered in the Purchasing supplier code master;
2. the company name matches that master entry *(switchable)*;
3. the Appendix II flow itself also reaches OK *(switchable)*.

The master is Purchasing's own registration, so a code found in it counts as confirmed by
Purchasing. A missing or unregistered supplier code is **never** auto-judged — it stays pending for
internal judgment, as does any case the flow cannot settle. Auto-judged rows are marked
**Auto Judged**, carry a timestamp and the reason, and an existing manual judgement is never
overwritten. Every case shows why it did or did not qualify.

### Destination codes

The reviewer picks the TMMIN destination code (No code, A, B, C …). The judgement rules are written
against seven regulatory regions, so each code carries the region used for Appendix 3 and the Pb
TOYOTA policy table — the mapping is listed in **Judgement Rules** and in the summary workbook.
Region meanings come from the Pb table's own note: EU = Europe, Israel, Turkey · KR = Korea ·
CN = China · TW = Taiwan · IN = India · JP = Japan · OTH = everywhere else.

Codes that cover more than one region are split so the reviewer states which applies: *No code*
becomes Japan or General Export, *X* becomes Russia or India, and *V* becomes Israel or the other
Middle and Near East countries. Korea has no code in the TMMIN chart but is offered because the
rules treat it separately.

Each row is matched by module ID, version, error type, component, material and CAS — never by the
daily "No." — so tomorrow's import refreshes the register without losing your work.

## Data and storage

Your work lives in the browser's local storage, per browser and per machine. Clearing site data
erases it, and colleagues do not see each other's work. Export regularly.

Reference data embedded in the app: GADSL 2026, TSZ0001G Rev.30 (Appendix 2), Appendix 3 POPs,
Appendix 4 REACH XVII, Appendix 5 REACH XIV, TSZ2900G mass tolerance, the Pb TOYOTA policy table
and the supplier code master.

> Most reference sets are still marked `EXTRACTED_PENDING_ADMIN_APPROVAL`, and the Pb policy table is
> a draft transcription awaiting verification. Until their owners sign them off, treat every
> recommendation as a suggestion to check, not an answer to trust.

This reference data is Toyota PROTECTED material. Keep this repository private.

## Repository

```
public/index.html    the whole app — engine, interface, styles and reference data in one file
vercel.json          output directory and security headers
```
