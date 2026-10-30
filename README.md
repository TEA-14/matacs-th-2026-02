# AI Proposal Triage Design

**Candidate:** Tayyab Ejaz Ahmed  
**Assessment:** Associate Software Developer  
**Reference:** MATACS-TH-2026-02  
**Time spent:** Approximately 4 hours

## Overview

This repository contains my design for an AI-assisted proposal triage system for commercial solar installation proposals.

The design combines AI-based interpretation of proposal documents with rule-based validation, scoring, ranking, and human oversight.

## Deliverables

- [Process Flow Diagram](diagram/flow.png)
- [Design Write-up](WRITEUP.md)
- [AI Prompts](PROMPTS.md)
- [Expected Proposal Ranking](ranking.json)

## AI Tool Usage

I used AI tools to help explore the design, discuss possible approaches, review edge cases, and improve the clarity of my explanations.

One design decision I reconsidered was using AI only for information extraction. I decided that AI could also evaluate qualitative criteria against admin-defined rubrics, while ordinary code would handle mandatory checks, numerical scoring, and ranking. I reviewed the suggestions and made the final design decisions myself.

## Design Approach

The system uses a hybrid approach:

- **AI:** Interprets proposal content and evaluates information against supplied criteria.
- **Code:** Validates extracted information, applies mandatory rules, calculates weighted scores, and sorts proposals.
- **Human:** Reviews flagged cases and makes the final award decision.
