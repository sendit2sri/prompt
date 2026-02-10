Here are copy-paste prompts you can use in Copilot Chat (VS Code) to generate the full project structure + starter files without you typing code manually.

⸻

Prompt 1 — Create full project structure + placeholder files

Create a new Python project folder named `ado-testcase-factory` in my workspace.

Generate the following directory tree and create empty placeholder files with the exact names:

ado-testcase-factory/
├─ README.md
├─ requirements.txt
├─ .gitignore
├─ docs/
│  ├─ 01-workflow.md
│  ├─ 02-input-pack-format.md
│  ├─ 03-review-process.md
│  ├─ 04-excel-import-guide.md
│  └─ 05-change-handling.md
├─ config/
│  ├─ roles.yaml
│  └─ squads/
│     ├─ squad-template.yaml
├─ templates/
│  ├─ ado_testcase_template.xlsx   (leave placeholder note; I will copy my real template later)
│  └─ normalized_requirement_template.md
├─ src/
│  ├─ main.py
│  ├─ normalize/
│  │  ├─ __init__.py
│  │  ├─ loaders.py
│  │  ├─ story_parser.py
│  │  └─ normalizer.py
│  ├─ export/
│  │  ├─ __init__.py
│  │  ├─ ado_excel_writer.py
│  │  └─ validators.py
│  └─ utils/
│     ├─ __init__.py
│     ├─ io.py
│     └─ hashing.py
└─ runs/
   └─ .gitkeep

Rules:
- Keep code minimal: only scaffolding and placeholders for now.
- Add basic content into README.md explaining what the tool does at a high level.
- Add a basic .gitignore for Python projects and /runs outputs.
- Do not implement full logic yet.


⸻

Prompt 2 — Fill config files with the defaults you told me (roles + squad template)

Populate these files with initial content:

1) config/roles.yaml
- Define roles: SIT, BUSINESS, TEAM
- For SIT: custom_test_type = "Integration Testing", default_state = "Design"
- For BUSINESS: custom_test_type = "Acceptance Testing", default_state = "Design"
- For TEAM: custom_test_type = "System Testing", default_state = "Design"
- allowed priorities: 1-5 for all roles

2) config/squads/squad-template.yaml
- Create a template with fields: name and area_path
- Add comments instructing users to copy this file and name it like hedgefund.yaml, liberty.yaml, etc.

Also create one example squad file `config/squads/hedgefund.yaml` with a dummy area_path string placeholder.


⸻

Prompt 3 — Add documentation templates (so team adoption is easy)

Write concise documentation into these markdown files:

docs/01-workflow.md:
- End-to-end flow: input_pack -> normalized -> generated -> excel -> review -> ADO upload
- Clarify: one user story folder per story (do not merge stories)

docs/02-input-pack-format.md:
- Define the required folder format runs/<run>/input_pack/US-xxxx/
- Allowed files: story.txt, notes.txt, rules.txt, mapping.xlsx, mapping.csv, reference csv
- Naming conventions and examples

docs/03-review-process.md:
- Explain: generated excel is draft; BA edits go into review folder; only review excel gets uploaded

docs/04-excel-import-guide.md:
- Explain ADO columns:
  ID blank always
  Work Item Type only on Title row = "Test Case"
  Title only on Title row
  State only on Title row = "Design"
  Test Step/Action/Expected only on step rows
  Priority only on Title row (1 is highest)
  Custom.TestType depends on role

docs/05-change-handling.md:
- Explain how to handle requirement changes and BA edits
- Recommend not regenerating unless story changes materially
- Mention keeping traceability and saving versions
Keep each doc short and practical.


⸻

Prompt 4 — Set up Python entrypoint skeleton (no full logic yet)

Implement minimal skeleton code:

- src/main.py:
  - Add argparse with options: --run_dir, --squad, --role
  - Print resolved paths and selected role/squad for now
  - Do not implement normalization/generation yet

- Add simple placeholder functions in:
  - src/normalize/normalizer.py
  - src/export/ado_excel_writer.py
  - src/export/validators.py

Goal: the CLI should run without errors and print "Scaffold OK".


⸻

After Copilot creates this…

You just copy your real ADO Excel template into:
templates/ado_testcase_template.xlsx

Then we’ll implement:
	•	batch runner for 5–12 stories
	•	JSON → ADO Excel export (with your exact row rules)
	•	run summary + traceability output

If you want, tell me your squad names (2–3 examples) and I’ll also give a Copilot prompt to create those squad YAMLs automatically.

-----

Cool — if you’ve already run the 4 Copilot prompts (structure + configs + docs + CLI scaffold), the next prompt should implement the first real working feature end-to-end:

✅ JSON → ADO Excel exporter (with your exact row rules: Work Item Type + State + Custom.TestType + Priority only on Title row; ID blank always; step rows only have step fields)

Copy-paste this into Copilot Chat:

Now implement the first working feature: export ADO Test Case Excel from a JSON input.

Context:
- Project structure already exists under `ado-testcase-factory/`.
- ADO template columns must be exactly:
  ID, Work Item Type, Title, Test Step, Step Action, Step Expected, Custom.TestType, Area Path, Assigned To, State, Priority

Rules (must match ADO import behavior):
1) ID must always be blank.
2) "Work Item Type" must be "Test Case" ONLY on the Title row of each test case. It must be blank for all step rows.
3) "State" must be "Design" ONLY on the Title row. Blank on step rows.
4) "Custom.TestType" must appear ONLY on the Title row:
   - SIT => "Integration Testing"
   - BUSINESS => "Acceptance Testing"
   - TEAM => "System Testing"
5) "Priority" values must be 1..5 (1 is highest) and must appear ONLY on Title row.
6) "Title" must appear ONLY on Title row. Blank on step rows.
7) Steps start on the NEXT row after the Title row.
   - "Test Step" must be 1..N on step rows
   - "Step Action" and "Step Expected" filled on step rows
8) Area Path and Assigned To should appear ONLY on Title row; blank on step rows.
9) If a test case has no steps, raise a validation error.

Implement files:
A) src/export/validators.py
- validate_priority(value): ensure 1..5
- validate_testcase_schema(testcase): title present, steps list non-empty, each step has action and expected
- validate_testcases_payload(payload): payload contains "test_cases" list

B) src/export/ado_excel_writer.py
- function: testcases_json_to_dataframe(payload, area_path, test_type, assigned_to="")
- function: write_ado_excel(df, out_path)
Use pandas + openpyxl. Output must have the exact columns in correct order.

C) src/main.py
- Add new args:
  --in_json (path to testcases json)
  --out_xlsx (path to output excel)
  --squad (name of squad yaml file without .yaml)
  --role (SIT/BUSINESS/TEAM)
- Load config:
  config/roles.yaml -> map role to Custom.TestType and default state ("Design")
  config/squads/<squad>.yaml -> area_path
- Read the input JSON and export excel using above rules.
- Print a success message with out_xlsx path.

Also:
- Update requirements.txt to include pandas and openpyxl.
- Keep code clean, typed where easy, and with clear error messages.

Finally, create a sample file `runs/sample/input_testcases.json` with 2 test cases and 3 steps each for quick testing.

After you run that prompt

----------------

Nice — exporter is working ✅
Next prompt should add the batch “run_dir” workflow for 5–12 user stories and generate the team-friendly documentation automatically:
	•	One Excel per story
	•	One combined Excel for the sprint/run
	•	RUN_SUMMARY.md
	•	TRACEABILITY.csv
	•	Review folders

Copy-paste this as your next Copilot prompt:

Next feature: Batch processing for 5–12 user stories using a run folder.

Goal:
Given a run directory structure:
runs/<run_name>/
  input_pack/
    US-12345/
      testcases.json          # produced by YODA (strict JSON)
    US-12346/
      testcases.json
  output/
    excel/
    combined/
    docs/
    review/

Implement batch export + auto documentation.

Requirements:
1) Add a new CLI mode in src/main.py:
   - --run_dir runs/<run_name>
   - --squad <squad>
   - --role <SIT|BUSINESS|TEAM>
   If --run_dir is passed, ignore --in_json/--out_xlsx and do batch mode.

2) Batch mode logic:
   - Find all story folders under <run_dir>/input_pack/ (e.g., US-*)
   - For each story folder:
     - Load <story_folder>/testcases.json (must exist; if missing, record error but continue)
     - Validate JSON using existing validators
     - Export ADO Excel to:
       <run_dir>/output/excel/<story_id>.ADO.xlsx
     - Collect traceability:
       story_id, test_case_title, priority, excel_file, role, squad
   - Create combined sprint excel:
     <run_dir>/output/combined/<run_name>.ALL.ADO.xlsx
     This file is a concatenation of all rows from all story excels, preserving row order.

3) Auto-generate documentation in <run_dir>/output/docs/:
   A) RUN_SUMMARY.md containing:
      - run name, date/time
      - role, resolved Custom.TestType, state=Design, squad, area_path
      - number of stories found, number processed, number failed, total testcases, total rows exported
      - list failures with reason (missing file, invalid schema, etc.)
      - output paths created
   B) TRACEABILITY.csv with columns:
      story_id, test_case_title, priority, excel_file, role, test_type, area_path

4) Review folder convention:
   - Ensure folder exists: <run_dir>/output/review/
   - Add a short note in RUN_SUMMARY.md: BA should copy reviewed Excel files into output/review before ADO upload.

5) Keep rules unchanged for Excel:
   - Work Item Type, State, Custom.TestType, Priority, Title only on Title row
   - Steps start next row
   - ID blank always

6) Implement helper module src/utils/run_helpers.py (create if needed) for:
   - resolving run_name from path
   - finding story folders
   - safe mkdir
   - timestamp helper

7) Add a sample run folder:
runs/sample_run/
  input_pack/US-10001/testcases.json
  input_pack/US-10002/testcases.json
so I can test batch mode quickly.

8) Output should print a concise summary at end:
   "Processed X/Y stories. Exported total Z rows. Docs generated at ..."

Do not add normalization/YODA automation yet. Only batch export + docs.

How you’ll test after this prompt

python src/main.py --run_dir runs/sample_run --squad hedgefund --role SIT

What you should see
	•	runs/sample_run/output/excel/US-10001.ADO.xlsx
	•	runs/sample_run/output/excel/US-10002.ADO.xlsx
	•	runs/sample_run/output/combined/sample_run.ALL.ADO.xlsx
	•	runs/sample_run/output/docs/RUN_SUMMARY.md
	•	runs/sample_run/output/docs/TRACEABILITY.csv

If Copilot generates combined Excel incorrectly (common issue: duplicates or column order), paste the issue and I’ll give the correction prompt.

Test it locally with:

python src/main.py --in_json runs/sample/input_testcases.json --out_xlsx runs/sample/output.xlsx --squad hedgefund --role SIT

If you paste the Copilot output errors (if any), I’ll fix the prompts or adjust the design quickly.

------------------

Next, to make this team-wide usable, you need two things:
	1.	“Input Pack Builder”: auto-create the folder skeleton for 5–12 stories (so people don’t manually create folders)
	2.	“Review Workflow Guardrails”: validate that reviewed files follow your ADO rules + create an upload-ready package

Here’s your next Copilot prompt (copy-paste):

Next feature: Run initializer + Review packaging.

Problem:
Team members will struggle with creating input_pack structure correctly and mixing draft vs reviewed excels.
Add tooling to create a run skeleton and to package reviewed excels for upload.

Implement:

A) New CLI subcommand mode in src/main.py: --init_run
Usage:
  python src/main.py --init_run --run_dir runs/sprint-24 --stories US-12345,US-12346,US-12347

Behavior:
1) Create folders if missing:
   <run_dir>/input_pack/<story_id>/
   <run_dir>/output/excel/
   <run_dir>/output/combined/
   <run_dir>/output/docs/
   <run_dir>/output/review/
   <run_dir>/output/upload_ready/
2) In each story folder, create placeholder files (if not exist):
   story.txt
   notes.txt
   mapping.xlsx (placeholder note only)
   testcases.json (placeholder JSON skeleton)
3) Generate <run_dir>/RUN_README.md explaining:
   - where to paste ADO story content (story.txt)
   - where to put YODA output (testcases.json)
   - how to run batch export
   - where BA should save reviewed excel (output/review)

B) New CLI mode: --package_review
Usage:
  python src/main.py --package_review --run_dir runs/sprint-24

Behavior:
1) Read all files in <run_dir>/output/review/*.xlsx
2) Validate each reviewed file:
   - Columns exactly match ADO template columns
   - Title row must have Work Item Type="Test Case", State="Design"
   - Step rows must have blank Work Item Type/Title/State/Priority/Custom.TestType
   - Priority on title row must be 1..5
   - ID column must be blank for all rows
   - Test Step values on step rows must be sequential integers starting at 1 for each test case block
3) Copy valid reviewed files into:
   <run_dir>/output/upload_ready/
   and create a manifest:
   <run_dir>/output/upload_ready/UPLOAD_MANIFEST.md containing:
   - list of files included
   - count of stories, testcases, total rows
   - role/test_type and squad/area_path used
4) If a reviewed file fails validation:
   - do NOT copy it
   - add it to a failures section in the manifest with the reason

Implementation detail:
- Create src/export/excel_review_validator.py to validate an existing Excel file.
- Reuse the same column list and row rules already implemented.
- Use openpyxl to read excel to preserve exact values and detect blanks.

C) Add clear console output:
- For init_run: print created folders and missing placeholders
- For package_review: "Packaged X files, Y failed. Upload-ready at ..."

Do NOT modify existing batch export behavior.
Keep code readable and with actionable error messages.

Why this is the right next step
	•	Makes it usable by everyone (no “how do I set up folders?” confusion)
	•	Prevents wrong Excel uploads
	•	Supports governance (manifest = audit + manager friendly)

After this, the next step would be: Normalization module (ADO paste + mapping excel/csv + notes → normalized_requirement.md), then YODA usage becomes even easier.

If you hit any validation false-positives (common with blank cells vs empty strings), paste one example row and I’ll give a correction prompt.

-------

Awesome — now your tool is team-ready (init run + batch export + review packaging) ✅
Next step is the missing “front half”:

✅ Normalize requirements from ADO story + mapping Excel/CSV + notes into ONE normalized requirement per story
…and generate YODA-ready prompt files so testers don’t craft prompts manually.

This will make adoption explode because it removes the hardest part: “how do I feed requirements?”

Here’s your next Copilot prompt (copy-paste):

Next feature: Requirement Normalizer + YODA prompt generator (per user story).

Context:
- We already have run_dir structure and init_run creates placeholder files in input_pack/<story_id>/:
  story.txt, notes.txt, mapping.xlsx (optional), mapping.csv (optional)
- Goal: Convert all these into a single normalized requirement document and create a YODA prompt file.

Implement in src/normalize/:
1) src/normalize/loaders.py
   - load_text(path) -> str
   - load_excel_mapping(path) -> list[dict]  (best effort: detect columns like source/target/mandatory/rule/remarks)
   - load_csv_mapping(path) -> list[dict]
   - functions must handle missing files gracefully (return empty list/string)

2) src/normalize/story_parser.py
   - parse_story_text(raw: str) -> dict containing:
     story_id (optional), title, description, acceptance_criteria[]
   - support common formats:
     - "Title:" line
     - "Description:" section
     - "Acceptance Criteria:" section with bullets/numbered lines
   - if acceptance criteria not found, keep empty list

3) src/normalize/normalizer.py
   - normalize_story_folder(story_dir: Path) -> outputs dict with:
     normalized_md (string),
     normalized_json (dict),
     warnings (list[str])
   - It should:
     - read story.txt (required; warn if missing/empty)
     - read notes.txt (optional)
     - read mapping.xlsx or mapping.csv if exists
     - build a canonical normalized markdown:
       ## USER STORY (id/title)
       ## DESCRIPTION
       ## ACCEPTANCE CRITERIA
       ## FIELD MAPPING (table)
       ## BUSINESS RULES / NOTES
       ## OPEN QUESTIONS (empty placeholder)
       ## SOURCE TRACE (which files used)
     - also produce normalized JSON with same sections

4) YODA prompt generator:
   - Create src/generate/yoda_prompt_builder.py (new folder src/generate/ if not exist)
   - build_yoda_prompt(normalized_md: str, role: str) -> str
   - Role-based instruction:
       SIT => Integration Testing focus: interface/files/jobs/negative checks
       BUSINESS => Acceptance Testing focus: user scenarios/outcomes/happy path
       TEAM => System Testing focus: internal validations + functional flows
   - Output instructions:
       - return JSON only
       - test_cases[] with title, priority(1-5), steps[{action, expected}]
       - open_questions[] if unclear

5) Wire into CLI (src/main.py):
   Add mode: --normalize_run
   Usage:
     python src/main.py --normalize_run --run_dir runs/sprint-24 --role SIT
   Behavior:
     - for each story folder in input_pack:
       - generate:
         <run_dir>/output/normalized/<story_id>.normalized.md
         <run_dir>/output/normalized/<story_id>.normalized.json
         <run_dir>/output/prompts/<story_id>.yoda.prompt.txt
       - include warnings in RUN_SUMMARY.md under a "Normalization warnings" section

6) Update project structure if needed:
   Ensure folders exist in output:
     output/normalized/
     output/prompts/

7) Provide a small sample:
   - Add runs/sample_run/input_pack/US-10001/story.txt with Title/Description/Acceptance Criteria
   - Add notes.txt and mapping.csv example
   - Ensure normalize_run works end-to-end.

Constraints:
- Keep parsing best-effort, not perfect.
- No external APIs, no OCR.
- Keep code clean, typed where practical, and include helpful warnings rather than crashing.

After Copilot finishes, you’ll run:

python src/main.py --normalize_run --run_dir runs/sample_run --role SIT

You should get:
	•	output/normalized/US-10001.normalized.md
	•	output/prompts/US-10001.yoda.prompt.txt

Then your testers simply:
	1.	Open prompt file
	2.	Paste into YODA
	3.	Save YODA JSON into input_pack/US-10001/testcases.json
	4.	Run batch export

⸻

If you want the next step after this: we’ll add “YODA response validator + auto-fix hints” (so if YODA returns wrong JSON, the tool tells the user exactly what to correct).
