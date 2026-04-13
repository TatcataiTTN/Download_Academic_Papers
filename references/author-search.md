# Author Search & Publication Discovery

## Why search by author?

Key authors in a field publish multiple related papers. Finding their full publication list often reveals papers not in the original list — especially preprints, conference papers, and follow-up work.

---

## Step 1: Find author's ORCID

ORCID is the most reliable author identifier.

```bash
# Search by name
curl -s "https://pub.orcid.org/v3.0/search/?q=family-name:<lastname>+AND+given-names:<firstname>&rows=5" \
  -H "Accept: application/json" \
| python3 -c "
import json,sys
d=json.load(sys.stdin)
for r in d.get('result',[]):
    orcid=r.get('orcid-identifier',{}).get('path','')
    name=r.get('person',{}).get('name',{})
    given=name.get('given-names',{}).get('value','')
    family=name.get('family-name',{}).get('value','')
    print(f'{given} {family} — ORCID: {orcid}')
"
```

---

## Step 2: Get all papers via OpenAlex (best for bulk)

```bash
# By ORCID
curl -s "https://api.openalex.org/works?filter=author.orcid:<orcid>&select=title,doi,publication_year,open_access,best_oa_location&per-page=50&sort=publication_year:desc"

# By author name (less reliable)
curl -s "https://api.openalex.org/authors?search=<Firstname+Lastname>&select=id,display_name,orcid,works_count,works_api_url" | python3 -c "
import json,sys
d=json.load(sys.stdin)
for a in d.get('results',[])[:3]:
    print(a.get('display_name'), '|', a.get('orcid','no orcid'), '|', a.get('works_count'), 'papers')
    print('  Works URL:', a.get('works_api_url',''))
"
```

---

## Step 3: Get papers via Semantic Scholar

```bash
# Search author
curl -s "https://api.semanticscholar.org/graph/v1/author/search?query=<Firstname+Lastname>&fields=name,affiliations,paperCount,papers.title,papers.year,papers.externalIds,papers.openAccessPdf&limit=3"

# Get papers by authorId
curl -s "https://api.semanticscholar.org/graph/v1/author/<authorId>/papers?fields=title,year,externalIds,openAccessPdf,citationCount&limit=50&sort=year"
```

---

## Key authors by domain (for CARIOQA-related work)

### Cold Atom Interferometry / Quantum Sensors
| Author | Institution | ORCID / Profile |
|---|---|---|
| Franck Pereira Dos Santos | SYRTE, Observatoire de Paris | Key PI, atom interferometry |
| Quentin Beaufils | SYRTE | Rotation effects, CARIOQA |
| Sébastien Merlet | SYRTE | Cold atom gravimetry |
| Thomas Lévèque | CNES | CARIOQA mission definition |
| Baptiste Battelier | LP2N, Bordeaux | Space atom interferometry |
| Philippe Bouyer | LP2N | Quantum sensors in space |
| Ernst Rasel | LUH Hannover | BEC in space, BECCAL |
| Naceur Gaaloul | LUH Hannover | BEC, delta-kick collimation |
| Carsten Klempt | LUH Hannover | Quantum optics |

### Satellite Geodesy / Gravity Missions
| Author | Institution | Specialty |
|---|---|---|
| Jürgen Müller | IfE, LUH Hannover | Satellite geodesy, CARIOQA PI |
| Manuel Schilling | DLR Hannover | Hybridization, mission design |
| Roland Pail | TUM Munich | Gravity field recovery |
| Federica Migliaccio | Politecnico Milano | Gravity field software |
| Rene Forsberg | DTU Denmark | Quantum gravimetry simulation |
| Nassim Zahzam | ONERA | Hybrid electrostatic-atomic acc |
| Bruno Christophe | ONERA | Electrostatic accelerometers |

### Hybrid Accelerometers / Kalman Filter
| Author | Institution | Specialty |
|---|---|---|
| Alireza HosseiniArani | IfE, LUH | In-orbit performance model |
| Benjamin Tennstedt | IfE, LUH | Hybridization, atom strapdown |
| Alexey Kupriyanov | IfE, LUH | Electrostatic accelerometry |
| Annike Knabe | IfE, LUH | CAI for satellite gravity |

---

## Step 4: Follow citation chains

```bash
# Papers that cite a given paper (forward citations)
curl -s "https://api.semanticscholar.org/graph/v1/paper/<paperId>/citations?fields=title,authors,year,externalIds,openAccessPdf&limit=20"

# Papers cited by a given paper (references)
curl -s "https://api.semanticscholar.org/graph/v1/paper/<paperId>/references?fields=title,authors,year,externalIds,openAccessPdf&limit=30"

# Get paperId from DOI
curl -s "https://api.semanticscholar.org/graph/v1/paper/DOI:<doi>?fields=paperId,title" | python3 -c "import json,sys; d=json.load(sys.stdin); print(d.get('paperId',''))"
```

---

## Step 5: Find related projects via funding

Search for papers acknowledging the same grant:
```bash
# Via OpenAlex
curl -s "https://api.openalex.org/works?filter=grants.funder_display_name:<funder>,grants.award_id:<grant_id>&select=title,doi,open_access&per-page=20"

# Via arXiv (text search in abstract)
curl -s "https://arxiv.org/search/?searchtype=all&query=<grant_number>+<topic>&start=0"
```

Example for CARIOQA: grant `101081775` (Horizon Europe)
