# /psa schedule — time-block ready work

Light calendar integration: take what's ready to work, find room in the user's actual week, and block time for it. The calendar is a PSA-aware component, not a space — this command reads the spaces and writes time.

## 1. Gather the work

- Task files in `status: "ready"` (armed) and `in-progress` across `<sb>/1 Projects/*/tasks/`, with priority and due dates.
- If the task-manager tier is `automated`, also pull flagged/due-soon tasks that have no task file.
- Ask the user what they want to schedule if the list is long: "These 6 are ready — which go on the calendar this week?"

## 2. Read availability (per calendar tier)

- `automated`: pull the next 5–7 days of events via the adapter; compute free blocks inside the user's working hours (ask once for working hours if unknown; suggest recording them in the config's `calendar.notes` for next time).
- `guided`, or no calendar (`"tool": "none"`): ask the user for their rough availability ("mornings free Tue/Thu, else after 3pm").

## 3. Propose blocks

Match work to blocks: high priority and due-soon first; deep work into the longest blocks; nothing over 90 minutes without a break; leave slack — don't fill every gap. Present the plan as a table (day, time, task, why then). Iterate until the user likes it.

## 4. Write (per tier, on approval)

- `automated`: create the events (title = task title, description = link/path to the task file). Confirm each created event.
- `guided`: list the events for the user to enter, one by one.
- no calendar (`"tool": "none"`): output the plan as text they can use anywhere.

Never move or delete existing events without being explicitly asked.
