# Side Quests

Optional challenges. Print the session's page and leave the cards on the table, or hand them out as students finish the core.

⭐ anyone who finished · ⭐⭐ confident · ⭐⭐⭐ hard on purpose

> **The point of these is not to teach more.** It's that no student ever sits with nothing to do, and no student ever feels like the slow one. A ⭐⭐⭐ that nobody finishes has done its job.

---

## Session 1 — It's Alive

- ⭐ Change `--background` and `--font` too.
- ⭐ Add another line of text about yourself.
- ⭐ Find a color you actually like at [htmlcolorcodes.com](https://htmlcolorcodes.com).
- ⭐⭐ Add a second variable in `app.py` (like `age = 12`) and get it onto the page.
- ⭐⭐⭐ Break your site four different ways. Write down what the last line of each error said. Fix them all.

## Session 2 — Python That Thinks

- ⭐ Add a third variable and a sentence that uses it.
- ⭐ Make your `if` check something else — a lucky number, how many pets you have.
- ⭐⭐ Add an `elif` so there are three possible messages instead of two.
- ⭐⭐ Different greeting on every refresh:
  ```python
  import random
  greeting = random.choice(["Hey!", "Hi there!", "Sup."])
  ```
- ⭐⭐⭐ Show how many days until your birthday, using `datetime`. This one is genuinely fiddly. Date math always is.

## Session 3 — Lists & Loops

- ⭐ Get to ten things in your list.
- ⭐ Add a heading that counts them: `f"My {len(favorites)} favorite things"`.
- ⭐⭐ Give each one an emoji:
  ```python
  favorites = [("tacos", "🌮"), ("Minecraft", "⛏️")]
  ```
  ```html
  {% for thing, emoji in favorites %}
    <li>{{ emoji }} {{ thing }}</li>
  {% endfor %}
  ```
- ⭐⭐ Put them in alphabetical order with `sorted()`.
- ⭐⭐⭐ Use a list of dictionaries with a name, an emoji, and a rating out of 10 — and show all three.

## Session 4 — Talk Back

- ⭐ Change the button text and the placeholder.
- ⭐ Sign three classmates' pages.
- ⭐⭐ Add a second box for the signer's name, so it says "Sam says: hi".
- ⭐⭐ Ignore empty messages: `if request.form["message"].strip():`
- ⭐⭐ Newest message at the top.
- ⭐⭐⭐ Add a `/clear` route and a button that wipes the guestbook.

## Session 5 — Remember Me

- ⭐ Sign **everybody's** page.
- ⭐⭐ Add a counter: "17 people have signed my page."
- ⭐⭐ Save the signer's name next to the message. You'll have to change `storage.py` for this one.
- ⭐⭐⭐ Add a delete button next to each message. Hard: you need each message's id from the database, through the template, and back to a new route.

## Session 6 — Give It a Brain

- ⭐ Rewrite your character's personality three different ways. Compare.
- ⭐ Make it answer in exactly one sentence. (Just say so in the prompt. That's the whole trick.)
- ⭐⭐ Add "If you don't know, say you don't know" to your prompt — then try to catch it out anyway.
- ⭐⭐ Show a "thinking…" message while it's working.
- ⭐⭐⭐ What does your page do when the AI is down? Wrap it in `try` / `except` and find out.

## Session 7 — Free Build

No quests. Build what you want.

**Stuck for an idea?**

- A page about a pet, a game, a band, a book
- A poll — visitors vote, you show the counts
- A "rate my X" page with a 1–10 form
- A joke generator
- A countdown to something you care about
- A second page, at a second address
- A secret page only findable with the link
- Make it look completely different

**Using an AI to help write it?** Two rules: don't paste code you can't read, and test it yourself. It'll hand you something that looks finished every time. Your job is to check.
