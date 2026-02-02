Perfect. Then your best “no-admin, no-MCP” system is:

One-line note → Copilot converts to ADO-ready fields (Subtask/Generic Task) with AC auto-drafted → you paste into ADO → later you tweak AC if needed.

Below is a complete setup you can start using today.

⸻

1) Use this single Copilot prompt (handles Subtask + Generic Task)

Copy/paste into Copilot Chat (IntelliJ) and reuse:

You are my Azure DevOps work-item formatter for daily work logging.

Output ONLY in this exact structure. No extra commentary.

WORK ITEM 1
TYPE: <Subtask|Generic Task>
TITLE: <short action title>
PRIORITY: <1|2|3|4|5>
TASK CATEGORY: <BAU|Development|Documentation|In Spring Bug|Testing|Training>  (ONLY if TYPE=Subtask)
DESCRIPTION:
- <bullet>
- <bullet>
- <bullet>
ACCEPTANCE CRITERIA:  (ONLY if TYPE=Generic Task)
- <bullet>
- <bullet>
- <bullet>

Rules:
- If user says "meeting", "sync", "follow-up", "demo", "uat support" -> TYPE=Subtask unless they explicitly say deliverable-based task; default Subtask.
- If there is a deliverable/outcome (deck/doc/test pack/KT note/UAT signoff/closure) -> TYPE=Generic Task.
- Priority inference:
  1 = prod/uat blocker or same-day deadline
  2 = sprint-critical
  3 = normal planned work
  4 = minor / nice-to-have
  5 = FYI / tracking only
- Category inference:
  Testing = testcase prep/execution/defects/retest
  BAU = meetings/support/status/coordination
  Documentation = decks/notes/KT/email summaries
  Training = learning/cert prep
  Development = coding/scripts/automation
  In Spring Bug = bugfix work during sprint
- DESCRIPTION: 3–6 bullets, crisp, human, not AI.
- AC: auto-create based on context. Make it measurable and easy to edit later.

Now convert my raw notes into 1 or more work items.
Raw notes:
<<<PASTE HERE>>>


⸻

2) Use a super-fast “one-line” format for your raw notes

This is the trick that makes Copilot accurate every time.

Subtask note format

S | <what> | P? | <Category?> | <keywords/outcome>

Examples:
	•	S | Execute SIT cases for HF cutoff cycle | P2 | Testing | run 20 cases; update evidence; log defects
	•	S | BA sync on calendar logic | P3 | BAU | clarify debit value date; confirm grouping rule; capture actions
	•	S | UAT support for Liberty demo | P1 | BAU | support business; answer queries; note issues

Generic Task note format

T | <deliverable> | P? | <keywords> | AC: <optional>

Examples:
	•	T | Prepare Test Summary deck for business | P2 | key findings; defect status; deployment readiness
	•	T | Create testcase pack for Redemption order flow | P2 | 5 scenarios; cutoff edge; FT variations
	•	T | UAT closure report | P1 | outstanding defects; sign-off mail; retest proof

If you don’t know priority/category, skip it. Copilot will infer.

⸻

3) AC patterns that work well (and stay editable)

Tell Copilot to draft AC like this (it will automatically using the prompt), but here are examples so you know what “good AC” looks like:

Generic Task: Test case pack

Acceptance Criteria
	•	Test cases created for agreed scenarios (count + scope mentioned)
	•	Reviewed with BA/dev or peer and comments incorporated
	•	Uploaded/shared in agreed location and link posted to team

Generic Task: Test summary deck

Acceptance Criteria
	•	Deck includes scope, execution status, pass/fail %, and key risks
	•	Defect list included with severity and current status
	•	Walkthrough completed with business and actions captured

Generic Task: UAT closure

Acceptance Criteria
	•	All critical UAT defects triaged and retested (evidence attached)
	•	Business confirmation/sign-off recorded (mail or ADO comment)
	•	Release readiness communicated to stakeholders

This matches your need: draft now, edit later.

⸻

4) “Batch mode” (best for busy days)

At end of day, paste all your one-liners at once like:

S | BA sync on HF calendar | P3 | BAU | debit value date rule; capture actions
S | Execute SIT for Story 9076331 | P2 | Testing | run 15 cases; update sheet; log defects
T | Prepare test summary deck | P2 | defects + risks + readiness

Copilot outputs WORK ITEM 1…N blocks. Then you create them quickly in ADO.

⸻

5) Copy/paste mapping into ADO (fast)

For each output block:
	•	Title → TITLE
	•	Priority dropdown → PRIORITY
	•	Task Category dropdown (only Subtask) → TASK CATEGORY
	•	Description → paste DESCRIPTION bullets
	•	Acceptance Criteria (Generic Task) → paste AC bullets

⸻

6) Small “quality rules” to keep your ADO clean
	•	Title should start with a verb: Prepare / Execute / Attend / Support / Review / Update
	•	Description bullets should be “work + outcome”
	•	AC should always include at least one measurable thing (count, completion, review, shared link)

⸻
