# Ship It — Server Setup

Everything needed to run the class on one VPS. Budget a weekend the first time; it's scripted and reusable after that.

> `class.example.dev` stands in for your domain throughout. Replace it everywhere.

---

## What you're building

| Address | What it is |
|---|---|
| `class.example.dev` | Class homepage — links to all eight student sites |
| `ada.class.example.dev` | Ada's website (the thing she shows her friends) |
| `code-ada.class.example.dev` | Ada's browser editor |
| `ai.class.example.dev` | The class AI proxy (see [`AI_PROXY.md`](AI_PROXY.md)) |

One VPS runs all of it. Students install nothing and have accounts nowhere.

### Sizing

**4 vCPU / 8 GB / 40 GB disk** comfortably runs eight students.

The memory is dominated by code-server, at roughly 300–400 MB per idle instance. Eight of those plus eight Flask apps plus Caddy and the two small services lands around 3.5 GB in practice, which leaves real headroom for the moment when three students all have an infinite loop at once. 4 GB will technically work and will make you nervous. The CPU matters less than you'd think — this workload is almost entirely idle.

### Stack

- Ubuntu 24.04 LTS
- [Caddy](https://caddyserver.com/) — reverse proxy, automatic HTTPS
- [code-server](https://github.com/coder/code-server) — VS Code in the browser, one per student
- Flask, one app per student, under systemd
- Two small services of your own: the **oops server** and the **AI proxy**

### DNS

Two records, both pointing at the VPS:

```
class.example.dev.      A    203.0.113.10
*.class.example.dev.    A    203.0.113.10
```

For the wildcard certificate, Caddy needs a DNS-01 challenge, which means a Caddy build with your DNS provider's plugin and an API token. Alternatively — and more simply — let Caddy issue a separate certificate per hostname over HTTP-01. With eight students that's 18 certificates, which is well inside Let's Encrypt's rate limits and requires no plugin. **Start with per-host certificates.** Reach for the wildcard only if you're running several classes.

---

## The oops server

**Build this. It is the highest-value 60 lines in the whole setup.**

Flask's debug mode renders a beautiful traceback in the browser — but only for errors raised *while handling a request*. A syntax error is different: the app can't start at all, so there's nothing to render the traceback, and Caddy returns a bare `502 Bad Gateway`.

A missing quote mark is the single most common beginner mistake. Without a fix, the most common error in the class produces the least useful message in the class, and every instance of it requires you to walk over.

The fix: each student's app logs to a file, and when their app is down, Caddy serves a page that shows them that file.

**Caddy side**, per student:

```caddyfile
ada.class.example.dev {
    reverse_proxy localhost:9001
    header X-Robots-Tag "noindex, nofollow"

    handle_errors {
        rewrite * /oops/ada
        reverse_proxy localhost:8500
    }
}
```

**systemd side** — send both streams to a file the oops server can read:

```ini
StandardOutput=append:/var/log/shipit/ada.log
StandardError=append:/var/log/shipit/ada.log
```

**The service** listens on `8500`, serves `/oops/<student>`, reads the last ~40 lines of `/var/log/shipit/<student>.log`, and renders them in a friendly page:

> ### Your app isn't running right now
> Here's the last thing Python said:
> ```
> File "/home/ada/app/app.py", line 7
>     name = "Ada
>            ^
> SyntaxError: unterminated string literal (detected at line 7)
> ```
> **Fix it in your editor and hit save.** This page will fix itself.

Match the tone to the room. The last line of that traceback is exactly what you taught them to read in session 1, so the lesson transfers with no new explanation.

Add a `logrotate` entry for `/var/log/shipit/*.log` — the Werkzeug reloader is chatty and these grow.

---

## Per-student layout

For a student `ada` at index 1:

```
/home/ada/
  app/                       ← their project; the editor opens here
    app.py
    templates/{base,index}.html
    static/style.css
    storage.py
    guestbook.db             ← appears in session 5
  venv/                      ← their own Python environment
  .config/code-server/config.yaml
```

| Thing | Port |
|---|---|
| Flask app | `9000 + index` → 9001 |
| code-server | `8100 + index` → 8101 |

Both bind to `127.0.0.1` only. Caddy is the sole thing facing the internet.

### code-server config

`/home/ada/.config/code-server/config.yaml`:

```yaml
bind-addr: 127.0.0.1:8101
auth: password
password: <the password on their card>
cert: false
```

### The two units

`/etc/systemd/system/app-ada.service`:

```ini
[Unit]
Description=Ship It app for ada
After=network.target

[Service]
User=ada
WorkingDirectory=/home/ada/app
Environment=FLASK_APP=app.py
Environment=FLASK_DEBUG=1
ExecStart=/home/ada/venv/bin/flask run --host 127.0.0.1 --port 9001
Restart=always
RestartSec=3
StandardOutput=append:/var/log/shipit/ada.log
StandardError=append:/var/log/shipit/ada.log
MemoryMax=512M
CPUQuota=50%

[Install]
WantedBy=multi-user.target
```

`FLASK_DEBUG=1` is what gives you the in-browser traceback and the reload-on-save loop that the whole curriculum is built around. It would be dangerous on a real production server; here it is the entire point.

`MemoryMax` and `CPUQuota` are not optional. A `while True:` in a view function is a normal thing for a 12-year-old to write, and without limits one student can make the class unusable for the other seven. With them, one student's site gets slow and everyone else is fine.

`/etc/systemd/system/code-ada.service` is the same shape:

```ini
[Service]
User=ada
Environment=HOME=/home/ada
ExecStart=/usr/bin/code-server --config /home/ada/.config/code-server/config.yaml /home/ada/app
Restart=always
RestartSec=3
MemoryMax=1G
```

---

## `provision.sh`

One idempotent script, run as root, taking a roster file (one username per line). Re-running it must be safe — you will re-run it.

For each student it should:

1. Create the Linux user with no shell login (`--shell /usr/sbin/nologin`); they only ever reach the box through code-server.
2. Create `venv` and `pip install flask requests`.
3. Copy `starter/` into `~/app` — **only if `~/app` doesn't already exist.** Guard this hard; re-running the script must never clobber a student's work.
4. Write the code-server config with a generated password.
5. Write and enable both systemd units.
6. Append the student's two blocks to the Caddyfile.
7. Create `/var/log/shipit/<name>.log`, owned appropriately.

Then, once across all students: regenerate the class homepage, `caddy reload`, and print the credential cards as a CSV you can mail-merge.

**Generate passwords, don't invent them.** Three common words (`orange-piano-42`) beats random characters by a wide margin — a student who can't type their password is a student who isn't coding, and these guard a sandbox, not a bank.

---

## The class homepage

Static HTML at `class.example.dev`, generated from the roster. Eight big links, one per student, first names only.

It looks like the least important file here. It isn't. It's what makes the class a room full of people who made things rather than eight people who each made a thing, and it's what you put on the projector in sessions 1, 3, and 5. It's also how you keep an eye on every site at once.

Add `noindex` here too.

---

## Checkpoints

The mechanism that makes the curriculum's "nobody starts a session behind" promise real.

**`snapshot.sh N`** — run right after each session:

```bash
for user in $(cat roster.txt); do
  tar czf /srv/checkpoints/s$N/$user.tar.gz -C /home/$user app
done
```

**`restore.sh <student> <N>`** — moves their current `app/` aside (never delete it; they may want something out of it), extracts checkpoint N, restarts their unit. Should take about ten seconds and be something you can do calmly while standing behind a student.

You'll use this more than you expect in weeks 1–3 and almost never after that.

---

## Backups

Checkpoints are for teaching. Backups are for disasters. They are not the same thing and one doesn't substitute for the other.

Nightly cron: tar every `/home/*/app` into `/srv/backups/$(date +%F)/`, keep 30 days, and copy off the box — object storage, or `rsync` to your own machine. A checkpoint on the same disk as the thing it's backing up is not a backup.

---

## Before the first session

Provision, then actually use it as a student would:

- [ ] Log into a student's editor from a **different** network on a **Chromebook** (or a locked-down profile). The school's network is not your network.
- [ ] Edit `app.py`, save, refresh the site. Did it reload?
- [ ] Introduce a syntax error deliberately. **Do you get the oops page or a raw 502?** If it's a 502, the oops server isn't wired up — fix it before day one.
- [ ] Introduce a runtime error. Do you get the Werkzeug traceback?
- [ ] Hit the AI proxy.
- [ ] Open the class homepage and click every link.
- [ ] Snapshot and restore one student to prove the mechanism works.
- [ ] Check HTTPS on every hostname.

## Before every session

Fifteen minutes, in the morning — not at 3:05pm.

```bash
# every site and every editor responds
for u in $(cat roster.txt); do
  curl -so /dev/null -w "$u app:%{http_code} " https://$u.class.example.dev/
  curl -so /dev/null -w "code:%{http_code}\n" https://code-$u.class.example.dev/
done
```

Then: last night's backup exists, last session's checkpoints are in place, the AI proxy answers, and disk isn't full.

---

## When it goes wrong

| Symptom | Likely cause | Fix |
|---|---|---|
| One student's site 502s | Their app won't start | They should be seeing the oops page. If not, `systemctl status app-<user>` |
| Oops page instead of the real site | App is down — working as intended | Read the traceback with them. This is the feature. |
| Everything is slow | Someone's infinite loop, or memory pressure | `systemd-cgtop`. The `CPUQuota` should be containing it; if not, tighten it. |
| Editor won't load | code-server died | `systemctl restart code-<user>` |
| Cert errors on a new hostname | HTTP-01 challenge failed | Check DNS propagation and that :80 is open |
| Student deleted everything | It happens | `restore.sh <student> <last N>` |
| **Whole VPS is down on class day** | — | Break-glass below |

### Break-glass

If the server is down and you're standing in front of eight students, don't debug in front of them. Have a plan on paper:

1. **Code as a group on the projector**, on your own laptop. Do the session's content as a live build with students calling out the next line. It's a genuinely good session, just a different one.
2. **A pre-made Colab notebook** covering the session's Python concepts without the web layer. Make it once, keep the link in your phone, hope you never use it.

Then fix the server that evening and restore from checkpoints.
