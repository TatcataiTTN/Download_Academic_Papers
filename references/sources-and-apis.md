# Sources & APIs — Full Reference

## 1. arXiv

### Search
```bash
# Basic
curl -s "https://arxiv.org/search/?searchtype=all&query=<Author>+<keywords>&start=0" \
  | grep -o 'arxiv.org/abs/[0-9]*\.[0-9]*' | head -5

# By author only
curl -s "https://arxiv.org/search/?searchtype=author&query=<Lastname>+<Firstname>&start=0" \
  | grep -o 'arxiv.org/abs/[0-9]*\.[0-9]*' | head -10
```

### Download
```bash
curl -L -A "Mozilla/5.0" -o "paper.pdf" "https://arxiv.org/pdf/<ID>" --silent
```

### arXiv API (structured)
```bash
curl -s "https://export.arxiv.org/api/query?search_query=au:<lastname>+AND+ti:<keyword>&max_results=5" \
  | grep -E '<id>|<title>' | head -20
```

**Critical gotcha:** arXiv IDs are assigned sequentially — `2212.06587` is a math paper, `2212.06686` is the Beaufils physics paper. Always verify content.

---

## 2. Semantic Scholar API

### Paper lookup by DOI
```bash
curl -s "https://api.semanticscholar.org/graph/v1/paper/DOI:<doi>?fields=externalIds,openAccessPdf,title,authors,year,citationCount,references"
```

### Paper search by title/keywords
```bash
curl -s "https://api.semanticscholar.org/graph/v1/paper/search?query=<keywords>&fields=title,authors,year,externalIds,openAccessPdf&limit=5"
```

### Author search
```bash
curl -s "https://api.semanticscholar.org/graph/v1/author/search?query=<Firstname+Lastname>&fields=name,affiliations,paperCount,papers.title,papers.year,papers.externalIds&limit=3"
```

### Get all papers by author ID
```bash
curl -s "https://api.semanticscholar.org/graph/v1/author/<authorId>/papers?fields=title,year,externalIds,openAccessPdf&limit=50"
```

### Parse full response
```python
import json, sys
d = json.load(sys.stdin)
print('Title:', d.get('title', ''))
print('Year:', d.get('year', ''))
print('Citations:', d.get('citationCount', 0))
print('ArXiv:', d.get('externalIds', {}).get('ArXiv', 'not found'))
oa = d.get('openAccessPdf')
print('OA PDF:', oa.get('url', 'none') if oa else 'none')
authors = d.get('authors', [])
print('Authors:', ', '.join(a.get('name','') for a in authors[:5]))
```

**Rate limit:** ~100 req/5min unauthenticated. For bulk use, request free API key at semanticscholar.org.

---

## 3. Unpaywall API

```bash
curl -s "https://api.unpaywall.org/v2/<doi>?email=research@example.com"
```

### Full parse
```python
import json, sys
d = json.load(sys.stdin)
print('Title:', d.get('title', ''))
print('OA status:', d.get('oa_status', 'unknown'))  # gold/green/hybrid/closed/bronze
oa = d.get('best_oa_location')
if oa:
    print('PDF URL:', oa.get('url_for_pdf', 'no direct pdf'))
    print('Host:', oa.get('host_type', ''))  # publisher/repository
    print('License:', oa.get('license', ''))
else:
    print('No OA version found')
```

**OA status meanings:**
- `gold` — published in fully OA journal
- `green` — free preprint/postprint available
- `hybrid` — OA article in subscription journal
- `bronze` — free on publisher site, no license
- `closed` — paywalled, no legal free version

---

## 4. OpenAlex API (comprehensive, free)

More complete than Unpaywall for finding OA versions:

```bash
# By DOI
curl -s "https://api.openalex.org/works/doi:<doi>?select=title,open_access,best_oa_location,authorships"

# By title search
curl -s "https://api.openalex.org/works?search=<title>&select=title,doi,open_access,best_oa_location&per-page=5"

# All papers by author (using ORCID)
curl -s "https://api.openalex.org/works?filter=author.orcid:<orcid>&select=title,doi,open_access,publication_year&per-page=50"
```

### Parse OA location
```python
import json, sys
d = json.load(sys.stdin)
oa = d.get('best_oa_location', {})
print('PDF:', oa.get('pdf_url', 'none'))
print('Landing:', oa.get('landing_page_url', 'none'))
print('Is OA:', d.get('open_access', {}).get('is_oa', False))
```

---

## 5. ORCID (author publication list)

```bash
# Search author by name
curl -s "https://pub.orcid.org/v3.0/search/?q=family-name:<lastname>+AND+given-names:<firstname>" \
  -H "Accept: application/json"

# Get all works for an ORCID
curl -s "https://pub.orcid.org/v3.0/<orcid>/works" -H "Accept: application/json" \
| python3 -c "
import json,sys
d=json.load(sys.stdin)
for g in d.get('group',[])[:10]:
    ws=g.get('work-summary',[{}])
    w=ws[0] if ws else {}
    print(w.get('title',{}).get('title',{}).get('value',''), '|', w.get('publication-date',{}).get('year',{}).get('value',''))
"
```

---

## 6. Institutional Repositories

### DLR elib (German Aerospace Center)
```bash
# Search
curl -s "https://elib.dlr.de/cgi/search/simple?q=<title>&_action_search=Search&output=JSON" | head -200

# Direct PDF pattern (from search results)
# https://elib.dlr.de/<ID>/1/<filename>.pdf
```

### HAL (French open archive — CNRS, ONERA, Observatoire de Paris)
```bash
# Search API
curl -s "https://api.archives-ouvertes.fr/search/?q=<title>&fl=halId_s,title_s,fileMain_s&rows=5&wt=json"

# Parse
python3 -c "
import json,sys
d=json.load(sys.stdin)
for doc in d.get('response',{}).get('docs',[]):
    print(doc.get('halId_s',''), '|', doc.get('title_s',[''])[0][:60])
    print('  PDF:', doc.get('fileMain_s','none'))
"
```

### Zenodo
```bash
curl -s "https://zenodo.org/api/records?q=<title>&size=5" \
| python3 -c "
import json,sys
d=json.load(sys.stdin)
for h in d.get('hits',{}).get('hits',[]):
    print(h.get('metadata',{}).get('title','')[:60])
    for f in h.get('files',[]):
        if f.get('type') == 'pdf':
            print('  PDF:', f.get('links',{}).get('self',''))
"
```

### LUH Hannover (IfE group)
```bash
curl -s "https://www.repo.uni-hannover.de/rest/items/search?query=<title>&limit=5" 2>/dev/null
```

---

## 7. Verification Protocol

```bash
# Full verification script for one file
verify_pdf() {
  local f="$1"
  local expected_author="$2"   # e.g. "Beaufils"
  local expected_keyword="$3"  # e.g. "interferometer"

  local size=$(wc -c < "$f")
  local text=$(pdftotext "$f" - 2>/dev/null | head -20)

  echo "Size: $size bytes"

  if [ $size -lt 5000 ]; then
    echo "FAIL: too small (HTML error page)"
    return 1
  fi

  if echo "$text" | grep -qi "<html\|<!doctype\|<body"; then
    echo "FAIL: HTML content (redirect to login)"
    return 1
  fi

  if echo "$text" | grep -qi "$expected_author\|$expected_keyword"; then
    echo "PASS: verified ✓"
    return 0
  else
    echo "FAIL: wrong paper (arXiv ID collision)"
    echo "First lines: $(echo "$text" | head -5)"
    return 1
  fi
}
```

---

## 8. Web Search Query Templates

### Find a specific paper
```
"<exact title>" filetype:pdf
"<author lastname>" "<year>" "<journal>" pdf
site:arxiv.org "<author>" "<keyword1>" "<keyword2>"
```

### Find all papers by an author
```
"<Firstname Lastname>" publications quantum accelerometer
author:"<Lastname>" site:arxiv.org
"<Firstname Lastname>" "<institution>" papers 2020..2026
```

### Find related projects / consortia
```
"<project name>" publications results papers
"<project name>" site:arxiv.org
"<funding grant number>" papers
"<topic>" pathfinder mission quantum site:arxiv.org
```

### Find citing papers (follow citation chain)
```
# Via Semantic Scholar
curl -s "https://api.semanticscholar.org/graph/v1/paper/<paperId>/citations?fields=title,authors,year,externalIds&limit=20"

# Via OpenAlex
curl -s "https://api.openalex.org/works?filter=cites:<openalex_id>&select=title,doi,open_access&per-page=20"
```

---

## 9. Common Failure Modes & Fixes

| Symptom | Cause | Fix |
|---|---|---|
| File < 5 KB | HTML error/redirect | Try different source |
| HTML in pdftotext | Login redirect | Use Unpaywall/HAL instead |
| Wrong paper content | arXiv ID collision | Refine search, add more keywords |
| 403 Forbidden | Bot detection | Add full User-Agent + Accept headers |
| Empty pdftotext, large file | Scanned/image PDF | Accept, note limitation |
| Unpaywall returns null | Genuinely paywalled | Record as closed, suggest manual |
| arXiv search returns nothing | Paper not on arXiv | Try Semantic Scholar, HAL, elib |
| Semantic Scholar 429 | Rate limit | Sleep 60s, retry |
| curl follows redirect to HTML | Publisher paywall | Check OA status first via Unpaywall |
