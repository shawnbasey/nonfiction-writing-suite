# Structure Frameworks

Ten frameworks for nonfiction books. A book's harness is generated from a **combination** of them, not one.

## The three-layer combination

| Layer | Decides | Pick |
|---|---|---|
| **Structure** | the book-level arc: which chapters exist and in what order, plus parts | exactly one |
| **Chapter unit** | the H2 template every chapter repeats, with word-budget percentages | one (a structure may supply its own); a book may use two units for two chapter kinds |
| **Story/evidence layer** | a required element added to every chapter (a case, a scene) | zero or one |

- **Layer merge rule:** a layer adds one named H2 or a placement rule. An added H2 takes its percentage from the
  unit's largest H2, so each template still totals 100%. Write the merged template, not two templates.
- **Brain Dump → Cluster → Outline is an outlining method, not a structure.** It turns loose notes into a TOC; the
  book still needs a structure and a chapter unit, chosen before or right after clustering.
- Front matter (`template: front`) and back matter (`template: back`) are fixed for every book; see
  `harness-generation.md` §7.
- H2 names below are defaults. The author may rename them in the interview; once generated they are exact and
  never renamed casually (summaries, repurposing and the build key off them).

Format of each entry: role, best for / weak at, book arc, chapter template (`template id`, H2s with % of the
chapter budget), evidence the research library must supply, interview probes, good combinations.

## 1. Problem–Solution — structure (supplies a unit)
- **Best for:** books that fix a costly, recognizable problem. **Weak at:** topics with no single problem; it can
  read as a sales pitch if the problem is inflated.
- **Arc:** Part 1 the problem and its cost → Part 2 why it happens (causes, myths) → Part 3 the solution, one
  chapter per principle or component → Part 4 objections, limits, and a plan.
- **Template** (`problem-solution`): `## The Problem` 15 · `## Why It Happens` 20 · `## The Solution` 30 ·
  `## How to Apply It` 25 · `## Objections and Limits` 10.
- **Evidence:** data on the problem's size and cost, studies of causes, evidence the solution works, known
  counter-evidence.
- **Probes:** What does the problem cost the reader? What have they already tried? What would make them doubt you?
- **Combines with:** Lesson unit; Case Study layer.

## 2. System / Playbook — structure (supplies a unit)
- **Best for:** a repeatable method with named steps. **Weak at:** ideas that aren't sequential; rigid when the
  real method branches.
- **Arc:** why the system exists → overview of the whole system → one chapter per step or component → putting it
  together → maintaining it and adapting it.
- **Template** (`playbook-step`): `## What This Step Does` 10 · `## The Principle` 20 · `## How to Do It` 35 ·
  `## Worked Example` 20 · `## Common Mistakes` 10 · `## Checklist` 5.
- **Evidence:** sources for each principle, worked examples (the author's own or documented ones), failure data.
- **Probes:** Name the steps. Which step do people skip? What does "done" look like after each step?
- **Combines with:** Case Study layer; Foundational-to-Advanced ordering of the steps.

## 3. Foundational-to-Advanced — structure
- **Best for:** skills with prerequisites (technical, academic, craft). **Weak at:** argument-driven books;
  experienced readers skip the early chapters.
- **Arc:** foundations → core skills → applied skills → advanced and edge cases → mastery and next steps. Each
  chapter assumes only the chapters before it.
- **Template** (`skill-level`): `## What You Need First` 5 · `## The Core Idea` 30 · `## Worked Example` 30 ·
  `## Going Further` 20 · `## Check Your Understanding` 15.
- **Evidence:** authoritative definitions, standard references, correct worked examples.
- **Probes:** What must a reader know on page one? Where do learners usually stall? What is the final skill?
- **Combines with:** Lesson unit (use it instead of `skill-level` for course-style books).

## 4. Chronological — structure
- **Best for:** history, biography of an idea or company, how-we-got-here books. **Weak at:** how-to books;
  pacing sags in quiet periods.
- **Arc:** origin → periods or turning points in order → the present → what it means now. Parts = eras.
- **Template** (`period`): `## The Moment` 15 · `## The Setting` 20 · `## What Happened` 35 ·
  `## Why It Mattered` 20 · `## What Came Next` 10.
- **Evidence:** dated primary sources, secondary histories, verified dates, names and figures.
- **Probes:** Where does the story start and why there? Which 8-12 moments change everything? What is the present day?
- **Combines with:** Narrative layer; Three-Act grouping of the periods.

## 5. Three-Act — structure (or story layer)
- **Best for:** investigative and idea-driven books with tension and a payoff. **Weak at:** reference and how-to
  books; needs a real turning point.
- **Arc:** Act I (about 25% of chapters) the situation and the inciting question → Act II (50%) complications,
  rising stakes, a midpoint reversal → Act III (25%) the answer and its consequences.
- **Template** (`act-chapter`): `## The Scene` 20 · `## The Stakes` 15 · `## The Turn` 30 ·
  `## The Consequence` 25 · `## The Open Question` 10.
- **As a layer:** each chapter opens on a scene and ends on an open question that the next chapter picks up.
- **Evidence:** documented events, interviews the author conducted, sources that confirm each turn.
- **Probes:** What is the central question? What is the midpoint surprise? What does the reader know at the end?
- **Combines with:** Case Study portfolio; Chronological periods.

## 6. Transformation Journey — structure
- **Best for:** self-improvement, career, health and habit books. **Weak at:** books with no reader change; it
  invites toxic optimism if the stages are not concrete.
- **Arc:** the before-state → why change is hard → stages of the shift, one per chapter → setbacks → the
  after-state and how to keep it.
- **Template** (`stage`): `## Where You Are Now` 15 · `## What Has to Change` 20 · `## The Shift` 30 ·
  `## Practice` 25 · `## Signs of Progress` 10.
- **Evidence:** research on the change itself, documented stories (with permission), measures of progress.
- **Probes:** Describe the reader before and after. Which stage do people quit at? What is a sign it's working?
- **Combines with:** Lesson unit; Narrative layer (the author's own transformation).

## 7. Case Study / Thematic Portfolio — structure (or evidence layer)
- **Best for:** business, leadership, design and policy books built on examples. **Weak at:** teaching a method in
  order; cases repeat if the themes are too close.
- **Arc:** the question the cases answer → one chapter per case or theme, grouped into parts → the cross-case
  pattern → how to apply it.
- **Template** (`case`): `## The Case` 30 · `## What Happened and Why` 25 · `## The Pattern` 25 ·
  `## Counter-Case` 10 · `## Lessons You Can Use` 10.
- **As a layer:** adds `## Case` (20%, taken from the unit's largest H2) to every chapter.
- **Evidence:** documented cases with sources for every fact, counter-examples, outcome data.
- **Probes:** List candidate cases. Which ones have public sources? Which case breaks your pattern?
- **Combines with:** Problem–Solution; System/Playbook; Three-Act.

## 8. Narrative / Memoir Arc — story layer (or structure for memoir-led books)
- **Best for:** books where the author's experience is the evidence. **Weak at:** books with thin personal story;
  hard to keep the reader's takeaway in view.
- **Arc (as structure):** life before → the event → struggle → understanding → what it means for the reader.
- **Template** (`narrative`): `## The Scene` 30 · `## Reflection` 25 · `## The Idea` 25 ·
  `## What It Means for You` 20.
- **As a layer:** each chapter opens with a scene from the author's story, taken only from raw material
  (`context/raw-material/`). Never invent or embellish a scene; a missing one is `[AUTHOR: need scene for …]`.
- **Evidence:** the author's own records (journals, emails, dates); sources for any wider claims.
- **Probes:** Which moments must be in the book? Who appears and have they agreed? What is off-limits?
- **Combines with:** Transformation Journey; Chronological.

## 9. Lesson / Teaching Unit — chapter unit
- **Best for:** textbooks, courses, workbooks, field manuals. **Weak at:** argument and story books; repetitive
  without variety in examples.
- **Arc:** none of its own; pair it with a structure (usually Problem–Solution or Foundational-to-Advanced).
- **Template** (`lesson`): `## Learning Goals` 5 · `## Why It Matters` 10 · `## Explanation` 35 ·
  `## Worked Example` 20 · `## Practice` 20 · `## Self-Check` 10.
- **Two-unit variant:** a concept unit (`## Why It Matters` 15 · `## The Big Idea` 30 · `## How It Works` 30 ·
  `## Common Misconceptions` 15 · `## Quick Exercise` 10) and a hands-on unit (`## Why It Matters` 10 ·
  `## The Model` 20 · `## Step-by-Step Lab` 40 · `## Templates` 15 · `## Troubleshooting` 15).
- **Evidence:** correct definitions, worked examples checked against sources, exercise answers.
- **Probes:** What can the reader do after each chapter? How long should practice take? Concept vs hands-on chapters?
- **Combines with:** Problem–Solution; Foundational-to-Advanced; Transformation Journey.

## 10. Brain Dump → Cluster → Outline — outlining method
- **Best for:** authors with lots of loose notes and no order yet. **Weak at:** nothing as a method, but clusters
  only show what is in the notes; gaps need the author.
- **Steps (inside the book, `/outline-from-braindump`):** notes into `context/raw-material/` →
  `python -m booktools research ingest --notes` (embedded as uncitable notes) →
  `python -m booktools research themes --chapters 8..12` → `context/themes.md` with clusters and an
  **Unplaced ideas** list → the chief editor proposes chapter titles and order using the chosen structure → the
  author approves → `book-index.yaml` chapters and stubs are regenerated.
- **Template:** none; it uses the chosen chapter unit.
- **Evidence:** notes are never citable; clustering only organizes. Every factual claim still needs a source.
- **Probes:** Roughly how many notes? In what formats? Is any order already fixed (a first or last chapter)?
- **Combines with:** any structure.

## Proven combinations

| Book type | Structure | Unit | Layer |
|---|---|---|---|
| Field manual / textbook | Problem–Solution | Lesson (two-unit variant) | none |
| Business playbook | System/Playbook | own (`playbook-step`) | Case Study |
| Technical guide | Foundational-to-Advanced | Lesson | none |
| Popular history | Chronological | own (`period`) | Narrative |
| Self-help | Transformation Journey | Lesson | Narrative |
| Investigative / big idea | Three-Act | own (`act-chapter`) | Case Study |
