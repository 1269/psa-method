# Adapters — driving tools at each access tier

How the skill actually touches the user's tools. The tier is recorded per tool in the config; this file is the how.

Platform note: AppleScript/JXA automation of local apps (OmniFocus, Apple Notes, Reminders, Evernote) is macOS-only. MCP- and CLI-backed tools are automatable on any platform; everything else falls back to the guided tier.

## Detecting the automated tier

A tool is `automated` when any of these is true in the current session:

1. **MCP server connected** for it (calendar, task, or notes MCPs — check the available tools).
2. **CLI available** — `which <binary>` succeeds (e.g. `gcalcli`, `todoist`, a vendor CLI). Record the binary in the tool's `notes` field.
3. **Scriptable macOS app** — the app actually has a scripting dictionary. Do NOT use `id of app "<Name>"` as the test: it resolves through Launch Services and succeeds for **any** installed app, scriptable or not (Notion passes it and has no scripting support at all). Check for a real dictionary instead:
   ```bash
   APP="/Applications/<Name>.app"
   ls "$APP"/Contents/Resources/*.sdef 2>/dev/null || \
     /usr/libexec/PlistBuddy -c "Print :NSAppleScriptEnabled" "$APP/Contents/Info.plist" 2>/dev/null
   ```
   Either succeeding → scriptable. Both failing → not automatable this way; the tier is `guided`.

When several are available, prefer MCP, then CLI, then AppleScript/JXA. When none are, the tier is `guided`.

**Demote on failure.** Detection can be wrong. If a tool classified `automated` fails on its first real write, don't retry blind: tell the user, fall back to guided instructions for the operation at hand, and offer to update the config to `"access": "guided"`.

## Worked example: OmniFocus (automated, JXA)

Battle-tested patterns. Two hard-won rules:

- **Always write JXA to a temp file** and run `osascript -l JavaScript <tmpfile>`. Never `osascript -e` — shell escaping corrupts scripts (e.g. `\t` in names becomes a tab).
- **Read ids with `.id()`**, not `.id.primaryKey()` — the latter throws `Can't convert types. (-1700)` inside `.map()` chains.

**List active projects (with ids — capture the id as the join key):**

```javascript
(function () {
  var app = Application("OmniFocus");
  var doc = app.defaultDocument;
  var projects = doc.flattenedProjects().filter(function (p) {
    return p.status() === "active status" || p.status() === "active";
  });
  return JSON.stringify(projects.map(function (p) {
    return { id: p.id(), name: p.name() };
  }));
})();
```

**Pull a project's open tasks by join key:**

```javascript
(function () {
  var app = Application("OmniFocus");
  var doc = app.defaultDocument;
  var proj = doc.flattenedProjects().find(function (p) {
    return p.id() === "<TASK_MANAGER_ID>";
  });
  if (!proj) return JSON.stringify({ error: "Project not found by id" });
  var tasks = proj.flattenedTasks().filter(function (t) {
    return !t.completed() && !t.effectivelyDropped();
  });
  return JSON.stringify(tasks.map(function (t) {
    return {
      id: t.id(),
      name: t.name(),
      note: t.note() || "",
      flagged: t.flagged(),
      tags: t.tags().map(function (g) { return g.name(); }),
      dueDate: t.dueDate() ? t.dueDate().toISOString() : null,
      hasSubtasks: t.tasks().length > 0
    };
  }));
})();
```

**Create a project inside a container folder:**

```javascript
(function () {
  var app = Application("OmniFocus");
  var doc = app.defaultDocument;
  var folder = doc.folders().find(function (f) {
    return f.name().indexOf("1 Projects") === 0;
  });
  if (!folder) return JSON.stringify({ error: "Container '1 Projects' not found" });
  var p = app.Project({ name: "<PROJECT_NAME>" });
  folder.projects.push(p);
  return JSON.stringify({ id: p.id(), name: p.name() });
})();
```

Create the project **in one statement** where possible; for writes (rename, note updates, completion), plain AppleScript is often more reliable than JXA:

```applescript
tell application "OmniFocus"
  tell default document
    set theProject to first flattened project whose id is "<TASK_MANAGER_ID>"
    set completed of theProject to true
  end tell
end tell
```

## Other automated patterns

- **Apple Reminders / Apple Notes / Apple Calendar** — same JXA/AppleScript approach; lists, folders, and calendars are the containers. Verify the app is scriptable before promising automation.
- **MCP-backed tools** (Google Calendar, Todoist, Notion, Asana…) — use the connected MCP tools directly; the three PSA containers are whatever the tool's API calls a top-level grouping. Record the server name in `notes`.
- **Evernote** — has AppleScript on macOS (notebooks = containers). When updating notes, always read-merge-write; never blind-overwrite a note body.

## The guided tier

When you can't drive the tool, you are the navigator and the user is the hands:

- One step at a time, exact names: "In Things, create an Area called `1 Projects`." Wait for "done" before the next step.
- Use the tool's real vocabulary (Areas in Things, Projects in Todoist, Notebooks in Evernote, databases or teamspace pages in Notion). If unsure what the tool calls its containers, ask the user what they see.
- After a create step, ask for any id/link the tool exposes (share link, copy-link menu item) and record it as the join key. If the tool exposes none, record the sentinel `task_manager_id: "name:<Project Name>"` in the Brief — the `name:` prefix means "resolve by name," and it is not a gap to warn about later.
- Batch reads ("paste your task list"), never batch writes — writes are confirmed one at a time.

**Database-shaped tools** (Notion, Airtable, Asana portfolios): these aren't hierarchical, so the three top-level containers map differently. Two sanctioned shapes — (a) three databases named `1 Projects`, `2 Systems`, `3 Archives`, or (b) one projects database with a `Stage` select property holding those three values. Ask which the user prefers, and record the chosen mapping in the tool's `notes` field so every later command knows how to read it.

## The markdown tier

No tool: the structure lives as files in the Second Brain (task files under each project's `tasks/`; the journal as `<second_brain>/Journal/` with the three stage subfolders). The skill operates on files directly, and there is nothing to keep in sync — the files are the single source of truth.
