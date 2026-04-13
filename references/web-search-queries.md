# Web Search Query Templates

## 1. Find a specific paper

```
# Google/Bing
"<exact title>" filetype:pdf
"<author lastname>" "<year>" "<journal or conference>" pdf
"<author lastname>" "<2-3 title keywords>" site:arxiv.org

# arXiv
https://arxiv.org/search/?searchtype=all&query=<Author>+<keyword1>+<keyword2>&start=0

# Semantic Scholar
https://api.semanticscholar.org/graph/v1/paper/search?query=<title keywords>&fields=title,authors,year,externalIds,openAccessPdf&limit=5
```

## 2. Find all papers by an author

```
# Google
"<Firstname Lastname>" publications "<institution>"
"<Firstname Lastname>" site:arxiv.org
author:"<Lastname, Firstname>" site:scholar.google.com

# OpenAlex (best)
https://api.openalex.org/works?filter=author.orcid:<orcid>&per-page=50&sort=publication_year:desc

# Semantic Scholar
https://api.semanticscholar.org/graph/v1/author/search?query=<name>&fields=name,papers.title,papers.year,papers.externalIds
```

## 3. Find related projects / consortia

```
# Find all papers from a project
"<project name>" publications OR papers OR results
"<project name>" site:arxiv.org
"<grant number>" acknowledgment papers

# Find consortium members' papers
"<institution1>" "<institution2>" "<topic>" site:arxiv.org
"<topic>" "Horizon Europe" OR "DFG" OR "ANR" site:arxiv.org

# Find follow-up / successor missions
"<project name>" future OR next-generation OR post-<name>
"<topic>" pathfinder mission quantum
```

## 4. Find citing papers (forward search)

```
# Semantic Scholar citations
https://api.semanticscholar.org/graph/v1/paper/<paperId>/citations?fields=title,authors,year,externalIds,openAccessPdf&limit=20

# OpenAlex
https://api.openalex.org/works?filter=cites:<openalex_id>&select=title,doi,open_access&per-page=20

# Google Scholar (manual)
Search: cite:<paper title> OR cited by "<author> <year>"
```

## 5. Find preprints before publication

```
# arXiv by author + year range
https://arxiv.org/search/?searchtype=author&query=<Lastname>&start=0

# bioRxiv/medRxiv (life sciences)
https://www.biorxiv.org/search/<keywords>

# ESSOAr (Earth/space sciences)
https://essopenarchive.org/search?q=<keywords>

# Zenodo
https://zenodo.org/search?q=<keywords>&type=publication
```

## 6. Find open access versions of paywalled papers

```
# Unpaywall (by DOI)
https://api.unpaywall.org/v2/<doi>?email=research@example.com

# OpenAlex (by DOI)
https://api.openalex.org/works/doi:<doi>?select=open_access,best_oa_location

# Google Scholar
"<title>" filetype:pdf -site:researchgate.net

# Institutional repo search
"<author lastname>" "<title keywords>" site:uni-hannover.de OR site:dlr.de OR site:obspm.fr
```

## 7. Domain-specific search strategies

### Physics / Quantum / Space
- arXiv categories: `physics.atom-ph`, `physics.ins-det`, `physics.geo-ph`, `quant-ph`
- Search: `https://arxiv.org/search/?searchtype=all&query=<keywords>&start=0&searchtype=all`
- Filter by category: add `&cross_list_search=include` or use `cat:physics.atom-ph`

### Geodesy / Earth Sciences
- AGU journals: search via `https://agupubs.onlinelibrary.wiley.com/action/doSearch?query=<title>`
- EGU abstracts: `https://meetingorganizer.copernicus.org/EGU<year>/search?q=<keywords>`
- Journal of Geodesy: `https://link.springer.com/search?query=<keywords>&search-within=Journal&facet-journal-id=190`

### Conference proceedings (SPIE, IAG, etc.)
- SPIE: `https://www.spiedigitallibrary.org/search#q=<keywords>`
- IAG: search via Springer `https://link.springer.com/search?query=<keywords>&search-within=Series&facet-series-id=<id>`

## 8. Verify author identity (disambiguation)

Multiple authors can share the same name. Disambiguate using:
```
"<Firstname Lastname>" "<institution>" "<specific topic>"
"<Firstname Lastname>" ORCID
"<Firstname Lastname>" "<coauthor name>"
```

Then confirm via ORCID profile:
```bash
curl -s "https://pub.orcid.org/v3.0/<orcid>/person" -H "Accept: application/json" \
| python3 -c "import json,sys; d=json.load(sys.stdin); print(d.get('name',{}))"
```
