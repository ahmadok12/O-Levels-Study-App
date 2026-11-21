# Studymate O/A Level: design and build spec

A self-contained brief for building Cambridge O Level and A Level topic pages in the Studymate style. It is extracted from the finished reference prototypes: **A Level Physics 9702, Topic 18 Electric fields** (`studymate-a-level.html`) and **O Level Physics 5054, Topic 3 Waves** (`studymate-olevel-5054.html`). Paste this file as context in a new project, then ask for a topic.

## 1. Product in one paragraph

Studymate is a **mobile-only** (max width 460 px) study app for Cambridge O Level and A Level students, mainly in Pakistan. Each syllabus topic gets one page with seven sections: syllabus objectives, notes with animations (plus prerequisite refresher, concept map, comparison tables, flashcards and step-reveal worked examples), command words, practice questions with annotated sample answers and mark schemes, a virtual practical, real-life applications and progress with a predicted grade. Teaching language is **English** (the exam language) with an optional **Roman Urdu helper** under each note. All questions are **original, written in Cambridge style**. Never copy past paper questions (they are copyrighted).

## 2. Tech rules

* **One self-contained HTML file** per prototype: inline `<style>` and `<script>`, no framework, no build step. Images sit next to it in `images/<name>.webp` (or embedded as optimised base64 data URIs when distributed as a single-file preview).

* Only external resource: Google Fonts (Plus Jakarta Sans 400–800, STIX Two Text 500 regular and italic, plus optional Noto Nastaliq Urdu for the RTL Urdu line).

* No KaTeX or MathJax. Equations are HTML: `<i>` for variables, `<sub>/<sup>`, and a `fr(num, den)` helper for stacked fractions, set in the `--math` font.

* All drawing is **2D canvas**, including the "3D" sphere (hand-rolled projection). No three.js needed. Optional live audio demonstrations (e.g. sound loudness/pitch) use the browser's native **Web Audio API** (`AudioContext`, `OscillatorNode`, `GainNode`) started on explicit user tap and stopped on unmount.

* State persists in `localStorage` under one key per build (e.g. `studymate.alevel.v1`, use `studymate.olevel.<code>.v1` for O Level). Always wrap access in try/catch.

* Must work in light and dark (tokens below), with `prefers-reduced-motion` respected (animations freeze, transitions off).

* Safe areas: `env(safe-area-inset-*)` on main padding, top bar and tab bar.

## 3. Brand

**Name:** Studymate. **Wordmark** (HTML, not an image): "study" in ink + "mate" in a purple gradient + a small orange gradient dot, weight 800, letter-spacing −0.04em.

```
<span class="wm"><span>study</span><span class="wm-b">mate</span><span class="wm-dot"></span></span><span class="lvl">A Level</span>


```

```
.wm{display:inline-flex;align-items:baseline;font-weight:800;letter-spacing:-.04em;line-height:1;white-space:nowrap;font-size:24px}
.wm-b{background:linear-gradient(100deg,var(--purple2) 0%,var(--purple) 60%);-webkit-background-clip:text;background-clip:text;color:transparent}
.wm-dot{display:inline-block;width:.22em;height:.22em;border-radius:50%;background:linear-gradient(135deg,#FFC53D,#FF7A1F);margin-left:.06em}
.lvl{font-size:10.5px;font-weight:800;letter-spacing:.06em;text-transform:uppercase;padding:4px 8px;border-radius:999px;background:var(--ink);color:var(--bg);margin-left:8px;transform:translateY(-3px)}


```

**Visual taste (from the owner):** soft but clear colours, white cards on cool grey, bright gradient tiles. No heavy transparency or glass effects, no garish palettes, nothing confusing. When a real photo would help, generate photo-real 4:3 images (880×660) or write image-generation prompts for the owner instead of inventing placeholder graphics.

### Design tokens (copy as is)

```
:root{
  --bg:#F5F5F9; --card:#FFFFFF; --ink:#1D1E2C; --ink2:#56576B; --muted:#8E8FA3; --line:#ECECF3; --line2:#DCDBEA;
  --purple:#6F5EF5; --purple2:#8D7BFF; --orange:#FF7A1F; --orange2:#FFA24A; --yellow:#FFC53D;
  --green:#22B07D; --red:#F0525A; --pink:#FFE9EE; --lav:#EFEDFF; --peach:#FFEEDF; --mint:#E2F7EE; --butter:#FFF5D6;
  --stage:#FBFAFF; --stage2:#F2F0FC; --grid:#E7E5F3;
  --font:"Plus Jakarta Sans", system-ui, -apple-system, "Segoe UI", Roboto, sans-serif;
  --math:"STIX Two Text", "Cambria Math", "Times New Roman", serif;
  --r-lg:26px; --r-md:18px; --r-sm:12px;
}
/* dark: same block under @media (prefers-color-scheme: dark){ :root:not([data-theme="light"]){…} } and :root[data-theme="dark"]{…} */
  --bg:#13131B; --card:#1D1D29; --ink:#ECECF6; --ink2:#B5B5CA; --muted:#8C8CA3; --line:#2A2A3A; --line2:#383849;
  --purple:#9A8BFF; --purple2:#B4A8FF; --lav:#2A2747; --peach:#3A2A1E; --mint:#17352B; --butter:#36301A; --pink:#3A2027;
  --stage:#171722; --stage2:#1C1C2A; --grid:#2A2A3B; color-scheme:dark;


```

**Colour roles:** purple = primary, links, A2/topic pills, best-fit line, field lines, concept-map core. Orange = predicted grade, marks, worked-example headers, worst-fit line. Green = correct, confident, AS pills, tips, full-mark sample answers. Red/pink = wrong, warnings, positive plate, lost-mark sample answers. Yellow/butter = "getting there", Roman Urdu box. Pastel pairs for icon chips and pills: `lav/purple`, `peach/orange`, `butter/#C98A00`, `mint/green`, `pink/red`.

**Type scale:** body 15 px; page h1 30 px/800/−0.6px; section h2 19 px/800; note h3 18 px/800; card title `.ttl` 15 px/700; `.sub` 12.5 px muted; eyebrow 10.5 px/800 uppercase +0.08em purple; numbers use `font-variant-numeric:tabular-nums`.

**Shape:** cards radius 26 px with 1 px `--line` border and 16 px padding; inner boxes 12–14 px radius; pills and chips fully round; icon chips 42 px square radius 14 px; buttons radius 14 px.

## 4. Layout and navigation

* `.app` column max 460 px centred; `main` has 18 px side padding, 120 px bottom padding, 20 px gap between blocks.

* **Top bar** (sticky): on Home it shows the wordmark + level badge and a theme/profile icon button; on a topic it shows a back button, the centred topic title and an **"Urdu help"** toggle (`.urdubtn`, turns purple when on).

* **Bottom tab bar** (fixed, rounded top 24 px, 4 columns): Home · Subjects · Study Hub · Profile, with inline SVG icons. Active tab is purple. (In the prototype only Home is wired; the others mirror the main Studymate app.)

* **Toast** (`#toast`) for small confirmations, 2.2 s.

* **Horizontal scroll affordances**: Both the sticky section strip (`.strip`) and the Home stat tiles (`.tiles`) must support touch swiping, mouse-wheel vertical-to-horizontal scrolling, click-and-drag scrolling on desktop, and (for `.strip`) auto-hiding left/right chevron scroll buttons (`‹` / `›`) plus automatic centering of the active pill on section change.

* Router: `S.scr` is `'home'` or `'topic'`; `S.sec` is the active topic section. `go(scr, sec, anchorId)` re-renders and scrolls (anchor scroll offsets the sticky strip). `render()` rebuilds `main` with a template string, rebuilds the tab bar, then calls `Sims.mount()` and `drawHero()`.

### Home screen (top to bottom)

1. Title: "Cambridge / O Level Physics" (or "A Level Physics") + muted line "Syllabus 5054 · topic by topic, with past-paper style practice".

2. **Qualification switch** (`.seg`, pill segmented control): `O Level 5054` | `A Level 9702`. Active = ink background.

3. **Stat tiles** (horizontal scroller, 138×150 px gradient tiles): Predicted grade (orange), Topic mastery % (purple), MCQ score (yellow, dark text), Structured marks (green). Each tile taps through to the relevant section.

4. **Continue card**: icon chip + "Continue: <Topic>" + objectives count + chevron.

5. **Syllabus list cards**: one card per stage or syllabus section, header with a pill naming the papers. Each row: number square (purple when built), name, sub line ("Open this topic" / "Sample progress" / "Not started"), and a conic-gradient **progress ring**. Unbuilt topics are dimmed.

6. Footer note: prototype status and "Questions are original, written in the style of Cambridge papers."

### Topic page frame

* **Hero** card: purple gradient, ambient physics canvas at 50% opacity, code line (e.g. `5054 · Topic 3 · Sub-topics 3.1–3.4`), h1 topic name, white translucent pills (level, papers), mastery bar.

* **Section strip** (sticky under the top bar, scrolls sideways with `‹` / `›` buttons, wheel & drag): Objectives · Learn · Command words · Practice · Virtual lab · Real life · Progress. Active pill = ink.

* One section shows at a time. At the bottom, "← Previous" (ghost) and "Next →" (purple) buttons.

## 5. The seven topic sections

### 5.1 Objectives

Intro card ("Every objective from the syllabus for this topic…") with a count score pill and a 3-state legend. Then one card per sub-topic (pill `3.1` + name). Each objective row: code (purple, tabular), full syllabus wording (first letter capitalised), **link chips** to the notes, MCQ, structured question part or lab that tests it, and a **confidence box** that cycles 0 not yet → 1 getting there (butter, dash icon) → 2 confident (green, tick).

Use the **exact learning-objective wording from the current syllabus document**, numbered as the syllabus numbers them.

### 5.2 Learn (notes & understanding boosters)

Open the Learn section with three reusable topic-level cards before the sub-topic notes:

1. **Prerequisite refresher card** (`.prereq`, lavender): 3 short "Before you start" reminders of earlier equations, SI unit conversions, or conventions the topic depends on.

2. **Concept map card** (`.cmap`): A visual hub showing the core unifying principle at the top and tappable sub-topic nodes below that jump straight to each sub-topic note.

3. **Formula & definition flashcards** (`.fc-card`): A compact active-recall flip-card deck covering every *Define* objective and *Recall and use* equation in the topic, with Prev / Flip / Next controls.

Then render one `<article class="card note">` per sub-topic, in this order:

1. Pill with sub-topic id + "Objectives …".

2. h3 title, then 3–4 bullet points in English (bold the key terms, keep sentences short and exam-worded).

3. **Roman Urdu helper** (only when the toggle is on): butter box headed "Roman Urdu help", 2–3 simple sentences in Roman Urdu with English technical terms kept, plus one line in Urdu script (Nastaliq, RTL).

4. **Side-by-side comparison table** (optional `.cmp-wrap`): 2-column contrast table for commonly confused pairs (e.g. *Transverse vs. Longitudinal*, *Real vs. Virtual image*, *Audible vs. Ultrasound*).

5. **Equation rows** (`.eq`): formula in the math font on the left, a tag on the right such as "Recall and use · unit m/s", "Definition · no unit", "Data sheet value".

6. **Interactive animation(s)** card (`sim` or `sims: ['id1', 'id2']`, see §7).

7. **Step-reveal worked example** (`.we`, dashed border, orange header): question + interactive **"Reveal step 1 / Reveal next step / Show all / Hide steps"** controls so students attempt each line (formula → conversion → substitution → bold answer with unit) before seeing it.

8. **Tip box**: green "Examiner tip" / "Sketch tip", or pink "Common mistake" (`warn`).

### 5.3 Command words

Intro card, then one card per command word used in the topic (Define, State, Describe, Explain, Calculate, Determine, Sketch, Suggest…): ink tag with the word, Cambridge meaning, "In this topic:" example, green "How to score" tip, and a chip linking to a question part (or note/lab) that uses it. Take the meanings from the syllabus's command word table.

### 5.4 Practice

1. **Multiple choice** card: score pill, a row of numbered dots (green right, red wrong, outlined current), "Question n of N" pill and objective pill, stem, four A–D option buttons. After an answer: options lock, right one turns green, the chosen wrong one red, and an explanation box says why (including why the tempting distractor is wrong). Previous/Next buttons, Reset at the end. Aim for **10–12 MCQs** per topic, each tied to one objective, with distractors built from real common mistakes (unit not converted, angle from surface instead of normal, missing factor of 2, r vs r²).

2. **Annotated "Lost 1 mark vs. Full marks" Examiner Clinic card** (`.ann-box`): 2 short side-by-side/stacked sample student responses comparing a typical mark-losing answer (pink box with exact reason it dropped a mark) against a full-credit Cambridge answer (mint box). Place directly above the structured questions.

3. **Structured questions**: intro card explaining B1/C1/A1, then 3 questions (A, B, C). Each has: total marks pill, paper-style pill ("Paper 2 style" for O Level, "Paper 4 style" for A Level), sub-topic pill, title, stem, then parts (a)–(d/e). A part shows `[marks]`, the command-word tag, the question, a textarea for the student's answer, and a "Show mark scheme" button. The mark scheme lists marking points as tappable rows with a code badge (B1, C1, A1, M1); tapping ticks it green and adds to the score. A "Sketch" part can show a model sketch canvas inside the mark scheme.

### 5.5 Virtual lab (Paper 3/4 ATP or Paper 5 practical skills)

Header card (orange lab icon, eyebrow "Virtual practical · Paper 3/4 skills" or "Paper 5 skills", aim, fixed values, objective pills and skill pills "Readings · Graph · Uncertainties"). Then numbered step cards:

1. **Take readings**: an animated apparatus canvas with a slider for the independent variable, a **zoom window** on the scale to read, and a "Record reading" button. Readings have seeded random noise (repeatable per student) and each is taken twice.

2. **Results table**: columns with quantity/unit headers (`i / °`, `r₁ / °`, `r₂ / °`, `mean r`, `sin i`, `sin r`), editable cells, delete per row, "Fill sample readings" and "Start again". Note on uncertainty: half the range of repeats or half the smallest division, whichever is larger; means to one more decimal place.

3. **Plot the graph**: canvas plot with gridlines, axis labels with units, error bars, **best-fit line** (least squares, purple) and **worst acceptable line** (through the error-bar extremes of the first and last points, orange dashed).

4. **Analyse** (after ≥5 rows): gradient, worst gradient, uncertainty in gradient (absolute and %), intercept, the theory relation that turns the gradient into the target quantity, the result with absolute uncertainty, an **Evaluate** tip (is the intercept near zero, what is the largest uncertainty source), and "Mark practical complete".

### 5.6 Real life

Intro card, then **6 photo cards**: 4:3 image (`images/<slug>.webp` or inlined base64 JPEG, 880×660, lazy), title, objective pill, 2-sentence explanation of the physics, and a lavender "Exam link:" box with a short exam-style question.

### 5.7 Progress

* **Predicted grade** card: orange gradient grade square, "x% average across topics", note that it is an estimate, and a grade scale strip (A\* A B C D E) with the current one highlighted.

* **Topic breakdown**: four progress bars (objectives confident green, MCQ yellow, structured purple, lab orange) and the weights line.

* **Focus next**: up to three objectives not yet confident, each a button back to Objectives.

## 6. Data shapes (all plain JS constants)

```
const SECTIONS=[{id:3,name:'Waves',papers:'Papers 1, 2, 3/4',live:true,topics:[['3.1','General properties of waves',null], …]}];
const PREREQ=['HTML prerequisite bullet', …];
const FLASHCARDS=[{tag:'Define · 3.1.3',front:'Wavelength (λ)',back:'…'}];
const ANNOTATED=[{q:'question [3]',bad:'…',badWhy:'…',ok:'…',okWhy:'…'}];

const OBJ=[{sub:'3.1',name:'General properties of waves',items:[
  ['3.1.1','Know that waves transfer energy without transferring matter.', [['n','3.1'],['m',0]]],
]}];                                  // link types: n=notes id, m=MCQ index, s='Q.part', l=lab
const ALLOBJ=OBJ.flatMap(g=>g.items);

const NOTES=[{id:'3.2',title:'Light: reflection, refraction & lenses',sims:['refract','lens'],
  pts:['HTML bullet', …],
  cmp:{title:'Compare: Real vs. Virtual image',cols:['Feature','Real','Virtual'],rows:[['Light rays','…','…']]},
  eqs:[[`<i>n</i> = ${fr('sin <i>i</i>','sin <i>r</i>')}`,'Refractive index · no unit']],
  ur:'Roman Urdu paragraph', urdu:'اردو جملہ',
  we:{q:'question',s:['step 1','step 2'],a:'answer with unit'},
  tip:['Examiner tip','text']}];      // 3rd item optional: 'warn' = pink

const CW=[['Define','meaning','example in this topic','how to score','s','B.b']];
const MCQ=[{q:'stem',o:['A','B','C','D'],a:2,e:'explanation',obj:'3.2.2'}];
const SQ=[{id:'B',title:'Refraction and optical fibres',paper:'Paper 2 style',sub:'3.2',stem:'…',
  parts:[{k:'c',cw:'Sketch',q:'…',m:4,ms:[['B1','…']],sketch:true}]}];
const LIFE=[['images/optical-fibre.webp','Fibre-optic broadband','explanation','3.2.5','exam link question']];
const LAB={nTrue:1.50,dAngle:0.5,iMin:15,iMax:65};

// Saved state
S={scr:'home',sec:'objectives',urdu:false,theme:'auto',conf:{'3.1.1':2},mcq:{0:1},mcqAt:0,
   sq:{'Ba':{txt:'…',rev:true,ticks:{0:true}}},lab:{rows:[],done:false,seed:5054},
   fcAt:0,fcFlip:false,weStep:{'3.1':1}}


```

### Scoring

* `objStats().score` = sum of confidence (0/1/2) ÷ (2 × count).

* `mcqStats()` = correct ÷ total; `sqStats()` = ticked points ÷ total marks (only revealed parts count as attempted).

* **Topic mastery** = 35% structured + 25% objectives + 25% MCQ + 15% lab (lab = 1 when complete, else up to 0.5 for six readings).

* **Predicted grade** = mean of all topic masteries mapped to bands. A\* 80, A 70, B 60, C 50, D 40, E 30, else U. These are illustrative thresholds, labelled as an estimate.

## 7. How animations are built

A tiny engine, `Sims`, mounts every `<div data-sim="id">` after each render. Notes may attach one sim (`sim:'wave'`) or multiple sims (`sims:['refract','lens']`). Each sim is a definition object in `SIMS`:

```
SIMS.lens={
  title:'Thin lens ray-diagram sandbox', icon:'ray', eyebrow:'Interactive animation',
  sub:'One-line instruction',
  aspect:.62, maxH:260, anim:0,                         // anim:1 = redraw every frame, else only when dirty
  state:{type:'conv',u:2.4},                            // copied into SST[id], survives re-renders
  ui:()=>CH('type',[['conv','Converging'],['div','Diverging']])+SL('u','Object distance u',0.5,3.2,0.1),
  fmt:{u:v=>Number(v).toFixed(1)+' f'},
  change(s,key){}, act(s,name){},                       // hooks (act = buttons with data-act, change = slider/chip updates)
  frame(s,dt){ const c=s.c, K=col(); /* draw in CSS px: s.W × s.H, time s.t */ },
  read(s){ return cells([['Image nature','Real, inverted'], …]); }
};


```

* `simCard(id)` renders the header (icon chip, eyebrow, title), sub text, the `.sim-stage` canvas, the UI controls and the readout grid.

* Helpers: `SL(key,label,min,max,step)` range slider, `CH(key,[[value,label]])` chip group, `cells([[label,value]])`, `arrow(c,x1,y1,x2,y2,color)`, `txt(c,text,x,y,{s,w,col,al})`, `sci(v)` for "× 10ⁿ" formatting, `col()` reads CSS tokens so canvases follow light and dark mode.

* **Audio sims**: When a topic involves sound/frequency (e.g. O Level 5054 `3.4 Sound`), wrap Web Audio in a lightweight controller (`SoundAudio`) triggered only by an explicit Play/Mute button (`data-act="audio"`), updated via `change(s)`, and always stopped in `Sims.unmount()` so audio never leaks across screens.

* The engine handles DPR scaling (max 2.5), ResizeObserver, reduced motion (time stops), and safe cleanup on re-render.

* Rules: show the physics truthfully (exaggerate scale only, and say so), label axes with units, keep 2–4 readouts that match the notes' equations, one clear instruction line, and draw with tokens, not hard-coded greys.

## 8. Copy and content rules

* Plain, exam-accurate English. Short sentences. Units with proper symbols and superscripts (`m/s`, `× 10⁸`, `V m⁻¹`).

* Every MCQ, structured part, command word, flashcard and real-life card links back to an objective code.

* Questions are original and say so ("Paper 2 style" / "Paper 4 style"). Never reproduce Cambridge past paper text, diagrams or mark schemes.

* Be honest about the paper: label MCQs "Paper 1 style" when the topic is examined in Paper 1, or "quick checks in the Paper 1 format" for A2-only topics.

* Roman Urdu: friendly and simple, keep English technical terms (field, wavelength, critical angle, gradient), bold the key idea, then one Urdu-script summary line.

* Mark codes: B1 independent mark, M1/C1 method mark, A1 accuracy mark (dependent on method).

## 9. Adapting to O Level (5054, 5070, 5090, 4024)

|  | Physics 5054 | Chemistry 5070 | Biology 5090 | Maths 4024 | 
| ----- | ----- | ----- | ----- | ----- | 
| Home title | Cambridge O Level Physics | Cambridge O Level Chemistry | Cambridge O Level Biology | Cambridge O Level Mathematics | 
| Papers (check current syllabus) | 1 MCQ, 2 Theory, 3 Practical, 4 Alternative to Practical | same pattern | same pattern | 1 and 2, both written (calculator rules differ by paper) | 
| MCQ section | Real Paper 1 format, label it so | same | same | Replace with short "Paper 1 style" quick questions | 
| Structured section | "Paper 2 style" | "Paper 2 style" | "Paper 2 style" | "Paper 2 style" with M1/A1/B1 | 
| Virtual lab | Paper 3/4 skills: readings, table, graph, gradient | titration, rates, salts, gas tests, chromatography | food tests, osmosis, enzymes, microscopy drawing | Replace with **"Explore"**: interactive construction or graph (e.g. drag points, transformations) | 
| Equations | data in the notes | equations and ionic equations | key-word definitions | worked methods | 

* **Home switch**: the segmented control lists the O Level subject and the A Level subject; the badge after the wordmark reads "O Level". Unbuilt subjects show a toast.

* **Syllabus list**: O Level has no AS/A2 split. Group topics by the syllabus's own sections (e.g. 5054: Motion, forces and energy · Thermal physics · Waves · Electricity and magnetism · Nuclear physics · Space physics), with one card per group and a pill naming the papers.

* **Objective codes**: use the syllabus numbering exactly (e.g. 5054 `3.2.2` or `4.2.1`).

* **Hero code line**: `5054 · Topic 3 · Sub-topics 3.1–3.4`, pills such as "O Level", "Paper 1 MCQ", "Paper 2 theory", "Paper 4 ATP".

* **Grade bands**: O Level is graded A\*–E. Keep the band list in one `GRADES` constant and label the thresholds as illustrative.

* **Mastery weights** stay 35/25/25/15. For Maths (no lab), use 40 structured / 25 objectives / 25 quick questions / 10 Explore.

* **Storage key**: `studymate.olevel.<code>.v1`.

* **Command words**: take the list from that syllabus (O Level science uses Define, State, Describe, Explain, Calculate, Determine, Sketch, Suggest; Maths uses Work out, Show that, Calculate, Construct).

* Always check the latest syllabus PDF for the exam year before writing objectives or paper labels.

## 10. How to add a topic (checklist)

 1. Pick the syllabus code and topic; copy the objective list word for word with its numbers into `OBJ`, grouped by sub-topic.

 2. Add the topic/section to `SECTIONS` (or `SYL`) and mark it live.

 3. Write `PREREQ` (3 prerequisite bullets), the concept map nodes, and `FLASHCARDS` (6–10 active-recall cards for definitions and formulas).

 4. Write `NOTES`: one entry per sub-topic, 3–4 points, Roman Urdu + Urdu line, side-by-side comparison table (`cmp`) where two concepts are commonly confused, equations with tags, step-reveal worked example (`we`), and an examiner tip or common mistake.

 5. Build 2–5 sims in `SIMS` (plus Web Audio where sound applies) and attach them via `sim` or `sims` on notes. Reuse the engine and helpers; test at 390 px width, light and dark.

 6. Write `CW` for the command words the topic's questions use, each linked to a question part or lab.

 7. Write 10–12 original `MCQ`s, each with an objective and an explanation that names the trap.

 8. Write 2 `ANNOTATED` "Lost 1 mark vs. Full marks" sample answers and 3 original structured questions in `SQ` with mark schemes (B1/C1/A1 or M1/A1), totals around 8–10 marks each; use `sketch:true` plus a sketch sim where a diagram/graph is asked for.

 9. Design the virtual practical: fixed values in `LAB`, a true model function, seeded noise, the table columns, the straight-line form that makes the gradient useful, and the uncertainty combination. (Maths: an Explore activity instead.)

10. Write 6 `LIFE` cards with 4:3 photo-real images (880×660) or image-generation prompts.

11. Update the hero code line, pills, page title, topbar title and the home tiles' captions for the topic.

12. Link every objective to at least one note, question or lab, and check the links scroll to the right place.

13. Test: every section renders, horizontal strip scrolls via arrows/wheel/drag/touch, flashcards flip, worked-example steps reveal, MCQ and mark-scheme scoring add up, progress and predicted grade move, the lab fills and analyses, Urdu toggle works, no horizontal scroll at 390 px, dark mode readable, reduced motion respected.

## 11. Known gaps in the reference prototype

* Only Home is wired in the tab bar; Subjects, Study Hub and Profile show a toast.

* The alternate-level button on the qualification switch only shows a toast.

* Other topics use sample progress so the predicted grade has something to average.

* Mark-scheme scoring is self-marked by ticking points; there is no automatic marking of typed answers.

### One-page-per-topic rule (O Level)

* Group Home rows into the syllabus's chapter cards, but give **every individual topic** (`1.1`–`6.2`) its own dedicated page (`PAGES[code]`) and independent state (`S.p[code]`), with per-topic progress rings and sibling-topic quick navigation in the Concept Map.