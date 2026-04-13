# download-academic-paper

A reusable AI agent skill for downloading academic papers (PDFs) from DOI, title, or arXiv ID. Handles open access resolution, verification, and failure reporting.

Follows the open [Agent Skills](https://agentskills.io) standard — works with Kiro, Claude Code, OpenAI Codex, Cursor, Roo Code, and any agent that supports `SKILL.md`.

---

## What it does

1. **Pre-searches** the paper landscape before downloading (authors, related projects, citation chains)
2. **Resolves PDF URLs** across multiple sources: arXiv → Semantic Scholar → Unpaywall → HAL/DLR elib → publisher OA
3. **Downloads** with proper headers to avoid bot detection
4. **Verifies** every file with `pdftotext` — catches arXiv ID collisions and paywall redirects
5. **Reports** what was downloaded, what failed, and why

---

## Installation

### Kiro CLI (global — works across all projects)

```bash
# Clone into Kiro's global skills folder
git clone https://github.com/TatcataiTTN/Download_Academic_Papers.git \
  ~/.kiro/skills/download-academic-paper
```

Kiro auto-activates the skill when your request matches the description. No further setup needed.

### Kiro CLI (workspace — project-specific)

```bash
# Clone into your project's skills folder
git clone https://github.com/TatcataiTTN/Download_Academic_Papers.git \
  .kiro/skills/download-academic-paper
```

### Claude Code / OpenAI Codex / Cursor / Roo Code

```bash
# Clone anywhere accessible to your agent
git clone https://github.com/TatcataiTTN/Download_Academic_Papers.git
```

Then reference it in your agent config or load it manually:
- **Claude Code**: add to `.claude/skills/` or reference in `CLAUDE.md`
- **Codex**: add to `.codex/skills/` or reference in `AGENTS.md`
- **Cursor**: add to `.cursor/skills/`
- **Roo Code**: add to `.roo/skills/`

### Manual (any AI assistant)

Copy the contents of `SKILL.md` into your system prompt or context window. Reference the `references/` files as needed.

---

## Usage

### Kiro (auto-activation)

Just describe what you want — Kiro activates the skill automatically:

```
Download all papers related to the CARIOQA project for me.
```

```
Fetch these papers and save them to ~/papers/quantum-sensors/:
- DOI: 10.1038/s41526-023-00297-w
- arXiv: 2404.10471
- Title: "High-accuracy inertial measurements with cold-atom sensors"
```

```
Find and download all papers by Quentin Beaufils on cold atom interferometry.
```

### Claude Code / Codex / Cursor

Invoke explicitly by referencing the skill file:

```
Use the skill in download-academic-paper/SKILL.md to download these papers: [list]
```

Or load it into context first:

```
Read download-academic-paper/SKILL.md and follow its workflow to download:
[paper list with DOIs/titles]
```

### Any AI assistant (manual prompt)

Paste the content of `SKILL.md` into your conversation, then provide your paper list.

---

## Example session

```
User: Download these CARIOQA papers to ~/Downloads/CARIOQA/papers/:
      - Beaufils et al. 2023, npj Microgravity, DOI: 10.1038/s41526-023-00297-w
      - HosseiniArani et al. 2024, Advances in Space Research, DOI: 10.1016/j.asr.2024.06.055
      - Geiger et al. 2020, AVS Quantum Science, DOI: 10.1116/5.0009093

Agent: [skill: download-academic-paper activated]

Phase 0 — Pre-search:
  Resolving DOIs via Semantic Scholar...
  Beaufils 2023 → arXiv: 2212.06686 ✓
  HosseiniArani 2024 → arXiv: 2404.10471 ✓
  Geiger 2020 → arXiv: 2003.12516 ✓

Downloading...
  [1/3] Beaufils2023_Rotation_Nadir_CARIOQA.pdf — 899 KB ✓ verified
  [2/3] HosseiniArani2024_Advances_AtomInterferometry.pdf — 1801 KB ✓ verified
  [3/3] Geiger2020_HighAccuracy_ColdAtom_Sensors.pdf — 4016 KB ✓ verified

=== COMPLETE: 3/3 downloaded ===
```

---

## Requirements

The agent needs access to these tools:
- `execute_bash` (or equivalent) — for `curl`, `pdftotext`, `mkdir`
- Internet access — for arXiv, Semantic Scholar, Unpaywall APIs

`pdftotext` must be installed on the system:
```bash
# macOS
brew install poppler

# Ubuntu/Debian
sudo apt install poppler-utils
```

---

## File structure

```
download-academic-paper/
├── SKILL.md                          # Main skill instructions (load this)
└── references/
    ├── sources-and-apis.md           # Full API docs: arXiv, Semantic Scholar,
    │                                 # Unpaywall, OpenAlex, HAL, DLR elib, Zenodo
    ├── author-search.md              # How to find author publication lists via
    │                                 # ORCID, OpenAlex, Semantic Scholar
    └── web-search-queries.md         # Search query templates by use case
```

---

## Supported sources

| Source | Type | Best for |
|---|---|---|
| arXiv | Preprint | Physics, CS, math, quantitative fields |
| Semantic Scholar | Aggregator | DOI → arXiv ID + OA PDF |
| Unpaywall | OA resolver | Any DOI — finds legal free version |
| OpenAlex | Aggregator | Author ORCID → full publication list |
| HAL | Institutional | French research (CNRS, ONERA, Observatoire de Paris) |
| DLR elib | Institutional | German Aerospace Center papers |
| Zenodo | Repository | Datasets, preprints, conference papers |
| Publisher direct | OA journals | CC-BY articles (AGU, Wiley, MDPI, Copernicus) |

---

## License

MIT — free to use, modify, and share.
