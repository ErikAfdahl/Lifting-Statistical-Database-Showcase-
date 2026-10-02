# L.S.D. — Lifting Statistical Database

*For lifters who want to see everything.*

**[Try the live app](https://liftingstatisticaldatabase.onrender.com/)**

A full-stack web app for logging workouts, tracking progress with estimated 1RM, and
getting data-driven recommendations for your next session. Built and maintained solo since
March 2026, and used regularly by about a dozen lifters.

![Run Program screen](Screenshot%202026-10-02%20134126.png)

## Why I built it

L.S.D. started as a Google Sheet I used to track my own lifts. Over time the spreadsheet
formulas got complicated enough that I wanted real control: a proper database, accounts,
and the ability to add any feature I wanted. So I rebuilt it as a web app.

## Features

- **Run Program:** Log sets for a workout, with each set showing the rep target, your last
  result, your 1RM and weight streaks, and a recommendation for today.
- **Smart autofill:** Inputs can be prefilled with the recommended weight and reps, so a
  typical set takes one click.
- **Results:** Set-by-set comparison of any two sessions of the same program.
- **Logs and analytics:** Summary statistics (mean, standard deviation, min, max) and
  time-series charts. Overlay any exercise and set on any other, and plot 1RM, weight,
  reps, or session-to-session differences.
- **Program management:** Build programs with per-exercise set counts, rep ranges, and
  weight increments.
- **Settings:** Choose what appears on screen, date format, autofill source, and default
  state of the "include" switches.
- **Quality-of-life:** Quick indexing (jump to the next exercise after the last set) and
  quick resume (return to where you left off).

## Why estimated 1RM instead of volume?

Volume (weight × reps) treats 400 lbs × 1 and 100 lbs × 4 as the same work, though they
are very different efforts. L.S.D. converts each set to an estimated one-rep max with the
Epley formula (`1RM = weight × (1 + reps / 30)`, with a single rep treated as the weight
itself) and tracks progress on that instead.

## How recommendations work

Each exercise has a rep range (min and max), a weight increment, and an "improvement
coefficient" (default 0.25) that defines what counts as a *significant* miss or overshoot.
The recommendation depends on the reps from your last session:

| Case | Condition (example: 6–8 range) | Recommendation |
|------|-------------------------------|----------------|
| 1 | Reps far above the max (> max × (1 + coefficient)) | Solve Epley for weight using last 1RM and **min** reps; round to increment |
| 2 | Reps far below the min (< min × (1 − coefficient)) | Same, using **max** reps |
| 3 | Reps at or above max | Same weight + one increment, min reps |
| 4 | Reps below min − 1 | Same weight − one increment, max reps |
| 5 | Reps from min − 1 to max − 1 | Same weight, one more rep |

Cases 1 and 2 are checked first, so large jumps are handled before the single-increment
adjustments. Setting min and max to 0 disables recommendations, and setting them equal
shows a target without recommending.

## Design decisions

- **Dates are stored in ISO format** and converted to the user's preferred format only
  at display time, so comparison and sorting always work.
- **Rep ranges are stored per set** (for example `6,0` / `8,0`), so each set in an
  exercise can have its own range without a separate table.
- **User preferences live alongside account data**, so settings follow the user across
  devices.
- **Passwords are hashed**, and a built-in feedback form lets users report problems
  without needing to contact me directly.

## Tech stack

| Layer | Technology |
|-------|-----------|
| Backend | Python, Flask |
| Frontend | HTML, CSS, JavaScript, [chart library] |
| Database | Supabase (PostgreSQL) |
| Hosting / deploy | Render, deployed from GitHub |

## Status and roadmap

Current version: 3.3.1. Planned work:

- [ ] Per-exercise choice of 1RM formula (the Epley estimate is less reliable at very high reps)
- [ ] Unit tests for the recommendation engine
- [ ] Allow users to share programs by sharing a unique 6-digit alphanumerical code corresponding to each program
- [ ] Allow users to select an interval of time to display on charts

## About

Built by [Erik Afdahl](https://www.linkedin.com/in/erik-afdahl-5a071b3a7), a Computer
Science and Statistics student at Gustavus Adolphus College. The source is private; I'm
happy to walk through the code or demo the app.
