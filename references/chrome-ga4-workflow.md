# Working in the GA4 interface

## Before touching anything

Use the existing signed-in session rather than starting a fresh login. Confirm the account and property in the universal picker — this is the step that gets skipped, and building a week of reports in the wrong property is a genuinely expensive mistake.

Then open `Reports → Library` and take stock of what's already there. If a report equivalent to the one you're about to build already exists, leave it alone and say so.

## Creating a detail report

`Create new report` → `Create detail report` → `Blank`, unless a template happens to match the scope closely, which it usually doesn't.

Add the default dimension, then the metrics, then filters — filters only when the question genuinely targets a subset of events. Look at the rows and totals before saving; a report that's obviously wrong at this point is much cheaper to fix now than after it's in the Library and someone has linked to it.

Save with `Save as a new report`, give it a descriptive English name and description, and wait for `Report saved in Library`.

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
