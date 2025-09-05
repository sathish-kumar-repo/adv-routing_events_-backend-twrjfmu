# That error means your Express app is trying to load events.json, but on Vercel it can’t find the file (wrong path).

## Why it Happens

- On Vercel, process.cwd() (current working directory) = project root.

- If you use just "events.json", Node looks in the wrong place (inside /api when the file might be in /data).

- Also, remember: you can read JSON files on Vercel, but you can’t persist changes.

# ✅ Fix: Use path.join

Always build the path relative to the current file:

```js
const fs = require("fs");
const path = require("path");

// if your events.json is inside a "data" folder in project root:
const filePath = path.join(__dirname, "..", "data", "events.json");

app.get("/events", (req, res, next) => {
  try {
    const data = fs.readFileSync(filePath, "utf-8");
    res.json(JSON.parse(data));
  } catch (err) {
    next(err);
  }
});
```

# 📂 Correct Folder Structure Example

```
project-root/
 ├─ api/
 │   └─ server.js
 ├─ routes/
 │   └─ events.js
 ├─ data/
 │   └─ events.json   ← ✅ your file here
 ├─ vercel.json
 ├─ package.json
```

# ⚠️ Reminder

You can read events.json fine on Vercel.

If you try fs.writeFileSync(filePath, ...), it won’t persist between requests (since Vercel’s filesystem is read-only).
