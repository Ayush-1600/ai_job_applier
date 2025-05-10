# Project Plan v2: AI Job Applier

*This file tracks the updated project plan, reflecting all changes and refinements made after the initial plan. The original plan remains in 'Project Plan.md'.*

## 1. Project Goal & MVP Definition
- Build an AI-powered job application bot (AI Job Applier) in Python.
- MVP: Takes a job link, user resume, and personal info, then fills and submits the job application form on a target job site.

## 2. Development Process & Tools
- Use git for version control, with commits after each major step.
- Taskmaster/MCP for task tracking (manual fallback if issues arise).
- Maintain detailed phased project plan and task tracker.

## 3. Project Structure & Setup
- Directory: `agentic_jobber`.
- Standard Python structure: `src/`, `data/`, `config/`, `design_plans/`.
- `.gitignore` excludes unnecessary files/folders.
- Git repo initialized and tested.

## 4. Data Models
- **User Profile:** Extensible data class matching YAML structure (personal info, education, experience, etc.).
- **Resume:** Hybrid model for strict/loose schema, recursive skills extraction.
- **Job Application Log:** Logs each application (company, role, URL, date, status, notes, etc.) in YAML.

## 5. Testing & Validation
- Test user profile and resume models with real YAML data.
- Enhance resume model for recursive skills extraction.
- Test job application log by creating/saving sample entries.

## 6. Task Tracking & Documentation
- Update task tracker for completed/pending tasks.
- Update design docs with rationale for hybrid resume and log models.

## 7. Integration Planning
- Workflow:
  1. User provides YAML files for profile/resume and a job link.
  2. Integration script loads data, prepares for browser automation, logs application.
  3. Browser automation (to be implemented) handles login, navigation, form-filling, submission.
  4. Application attempts logged with error handling and user feedback.
- Config file (`config.yaml`) for mode, logging, file paths; credentials in env vars.

## 8. Pending Work
- Scaffold integration script (placeholders for browser automation, error handling).
- Next: Implement browser automation (Playwright) for job application submission.
- Add error handling, logging, and optional CLI interface.

## 9. User Preferences & Decisions
- Batch job application is a future goal; MVP handles one job at a time.
- In dev mode, errors prompt for manual intervention; in prod, errors are logged and process continues.
- Modular approach, YAML-based data models, and current task tracking setup are preferred.

## 10. CLI Interface & Integration Testing
- Implemented a robust CLI using argparse, supporting positional and optional arguments (job_url, --profile, --resume, --log, --mode, --dry-run, --verbose, --batch, --output).
- CLI defaults to config.yaml values if arguments are not provided.
- Added dry run mode for safe testing without browser automation.
- Improved verbose output and summary reporting at major milestones and completion.
- Fixed schema mismatch in user_profile.yaml (all personal fields now under personal_information).
- Successfully tested end-to-end integration: CLI, YAML loading, logging, and dry run all work as expected.

---

*Update this file as the project plan evolves. The original plan is preserved in 'Project Plan.md'.* 