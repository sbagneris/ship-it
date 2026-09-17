# Ship It — Safety and Privacy

**Read this before session 1.** It's short. It's also the part that keeps a fun elective from becoming a problem for you and the school.

The core situation: you are publishing websites made by children, some of whom are eleven, on the public internet, and some of those websites accept input from anyone with the link. That's manageable. It is not automatically safe.

---

## Why students have no accounts

GitHub requires account holders to be at least 13. Most hosting platforms set 18 in their terms and rely on a parent or teacher to hold the account. A typical 6th grade class is 11 and 12 year olds.

This isn't a technicality to route around — it's why the whole course runs on your server. Students authenticate to one thing (your code-server, with a password on a card), no data leaves your box except AI prompts, and there is no third party holding anything about them.

If you ever find yourself thinking "we could just have them sign up for—", the answer is no. Everything in this curriculum was designed so that you never need to.

---

## The three rules

Put these on the wall in session 1 and say them out loud again in session 5, when the guestbooks go live.

### 1. First names only

No last names anywhere — not on the page, not in a guestbook message, not in the AI prompts. A first name plus a school is already more than enough to identify a child.

### 2. Nothing that finds you in real life

No addresses, no phone numbers, no email, no social handles, no school name, no team or club names, no "I walk home down X street."

The subtle ones are what to watch for. "My soccer team is the Northside Falcons" is a location. So is a photo of a house.

### 3. No photos of people

Their dog, their drawing, their Minecraft build, a screenshot, a meme: all fine. Photographs of themselves or their friends: not on a public page.

This is the rule they'll push on, because a photo is the obvious thing to put on a page about yourself. Hold it. Offer the alternative — an emoji, a drawing, an AI-generated picture of what they'd look like as a wizard — and they'll usually be happier with it anyway.

---

## Technical measures

These reduce exposure. They do not replace the three rules — assume anything published could be found.

**Unlisted, not secret.** Student URLs aren't linked from anywhere public. The class homepage is the only index and it's unlisted too.

**`noindex` everywhere.** `X-Robots-Tag: noindex, nofollow` on every student site and the homepage, plus a `robots.txt`. This is what keeps a student's name out of search results, which is the specific consequence that actually matters years later.

**Instructor visibility.** You can open every student site from the homepage, and you can read every guestbook directly on the server. Students should know this. Say it plainly in session 1 — "I can see everything on all of your sites" — and it does more for behavior than any technical control.

**Everything on one box you control.** No third-party accounts, no analytics, no trackers, no CDN. The starter template pulls in nothing external — keep it that way; a font loaded from elsewhere is a request from a child's browser to a company you didn't vet.

**Shutdown date.** Sites come down at a fixed date you tell families up front. Nothing lingers online for years. Put it on the Keep Going sheet and email families two weeks before.

---

## The guestbook

Session 5 turns eight sites into eight public comment boxes. It's the best moment in the course and it's also the one place unmoderated text from outside can appear.

**Before you flip it on:**

- Re-state the three rules and the code of conduct.
- Tell them the guestbooks are for classmates and family, not for posting the link around.
- Remind them you can read all of them.

**Have a plan for the inevitable:**

- **Unkind message between classmates.** Delete the row, handle it as you'd handle anything else between two students in your room. The code is not the issue.
- **Something concerning about a student's wellbeing.** Follow your school's existing procedure. You are a mandatory reporter in many jurisdictions and a coding elective doesn't change that.
- **Something from outside the class.** Means the URL got shared. Clear the guestbook, talk to the student about where the link went, consider putting a password on that site for a while.

Deleting a message is a `DELETE` on one row of one SQLite file. Know how to do it before you need to, not while a student is standing next to you upset.

The "add a delete button" side quest in session 5 exists partly so that a student who wants control of their own page can have it.

---

## The AI feature

Full detail in [`AI_PROXY.md`](AI_PROXY.md). The safety-relevant summary:

- A system prompt on every call constrains tone and content, and students cannot bypass or see it.
- Every prompt and response is logged in full, with the student's name.
- One environment variable turns it off for everyone.
- `max_tokens` and rate limits bound the damage from a runaway loop.
- Nothing about a student is sent to the model beyond what they type into their own page.

**Tell students they're being logged.** Not as a threat — as a fact, mentioned once in session 6. "Everything you send goes through my server and I can read all of it." It's true, they should know it, and it heads off most of what you'd otherwise have to deal with.

Expect at least one student to try to make it say something rude. That's normal boundary-testing and the system prompt handles it. Something aimed at a specific classmate is a different thing and is a conversation, not a config change.

---

## Talking to families

A short note home before session 1 prevents nearly every awkward conversation later. Cover:

- Their child will build a real website on the internet at a URL you'll share.
- It's hosted on a private server run by you; no student accounts, no third-party services, no ads, no trackers.
- First names only, no photos of people, no contact information. Here are the three rules.
- Sites are unlisted and hidden from search engines.
- Two sessions use an AI assistant through a filtered, logged, school-appropriate endpoint — one building an AI feature into their site, one on how to check AI-written code.
- Sites come down on `<date>`; your child keeps a copy of everything they made.
- Here's how to reach you with questions.

Check your school's existing media-release and acceptable-use policies first. This elective probably sits inside them already, but "probably" is worth five minutes of confirming.

---

## Instructor checklist

**Before session 1**
- [ ] School policies reviewed; note home to families sent
- [ ] Three rules printed for the wall
- [ ] Code of conduct printed for students to sign
- [ ] `noindex` verified on every hostname
- [ ] Shutdown date decided and written down

**Before session 5 (guestbooks)**
- [ ] Rules re-stated
- [ ] You know how to delete a message from the database
- [ ] You've told them you can read everything

**Before session 6 (AI)**
- [ ] System prompt tested against a deliberately bad prompt
- [ ] Logging confirmed working
- [ ] Kill switch tested
- [ ] Students told they're logged

**Weekly**
- [ ] Skim every guestbook
- [ ] Skim the AI log
- [ ] Check nobody's added a last name, a photo, or a location

**After the last session**
- [ ] Families emailed the shutdown date
- [ ] Students have their ZIPs
- [ ] GitHub mirrors contain no personal information
- [ ] Reminder set to take the sites down and delete the logs
