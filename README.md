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
