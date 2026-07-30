# Does the "graphify" tool actually help an AI understand code better?

This is a small, informal experiment I ran to check a claim: that turning a
codebase into a **map (a "knowledge graph")** first, before asking an AI
questions about it, makes the AI's answers better or faster than just
letting it read the raw files on its own.

Think of it like this: imagine you're dropped into a school you've never
been to and someone asks you "how do you get from the front gate to the
principal's office?"

- **Option A:** You wander the hallways yourself, peeking into classrooms,
  reading room numbers, until you figure out the path.
- **Option B:** Someone hands you a **map of the school** first, with all
  the hallways and rooms already marked, and you trace the route on it.

I compared Option A and Option B, but for computer code instead of a
school. This is one small test, not a scientific study — treat the numbers
below as a rough first look, not a final verdict.

## What I actually did

1. I picked a real, well-known piece of software called **Flask** (a
   popular tool programmers use to build websites). It's medium-sized —
   not tiny, not huge — about 9,500 lines of code across 25 files.

2. I made **two identical copies** of it:
   - `control_repo/` — the plain copy, no map, no help. The AI could only
     open files and search through them, like a person flipping through a
     textbook.
   - `graph_repo/` — the copy that has a **map already built** using a tool
     called `graphify`. That map lives in `graph_repo/graphify-out/` — a
     file (`graph.json`) that lists the important pieces of the code and
     how they connect, plus a plain-language summary (`GRAPH_REPORT.md`).

3. I wrote **10 questions** about the code, ranging from easy ("what does
   this one function do?") to harder ("trace everything that happens
   between a webpage request coming in and the code that handles it"). I
   split them into three rough difficulty groups:
   - **Easy / single-file** questions (4 of them)
   - **Medium / multi-file "trace the path"** questions (4 of them)
   - **Harder / "explain the whole structure"** questions (2 of them)

4. For every question, I asked a **fresh AI assistant** — one with no
   memory of the other questions or attempts — to answer it twice using the
   plain copy (`control_repo`), and twice using the mapped copy
   (`graph_repo`). That's 40 attempts total (10 questions × 2 conditions ×
   2 repeats), so it's not resting on a single lucky run — but it's still a
   small number of repeats.

5. I then checked every answer by hand against the real code, to see
   whether it was actually correct and not just confident-sounding.

## What I found (with the caveat that this is a small test)

- **Both ways got the right answers, in this test.** All 40 answers were
  factually correct when I checked them. I didn't see the map make the AI
  noticeably more accurate here — it read the code carefully either way.
  That could be specific to Flask being a fairly clean, well-organized
  codebase; a messier one might behave differently.

- **The map seemed to reduce effort, mainly on harder questions.** I
  measured "thinking effort" using something called tokens — roughly, how
  much text the AI had to read and generate to answer. On this small
  sample:
  - Easy, single-file questions: no real difference either way.
  - Medium, multi-file trace questions: the map version used, on average,
    around 23% less effort.
  - Harder, whole-codebase questions: the map version used, on average,
    around 54% less effort — but this is based on only 2 questions in that
    category, so it's the least solid number here.

- **Building the map itself was cheap in this case.** It took a small,
  one-time amount of effort (roughly the size of one extra question), and
  could then be reused for later questions. Flask's map was cheap partly
  because most of it came from reading code structure directly, which
  doesn't need much AI effort — a codebase with a lot of plain-text
  documentation might cost more to map.

- **I couldn't draw a clear conclusion about real-world speed.** The time
  each attempt took bounced around a lot, seemingly for reasons unrelated
  to whether the map was used. I'm not claiming the map makes things
  faster in a stopwatch sense — only that it seemed to use less effort on
  the harder questions.

## Limitations — please read before trusting this too much

- Only one codebase was tested (Flask). This is not enough to say "graphify
  works" in general — it's one data point.
- Each question was only run twice per method. That's enough to notice a
  pattern, but far too few repeats to call this statistically solid.
- I graded the answers myself, using the same AI system that ran the test,
  by comparing them to the real source code. It wasn't an independent or
  blind grading process, so take the "accuracy tie" finding with that in
  mind.
- The "harder question" category only had 2 questions in it, so that
  biggest-looking number (~54%) is the shakiest one in this whole write-up.

## What's in this repository

- `control_repo/` — the Flask code as the AI saw it **without** the map.
- `graph_repo/` — the same Flask code, but **with** the map built (see
  `graph_repo/graphify-out/graph.json` and
  `graph_repo/graphify-out/GRAPH_REPORT.md`).
- This `README.md` — the plain-language explanation you just read.

## Bottom line

In this small test, giving the AI a pre-built map of the code didn't make
it noticeably more correct — it did well either way. What it did seem to
do was cut down how much effort the AI needed for harder, spread-out
questions, while making basically no difference on simple, single-file
ones. I wouldn't treat this as proof the tool "works" in general — just as
a reasonably encouraging first signal that's worth testing further, on
more codebases and with more repeats, before drawing any firm conclusions.
