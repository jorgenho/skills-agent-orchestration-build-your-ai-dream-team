# Project Pulse implementation plan

## Summary

Build a lightweight, static Project Pulse dashboard that helps contributors scan
which projects are active, who owns them, their status, recent activity, and
priority or risk. The first view should be a polished, responsive set of
readable project cards with an accessible information hierarchy. Follow
`.github/project-pulse-brief.md` and the design expectations in
`.github/agents/designer.agent.md`; the app has no existing framework or
dependency manifest to extend.

The page will load project records from `app/project-data.json` and render them
in `app/index.html`. Keep the implementation static and dependency-free; use
the HTML file for any small data-loading/rendering script rather than creating
additional app files. Configure VS Code to serve the `app/` directory and open
`index.html`, not a directory listing.

## Roles and file ownership

| Owner | Assigned files | Responsibility |
| --- | --- | --- |
| **Designer** | `app/styles.css` | Define and implement visual hierarchy, responsive layout, readable spacing and contrast, accessible focus/interaction affordances, status badges, and priority treatment. Use the agreed markup contract below; do not edit the HTML, data, or launch configuration. |
| **Coder** | `app/index.html`, `app/project-data.json`, `.vscode/launch.json` | Implement semantic page structure and data rendering, create representative project records, and provide the VS Code preview configuration. Do not edit the stylesheet; report any required selector changes to the Orchestrator and Designer. |
| **Orchestrator** | No implementation files | Establish the shared markup contract, assign the phases below, keep ownership non-overlapping, integrate the Designer/Coder handoffs, and verify the finished app against this plan. |

### Shared markup/data contract

Before implementation starts, the Orchestrator confirms this interface with
both specialists:

- The page title is **Project Pulse** and has a clear page heading.
- A `.dashboard` container holds zero or more `.project-card` elements. Agree
  any additional class names needed for headings, badges, activity, and
  priority before the Designer writes selectors.
- Each card visibly presents `name`, `owner`, `status`, `recentActivity`, and
  `priority`; include a short contributor-friendly `summary` when sample data
  supports it. The required JSON shape is a top-level `projects` array with
  those five required fields on every project.
- Status and priority values are displayed as text as well as color, so the
  meaning remains clear without color perception.
- The HTML references `styles.css` and `project-data.json`. Load JSON from the
  local static server and render project values as text, not executable markup.

## Ordered implementation steps

### 1. Agree on the interface and design direction

- **Orchestrator** aligns Designer and Coder on the shared contract above,
  including class names and the minimum project fields.
- **Designer** decides the visual hierarchy, card layout, typography, spacing,
  contrast, responsive breakpoints, and status/priority treatments.
- **Coder** confirms the JSON/rendering contract and static-server assumptions.
- **Files:** no files are edited in this step.
- **Dependency:** prerequisite for the parallel implementation in step 2.

### 2. Implement the dashboard in parallel

Start both assignments after step 1's contract is agreed. These work streams
have separate file ownership and can proceed in parallel.

1. **Designer — `app/styles.css`:** implement the polished responsive
   dashboard and card styling. Include `.dashboard` and `.project-card`
   selectors, clear status/priority treatments, rounded cards, subtle shadows,
   visible keyboard focus where relevant, and layouts that remain usable on
   narrow screens and at browser zoom.
2. **Coder — `app/project-data.json`:** add a top-level `projects` array with
   multiple representative records. Every record includes `name`, `owner`,
   `status`, `recentActivity`, and `priority`; provide a concise `summary` if
   consistent with the brief. Use consistent, human-readable values, and do
   not imply that sample entries are confirmed live project data.
3. **Coder — `app/index.html`:** create accessible semantic markup, reference
   `styles.css`, and load `project-data.json`. Render one visible
   `.project-card` per project and show its name, owner, status,
   `recentActivity`, priority, and available summary. Keep the rendering
   logic small and dependency-free. Show a useful loading state and a clear
   error or empty state instead of a blank page if data cannot be loaded or
   there are no projects.
4. **Coder — `.vscode/launch.json`:** add strict JSON with a **Run Project
   Pulse Dashboard** configuration. Serve from
   `${workspaceFolder}/app` using `python3 -m http.server 5500`; configure
   `serverReadyAction` to open `http://localhost:%s/index.html` when the
   server is ready.

- **Dependencies:** the agreed contract from step 1; the page/data interface
  is specified by the brief. The stylesheet and HTML can be authored in
  parallel because their selector contract is fixed. The Coder may create the
  HTML, JSON, and launch files in parallel with the Designer's stylesheet.
- **Handoff:** the Orchestrator checks that actual HTML class names match the
  CSS selectors and that the JSON fields match the rendered labels. Each
  specialist corrects only their assigned file(s).

### 3. Integrate and validate

- **Orchestrator** runs the checks below and reviews the complete preview.
- **Designer** addresses visual, responsive, or accessibility issues in
  `app/styles.css`.
- **Coder** addresses markup, data loading/rendering, or launch issues in
  `app/index.html`, `app/project-data.json`, or `.vscode/launch.json`.
- **Dependencies:** step 2 must be complete before integrated validation.
  Fixes can proceed in parallel only when they remain in their assigned files;
  any change to the shared markup contract must be communicated and checked
  across both HTML and CSS before re-validation.

## Parallel-versus-sequential decisions

- **Sequential:** agree the HTML/CSS and JSON/rendering contracts first. Do not
  let implementation invent conflicting selectors or field names.
- **Parallel:** after that agreement, Designer can implement `app/styles.css`
  while Coder implements `app/index.html`, `app/project-data.json`, and
  `.vscode/launch.json`. These assignments do not overlap.
- **Sequential:** integrated review and launch testing follow implementation.
  Any interface change discovered then requires coordination and a final
  cross-file check.

## Edge cases, risks, and assumptions

- Empty `projects` array: render an intentional empty state, not an empty
  dashboard or JavaScript error.
- Missing, malformed, or unavailable JSON: show a useful error message; do
  not leave an unexplained blank page. Treat optional `summary` as absent
  without breaking the card.
- Missing required field or an unfamiliar status/priority value: avoid
  rendering `undefined`; show a safe fallback label and retain readable text
  rather than relying on a color mapping.
- Long project names, owner names, activity text, and summaries: allow wrapping
  without horizontal overflow or overlapping badges.
- Small screens and zoom: preserve reading order and usable card spacing;
  do not rely on a multi-column layout at every width.
- Accessibility: use semantic headings and landmarks, sufficient text
  contrast, visible focus, and text labels in addition to status/priority
  colors.
- Static JSON fetches can fail when opening the HTML directly as a `file://`
  URL. Validate through the configured HTTP server; use the VS Code launch
  target that serves `app/` and opens `index.html`.
- Assumption: because no canonical project records are supplied, sample data
  is illustrative. Confirm the projects, owners, and allowed status/priority
  vocabulary with Mona before treating the sample content as production data.
- Risk/open question: verify the available VS Code Python/debug adapter can
  run the server launch configuration and recognize the server-ready output.
  If not, adjust the configuration without changing its required name,
  `app/` working directory, command, port, or `index.html` URL.

## Validation expectations

1. **Structure and content:** confirm all four assigned files exist; the
   document title is **Project Pulse**, references `styles.css` and
   `project-data.json`, and renders cards with `.project-card` from the
   top-level `projects` array. Confirm each card exposes `name`, `owner`,
   `status`, `recentActivity`, and `priority`.
2. **Data/config syntax:** run
   `python3 -m json.tool app/project-data.json` and
   `python3 -m json.tool .vscode/launch.json`; both must parse as strict JSON.
   Check for `.dashboard`, `.project-card`, `border-radius`, and `box-shadow`
   in `app/styles.css`.
3. **Behavior:** launch **Run Project Pulse Dashboard** in VS Code. Verify it
   serves the `app/` directory, opens
   `http://localhost:<server-port>/index.html`, and shows the dashboard rather
   than a directory listing. Confirm project values render, then verify the
   empty and failed-data-load states (for example by temporarily using an
   empty or unavailable data source and restoring the sample data afterward).
4. **Visual/accessibility review:** inspect a wide and narrow viewport and
   browser zoom; check wrapping, contrast, readable order, text labels,
   keyboard focus, and that status/priority meaning does not depend on color.
5. **Repository checks:** compare the files against the relevant checks in
   `.github/workflows/3-step.yml` (required names/fields, CSS hooks, and JSON
   parsing). Run the repository exercise validator only if its template
   expectations remain applicable to the learner's working tree.

## Open questions

- Which real project names, owners, current activity, and summaries should
  replace the clearly illustrative starter records?
- What status and priority/risk values should be treated as canonical, and
  should priority be shown as a label, risk level, or both?
- Is the VS Code Python/debug extension available in the Codespace for the
  proposed launch configuration, or is an alternative supported launch type
  needed?
