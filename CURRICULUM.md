# Ship It — Session Plans

Detailed plans for all eight sessions. Each includes a goal, minute-by-minute timing, the exact code students write, tiered side quests, the failures you should expect, and the checkpoint state.

> **Notation.** `class.example.dev` stands in for your domain throughout. A student named Ada edits at `code-ada.class.example.dev` and her site is at `ada.class.example.dev`. The class homepage linking everyone is at `class.example.dev`.

---

## How every session is shaped

45 minutes goes faster than it reads. The shape below is the one that survives contact with a real room:

| Minutes | What |
|---|---|
| 0–5 | Settle, log in, open two tabs: the editor and their own site |
| 5–13 | You demo at the front. **Eight minutes, hard stop.** |
| 13–38 | Hands-on. You circulate. |
| 38–45 | Share round + one sentence about next week |

**You get about 25 minutes of real work per session.** Every plan below is built against that number, not against 45.

### The rules that make a room of eight work

- **Two tabs, always.** Editor on the left, their live site on the right. The loop is: change something, save, refresh, look. Drill this in session 1 and never explain it again.
- **"Ask two, then me."** Before a student asks you, they ask the two people next to them. This roughly halves your support load and the explainers learn more than the askers.
- **Core, then side quests.** Every session has one thing everyone finishes and a stack of optional ones nobody finishes. Never let a fast student sit idle and never let a slow one feel behind.
- **Read the last line.** When something breaks, the error appears *on their own site*. Point at the bottom line of the traceback. By week three they should be doing this themselves.

### Side quest difficulty

- ⭐ Anyone who finished the core
- ⭐⭐ Confident students
- ⭐⭐⭐ The student who already codes at home — these are meant to be hard

---

## What's on their screen at the start

Every student begins session 1 with a working site. Files:

```
app.py                  ← the only file they edit at first
templates/
  base.html             ← provided, mostly leave alone
  index.html            ← they edit this from session 1
static/
  style.css             ← provided, they customize the top of it
storage.py              ← provided, introduced session 5
```

`app.py` as they find it:

```python
from flask import Flask, render_template

app = Flask(__name__)

name = "Your Name"

@app.route("/")
def home():
    return render_template("index.html", name=name)
```

`templates/index.html`:

```html
{% extends "base.html" %}

{% block content %}
  <h1>Hi, I'm {{ name }}!</h1>
  <p>This is my website.</p>
{% endblock %}
```

The top of `static/style.css` — the only part they touch:

```css
:root {
  --main-color: #7c3aed;
  --background: #faf9f7;
  --font: system-ui, sans-serif;
}
```

Everything below that line in the stylesheet already makes the page look decent. This matters more than it sounds like it does: a student whose first page looks good is a student who wants a second one.

---

# Session 1 — It's Alive

**Goal:** every student's name is on a live website by minute 20. Nothing else in this session matters as much as that.

**Concepts:** a file on a server is a website; save and refresh; text in quotes is data you control.

### Before class

Everything provisioned and tested. Open all eight sites yourself the morning of. Print the credential cards. **If the server is down at 9am, you have all day to fix it; if you discover it at 3:05pm you have no class.**

### 0–5 — Cards out

Hand each student a card with their username, editor URL, and site URL. They open both tabs. Do not explain anything yet.

### 5–13 — The demo

Put your own site on the projector. Say roughly this:

> "This is a website. It's on the internet right now — you could open it on your phone. It's made of this file." *(switch to `app.py`)* "See this? `name = "Your Name"`. Watch."

Change it to your name. Save. Switch to the browser. Refresh. It changed.

> "That's the whole job. Change the file, save it, refresh the page. Everything we do for the next eight weeks is that."

Then do it wrong on purpose. Delete a quote mark. Save. Refresh. An error page appears.

> "This is going to happen to all of you, a lot. It is not a big deal. Look at the very last line."

Point at it. Read it out loud. Put the quote back. Save. Refresh. Fixed.

Teaching the error before they hit it is worth five minutes here and saves twenty later.

> **Two kinds of error page, for your own orientation — don't explain this part to them.** A mistake Python hits while *running* (a misspelled variable, say) produces the orange Werkzeug traceback. A mistake that stops the file loading at all (a missing quote, bad indentation) stops the app from starting, so the [oops server](SETUP.md#the-oops-server) serves a plain page carrying the same traceback instead. Both end in the line that names the problem, which is the only part students need. If you ever see a raw `502 Bad Gateway`, the oops server isn't running — that's a setup bug, not a student one.

### 13–38 — Hands-on

**Core (everyone):**

1. Change `name` to your name. Save, refresh.
2. In `templates/index.html`, change the second line to say something true about you.
3. In `static/style.css`, change `--main-color` to a color you like. ([Color picker](https://htmlcolorcodes.com/) on the projector.)

That's it. That is the entire core, and some students will take the full 25 minutes on the color.

**Side quests:**

- ⭐ Change `--background` and `--font` too.
- ⭐ Add another `<p>` line to your page.
- ⭐⭐ Add a second variable in `app.py` (like `age = 12`), pass it to the template next to `name`, and show it on the page.
- ⭐⭐⭐ Break your site four different ways and write down what each error's last line said. Fix them all.

### 38–45 — The share round

Everyone opens `class.example.dev`. Eight links, eight names. Give them four minutes to click through each other's sites.

This is the payoff. Let it be loud.

> "Next week we make the page do math."

### What goes wrong

| Symptom | Cause | Fix |
|---|---|---|
| Error page after editing | Missing quote or a stray character | Point at the last line; it names the line number |
| "Nothing changed!" | Didn't save, or refreshed the wrong tab | Ctrl-S, then check which tab they're in. Happens constantly in week 1. |
| Plain error page instead of the orange one | Syntax error — the app can't start | Same skill: read the last line. If they're stuck, restore from checkpoint (30s) |
| Color didn't apply | Edited below the `:root` block | Show them the top of the file again |

**Checkpoint 1:** `name` changed, one edited line in `index.html`, one changed color.

---

# Session 2 — Python That Thinks

**Goal:** the page computes something instead of just displaying what was typed.

**Concepts:** variables, numbers vs. text, f-strings, `if`/`else`.

### 5–13 — The demo

Start with the difference between the two kinds of thing:

```python
name = "Ada"      # text — it has quotes
age = 12          # a number — no quotes
```

> "Python can do math with numbers. It can't do math with text. That's basically why we tell it which is which."

Then the f-string, which is the session's real content:

```python
greeting = f"Hi, I'm {name} and I'm {age} years old."
```

> "The `f` means 'fill in the blanks.' Anything in curly braces gets replaced with what's in that variable."

Then the part that makes it click — put math inside the braces:

```python
status = f"I'll be a teenager in {13 - age} years."
```

Change `age` to 11. Save. Refresh. Both lines changed, and one of them did arithmetic you never typed. Let that land.

Then `if`/`else`:

```python
if age >= 13:
    status = "I'm a teenager already!"
else:
    status = f"I'll be a teenager in {13 - age} years."
```

Mention the indentation once, plainly: "the indented lines are the ones that belong to the `if`. Python cares about this." Don't lecture it — they'll learn it from the error.

### 13–38 — Hands-on

**Core:** `app.py` becomes:

```python
from flask import Flask, render_template

app = Flask(__name__)

name = "Ada"
age = 12

@app.route("/")
def home():
    greeting = f"Hi, I'm {name} and I'm {age} years old."
    if age >= 13:
        status = "I'm a teenager already!"
    else:
        status = f"I'll be a teenager in {13 - age} years."
    return render_template("index.html", greeting=greeting, status=status)
```

and `index.html`:

```html
{% extends "base.html" %}

{% block content %}
  <h1>{{ greeting }}</h1>
  <p>{{ status }}</p>
{% endblock %}
```

Then: change `age` and watch both lines update.

**Side quests:**

- ⭐ Add a third variable (`favorite_food`) and a sentence that uses it.
- ⭐ Make the `if` check something else — a lucky number, how many pets you have.
- ⭐⭐ Add an `elif` so there are three possible messages instead of two.
- ⭐⭐ Use `import random` and `random.choice([...])` so the page greets you differently on every refresh.
- ⭐⭐⭐ Use `datetime` to show how many days until your birthday. (Hard on purpose — date math is genuinely fiddly.)

### 38–45

Refresh a few students' sites on the projector. The random-greeting students will want to show theirs; let them.

### What goes wrong

| Symptom | Cause | Fix |
|---|---|---|
| `IndentationError` | Mixed spaces, or the `if` body isn't indented | The editor shows it; have them delete the whitespace and retype it |
| `NameError: name 'x' is not defined` | Typo, or used a variable before creating it | Last line of the error names the variable |
| Braces show up literally on the page | Forgot the `f` before the quote | Most common error of the session. Expect it eight times. |
| `TypeError` doing math | Age is `"12"` with quotes | "Quotes make it text. Text can't do math." |

**Checkpoint 2:** greeting and status computed in Python, both rendered, one working `if`/`else`.

---

# Session 3 — Lists & Loops

**Goal:** the page's *structure* is generated, not typed. This is the conceptual peak of the fundamentals — budget extra circulating time.

**Concepts:** lists, `for` loops, loops in templates.

### 5–13 — The demo

> "Last week one variable held one thing. This week one variable holds a bunch of things."

```python
favorites = ["tacos", "Minecraft", "my dog Rex"]
```

Show the loop in the template, and narrate it as a machine:

```html
<ul>
  {% for thing in favorites %}
    <li>{{ thing }}</li>
  {% endfor %}
</ul>
```

> "This runs once for every item in the list. Three items, three lines on the page. Add a fourth thing to the list —" *(do it)* "— and there are four. I didn't touch the HTML."

Adding an item to the list and watching a new bullet appear is the moment the idea lands. Do it twice.

### 13–38 — Hands-on

**Core:** add a list of 3–5 things to `app.py`, pass it through, loop over it in the template.

```python
favorites = ["tacos", "Minecraft", "my dog Rex"]

@app.route("/")
def home():
    return render_template("index.html", greeting=greeting, favorites=favorites)
```

Expect this to take most students the full 25 minutes. There are three separate places to get it right — the list, the argument, the loop — and a mistake in any one produces a blank section with no error at all. That silence is the hard part of this session.

**Side quests:**

- ⭐ Get to ten things in your list.
- ⭐ Add a heading above the list that uses `len(favorites)`: "Ada's 7 favorite things".
- ⭐⭐ Make each item a pair with an emoji: `[("tacos", "🌮"), ("Minecraft", "⛏️")]` and show both in the loop.
- ⭐⭐ Sort it: `sorted(favorites)`.
- ⭐⭐⭐ Use a list of dictionaries with a name, an emoji, and a rating out of 10, and show all three per row.

### 38–45

Share round. The lists are personal, so this one is genuinely fun to look at.

### What goes wrong

| Symptom | Cause | Fix |
|---|---|---|
| **Nothing appears, no error** | Forgot `favorites=favorites` in `render_template` | The signature failure of this session. Check it first, every time. |
| Only one bullet, with the whole list in it | Wrote `{{ favorites }}` instead of looping | Show the difference side by side |
| `{% endfor %}` missing | — | The error mentions the block; it's unusually readable |
| Emoji won't type | Chromebook shortcut is unknown to them | Ctrl-Shift-Space, or [emojipedia](https://emojipedia.org/) and copy-paste |

**Checkpoint 3:** a list in `app.py` rendering as a list on the page.

---

# Session 4 — Talk Back

**Goal:** someone else can put something on their page. The site stops being a broadcast.

**Concepts:** forms, a second route, receiving data, `redirect`.

### 5–13 — The demo

> "Everything so far, you decided. Today other people get to type things into your site."

Three new ideas, in order:

1. **A form** is a box plus a button. It has an `action` — the address it sends to.
2. **A second route** is a second address on your site that does a job.
3. **A redirect** sends the visitor back to where they came from.

```html
<form action="/sign" method="POST">
  <input name="message" placeholder="Say something nice">
  <button>Sign my page</button>
</form>
```

```python
from flask import Flask, render_template, request, redirect

messages = []

@app.route("/sign", methods=["POST"])
def sign():
    messages.append(request.form["message"])
    return redirect("/")
```

> "`request.form["message"]` is how you grab what they typed. The name in the brackets matches the `name=` on the input box. Those two have to be the same word."

Demo it: type something, click, watch it appear. Type two more.

**Then the cliffhanger.** Save `app.py` without changing anything.

> "Watch what happens to all those messages."

They're gone.

> "That's next week."

Do not explain why yet. Let them be annoyed about it — it makes session 5 land.

### 13–38 — Hands-on

**Core:** the form, the `/sign` route, and the messages rendered in a loop (which they can do already — it's session 3's skill on new data).

```python
@app.route("/")
def home():
    return render_template("index.html", greeting=greeting,
                           favorites=favorites, messages=messages)
```

```html
<h2>Messages</h2>
{% for m in messages %}
  <p>{{ m }}</p>
{% endfor %}
```

Once it works, have them walk to a neighbor's machine and sign each other's pages. Ten minutes of this is not wasted time — it's the reason they'll show up next week.

**Side quests:**

- ⭐ Change the button text and the placeholder.
- ⭐ Add a second input for the signer's name and show "Sam says: hi".
- ⭐⭐ Ignore empty messages: `if request.form["message"].strip():`
- ⭐⭐ Show the newest message first (`messages.insert(0, ...)` or reverse the loop).
- ⭐⭐⭐ Add a `/clear` route that empties the list, and a button that hits it.

### What goes wrong

| Symptom | Cause | Fix |
|---|---|---|
| `400 Bad Request` | `name=` on the input doesn't match the key in `request.form[...]` | The number one error of this session. Check both spellings together. |
| `405 Method Not Allowed` | Missing `methods=["POST"]` on the route | — |
| Message appears then vanishes on save | Working as intended | "Hold that thought" |
| Form does nothing | `action` doesn't match the route path | Compare `/sign` to `/sign` character by character |

**Checkpoint 4:** working form, `/sign` route, messages rendering (in memory).

---

# Session 5 — Remember Me

**Goal:** messages survive. Then the whole class signs each other's pages for real. **This is the best session of the course — protect its last ten minutes.**

**Concepts:** memory vs. storage; using a module someone else wrote; what a database is.

### 5–13 — The demo

> "Last week your messages disappeared every time you saved. Here's why."

> "The list lived in the computer's memory. Memory is a whiteboard — fast, and it gets wiped every time the program restarts. We need a filing cabinet instead."

Open `storage.py` — which has been sitting in their folder since week one — and show them the two functions they'll use:

```python
storage.save("hello")     # put a message in the cabinet
storage.load_all()        # get all the messages back out
```

Then the change, which is smaller than they expect:

```python
import storage

@app.route("/sign", methods=["POST"])
def sign():
    storage.save(request.form["message"])
    return redirect("/")

@app.route("/")
def home():
    messages = storage.load_all()
    return render_template("index.html", greeting=greeting,
                           favorites=favorites, messages=messages)
```

Delete `messages = []` entirely. Save. Sign the page. **Save `app.py` again — the messages are still there.** That's the whole session in one gesture.

### The "look under the hood" moment (3 minutes, worth it)

Open `storage.py` and show them the inside:

```python
conn.execute("INSERT INTO messages (text) VALUES (?)", (text,))
conn.execute("SELECT text FROM messages ORDER BY id DESC")
```

> "That's SQL. It's a different language, and it's how basically every app you use talks to its database. `INSERT` puts a row in. `SELECT` gets rows out. You don't need to write it today — I wrote it for you. But now you know what's down there."

Then show them `guestbook.db` in the file list. It's a real file. It has their messages in it.

**Do not teach SQL.** Show it, name it, move on. Naming a thing they'll meet again later is the goal.

### 13–38 — Hands-on, then the main event

**Core (15 min):** make the three changes above. It's a short core on purpose.

**The signing round (last 10 min of hands-on):** everyone opens `class.example.dev` and visits every other student's site, leaving a real message on each. Eight students, eight sites, seven messages each.

This is the single highest-engagement moment in the course. It will be loud and slightly chaotic. That's the correct outcome.

> Remind them once, lightly, of the code of conduct they signed in session 1 — kind messages only, no last names. Then let them go. You can see every site from the class homepage.

**Side quests:**

- ⭐ Sign every classmate's page (this is the core for most of them).
- ⭐⭐ Add a `storage.count()` and show "17 people have signed my page".
- ⭐⭐ Save the signer's name alongside the message. (Requires touching `storage.py` — a real step up.)
- ⭐⭐⭐ Add a delete button next to each message. Genuinely hard: needs the row's `id` threaded from the database through the template and back to a route.

### What goes wrong

| Symptom | Cause | Fix |
|---|---|---|
| `NameError: storage` | Forgot `import storage` at the top | — |
| Messages still vanish | Left `messages = []` in, so it shadows the load | Delete that line |
| Old in-memory messages gone forever | They were never saved | Be upfront: "those are gone, the new ones stay" |
| Someone writes something unkind | It happens | Handle it in the room, not in code. You can delete from the `.db` file. See `SAFETY.md`. |

**Checkpoint 5:** persistent guestbook; messages survive a restart.

---

# Session 6 — Give It a Brain

**Goal:** their site does something they couldn't have programmed themselves.

**Concepts:** what an API is; a prompt is a string you build; models are confidently wrong sometimes.

### 5–13 — The demo

> "There's a computer somewhere else that's good at writing. We can send it a question and get an answer back. That's called an API — it just means asking another computer to do a job for you."

```python
import requests

def ask_ai(question):
    r = requests.post("https://ai.class.example.dev/ask", json={"prompt": question})
    return r.json()["text"]
```

> "That's the whole thing. Send a string, get a string."

Then the part that matters:

```python
prompt = f"You are Rex, a very good dog. Answer in one or two sentences, as Rex: {question}"
```

> "Look what that is. It's an f-string. You already know how to do this. **The instructions you give the AI are just text you wrote** — change the text, change the personality."

Change "a very good dog" to "a grumpy cat who is tired of questions." Same code, completely different site. Do this live; it gets a reaction.

**Then, deliberately, show it being wrong.** Ask it something factual and checkable that it's likely to fumble, or ask your character something it can't know.

> "It said that very confidently. It's also wrong. This thing never says 'I don't know' unless you ask it to. That's the most important thing to understand about it."

### 13–38 — Hands-on

**Core:** add `ask_ai`, then pick one feature from the menu.

**Option A — Ask my character** (most popular; recommended default):

```python
@app.route("/ask", methods=["POST"])
def ask():
    question = request.form["question"]
    prompt = f"You are Rex, a very good dog. Answer in one or two sentences, as Rex: {question}"
    answer = ask_ai(prompt)
    return render_template("index.html", greeting=greeting, favorites=favorites,
                           messages=storage.load_all(), answer=answer)
```

```html
<form action="/ask" method="POST">
  <input name="question" placeholder="Ask Rex anything">
  <button>Ask</button>
</form>
{% if answer %}
  <p class="answer">{{ answer }}</p>
{% endif %}
```

**Option B — AI captions your favorites.** Loop over `favorites`, ask for a one-line caption for each, show them underneath. (Warn them: eight calls means eight waits. Slower than it sounds.)

**Option C — Poem-ifier.** A button that takes the newest guestbook message and turns it into a four-line poem.

**Side quests:**

- ⭐ Rewrite your character's personality three different ways and compare.
- ⭐ Make it answer in exactly one sentence. (Say so in the prompt. It's that easy — that's the lesson.)
- ⭐⭐ Add "If you don't know, say you don't know" to your prompt and try to catch it out anyway.
- ⭐⭐ Show a "thinking…" message while waiting.
- ⭐⭐⭐ Handle failure: what does your page do when the AI is down or slow? Try `try`/`except` around the request.

### 38–45

Demo round on the projector. The character personalities are funny and they know it.

### What goes wrong

| Symptom | Cause | Fix |
|---|---|---|
| Page hangs ~2s | Working as intended — it's a real network call | "That's a real computer somewhere thinking" |
| `KeyError: 'text'` | Proxy returned an error shape, not an answer | Check the proxy log; usually rate limit |
| `429` | Hit their per-student limit | Limits are per session; raise it or have them wait |
| Same answer every time | Same prompt, and the temperature is low | Explain it briefly; not worth a detour |
| Someone tries to make it say something rude | Guaranteed. At least one will try. | The proxy's system prompt handles it. Everything is logged. Treat it as a teaching moment, not a crisis. |

**Checkpoint 6:** a working AI feature on the page.

---

# Session 7 — Free Build (and AI as a Coding Partner)

**Goal:** build their own thing. Also: the honest conversation about AI writing code.

**This session is also your buffer.** If earlier sessions ran long, this is where the slack lives. Cut the free-build time before you cut anything else.

### 5–20 — The AI-as-partner lesson (15 minutes)

Longer than the usual demo, and worth it.

> "The same thing that's answering questions on your site can also write code. I want to show you how to use it without it wrecking your project."

Live, on the projector, ask an assistant for a feature one of them wants. Paste the code in. Then:

1. **Read it out loud before running it.** "Do I recognize these pieces? What's this line doing?"
2. **Run it.** If it works, ask *why* it works.
3. **Now break it on purpose** — or use a case where it's wrong, which is easy to find. Show the error. Ask the assistant to fix it. Note whether the fix is real or just confident.

The framing that works with this age group:

> **"You're the boss. It's a very fast intern who never says 'I don't know.' It'll hand you something that looks finished every single time. Your job is to check."**

Two rules on the board:

- **Don't paste code you can't read.** If you don't know what a line does, ask — the assistant or me.
- **Test it yourself.** "It said it works" isn't the same as it working.

This is the most durable thing in the entire eight weeks. Most of them will use these tools for the rest of their lives, and almost nobody teaches them the checking half.

### 20–40 — Free build

They pick. Have a printed idea list ready for the ones who freeze — a blank page is a real obstacle at this age:

- A page for a pet, a game, a band, a book
- A poll where visitors vote and you show the counts
- A "rate my X" page with a 1–10 form
- A joke generator (list + `random.choice`, or AI-written)
- A countdown to something they care about
- A second page at a second route
- A "secret" page only someone with the link can find
- Make it look completely different with CSS

Circulate. Don't fix things for them — ask what they've tried and where the error points.

### 40–45

Tell them what Demo Day is. Ask them to think about which one thing they want to show. Knowing it's coming raises the quality of the last week noticeably.

**Checkpoint 7:** whatever they built. Snapshot it — it's the version they'll demo.

---

# Session 8 — Demo Day

**Goal:** they present, and they leave with their work.

### 0–5 — Setup

Everyone's site open on the projector, ready to switch. If you can get parents or another teacher in the room, do it. The presence of a real audience changes how they talk about their own work.

### 5–28 — Demos

**90 seconds each**, eight students, plus switching. Give them three prompts on the board so nobody freezes:

1. What is your site?
2. Show us the coolest thing it does.
3. What was the hardest part?

Question three is the one that matters. "The hardest part" is where they realize they got through something hard.

Applaud everyone. Ask each one a genuine question.

### 28–40 — Taking it home

Hand out the **Keep Going** sheet and walk through all four things (details in `SETUP.md`):

1. **Your site stays up.** Same address, through the end of the school year. Show them the date written on the sheet.
2. **Here's a copy of everything you wrote.** They download a ZIP of their project.
3. **It's also on GitHub**, at a link on the sheet. Explain plainly: GitHub is where the world keeps its code, you have to be 13 to have your own account, so this one is hosted under the class. When you're old enough you can copy it into your own and keep going.
4. **How to run it at home.** Three steps: install Python, `pip install flask`, `flask run`. With a note for whoever helps them.

Three concrete next steps on the sheet, not a vague "keep learning":

- Add one more feature to this site
- Try [replit.com](https://replit.com) or [PythonAnywhere](https://www.pythonanywhere.com) with a parent
- The AI feature stops working when the class server key expires — here's how to get your own

### 40–45 — Wrap

Group photo of the class homepage with all eight sites on it. Thank them by name.

### After class

- Snapshot every project one final time.
- Push the eight mirrors to GitHub.
- Put a calendar reminder for the shutdown date, and email families two weeks before it.
- Write down what ran long. You'll teach this again.

---

# Appendix A — The 6-session version

| Session | Contents |
|---|---|
| 1 | It's Alive + the front half of session 2 (variables, f-strings) |
| 2 | `if`/`else` + Lists & Loops |
| 3 | Talk Back |
| 4 | Remember Me + the signing round |
| 5 | Give It a Brain + a short version of the AI-partner talk |
| 6 | Demo Day |

You lose the free build, which is a real loss — but Demo Day and the signing round are load-bearing and should be the last things you cut. If you're down to six sessions *and* the room is struggling, cut the AI session instead of Demo Day.

# Appendix B — For the student who's already coding

Every session's ⭐⭐⭐ quest is aimed at them. If those run out:

- Let them add a second page, then a third, and navigation between them
- Point them at `base.html` and the CSS below the `:root` block — a real design project
- Have them build something that reads *another* student's guestbook (with permission) — their first taste of consuming an API
- Hand them the delete-a-message quest from session 5; it's genuinely nontrivial
- Ask them to help two specific classmates. Give it status. It works better than more code.

# Appendix C — The instructor's session-day checklist

**The morning of:**

- [ ] Open all eight student sites — do they load?
- [ ] Open all eight editors — do they load?
- [ ] Hit the AI proxy once (session 6+)
- [ ] Confirm last night's backup exists
- [ ] Have last session's checkpoints ready to restore

**In the room:**

- [ ] Credential cards for anyone who lost theirs
- [ ] Projector mirroring your editor and browser
- [ ] The class homepage open in a tab
- [ ] Today's side quests printed

**After:**

- [ ] Snapshot all eight projects as this session's checkpoint
- [ ] Skim the AI proxy log (session 6+)
- [ ] Skim the guestbooks
- [ ] Note what ran long

# Appendix D — Concept map

What's introduced where, for anyone adapting this:

| Concept | Session |
|---|---|
| Edit → save → refresh | 1 |
| Reading an error | 1 |
| Strings, variables | 1–2 |
| Numbers vs. text | 2 |
| f-strings | 2 |
| `if` / `elif` / `else` | 2 |
| Indentation as meaning | 2 |
| Lists | 3 |
| `for` loops | 3 |
| Templates and template loops | 3 |
| Functions (`def`) | 1–4, named in 4 |
| Routes and addresses | 4 |
| Forms and POST | 4 |
| Redirects | 4 |
| Memory vs. storage | 5 |
| Importing a module | 5 |
| Databases (named, not taught) | 5 |
| SQL (shown, not taught) | 5 |
| APIs | 6 |
| Prompts as data | 6 |
| Model fallibility | 6–7 |
| Debugging as a skill | all |
| Checking AI-written code | 7 |
