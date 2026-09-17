# Ship It

**A 6–8 session coding elective for grades 6–8, where every student's website is live on the internet from day one.**

Students write real Python — not blocks, not puzzles — and every week their own site at their own URL does something new. By the last session they have a working web app with a database and an AI feature, and a link they can text to a friend.

---

## The idea

Most intro-to-coding curricula produce exercises. You finish the puzzle, you get a green checkmark, and nothing survives the class. This one produces **a website**, and it produces it in the first twenty minutes.

That ordering is the entire design. A 12-year-old who can send a friend a URL that works on their phone will come back for seven more weeks. So session 1 doesn't build up to a deployment — it opens with one, and every session after that adds something visible at the same address.

The technical spine is identical for all students, so one instructor can run a room of eight. The *subject* is entirely theirs: their dog, their Minecraft builds, a fake restaurant, a fan page. Same code shape, wildly different results.

## Who this is for

| | |
|---|---|
| **Students** | Grades 6–8, 8–10 max (8 is better). No prior experience assumed; mixed experience handled by design. |
| **Format** | 6–8 sessions, 45 minutes each, one per week. |
| **Hardware** | Anything with a browser. Chromebooks are a first-class target — nothing is installed locally. |
| **Instructor** | Comfortable with Python and a Linux server. You run a small VPS for the class; students need no accounts anywhere. |

## The arc

Every session ends with something new working at the student's URL.

| # | Session | What's newly live | What they learn |
|---|---------|-------------------|-----------------|
| 1 | **It's Alive** | Their name, their colors — a real page on the internet | The save → refresh loop; text is code |
| 2 | **Python That Thinks** | Content the page computes by itself | Variables, types, f-strings, `if`/`else` |
| 3 | **Lists & Loops** | A generated list of their favorite things | Lists, `for` loops, templates |
| 4 | **Talk Back** | A form — visitors can leave a message | Routes, form data, functions |
| 5 | **Remember Me** | Messages that survive a restart | Persistence and databases |
| 6 | **Give It a Brain** | An AI-powered feature | What an API is; prompts; when models are wrong |
| 7 | **Free Build** | Whatever they want | Debugging; using an AI coding assistant well |
| 8 | **Demo Day** | — | Presenting; taking it home |

**Session 5 is the one they'll remember.** Once messages persist, everyone spends five minutes visiting everyone else's site and leaving real comments. It is the single highest-engagement moment in the course, and it is worth protecting time for.

### Running it in 6 sessions

Drop session 7 and fold its AI-assistant discussion into session 6; merge the back half of session 2 into session 1. Cut session 6 only as a last resort — the AI feature is a bigger hook than the free build.

## What students walk away with

Not a certificate. Four concrete things:

1. **Their site stays live** at its URL through the end of the school year.
2. **A ZIP of their project** — every file they wrote.
3. **A public GitHub mirror** the instructor hosts, which they can fork themselves once they turn 13.
4. **A one-page "keep going at home"** handout with the three next things to try.

## What the instructor needs

- A VPS (4 vCPU / 8 GB comfortably runs 8 students) and a domain. → [`SETUP.md`](SETUP.md)
- An Anthropic API key for the class AI proxy. **Real cost for the full course: under $5.** → [`AI_PROXY.md`](AI_PROXY.md)
- About a weekend of setup the first time. It's scripted and reusable next year.
- To read [`SAFETY.md`](SAFETY.md) before session 1. It's short and it matters.

Students need **nothing**. No accounts, no signups, no installs, no passwords to remember — they get a printed card with a username and a URL.

## Design decisions worth knowing about

**Flask, not FastAPI.** FastAPI is built for JSON APIs — async, Pydantic, no built-in templating. For server-rendered HTML in front of beginners, Flask + Jinja is strictly fewer concepts. FastAPI is a great thing to learn *after* you want one.

**No git, no GitHub, no deploy step during the course.** Students edit in a browser IDE and the app auto-reloads on save. Save → refresh → see it, in about a second. Version control is a genuinely important skill and it is also a reliable way to lose a session and a half out of eight; it is not in the budget. (The GitHub mirror at the end is the instructor's job, not theirs.)

**Errors show up on their own page.** With Flask's debug mode on, a traceback renders in the browser at their URL. This turns the framework's debug mode into the best teaching tool in the course: "read the last line" becomes a week-two skill instead of a year-three one.

**The boring parts are pre-built.** Students get a stylesheet that already looks good and a `storage.py` with `save()` and `load_all()`. They *use* SQLite three weeks before they could explain it — then you open the file and show them the SQL underneath. Nobody learns `CREATE TABLE` syntax in a 45-minute period and nobody should have to.

**Every session has a floor and a ceiling.** A core everyone finishes, plus side quests nobody finishes. In an elective with unknown prior experience, this is not a nice-to-have.

**Checkpoints.** Each student's app can be restored to the known-good end state of any previous session in about thirty seconds. Nobody starts a session behind because of what they broke last week.

## Why not code.org? Why not notebooks?

**code.org** is genuinely excellent, and it's what these students likely already did in 4th and 5th grade. Block-based puzzles teach sequencing, loops, and conditionals well. What they don't do is convince a 13-year-old that they are doing the real thing. At this age that belief is most of the battle.

**Jupyter or Colab notebooks** are the obvious next step and they're a reasonable choice — real Python, zero setup, Chromebook-friendly. The reason this curriculum doesn't use them: a notebook is still an exercise. It runs where you made it and nowhere else, and it can't be shown to anyone who isn't looking over your shoulder. A URL can.

The tradeoff is real. Notebooks cost the instructor almost nothing to set up; this costs a weekend. You're buying the students something notebooks can't sell.

## Repo contents

| File | What's in it |
|---|---|
| [`CURRICULUM.md`](CURRICULUM.md) | Minute-by-minute session plans, the code students type, side quests, and what goes wrong |
| [`SETUP.md`](SETUP.md) | VPS architecture, provisioning, checkpoints, backups |
| [`AI_PROXY.md`](AI_PROXY.md) | The class AI endpoint: design, safety, rate limits, cost |
| [`SAFETY.md`](SAFETY.md) | Under-13 privacy policy and content moderation — read before session 1 |
| [`handouts/`](handouts/) | Printable: credential cards, cheat sheet, side quests, code of conduct |

## License

Use it, fork it, change it, run it at your school. Attribution appreciated, not required.
