# Python Cheat Sheet

One page. Everything this class uses. Print double-sided, one per student.

---

## Remembering things

```python
name = "Ada"          # text — it has quotes
age = 12              # a number — no quotes
is_cool = True        # yes or no
```

Text can't do math. Numbers can't be glued to words without help. That's mostly why we tell Python which is which.

## Filling in the blanks

```python
greeting = f"Hi, I'm {name} and I'm {age} years old."
```

The `f` means **fill in the blanks**. Anything in `{curly braces}` gets swapped for what's in that variable.

You can do math inside the braces:

```python
f"I'll be a teenager in {13 - age} years."
```

**Forgot the `f`?** The braces show up on your page as literal `{age}`. This will happen to you. It happens to everyone.

## Choosing

```python
if age >= 13:
    status = "I'm a teenager!"
elif age == 12:
    status = "Almost!"
else:
    status = "Not yet."
```

| | means |
|---|---|
| `==` | is the same as (**two** equals signs) |
| `!=` | is not the same as |
| `>` `<` | bigger / smaller |
| `>=` `<=` | bigger or equal / smaller or equal |

**The indented lines belong to the `if`.** Python cares about this a lot.

## Lists

```python
favorites = ["tacos", "Minecraft", "my dog Rex"]

favorites.append("pizza")     # add to the end
len(favorites)                # how many? → 4
favorites[0]                  # the first one → "tacos"   (counting starts at 0!)
sorted(favorites)             # alphabetical
```

## Doing something to every item

In `app.py`:

```python
for thing in favorites:
    print(thing)
```

On your page (`index.html`):

```html
{% for thing in favorites %}
  <li>{{ thing }}</li>
{% endfor %}
```

Three things in the list, three lines on the page. Add a fourth thing — four lines. You don't touch the HTML.

## Pages and addresses

```python
@app.route("/")                    # the main page
def home():
    return render_template("index.html", name=name)
```

**Every single thing you want on the page has to be passed in.** `name=name` looks silly and it's the number one reason something doesn't show up.

## Forms

```html
<form action="/sign" method="POST">
  <input name="message">
  <button>Send</button>
</form>
```

```python
@app.route("/sign", methods=["POST"])
def sign():
    text = request.form["message"]
    return redirect("/")
```

The `name="message"` on the box and the `["message"]` in the code **must be the same word.**

## Saving things

```python
import storage

storage.save("hello")      # into the filing cabinet
storage.load_all()         # everything back out, newest first
```

A plain list is a whiteboard — wiped every time your app restarts. `storage` is a filing cabinet.

## Asking the AI

```python
import requests

def ask_ai(question):
    r = requests.post("https://ai.class.example.dev/ask", json={"prompt": question})
    return r.json()["text"]
```

The prompt is just text you built. Change the text, change what it does:

```python
prompt = f"You are a grumpy cat. Answer in one sentence: {question}"
```

---

# When it breaks

**Read the last line of the error.** It says what went wrong and which line.

| The error says | It usually means |
|---|---|
| `SyntaxError` | A missing quote, bracket, or colon — often on the line *above* the one it names |
| `IndentationError` | Spacing is off. Delete the whitespace and retype it. |
| `NameError: 'x' is not defined` | Typo in a name, or you used it before you made it |
| `KeyError: 'message'` | The `name=` on your input doesn't match the `["..."]` in your code |
| `TypeError` | Doing math on text, or text-ing a number |
| `405 Method Not Allowed` | Your route needs `methods=["POST"]` |
| `400 Bad Request` | The form sent something the route didn't expect |
| **Nothing shows up, no error at all** | You forgot to pass it into `render_template` |

That last row is the sneakiest one. No error, no content, nothing to read. **Check `render_template` first.**

---

```
Change it → save it → refresh it.
Stuck? Read the last line. Then ask two people. Then ask the teacher.
```
