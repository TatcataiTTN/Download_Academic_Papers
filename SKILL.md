---
name: download-academic-paper
description: Download academic papers (PDF) from DOI or title. Use when asked to download, fetch, or collect research papers, journal articles, or preprints. Handles arXiv, open access sources, and verifies downloaded content is correct.
---

## Overview

This skill downloads academic papers as PDFs, verifies correctness, and organizes them. It covers the full pipeline: pre-search to understand the paper landscape → URL resolution across multiple sources → download → verification → failure handling → final report.

---

## Phase 0 — Pre-search (ALWAYS do this first)

Before downloading anything, build a map of the paper landscape. This prevents wasted effort and finds papers you might have missed.

### 0.1 Web search to understand the field

Use these search query patterns to find papers, authors, and related projects:

```
# Find the paper itself
"<title keywords>" filetype:pdf
"<author lastname>" "<year>" "<journal or conference>"
site:arxiv.org "<title keywords>"

# Find related work
"<topic>" quantum accelerometer satellite gravity site:arxiv.org
"<author>" publications site:scholar.google.com OR site:semanticscholar.org

# Find the project/consortium
"<project name>" publications OR papers OR results
"<project name>" site:arxiv.org OR site:nature.com OR site:agu.org

# Find key authors' full publication list
"<Firstname Lastname>" site:orcid.org
"<Firstname Lastname>" "<institution>" publications
```

### 0.2 Identify key authors

For each paper, extract all authors. Then for the most important ones (first/last author, PI):
- Search their ORCID: `https://orcid.org/search/?searchQuery=<name>`
- Search their Google Scholar: `https://scholar.google.com/scholar?q=author:<name>`
- Search Semantic Scholar author page: `https://api.semanticscholar.org/graph/v1/author/search?query=<name>&fields=name,papers`

This reveals their full publication list — often finding related papers not in the original list.

### 0.3 Find related projects

Search for funding acknowledgments and project names:
```bash
# Via arXiv abstract search
curl -s "https://arxiv.org/search/?searchtype=all&query=<project_name>+<topic>&start=0"

# Via Semantic Scholar
curl -s "https://api.semanticscholar.org/graph/v1/paper/search?query=<project+topic>&fields=title,authors,year,externalIds,openAccessPdf&limit=10"
```

Related projects often share authors and cite each other — follow citation chains.

### 0.4 Build a paper list before downloading

Create a structured list:
```
ID | Title | Authors | Year | DOI | arXiv | Status
```
Confirm with user if the list looks complete before proceeding to download.

---

## Phase 1 — Prepare folder structure

```bash
mkdir -p "<target_folder>/<Category1>"
mkdir -p "<target_folder>/<Category2>"
```

Categories depend on context (e.g., Core, Related, Foundational, Conference).
Never mix with existing papers in the parent folder.

---

## Phase 2 — Resolve PDF URL (priority order)

For each paper, try sources in this exact order. Stop at first success.

### Source 1: Direct arXiv ID (if known)

```bash
curl -L -A "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36" \
  -o "paper.pdf" "https://arxiv.org/pdf/<ID>" --silent
```

### Source 2: arXiv search (if no ID)

```bash
# Use specific keywords: author lastname + distinctive title words + year
curl -s "https://arxiv.org/search/?searchtype=all&query=<Author>+<keyword1>+<keyword2>+<year>&start=0" \
  | grep -o 'arxiv.org/abs/[0-9]*\.[0-9]*' | head -5
```

**Search query tips:**
- Use author's last name + 2–3 distinctive title words
- Avoid common words (the, for, of, in, a)
- Add year to narrow results
- Try multiple queries if first fails: swap keywords, remove year, add journal name

### Source 3: Semantic Scholar API (DOI → arXiv + OA PDF)

```bash
curl -s "https://api.semanticscholar.org/graph/v1/paper/DOI:<doi>?fields=externalIds,openAccessPdf,title,authors,year" \
| python3 -c "
import json,sys
d=json.load(sys.stdin)
print('Title:', d.get('title',''))
print('ArXiv:', d.get('externalIds',{}).get('ArXiv','not found'))
oa=d.get('openAccessPdf')
print('OA PDF:', oa.get('url','none') if oa else 'none')
"
```

### Source 4: Unpaywall API (any DOI)

```bash
curl -s "https://api.unpaywall.org/v2/<doi>?email=research@example.com" \
| python3 -c "
import json,sys
d=json.load(sys.stdin)
oa=d.get('best_oa_location')
print(oa.get('url_for_pdf','no pdf url') if oa else 'not open access')
"
```

### Source 5: Institutional repositories

| Institution | URL pattern |
|---|---|
| DLR (Germany) | `https://elib.dlr.de/cgi/search/simple?q=<title>` |
| HAL (France) | `https://hal.science/search/index/?q=<title>&rows=5` |
| LUH Hannover | `https://www.repo.uni-hannover.de/discover?query=<title>` |
| TU Munich | `https://mediatum.ub.tum.de/?query=<title>` |
| Zenodo | `https://zenodo.org/search?q=<title>` |
| OpenAIRE | `https://api.openaire.eu/search/publications?title=<title>&format=json` |

### Source 6: Publisher direct (open access only)

Only attempt if the paper is confirmed CC-BY or gold OA:
- AGU/Wiley: `https://agupubs.onlinelibrary.wiley.com/doi/epdf/<doi>`
- Nature/Springer: `https://link.springer.com/content/pdf/<doi>.pdf`
- MDPI: `https://www.mdpi.com/<journal>/<vol>/<issue>/<article>/pdf`
- Copernicus (EGU): `https://egusphere.copernicus.org/preprints/<year>/<id>/<id>.pdf`

---

## Phase 3 — Download

```bash
curl -L \
  -A "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.0.0 Safari/537.36" \
  -H "Accept: application/pdf,*/*" \
  -o "<filepath>.pdf" \
  "<url>" --silent --show-error
sleep 1.5
```

---

## Phase 4 — Verify (MANDATORY for every file)

Run immediately after each download. Never skip.

```bash
# Step 1: Check file size
size=$(wc -c < "paper.pdf")
echo "Size: $size bytes"

# Step 2: Read content
pdftotext "paper.pdf" - 2>/dev/null | head -15
```

**Verification decision tree:**

```
Size < 5000 bytes?
  → DELETE, try next source (it's an HTML error page)

pdftotext output contains HTML tags (<html>, <body>, <title>)?
  → DELETE, try next source (redirect to login page)

pdftotext output is empty?
  → Keep if size > 100KB (scanned/image PDF), note limitation
  → DELETE if size < 100KB

Title/author keywords NOT found in first 15 lines?
  → WRONG PAPER — arXiv ID collision
  → DELETE, refine search query, try again

All checks pass?
  → VERIFIED ✓
```

---

## Phase 5 — Failure handling

When all sources fail for a paper:

1. **Check if paper is truly paywalled:**
   ```bash
   curl -s "https://api.unpaywall.org/v2/<doi>?email=research@example.com" | python3 -c "
   import json,sys; d=json.load(sys.stdin); print('OA status:', d.get('oa_status','unknown'))
   "
   ```
   - `gold` / `green` / `hybrid` → should be findable, try harder
   - `closed` → genuinely paywalled, record as such

2. **Try Google Scholar scrape via search:**
   ```bash
   # Search for "[PDF]" links — these are often direct PDF links
   curl -s "https://scholar.google.com/scholar?q=<title>" | grep -o 'href="[^"]*\.pdf[^"]*"' | head -5
   ```
   Note: Google Scholar may block automated requests.

3. **Try ResearchGate / Academia.edu** (manual only — cannot automate):
   - Note the paper as "available on ResearchGate, requires manual download"

4. **Record failure with reason:**
   ```
   [ID] Title (Year) — FAILED: <reason>
   Reason options:
   - "no arXiv preprint found"
   - "paywalled (oa_status: closed)"
   - "arXiv ID not found, DOI only"
   - "institutional repo requires login"
   - "conference abstract only, no full paper"
   ```

---

## Phase 6 — Final report

```
============================================================
DOWNLOAD COMPLETE: <N_success>/<N_total> papers
============================================================

✓ DOWNLOADED & VERIFIED:
  [P1] Beaufils2023_Rotation_Nadir.pdf        899 KB
       → "Rotation related systematic effects in a cold atom..."
  [F1] Geiger2020_HighAccuracy_ColdAtom.pdf  4016 KB
       → "High-accuracy inertial measurements with cold-atom..."

✗ NOT DOWNLOADED (no open access):
  [R2] Knabe 2022 — paywalled (Springer book chapter)
  [F2] Kasevich & Chu 1991 — no OA version (PRL 1991)

⚠ DOWNLOADED BUT UNVERIFIED (scanned PDF):
  [X1] SomePaper2010.pdf — image-only, pdftotext empty

Manual download needed for:
  → [R2] https://doi.org/10.1007/1345_2022_151
  → [F2] https://doi.org/10.1103/PhysRevLett.67.181
============================================================
```

---

## File naming convention

```
<ID>_<FirstAuthorLastname><Year>_<3-5_word_title>.pdf
```

- Replace spaces with `_`
- Remove `:`, `/`, `,`, `?`
- Max 80 chars total

Examples:
- `P1_Beaufils2023_Rotation_Nadir_CARIOQA.pdf`
- `F1_Geiger2020_HighAccuracy_ColdAtom_Sensors.pdf`
- `C1_Leveque2022_CARIOQA_Definition_Pathfinder.pdf`

---

## Key rules (non-negotiable)

1. **Always do Phase 0** — pre-search before any download
2. **Always verify** — run `pdftotext` + size check on every file
3. **Never overwrite** — check `wc -c` first; skip if >10KB already exists
4. **Separate folder** — never mix with existing papers
5. **Sleep 1.5s** between requests minimum
6. **Record all failures** with specific reason
7. **Wrong paper = delete immediately** — don't keep mismatched files

---

## Detailed references

- `references/sources-and-apis.md` — full API docs, rate limits, edge cases
- `references/author-search.md` — how to find author publication lists
- `references/web-search-queries.md` — search query templates by use case
