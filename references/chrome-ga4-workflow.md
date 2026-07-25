# Working in the GA4 interface

## Before touching anything

Use the existing signed-in session rather than starting a fresh login. Confirm the account and property in the universal picker — this is the step that gets skipped, and building a week of reports in the wrong property is a genuinely expensive mistake.

Then open `Reports → Library` and take stock of what's already there. If a report equivalent to the one you're about to build already exists, leave it alone and say so.

## Creating a detail report

`Create new report` → `Create detail report` → `Blank`, unless a template happens to match the scope closely, which it usually doesn't.

Add the default dimension, then the metrics, then filters — filters only when the question genuinely targets a subset of events. Look at the rows and totals before saving; a report that's obviously wrong at this point is much cheaper to fix now than after it's in the Library and someone has linked to it.

Save with `Save as a new report`, give it a descriptive English name and description, and wait for `Report saved in Library`.

## Taking inventory of events and definitions

Before any modeling, open `Admin → Data display → Events` and widen the date range past the default. The list is the property's full event surface, but only for events that fired inside the range, so a short window will hide anything seasonal or infrequent.

Then open `Admin → Data display → Custom definitions`. Both tabs matter: the dimensions tab tells you which categorical parameters are already reportable, and the metrics tab tells you which numeric ones are already aggregating. Note the scope and the underlying parameter name for each, not just the display name — the display name is the part most likely to be wrong.

Neither screen lists unregistered parameters. When you need those, `Admin → DebugView` shows the full parameter payload for events arriving from a device in debug mode, one event at a time. It's the slowest option and it only reflects whatever that device happens to do, so treat it as sampling rather than enumeration, and say so when you report back.

## Cohort retention in Explore

The recipes cover detail reports, but retention with a custom inclusion criterion (users who purchased, users who completed onboarding, and so on) can't be built as one. It belongs in `Explore → Cohort exploration`.

Open `Explore`, pick the `Cohort exploration` template, then work through four settings in the left panel:

```
Cohort inclusion    what puts a user into the cohort
Return criteria     what counts as coming back
Cohort granularity  Daily, Weekly, or Monthly
Calculation         Standard, Rolling, or Cumulative
```

Inclusion offers first touch, all events, any transaction, any conversion, and specific events. Behavioral cohorts like "users who bought last month" come from picking the relevant event here, not from filtering afterwards. Return criteria works the same way and should usually be narrower than "all events" — otherwise you're measuring app opens, not retention of the behavior you care about.

Set the date range to cover the cohort period plus the full return window you want to observe. A 30-day range cannot show Day 30 retention for a cohort formed on day 29.

Two limits worth stating out loud before someone asks. Explorations live under `Explore`, not `Reports → Library`, so none of the Library verification steps apply and the usual "check it appears in Library" confirmation is meaningless here. And an exploration is private to whoever created it until it's explicitly shared, which surprises people who expected to send a link.

Cohort exploration is also subject to sampling and cardinality thresholds on large properties. When a result looks suspiciously round or a cohort collapses to zero, check for a sampling notice before interpreting it.

## Browser-control discipline

Take a fresh DOM snapshot after any navigation or state change that could invalidate your locators. GA4's report editor updates panels in place, so a locator captured before opening the dimension picker is not trustworthy afterwards.

Build locators only from the current visible state, and confirm one is unique before clicking or filling it.

Nothing gets overwritten, archived, published, or reassigned without authorization.

Leave the finished GA4 report tab open — it's part of the deliverable.

Account names, property names, IDs, event schemas, URLs, and report values visible on screen are confidential. None of them get transferred into reusable files or public documentation.

## Naming reports

Keep names short and descriptive:

```
User Growth & Activity
Feature Usage
Top Error Reasons
Product Analytics Overview
```

The description field should state the dimensions, metrics, and event scope, so the next person doesn't have to open the editor to find out what they're looking at.

## Confirming a save

Any of these counts as confirmation:

- the report heading changes to the saved name
- GA4 shows `Report saved in Library`
- the report appears as a row in `Library`

If they disagree with each other, reopen `Library` and verify the row is there before reporting the work as finished.
