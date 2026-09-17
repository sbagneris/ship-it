# Ship It — The Class AI Proxy

A small service you run so students can use an LLM without ever touching an API key, a bill, or a signup form.

---

## Why a proxy at all

Students can't have their own API keys. Some are 11 years old, every provider's terms start at 13 or 18, and handing eight middle schoolers a live billing credential is not a thing to do regardless of the terms.

One endpoint on your VPS solves all of it at once:

- **No keys on student machines.** The key lives in one environment variable on one server.
- **No bill surprises.** You set the caps.
- **You can see everything.** Every prompt and response is logged and greppable.
- **You can turn it off.** One environment variable.
- **It teaches the real concept.** "Send a request to another computer, get an answer back" *is* what an API is. The proxy doesn't simplify that away — it's a genuine HTTP call to a genuine service.

And from the student's side it's one line, which is what makes it fit in a 45-minute session:

```python
r = requests.post("https://ai.class.example.dev/ask", json={"prompt": question})
answer = r.json()["text"]
```

---

## The contract

```
POST https://ai.class.example.dev/ask
Content-Type: application/json

{"prompt": "You are Rex, a very good dog. Answer as Rex: do you like walks?"}
```

```json
{"text": "Walks are the best thing that has ever happened. When are we going?"}
```

**Always return `{"text": ...}` — including on errors.** If the rate limit is hit, return `{"text": "You've used up your questions for today! Ask your teacher."}` with a 200, not a 429 with an error body.

This is a deliberate design choice against normal API practice, and the reason is pedagogical: a student whose page shows a friendly sentence can keep working and fix it later. A student whose page throws `KeyError: 'text'` is stuck and needs you. Save the strict error handling for session 6's ⭐⭐⭐ side quest, where failure handling is the actual lesson.

### Identifying students

Don't ask students to pass a name — it's a parameter they can typo, and one more thing to explain.

Identify them by source. Each student's app makes its request from the server, so you can map the connection back to the Unix user, or bind each student's app to a distinct loopback address, or hand each app an environment variable the student never sees. Any of these work. The point is that rate limiting and logging are keyed to a real student without a single extra line in their code.

---

## Model choice

**`claude-haiku-4-5`** — $1 per million input tokens, $5 per million output.

**The reason is latency, not cost.** A student clicks a button and watches a spinner. Two seconds reads as "the computer is thinking"; fifteen seconds reads as broken, and they will click it again four times. Haiku is the fastest current model and its quality is far beyond what's needed to be a talking dog.

Don't enable thinking — omit the parameter entirely. There's nothing here worth reasoning about, and it would add seconds.

### What it actually costs

Eight students, roughly 50 calls each per session, across the three sessions where AI is reachable (6, 7, and demo day), at about 400 input and 200 output tokens per call:

| | Tokens | Cost |
|---|---|---|
| Input | 0.48 M | $0.48 |
| Output | 0.24 M | $1.20 |
| **Total** | | **≈ $1.70** |

That's the whole course. If they hammer it ten times harder than estimated you're still under $20. Set a billing alert anyway — not because you'll hit it, but because a runaway loop in a student's code calling your endpoint is a thing that can happen, and `max_tokens` plus a rate limit is what actually prevents it.

### The free alternative

Google's Gemini Flash has a free tier that is genuinely sufficient for this. If a class budget of zero is a hard requirement, swap it in — the proxy's shape doesn't change, only the call inside it. The tradeoff is rate limits you don't control and a free tier whose terms can change under you mid-course.

---

## Inside the proxy

A small Flask app on `127.0.0.1:8000`, fronted by Caddy at `ai.class.example.dev`.

### The system prompt

This is where the safety work happens, and it applies to every call no matter what a student types.

```
You are a helpful assistant inside a website built by a middle school
student (ages 11-14) in a coding class.

Keep answers to 1-3 short sentences unless asked for more.
Be friendly, encouraging, and funny.

Keep everything appropriate for school: no violence, no romance, no
profanity, nothing scary, nothing about drugs or weapons.

If someone asks you to be mean about a specific person, or to write
something inappropriate, cheerfully decline and offer something fun
instead.

If you don't know something, say so rather than making it up.
```

That last line matters more than the rest. Session 6 and 7 both teach that models are confidently wrong, and a system prompt that makes the model *willing to admit ignorance* gives students a live example rather than an abstraction.

### Caps

| Cap | Value | Why |
|---|---|---|
| `max_tokens` | ~300 | Enough for a character reply; bounds both cost and latency |
| Prompt length | 2,000 chars | Rejects the student who pastes their whole file in |
| Per-student rate | ~60/hour | Generous for a 45-minute session; stops a runaway loop |
| Global per day | ~2,000 | Backstop if something goes wrong overnight |
| Request timeout | 15s | Their page shouldn't hang forever |

### The kill switch

```bash
AI_PROXY_ENABLED=false
```

When false, return `{"text": "The class AI is turned off right now."}` and log the attempt. You want this to exist before you need it, and needing it is not hypothetical — if something goes sideways mid-session you want one command, not a debugging session in front of a room.

### Logging

Every call, appended as JSON lines:

```json
{"ts": "2026-03-04T14:22:11Z", "student": "ada", "prompt": "...", "response": "...", "tokens_in": 412, "tokens_out": 88}
```

Log the **full prompt and the full response**. Not a hash, not a truncation. If a parent asks what their kid was doing, or a student shows a classmate something they shouldn't have, the log is the only record that exists. Keep it on the server, not in the repo, and delete it when the course ends.

Skim it after each AI session. It takes two minutes and it's the single best signal you'll get about what the room is actually doing.

---

## What students will try

Someone will try to make it swear. This is not a discipline problem, it's the most predictable thing in the entire course, and it happens in roughly the first ninety seconds of session 6.

Plan for it:

- **The system prompt handles most of it.** The model declines and offers something else.
- **The log catches the rest.** You'll see exactly who tried what.
- **Address it once, lightly, in the room.** "Yes, some of you tried. I can see all of it. Let's build something cooler." Moving on quickly is more effective than making it a moment.
- **Escalate only if it's aimed at a person.** Trying to make the robot swear is boundary-testing. Trying to make it write something cruel about a specific classmate is different, and is a `SAFETY.md` matter.

Session 7's AI-literacy lesson lands considerably better once they've already found the edges themselves.

---

## Checks before session 6

- [ ] `curl` it and get a sensible answer back
- [ ] Try a deliberately inappropriate prompt — does the system prompt hold?
- [ ] Exceed the rate limit on purpose — does it return the friendly `{"text": ...}` shape?
- [ ] Flip the kill switch, confirm it works, flip it back
- [ ] Confirm the log file is being written
- [ ] Set a billing alert on the API account
- [ ] Time a typical call. If it's over ~3 seconds, find out why before it's in front of students.
