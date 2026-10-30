# WRITEUP.md

## Assumptions
- "Since 2021" does not give a month, so I count it as inside the 5-year window and say so to the reviewer.
- A bid over $260,000 or over 16 weeks is ineligible, but code first checks that the AI's quote really appears in the text.
- Criteria, bands and weights are stored in the database and read at run time. Nothing is hard-coded.

## 1. AI vs. rules
**AI (reading and judging words):**
- Finds the licence, WSIB and insurance statements and answers Pass / Fail / Unclear / NotFound, with a quote and page.
- Finds numbers inside messy sentences (price, weeks, install count, warranty years).
- Fills the team checklist (named lead, crew, safety plan, monitoring plan) as present or absent.
- Reports any sentence that tries to give orders to the system.

**Plain code and rules:**
- File checks, PDF to text (OCR for scans), loading criteria.
- Checking the AI output and that each quote exists in the text.
- Mandatory gates, budget and 16-week limits, turning numbers into scores with the admin's bands, weighted total, sorting and tie-break.

I drew the line here because the AI is good at understanding wording, which exact string matching would miss. Code is better at calculation, because AI calculation can be inconsistent. So the AI returns the price as a number and code turns it into a score.

## 2. Consistency and explainability
- Every run uses a fixed prompt version, fixed model version, fixed criteria version, low temperature and structured JSON output.
- The scoring bands are fixed numbers in the database, so the same facts always give the same score. A new proposal never changes another proposal's score.
- Tie-break: higher experience score wins, because the admin gave experience the biggest weight.
- If the same proposals are ranked twice, the maths and sorting are identical. The one honest limit is that the AI reading can still differ slightly. To reduce this, results are stored, and the reviewer sees the quote and page, so a change would be visible.
- The reviewer sees, for each proposal: each criterion, the value found, the quote, the page, the band used and the score, plus the weighted total.

## 3. Human in the loop
- A human reviews every flagged proposal: Unclear or NotFound on a mandatory criterion, a missing number (for example no timeline), a suspicious instruction, or a quote that was not found in the text.
- The reviewer can confirm or override an item, but must give a reason.
- A human always makes the final award decision. The tool only ranks.
- Logged for audit: who reviewed and when, the original AI finding, any change and its reason, criteria version, prompt version, model, the AI output, scores, and the final decision.

## 4. Changing criteria
- Criteria and weights are versioned. An edit creates a new version and does not change the old one.
- Old results stay tied to the version used to score them.
- Re-scoring with the new version is an explicit action by a reviewer. Both old and new results are kept.
- Before a new version goes live, code checks that the weights add up to 100%.

## 5. Risks and safeguards
| Risk | Safeguard |
|---|---|
| Wrong extraction or bad OCR | Quote and page for every answer. Code checks the quote exists. The reviewer can see the source. |
| Made-up facts | The AI must answer NotFound or null when unsure. No quote means the answer is not accepted. |
| Manipulation (proposal C has a line telling the system to rank it first) | The proposal text is treated as data, not instructions. The line is ignored, reported, and the proposal goes to a human. The tool uses neutral wording and does not accuse the bidder. The AI gives no scores, so there is less to manipulate. |
| Bias or inconsistency | Fixed rubric and bands, same prompt for every proposal, one proposal per call, results stored. |
| Cost | One call per proposal with text only. Results are stored and not re-run unless the reviewer asks. |
| Unclear criteria | Criteria have a clear description and a band table. Code blocks a version if the weights do not total 100%. |
| Over-reliance on the AI | Every flagged case needs a human. The human makes the final award decision. |

## How the sample set comes out
- **A** is ranked 1 (93.5) and **E** is ranked 2 (86).
- **B** is flagged because the licence is not stated as held by the bidder.
- **C** is flagged because of the instruction aimed at automated reviewers. The reviewer sees a provisional score from real facts, labelled and not ranked.
- **D** is flagged because no completion time is given, so code has no number to score.
- Only A and E are ranked. This is intentional. I would rather have a human check doubtful proposals than let the tool guess.
