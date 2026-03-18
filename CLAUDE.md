# Job Search Management System

A file-based job search pipeline managed conversationally through Claude Code. All workflow logic, scoring, and conventions are defined here.

---

## Directory Structure

```
jobsearch/
├── CLAUDE.md                  # This file — workflow definitions
├── config.json                # Personal info, portfolio URL, settings
├── base-resume.md             # Machine-readable resume (source of truth for scoring)
├── pipeline.json              # Master index of all opportunities
├── templates/
│   ├── resume.tex             # LaTeX resume template
│   ├── cover-letter-startup.tex
│   └── cover-letter-established.tex
└── opportunities/
    └── YYYY-MM-DD-company-role/
        ├── jd.md              # Job description (markdown)
        ├── analysis.md        # Score breakdown + recommendation
        ├── metadata.json      # Status, dates, score, tracking link
        ├── resume.tex         # Tailored resume
        ├── cover-letter.tex   # Tailored cover letter
        └── notes.md           # Freeform notes
```

---

## Workflow: "Process this opportunity"

When the user says **"process this opportunity"** (or pastes a job description, or says "add this job"), execute this 4-step pipeline:

### Step 1: Parse & Create Folder

1. Extract from the JD: **company name**, **role title**, **location** (or "Remote"), **salary range** (if listed).
2. Generate a folder slug: `YYYY-MM-DD-company-role` (today's date, lowercase, hyphens, no special chars).
   - Example: `2026-03-14-acme-corp-sre`
3. Create the folder under `opportunities/`.
4. Save the JD as `jd.md` — clean it up to readable markdown but preserve all content.

### Step 2: Initialize Metadata

Create `metadata.json` in the opportunity folder:

```json
{
  "id": "YYYY-MM-DD-company-role",
  "company": "Company Name",
  "role": "Role Title",
  "location": "Location",
  "status": "new",
  "score": null,
  "score_label": null,
  "company_type": null,
  "date_added": "YYYY-MM-DD",
  "date_applied": null,
  "date_updated": "YYYY-MM-DD",
  "salary_range": null,
  "compensation_notes": null,
  "portfolio_tracking_link": "",
  "folder": "opportunities/YYYY-MM-DD-company-role"
}
```

Generate the portfolio tracking link using the UTM format (see UTM section below). If `portfolio_url` in `config.json` is empty, leave the link blank.

### Step 3: Score Against Resume

1. Read `base-resume.md` (especially the Skills Index section at the bottom).
2. Extract all **required** and **preferred/nice-to-have** skills from the JD.
3. Score using the rubric below.
4. Detect company type (startup vs. established) using the signals below.
5. Generate `analysis.md` in the opportunity folder (see format below).
6. Update `metadata.json` with the score, score_label, and company_type.

### Step 4: Present & Generate

1. **Present the analysis** to the user: show the score, label, key strengths, notable gaps, and recommendation.
2. **Ask**: "Ready to generate the tailored resume and cover letter?"
3. If yes:
   - Generate `resume.tex` — tailored from `templates/resume.tex` using the tailoring rules below.
   - Generate `cover-letter.tex` — using the appropriate tone template (startup or established).
   - Fill in all placeholders with real content tailored to this specific JD.
4. **Update `pipeline.json`** — add the opportunity entry to the `opportunities` array and update `last_updated`.
5. Initialize `notes.md` with the template headers (see format below).

---

## Scoring Rubric

### Skill Extraction

From the JD, identify:
- **Required skills**: anything under "requirements", "must have", "qualifications", or stated as mandatory. Weight = **2x**.
- **Preferred skills**: anything under "nice to have", "preferred", "bonus", or "plus". Weight = **1x**.

#### Extraction Rules

- **Technical skills only**: Score only skills, tools, technologies, certifications, and domain knowledge that can be matched against `base-resume.md`. Exclude soft skills and personality traits (e.g., "collaborative", "self-motivated", "enthusiastic") — these cannot be objectively matched.
- **Degrees and education**: Include as a scored skill. Match against `base-resume.md` Education section. Score 100% for exact or closely related degree (e.g., "BS Information Systems" matches "BS in CS or related field"). Score 0% for degree levels not held (e.g., MS when only BS).
- **Years of experience**: Include as a scored skill. Score 100% if candidate meets or exceeds the requirement. Score 50% if within 1 year of the requirement. Score 0% if more than 1 year short.
- **Clearances and background requirements**: Include as a scored skill. Score 100% if held, 0% if not. If the JD says "able to obtain" or "willingness to acquire", note this in the analysis but still score based on current state.
- **Compound skills**: When a JD lists multiple items as one requirement (e.g., "C/C++, Java, Python"), treat it as a **single skill**. Score based on coverage: 100% if all matched, 50% if at least one is a direct match, 25% if only adjacent matches exist, 0% if none match.

### Match Types

For each skill, compare against `base-resume.md` Skills Index:
- **Direct match (100%)**: Skill is explicitly listed in the Direct Skills section.
- **Adjacent match (50%)**: Skill is listed as an adjacency in the Adjacent Skills section.
- **Gap (0%)**: No match found.

### Score Calculation

```
Score = (sum of weighted matches / total possible weighted points) × 100
```

Where:
- Each required skill contributes: `match_percent × 2` points out of `2` possible
- Each preferred skill contributes: `match_percent × 1` points out of `1` possible

### Bonuses (up to +5 total)

Add up to +5 bonus points for:

- **Certification match**: +2 if JD explicitly mentions a certification the candidate holds (e.g., AWS SA-Associate). +0 if the JD mentions the technology but not the cert specifically.
- **Experience level match**: +2 if candidate meets or exceeds the required years. +1 if within 1 year short. +0 if more than 1 year short.
- **Industry match**: +1 if JD mentions security, access control, or enterprise support and candidate has direct experience in that domain.

### Score Labels

| Score    | Label        |
|----------|--------------|
| 80 – 100 | Strong Match |
| 60 – 79  | Good Match   |
| 40 – 59  | Stretch      |
| 0 – 39   | Long Shot    |

---

## Company Type Detection

Auto-detect company type from JD signals. No user confirmation needed.

### Startup Signals

Look for: "Series A", "Series B", "Series C", "fast-paced", "scrappy", "small team", "equity", "stock options", "early-stage", "seed", "growing team", "wear many hats", "startup", "founding", unknown/unfamiliar brand name.

### Established Signals

Look for: Fortune 500, "enterprise", "at scale", well-known brand (Google, Microsoft, Amazon, Honeywell, IBM, etc.), formal job levels (L3/L4/L5, Band, Grade), structured benefits (401k match, RSU), "compliance", "SOC2", "FedRAMP", "global", large team size.

**Default**: If signals are ambiguous, default to **established**.

---

## Cover Letter Tone Rules

### Startup Tone (use `templates/cover-letter-startup.tex`)
- Energetic, conversational opening with a hook about the company's mission or the problem they solve
- Show **builder mentality**: adaptability, wearing multiple hats, shipping fast
- Emphasize **side projects**, lean processes, **impact metrics**
- Casual but professional close — show excitement about contributing

### Established Tone (use `templates/cover-letter-established.tex`)
- Professional, structured opening that maps directly to role requirements
- Emphasize **enterprise experience**: global customer base, scale, structured processes
- Highlight **process, compliance, certifications** (AWS SA, security practices)
- Formal close with clear next steps

---

## Resume Tailoring Rules

When generating a tailored `resume.tex` from the template:

1. **Skills section**: Reorder skill categories to prioritize JD-matched skills first. Within categories, list matched skills before unmatched ones.
2. **Professional summary**: Rewrite to mirror JD language. If the JD says "cloud infrastructure", use that phrase. If it says "DevOps", lean into that framing.
3. **Experience bullets**: Reorder bullets within each role to put the most JD-relevant ones first. Do not remove bullets — just reorder for emphasis.
4. **Portfolio link**: Include the UTM-tagged portfolio tracking link in the header (if portfolio_url is set in config.json).
5. **All placeholders**: Replace every PLACEHOLDER with real, tailored content. The output must compile with `pdflatex` without errors.

---

## UTM Link Format

```
{portfolio_url}?utm_source=resume&utm_medium=application&utm_campaign={company-slug}
```

- `{portfolio_url}`: from `config.json`
- `{company-slug}`: lowercase company name, hyphens for spaces (e.g., `acme-corp`)

If `portfolio_url` is empty in config.json, omit the portfolio link from generated documents.

---

## Pipeline Management Commands

### "Update me on all opportunities" / "Show dashboard"

Read `pipeline.json` and display a formatted table:

```
| # | Company       | Role                  | Score | Label        | Status       | Added      |
|---|---------------|-----------------------|-------|--------------|--------------|------------|
| 1 | Acme Corp     | SRE                   | 78    | Good Match   | applied      | 2026-03-14 |
| 2 | Startup X     | DevOps Engineer       | 65    | Good Match   | interviewing | 2026-03-10 |
| 3 | BigCo         | Cloud Engineer        | 42    | Stretch      | sunset       | 2026-03-08 |
```

Sort by: active statuses first (new, applied, interviewing, offer), then by score descending.

### "Sunset [company]"

1. Find the opportunity in `pipeline.json` by company name (fuzzy match OK).
2. Update status to `sunset` in both `pipeline.json` and the opportunity's `metadata.json`.
3. Set `date_updated` to today.
4. Confirm: "Sunset [Company] — [Role]. Moved to inactive."

### "Update [company] to [status]"

1. Find the opportunity by company name.
2. Validate the status is one of: `new`, `applied`, `interviewing`, `offer`, `accepted`, `rejected`, `passed`, `sunset`.
3. Update status in both `pipeline.json` and `metadata.json`.
4. If status is `applied`, also set `date_applied` to today.
5. Set `date_updated` to today.
6. Confirm the change.

### "Show me my top opportunities"

Filter `pipeline.json` to active statuses (new, applied, interviewing, offer) and sort by score descending. Display top results.

### "Add notes for [company]"

1. Find the opportunity folder.
2. Open/read the `notes.md` file.
3. Append the user's notes under the appropriate section header.
4. Save.

### "What are my gaps?"

1. Read `analysis.md` from all active opportunities.
2. Aggregate all skills that scored as **Gap (0%)** across opportunities.
3. Rank by frequency (skills that appear as gaps in the most JDs = highest priority).
4. Display: skill name, how many active JDs require it, suggested learning path.

---

## Status Lifecycle

```
new → applied → interviewing → offer → accepted
                                     → rejected

At any point: → passed (candidate decided not to pursue)
              → sunset (opportunity no longer available or relevant)
```

Valid statuses: `new`, `applied`, `interviewing`, `offer`, `accepted`, `rejected`, `passed`, `sunset`

Active statuses (shown in dashboard by default): `new`, `applied`, `interviewing`, `offer`
Inactive statuses: `accepted`, `rejected`, `passed`, `sunset`

---

## File Formats

### analysis.md

```markdown
# Opportunity Analysis: [Company] — [Role]
**Date**: YYYY-MM-DD
**Score**: XX/100 (Label)
**Company Type**: Startup | Established

## Skills Match

| Requirement | Type | Your Evidence | Match |
|-------------|------|---------------|-------|
| Kubernetes  | Required | Adjacent: Docker/VM experience | 50% |
| PostgreSQL  | Required | Direct: DB optimizations at Honeywell | 100% |
| AWS         | Preferred | Direct: Certified SA-Associate | 100% |

## Score Calculation

- Required skills: X.X / X.X weighted points
- Preferred skills: X.X / X.X weighted points
- Base score: X.X / X.X = XX.X%
- Bonuses: +X (itemize each bonus and its justification)
- **Final score: XX/100**

## Key Strengths
- [Bullet points of strongest matches and relevant experience]

## Notable Gaps
- [Bullet points of skills not matched, with brief learning suggestions]

## Recommendation
[Strong Match / Good Match / Stretch / Long Shot] — [1-2 sentence reasoning with advice on whether to apply]
```

### notes.md

```markdown
# Notes: [Company] — [Role]

## Contacts
-

## Interview Prep
-

## Follow-ups
-

## General Notes
-
```

### metadata.json

See Step 2 above for the full schema.

---

## Conventions

- **Dates**: Always use `YYYY-MM-DD` format.
- **Folder naming**: `YYYY-MM-DD-company-role` — lowercase, hyphens, no special characters.
- **Company slug**: lowercase, hyphens for spaces (used in UTM links and folder names).
- **File encoding**: UTF-8 for all files.
- **LaTeX (resume)**: Allowed packages: `extarticle` (document class), `geometry`, `fontenc`, `helvet`, `xcolor`, `titlesec`, `enumitem`, `hyperref`, `tabularx`. No exotic dependencies.
- **LaTeX (cover letters)**: Allowed packages: `inputenc`, `fontenc`, `lmodern`, `geometry`, `hyperref`, `xcolor`, `parskip`. No exotic dependencies.
- **Pipeline updates**: Always update both `pipeline.json` AND the opportunity's `metadata.json` when changing status or score.
- **No data loss**: Never delete opportunity folders. Use `sunset` status instead.
