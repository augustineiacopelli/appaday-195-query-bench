# Query Bench | AppADay 195

Query Bench is a SQL workbench that runs in a browser tab. Open any SQLite file, browse its tables, write a query and see the result grid at once. Errors come back in plain English with the name you probably meant, history is kept, and results export to CSV. Read only until you say otherwise.

Live at https://augustineiacopelli.github.io/appaday-195-query-bench/ and part of the [AppADay portfolio](https://augustineiacopelli.github.io/appaday/). Day 195 is the second of six daily builds that assemble into the Query Desk milestone on Day 200.

## Using it

Tap **Load sample data** for a made up college with ten tables, or use **Open** to pick a project saved on this device (projects from CSV Intake appear there automatically) or any `.sqlite` file. The schema panel lists every table with its row count. Tap a name to insert it into the editor at the cursor, tap the chevron to see its columns with their types, and tap **100 rows** to preview a table without touching your query. On a phone the panel is a drawer behind the **Tables** button.

Write a query and press **Run**, or Ctrl or Cmd plus Enter. If text is selected, only the selection runs. The grid shows the first 200 rows with **Show 200 more** below, the status line gives the row count and time, and a result over 5,000 rows is cut there with a notice. Empty values show as a dimmed NULL. Errors come back as one plain sentence, so `SELECT * FROM studnets` answers that there is no table named studnets and asks whether you meant students. **Export CSV** downloads the current result.

**History** opens a sheet with two tabs. History lists the last 50 runs with their row counts or errors; Saved lists your saved queries with Load, Pin and a two tap Delete. **Save** takes a name and a note, and saving again under the same name updates the query you loaded. Both live inside the project file, so they travel with it. The editor text is kept per project between visits.

Nothing changes until you switch on **Allow changes**. The first time, a short sheet explains what that means. While it is on, INSERT, UPDATE, DELETE, CREATE and multi statement scripts run against the project, a write with no result reads Done with the number of rows changed, and the project saves itself. Tables that start with `_qd_` stay protected, and the switch turns off again whenever a project opens.

Settings, behind the gear, holds Clear history for this project, the saved projects, and the file size limits.

## How it is built

One `index.html`, vendored from the Query Desk workspace in [appaday-194-csv-intake](https://github.com/augustineiacopelli/appaday-194-csv-intake). The head carries the shell CSS (accent `#289664` on a dark neutral ground) and sql.js 1.16.0 loaded by relative path from the portal repository at `../appaday/vendor/sql.js-1.16.0/`. The first script block holds the shared core and clinic modules (`QD:CORE`, then `QD:CLINIC`), written by `tools/vendor.js` and never edited here. The second holds the interface. Reads go through `project.query()`, which checks the text and switches the engine to `PRAGMA query_only` for the statement. With changes allowed, a statement that fails the read check goes to `project.execute()`, and the app logs it to history itself with the rows changed.

The project bar, open sheet, Settings sheet and toast are copied forward from CSV Intake. Autosave starts once a project holds a table.

Not included by design: syntax highlighting, autocomplete, result tabs and undo.

## Checks

`test/e2e_query_bench.py` in the 194 repository drives this app in Chromium at 375 px: the sample loads with ten tables, the drawer opens and closes, the 100 row peek works, studnets suggests students, a DELETE is refused with changes off and works with them on, a saved query and history survive a reload, there is no sideways page scroll, and a project built in the CSV Intake page opens from this app's open sheet. `node tools/vendor.js --check` verifies the vendored modules match `core/`.
