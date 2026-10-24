# Project Pulse Dashboard Implementation Plan

## Summary

Build a small, dependency-free static dashboard that helps contributors see active projects, owners, status, recent activity, priority or risk, and a concise summary. Use semantic HTML, responsive CSS, and project records in JSON. The VS Code launch configuration must serve the `app/` directory and open `index.html`, rather than showing a directory listing.

The repository has no existing app framework or app-specific conventions to preserve. Follow the exercise’s explicit requirements and keep the implementation vanilla HTML/CSS/JSON.

## Roles and file ownership

- **Designer:** Define information hierarchy, responsive layout, visual treatment, and accessibility guidance. Owns `app/styles.css`. Agree on shared class names and component structure before implementation so styling matches the markup.
- **Coder:** Implement data loading/rendering and runnable-app configuration. Owns `app/index.html`, `app/project-data.json`, and `.vscode/launch.json`. Keep HTML hooks aligned with the Designer’s agreed CSS contract.
- **Orchestrator:** Assign the scopes, enforce the dependency/order below, integrate the work, and verify the result.
- **Planner:** Establishes this plan, file ownership, dependencies, edge cases, and validation criteria.

## Ordered implementation steps

### 1. Agree on the UI and data contract
**Participants:** Designer, Coder  
**Files:** No files required for this agreement.

Settle the project card structure and shared CSS hooks before parallel implementation. At minimum, agree on `.dashboard` and `.project-card`, plus hooks or semantic elements for status and priority. Confirm the data contract: a top-level `projects` array; each project has `name`, `owner`, `status`, `recentActivity`, and `priority`. Include a short contributor-friendly `summary` field to satisfy the brief’s summary requirement; treat it as an additive field.

**Dependency:** Must precede parallel file work.

### 2. Implement the dashboard presentation and data
**Designer — `app/styles.css`:**
- Style the agreed dashboard and card hooks with readable typography, spacing, contrast, rounded corners, and shadows.
- Make the layout responsive across narrow and wide viewports.
- Distinguish status and priority visually without relying on color alone.
- Include accessible focus treatment if any controls are introduced; respect reduced-motion preferences if animations are used.

**Coder — `app/index.html` and `app/project-data.json`:**
- `app/index.html`: Use the exact title **Project Pulse**; reference `styles.css` and `project-data.json`; provide semantic headings and an accessible dashboard container; render visible `.project-card` elements populated from project data.
- `app/project-data.json`: Supply several representative projects in the required top-level `projects` array. Give each project non-empty required values and a concise summary.
- Ensure cards show each project’s name, owner, status, recent activity, priority, and summary.

**Can run in parallel:** Designer styling and Coder HTML/data can proceed after Step 1 because file ownership does not overlap. The Orchestrator should verify their shared hooks and field names at integration.

### 3. Add the preview launch configuration
**Coder — `.vscode/launch.json`:**
- Use strict JSON with no comments.
- Add a configuration named **Run Project Pulse Dashboard**.
- Set `cwd` to `${workspaceFolder}/app` and serve with `python3 -m http.server 5500`.
- Configure `serverReadyAction` to open `http://localhost:%s/index.html`; ensure the readiness pattern captures the server port and the browser opens the app page, not the directory root.

**Dependency:** Can be authored in parallel with Step 2 after the launch requirements are fixed, but must be validated with the integrated app. VS Code launch property support depends on the selected debugger/terminal launch type; confirm the configuration schema in VS Code and smoke-test it.

### 4. Integrate and validate
**Participants:** Coder and Orchestrator; Designer advises on visual/accessibility fixes.  
**Files:** All four implementation files, with fixes assigned to their existing owners.

Check that the HTML hooks match the stylesheet and that JSON keys match the renderer. Run the validation checks below, then manually launch and inspect the dashboard. The exercise expects the learner to use Copilot CLI orchestration; avoid treating the work as one undifferentiated implementation task.

## Dependencies and sequencing

- **Sequential:** Agree on the card/data contract before implementation; integrate and verify after parallel work.
- **Parallel:** After that contract is agreed, Designer can work on `app/styles.css` while Coder works on `app/index.html` and `app/project-data.json`. Coder can also prepare `.vscode/launch.json` in parallel, subject to confirming its launch type and readiness pattern.
- **Sequential final check:** Test the launch configuration only after the app files exist; correct any mismatch between selectors, data fields, and rendered content before handoff.

## Edge cases and risks

- Missing, malformed, or empty `projects` data should not leave a broken or misleading dashboard; show a clear empty/error state where practical.
- Missing individual fields should not produce `undefined` in the UI. Keep sample values non-empty and use safe fallbacks if rendering permits.
- JSON must be fetched over HTTP from the same `app/` server; opening `index.html` directly as a local file may prevent data loading.
- A fixed port (`5500`) may already be in use. If the server fails to start, report the cause and stop the existing process or choose a coordinated free port while keeping the launch URL aligned.
- Python’s HTTP server is suitable for this exercise’s local preview, not production hosting.
- Avoid status/priority meaning conveyed by color alone; check text contrast and narrow-screen wrapping.
- `summary` is required by the brief’s contributor-friendly-summary goal but is not among the five explicitly enumerated data keys. Keep the five required fields intact and make `summary` additive.

## Validation expectations

1. Confirm all expected files exist:
   - `app/index.html`
   - `app/styles.css`
   - `app/project-data.json`
   - `.vscode/launch.json`
2. Parse both JSON files:
   - `python3 -m json.tool app/project-data.json`
   - `python3 -m json.tool .vscode/launch.json`
3. Review the exercise’s required content:
   - HTML contains **Project Pulse**, references both `styles.css` and `project-data.json`, and renders `.project-card` entries showing status, `recentActivity`, and priority.
   - CSS contains `.dashboard`, `.project-card`, `border-radius`, and `box-shadow`, with responsive styling.
   - JSON contains a top-level `projects` array; each record has `name`, `owner`, `status`, `recentActivity`, and `priority`.
   - Launch JSON contains **Run Project Pulse Dashboard**, serves from `app/`, and targets `index.html`.
4. In VS Code, start **Run Project Pulse Dashboard**. Confirm the browser opens the dashboard at `http://localhost:<port>/index.html`, project cards and data load, and the layout remains readable at a narrow viewport. Stop the preview server afterward.
5. Treat the exercise workflow checks as useful structural checks, not proof of runtime behavior; manual launch verification is still required.

## Open questions

- What project names, owners, statuses, activity, and priorities should the sample data represent? The brief provides no sample records.
- Should the optional summary be displayed as a separate card field, or is a concise summary of recent activity sufficient?
- Which VS Code launch type is available and supported in the target Codespace for launching the Python server and handling `serverReadyAction`? Validate this in the workspace before finalizing the configuration.
