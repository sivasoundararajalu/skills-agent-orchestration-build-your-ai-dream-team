# Project Pulse Implementation Plan

## Project summary

Build Mona's **Project Pulse** as a lightweight static dashboard for
contributors. It will display multiple projects with each project's name,
owner, status, recent activity, priority/risk, and a short
contributor-friendly summary. The result must be a polished, accessible,
responsive card-based UI that can be previewed through VS Code.

## File assignments and responsibilities

| File | Owner | Purpose |
| --- | --- | --- |
| `app/index.html` | Coder | Accessible dashboard structure, Project Pulse title, data-loading/rendering logic, loading/error states, and visible `.project-card` markup. |
| `app/styles.css` | Designer | Visual system and responsive dashboard styling, including deterministic `.dashboard` and `.project-card` hooks. |
| `app/project-data.json` | Coder | Valid project data with a top-level `projects` array. |
| `.vscode/launch.json` | Coder | Strict JSON, comment-free VS Code configuration for running the dashboard. |
| `docs/project-pulse-plan.md` | Planner | This implementation plan. |

### Designer responsibilities

- Define clear information hierarchy for title, project name, owner, status,
  recent activity, priority, and summary.
- Create a polished responsive visual design in `app/styles.css`.
- Include deterministic CSS hooks: `.dashboard` and `.project-card`.
- Use visible cards, status badges, priority treatment, readable spacing,
  rounded corners, shadows, contrast, and typography.
- Support keyboard and small-screen use through focus styles, responsive
  layout, and legible color contrast.
- Provide CSS-only design direction without changing Coder-owned files.

### Coder responsibilities

- Implement semantic, accessible `app/index.html`.
- Load `app/project-data.json`, render one visible `.project-card` per
  project, and show status, `recentActivity`, and priority values.
- Implement explicit loading, empty-data, malformed-data, and fetch-failure
  messages.
- Create valid JSON data in `app/project-data.json`.
- Create and validate `.vscode/launch.json`.
- Test the page through the configured local server and confirm it opens
  `index.html`, not a directory listing.

## Ordered implementation steps

1. **Confirm the implementation contract**
   - Review this plan and the Project Pulse brief before changing application
     files.
   - Preserve the fixed deliverables: `app/index.html`, `app/styles.css`,
     `app/project-data.json`, and `.vscode/launch.json`.
   - Treat `.dashboard` and `.project-card` as required deterministic hooks.

2. **Establish the data contract**
   - **Owner:** Coder
   - Create `app/project-data.json` with a top-level object containing
     `projects`.
   - Add several representative projects. Every project must include:
     - `name`
     - `owner`
     - `status`
     - `recentActivity`
     - `priority`
   - Add a concise contributor-friendly `summary` field to fulfill the
     dashboard brief, while retaining all required fields.
   - Keep values deterministic and suitable for status and priority badge
     styling.

3. **Define the visual system**
   - **Owner:** Designer
   - Create `app/styles.css` with:
     - `.dashboard` as the primary layout hook.
     - `.project-card` as the project-card hook.
     - Responsive grid behavior that collapses cleanly to one column.
     - Card `border-radius` and `box-shadow`.
     - Distinct but accessible status and priority treatments.
     - Focus-visible styling and reduced-motion-safe transitions where
       animation is used.
   - Establish styles for page header, card metadata, badges, empty/error
     messages, and narrow displays.

4. **Implement the dashboard markup and rendering**
   - **Owner:** Coder
   - Create `app/index.html` with the exact visible title **Project Pulse**.
   - Link `styles.css`.
   - Include semantic landmarks, a page heading, an accessible status region
     for loading/errors, and a card container using `.dashboard`.
   - Reference and fetch `project-data.json` using browser-side JavaScript in
     the page.
   - Validate that `projects` is an array and required project fields are
     present before rendering.
   - Render all project fields, including `status`, `recentActivity`, and
     `priority`, into visible `.project-card` elements.
   - Escape or construct text safely with DOM APIs rather than interpolating
     untrusted data into HTML strings.

5. **Create the VS Code preview launch configuration**
   - **Owner:** Coder
   - Create `.vscode/launch.json` as strict JSON with no comments.
   - Define a configuration named exactly **Run Project Pulse Dashboard**.
   - Set `cwd` to `${workspaceFolder}/app`.
   - Run `python3 -m http.server 5500` through the launch configuration.
   - Configure `serverReadyAction` to open
     `http://localhost:%s/index.html`, with a capture pattern for the served
     port.
   - Ensure the configuration opens the frontend `index.html` rather than the
     server's directory root.

6. **Integrate and validate**
   - **Owners:** Coder validates implementation; Designer reviews
     visual/accessibility result; Orchestrator performs the final integration
     check.
   - Parse both JSON files.
   - Run the VS Code launch configuration and inspect the rendered dashboard.
   - Confirm the dashboard loads JSON over HTTP, renders all projects, and
     remains usable at desktop and narrow viewport widths.
   - Stop the preview server after validation.

## Dependencies

| Step | Depends on | Reason |
| --- | --- | --- |
| Data contract | Project brief and required schema | Rendering requires stable field names and data shape. |
| Visual system | Project brief and deterministic hook requirements | Styles need the expected dashboard/card semantics. |
| HTML rendering | Data contract; visual-hook agreement | Rendering must consume the final field names and emit `.dashboard`/`.project-card`. |
| Launch configuration | App directory definition | The configuration must serve `app/` and target `index.html`. |
| Integration validation | All implementation steps | Rendering, styling, JSON, and launch behavior must be verified together. |

## Parallel and sequential decisions

### Can run in parallel

- **Step 2, data contract (`app/project-data.json`)** and **Step 3, visual
  system (`app/styles.css`)** can run in parallel because they have separate
  file ownership.
- **Step 5, `.vscode/launch.json`** can run in parallel with Steps 2 and 3
  because it only depends on the agreed `app/` directory and has no shared
  file scope.

### Must run sequentially

- **Step 4, `app/index.html`** must follow the data-contract decision so it
  uses the exact JSON property names.
- CSS finalization must follow agreement on the required `.dashboard` and
  `.project-card` hooks; otherwise Coder and Designer could create
  incompatible selectors.
- Full visual, functional, and launch validation must follow completion of all
  app files and `.vscode/launch.json`.
- Any cross-file correction discovered during integration is sequential:
  identify the contract mismatch, update the owning file, then rerun the
  relevant validation.

## Edge cases and risks

- **JSON cannot be fetched from `file://`:** the dashboard must be tested
  through the configured HTTP server, not by opening the HTML file directly.
- **Malformed or missing JSON:** show a clear in-page error rather than
  leaving an empty dashboard or uncaught console error.
- **Unexpected data shape:** validate the top-level `projects` array and
  safely handle missing/empty project fields.
- **No projects:** render a contributor-friendly empty state.
- **Status/priority variations:** unknown values should receive a neutral
  visual treatment rather than relying on an exhaustive hard-coded set.
- **Color-only meaning:** badges must retain readable text labels; color
  cannot be the sole status or priority indicator.
- **Long names and summaries:** cards must wrap text without overflow at
  narrow widths.
- **Launch-server output variability:** the `serverReadyAction` pattern must
  capture the port emitted by Python's HTTP server so `%s` resolves correctly.
- **Directory listing risk:** the launch URL must end in `/index.html`;
  opening only the server root is not sufficient.
- **Configuration validity:** comments or trailing commas in
  `.vscode/launch.json` would violate the strict-JSON requirement.

## Validation expectations

1. `app/project-data.json` parses with
   `python3 -m json.tool app/project-data.json`.
2. `.vscode/launch.json` parses with
   `python3 -m json.tool .vscode/launch.json`.
3. `app/project-data.json` has a top-level `projects` array, and every
   project includes `name`, `owner`, `status`, `recentActivity`, and
   `priority`.
4. `app/index.html` visibly includes **Project Pulse**, links `styles.css`,
   and references `project-data.json`.
5. The running page displays multiple `.project-card` elements populated from
   the JSON data, including each project's status, recent activity, priority,
   owner, and summary.
6. `app/styles.css` contains `.dashboard`, `.project-card`, `border-radius`,
   and `box-shadow`, with a responsive layout.
7. Keyboard focus is visible, semantic headings/landmarks are present, text
   remains readable, and badges include textual labels.
8. **Run Project Pulse Dashboard** in `.vscode/launch.json` uses
   `${workspaceFolder}/app`, runs `python3 -m http.server 5500`, and opens
   `http://localhost:%s/index.html`.
9. The browser preview loads the dashboard rather than a directory listing,
   and the preview server is stopped after testing.

## Open questions

No additional product requirements are currently needed. Use representative
static project data and a concise `summary` field unless Mona supplies
specific projects, statuses, or priority vocabulary.
