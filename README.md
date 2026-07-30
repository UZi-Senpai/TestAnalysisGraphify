# Does the "graphify" tool actually help an AI understand code better?

This is a little experiment to test a claim: that turning a codebase into a
**map (a "knowledge graph")** first, before asking an AI questions about it,
makes the AI's answers better or faster than just letting it read the raw
files on its own.

Think of it like this: imagine you're dropped into a huge school you've
never been to and someone asks you "how do you get from the front gate to
the principal's office?"

- **Option A:** You wander the hallways yourself, peeking into classrooms,
  reading room numbers, until you figure out the path.
- **Option B:** Someone hands you a **map of the school** first, with all
  the hallways and rooms already marked, and you just trace the route.

This test compares Option A and Option B, but for computer code instead of
a school.

## What we actually did

1. We picked a real, well-known piece of software called **Flask** (a
   popular tool programmers use to build websites). It's medium-sized —
   not tiny, not huge — about 9,500 lines of code across 25 files.

2. We made **two identical copies** of it:
   - `control_repo/` — the plain copy, no map, no help. The AI could only
     open files and search through them like a person flipping through a
     textbook.
   - `graph_repo/` — the copy that has a **map already built** using a tool
     called `graphify`. That map lives in `graph_repo/graphify-out/` — it's
     a file (`graph.json`) that lists all the important pieces of the code
     and how they connect to each other, plus a plain-language summary
     (`GRAPH_REPORT.md`).

3. We wrote **10 questions** about the code, ranging from easy ("what does
   this one function do?") to hard ("trace everything that happens between
   a webpage request coming in and the code that handles it"). We split the
   questions into three difficulty types:
   - **Easy / single-file** questions (4 of them)
   - **Medium / multi-file "trace the path"** questions (4 of them)
   - **Hard / "explain the whole structure"** questions (2 of them)

4. For every question, we asked a **fresh AI assistant** — one that had
   never seen the question before and had no memory of other questions —
   to answer it twice using the plain copy (`control_repo`), and twice
   using the mapped copy (`graph_repo`). That's 40 separate attempts in
   total (10 questions × 2 conditions × 2 repeats), so we're not just
   trusting a single lucky run.

5. We then checked every single answer by hand against the real code to
   make sure it was actually correct, not just confident-sounding.

## What we found

- **Both ways got the right answers.** Every one of the 40 answers was
  factually correct. Having the map didn't make the AI smarter or more
  accurate in this test — it was already good at reading code carefully
  either way.

- **The map saved effort, but only on harder questions.** We measured how
  much "thinking work" (technically: how many tokens, which is roughly
  like counting words the AI had to read and write) each attempt used.
  - On the easy, single-file questions: the map didn't help at all — about
    the same effort either way.
  - On the medium, multi-file trace questions: the map saved about
    **23%** of the effort.
  - On the hard, whole-codebase questions: the map saved about **54%** of
    the effort — roughly half.

- **Building the map itself was cheap.** It cost a small, one-time amount
  of effort to build (the equivalent of about one extra question's worth),
  and after that it can be reused for every future question for free.

- **Speed (real-world time) was not a clear winner either way.** It bounced
  around a lot between attempts, so we can't honestly say the map made
  things faster in a stopwatch sense — just cheaper in terms of effort for
  the harder questions.

## The honest limitations (things a smart reader would ask about)

- We only tried this on one piece of software (Flask). A messier or more
  confusing codebase might show a bigger — or smaller — difference.
- We only ran each question twice per method. That's enough to see a clear
  pattern, but not enough to call it "scientifically proven" — more repeats
  would make the numbers more solid.
- The person grading the answers (checking if they were correct) was the
  same AI system that ran the test, just double-checking its own work
  against the real code. A truly independent judge would make this more
  trustworthy.

## What's in this repository

- `control_repo/` — the Flask code as the AI saw it **without** the map.
- `graph_repo/` — the exact same Flask code, but **with** the map built
  (see `graph_repo/graphify-out/graph.json` and
  `graph_repo/graphify-out/GRAPH_REPORT.md`).
- This `README.md` — the plain-language explanation you just read.

## Bottom line

Giving the AI a pre-built map of the code doesn't make it get things more
right — it was already accurate either way in this test. What it does is
make the AI work less hard to get there, and that saving gets bigger the
more complicated and spread-out the question is. For a quick, one-file
question, don't bother making the map. For "explain how this whole system
fits together" questions, the map roughly cut the effort in half.
