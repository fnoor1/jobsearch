# Job Search Management System

AI-assisted job search pipeline with LaTeX resume generation, opportunity scoring, and application tracking.

This repository is a structured workflow for managing a serious technical job search. It is designed around repeatable execution: saving job descriptions, scoring role fit, tailoring resumes, tracking applications, and keeping each opportunity organized in a consistent folder structure.

## Why This Exists

Modern technical job searches are noisy. A single generic resume is weak, and manually tracking dozens of applications becomes messy fast. This project turns the job search into a controlled system:

- save each opportunity in a structured folder
- score role fit before applying
- tailor resumes using a controlled baseline
- generate ATS-friendly LaTeX resumes
- preserve application notes and follow-up details
- use AI assistance without letting the process become sloppy

## What This Demonstrates

- LaTeX resume workflow
- file-based process design
- structured job tracking
- AI-assisted workflow development
- technical documentation
- repeatable operating procedure design
- practical use of GitHub for career operations

## Repository Structure

```text
jobsearch/
├── CLAUDE.md              # operating rules for AI-assisted workflow
├── base-resume.md         # baseline resume source content
├── config.json            # job search configuration
├── pipeline.json          # pipeline metadata and process logic
├── opportunities/         # individual job opportunity folders
├── resume/                # resume outputs and LaTeX files
└── templates/             # reusable templates for applications
```

## Workflow

1. Capture the job description.
2. Create a folder for the opportunity.
3. Score the role against technical fit, location, compensation, and career value.
4. Tailor the resume from the baseline resume source.
5. Generate a clean LaTeX resume.
6. Track application status and follow-up actions.

## Technical Focus

This workflow is built for roles such as:

- Technical Support Engineer
- Cloud Support Engineer
- Application Support Engineer
- Production Support Engineer
- Linux Support Engineer
- Solutions Support Engineer

## Notes

This repository is not meant to be a traditional software engineering project. It is a practical systems-thinking project that shows organization, process design, documentation, LaTeX usage, GitHub usage, and AI-assisted workflow control.