# Assignment Tracker — Plan

## Overview

Build a self-contained `assignment-tracker.html` file that renders a full Assignment Tracker dashboard. The page uses plain HTML, CSS, and JavaScript — no frameworks, no build tools, no external dependencies — consistent with the existing `index.html` in this workspace.

The page features three summary cards, a list of assignments with full CRUD (create, edit, delete), and a per-assignment student submission checklist revealed when an assignment is selected.

---

## Design Decisions

| Decision | Choice | Reason |
|---|---|---|
| Stack | Plain HTML / CSS / JS | Consistent with existing index.html |
| Data | Hardcoded mock data in JS | No backend needed |
| Layout | CSS Flexbox | Consistent with existing project patterns |
| Interactivity | Inline script | Single-file, no build tools |
| Checklist | Per-assignment, revealed on click | User confirmed this UX pattern |
| Responsive | flex-wrap on cards | Cards stack gracefully on mobile |
| Color palette | Reuse existing palette from index.html | Visual consistency across pages |

---

## File Structure

```
assignment-tracker.html   ← single self-contained file (HTML + CSS + JS)
```

---

## Sub-Tasks

---

### Sub-Task 1 — Page Shell and Mock Data

**Intent**
Establish the HTML document structure, base CSS, and the JavaScript mock data layer that all other sub-tasks will read from. Defining data first ensures every later sub-task has a stable, realistic source to render.

**Expected Outcomes**
- A valid HTML5 file `assignment-tracker.html` exists at the workspace root.
- The browser tab title reads "Assignment Tracker".
- A visible `<h1>` heading reads "Assignment Tracker".
- The body uses the same font, background (`#f5f6fa`), and centred max-width container (`960px`) as `index.html`.
- A `<script>` block declares a `assignments` array of mock data. Each assignment object contains:
  - `id` — unique number
  - `title` — assignment name (string)
  - `subject` — subject/course label (string)
  - `dueDate` — ISO date string (string)
  - `students` — array of 5 objects, each with `name` (string) and `submitted` (boolean)

**Todo List**
- [ ] Create `assignment-tracker.html` with a valid HTML5 boilerplate.
- [ ] Set `<title>Assignment Tracker</title>` in the `<head>`.
- [ ] Add a `<style>` block with base reset, font, background, and container styles — matching the existing `index.html` patterns.
- [ ] Add `<h1>Assignment Tracker</h1>` in the `<body>`.
- [ ] Add a `<script>` block declaring a `let assignments = [...]` array with at least 4 mock assignment objects, each with 5 student entries.

**Relevant Context**
- Reuse the CSS patterns from [`index.html`](index.html) (lines 7–30): system font stack, `#f5f6fa` background, `.container` with `max-width: 960px`.
- Greenfield file — do not modify `index.html`.

**Status** — `[x] done`

---

### Sub-Task 2 — Summary Cards Row

**Intent**
Render three summary cards that give an at-a-glance view of the assignment workload. Cards must read from the mock `assignments` array and update dynamically as data changes (via CRUD operations in later sub-tasks).

**Expected Outcomes**
- Three cards appear side-by-side (stacking on narrow screens) below the heading.
- **Card 1 — Total Assignments**: shows the total count of assignments (e.g. `4 Assignments`), with a sub-label `Active · Completed` breakdown.
- **Card 2 — Submission Rate**: shows the percentage of students who have submitted across all assignments (e.g. `60% Submitted`), with a sub-label showing raw numbers (e.g. `12 of 20 students`).
- **Card 3 — Due Soon**: shows the count of assignments with a due date within the next 7 days (e.g. `2 Due Soon`), color-coded red if any are overdue.
- Each card has a white background, `border-radius`, subtle `box-shadow`, and a coloured top-border accent (blue, green, amber).
- A `renderCards()` JavaScript function computes values from `assignments` and writes them into the DOM.

**Todo List**
- [ ] Add a `.cards` flex container `<div>` below the `<h1>`.
- [ ] Add three `.card` `<div>` elements, each with a label `<p>`, primary value `<h2>`, and secondary detail `<p>` — all with stable `id` attributes for JS targeting.
- [ ] Add CSS for `.cards` (flex, gap, flex-wrap) and `.card` (white, padding, border-radius, box-shadow, coloured top-border).
- [ ] Write a `renderCards()` JS function that reads `assignments`, computes the three values, and injects them into the card elements.
- [ ] Call `renderCards()` on page load and after every CRUD operation (later sub-tasks will call it).

**Relevant Context**
- Reuse `.card`, `.cards` CSS patterns from [`index.html`](index.html) (lines 31–80) — same color accents (blue `#3b82f6`, amber `#f59e0b`, green `#22c55e`).
- Cards must be driven by computed JS values, not hardcoded HTML text.

**Status** — `[x] done`

---

### Sub-Task 3 — Assignment List with Delete and Select

**Intent**
Render a list of all assignments below the cards. Each row shows the assignment title, subject, and due date. Clicking a row selects it (reveals the checklist in Sub-Task 4). A delete button on each row removes the assignment from the data and re-renders.

**Expected Outcomes**
- A section heading **"Assignments"** appears below the cards.
- Each assignment is rendered as a row (card or list item) showing: title, subject, due date, and a "Delete" button.
- Clicking anywhere on a row (except the Delete button) sets it as the active assignment and highlights it visually.
- Clicking "Delete" removes that assignment from `assignments`, re-renders the list and cards, and clears the checklist panel if the deleted assignment was selected.
- A `renderAssignments()` function drives the list from the `assignments` array.

**Todo List**
- [ ] Add an `<section id="assignments-section">` with a heading below the cards div.
- [ ] Add a `<div id="assignment-list">` placeholder that `renderAssignments()` will populate.
- [ ] Write `renderAssignments()` to loop over `assignments` and inject a row `<div>` for each, with title, subject, due-date, and a Delete button.
- [ ] Add CSS for assignment rows: hover state, active/selected highlight (e.g. blue left-border), row layout (flexbox, space-between).
- [ ] Attach a click handler per row to set `selectedId` and call `renderChecklist()` (Sub-Task 4 will implement that function — use a placeholder call for now).
- [ ] Attach a click handler on the Delete button that splices the item from `assignments`, calls `renderAssignments()`, `renderCards()`, and clears the checklist panel if needed.

**Relevant Context**
- `renderCards()` from Sub-Task 2 must be called after any delete.
- `selectedId` is a module-level variable tracking the currently selected assignment.

**Status** — `[x] done`

---

### Sub-Task 4 — Per-Assignment Student Checklist

**Intent**
When an assignment is selected, display its student submission checklist in a panel. Each student row has a checkbox that toggles their `submitted` state. A live counter shows how many students have submitted.

**Expected Outcomes**
- A `<section id="checklist-section">` panel appears below the assignment list, hidden by default.
- When an assignment row is clicked, the panel becomes visible and shows:
  - The selected assignment's title as the panel heading.
  - A counter: e.g. `3 of 5 students submitted`.
  - Five student rows, each with a checkbox, student name, and a status label (`Submitted` / `Not submitted`).
- Toggling a checkbox updates the `submitted` boolean in the `assignments` data, recalculates the counter, updates the Submission Rate card, and visually marks the row (checked = strikethrough name, muted color).
- A `renderChecklist(id)` function drives this panel.

**Todo List**
- [ ] Add `<section id="checklist-section" hidden>` below the assignment list section.
- [ ] Add a `<h2 id="checklist-title">` and `<p id="checklist-counter">` inside it.
- [ ] Add a `<ul id="checklist-list">` placeholder inside it.
- [ ] Write `renderChecklist(id)` that finds the assignment by `id`, makes the section visible, sets the title and counter, and injects a `<li>` per student with a checkbox and labels.
- [ ] Attach a `change` handler to each checkbox that updates `assignments[...].students[...].submitted`, calls `renderChecklist(id)` and `renderCards()`.
- [ ] Add CSS for checklist rows: flex layout, checked state (strikethrough, muted), status label pill (green for submitted, grey for not).
- [ ] Update the `renderAssignments()` row click handler from Sub-Task 3 to call `renderChecklist(id)`.

**Relevant Context**
- `renderCards()` must be called after each checkbox toggle to keep the Submission Rate card accurate.
- The `students` array lives inside each assignment object in `assignments` — mutate it in place.

**Status** — `[x] done`

---

### Sub-Task 5 — Create and Edit Assignment Form

**Intent**
Provide a form to add new assignments and edit existing ones. The form collects title, subject, and due date. On submission it either adds a new assignment (with a fresh set of 5 students, all unsubmitted) or updates the selected one.

**Expected Outcomes**
- A **"New Assignment"** button appears in the Assignments section header.
- Clicking it reveals an inline form with fields: Title (text), Subject (text), Due Date (date), and a Submit / Cancel button.
- Submitting the form with valid data adds a new assignment to `assignments` (with 5 default students) and re-renders the list and cards.
- An **"Edit"** button on each assignment row pre-fills the form with that assignment's data; submitting updates it in place.
- Basic validation: Title and Due Date are required — show a simple inline error message if blank.
- After a successful submit the form is hidden and the list re-renders.

**Todo List**
- [ ] Add a `<div id="assignment-form" hidden>` below the section heading, containing: Title `<input>`, Subject `<input>`, Due Date `<input type="date">`, a Submit `<button>`, and a Cancel `<button>`.
- [ ] Add a "New Assignment" `<button id="new-btn">` in the section header.
- [ ] Add an `<p id="form-error" hidden>` element inside the form for inline validation messages.
- [ ] Write `openForm(assignment?)` — shows the form, and if an assignment is passed pre-fills the fields and stores its `id` in `editingId`.
- [ ] Write `submitForm()` — reads field values, validates, then either pushes a new object into `assignments` (with 5 blank students) or updates the existing one; then calls `renderAssignments()` and `renderCards()`, hides the form.
- [ ] Attach click handlers: New button calls `openForm()`, Edit button calls `openForm(assignment)`, Cancel button hides the form.
- [ ] Add CSS for the form panel: white background, padding, border-radius, field styles, error text in red.

**Relevant Context**
- New assignments must be given a unique `id` (e.g. `Date.now()`).
- Default students for a new assignment: 5 entries with placeholder names (`Student 1` … `Student 5`) and `submitted: false`.
- `renderCards()` must be called after add/edit to keep card counts accurate.

**Status** — `[x] done`

---

## Execution Order

```
Sub-Task 1 → Sub-Task 2 → Sub-Task 3 → Sub-Task 4 → Sub-Task 5
```

Each sub-task depends on the previous one being complete before implementation begins.
