---
name: ga4-report-builder
description: Build, validate, and organize Google Analytics 4 custom reports (detail reports, summary cards, overview reports, collections) starting from a product event dictionary. Covers events, event parameters, custom dimensions and metrics, report filters, DAU/MAU, new users, retention, feature usage, and error rankings in the English GA4 interface, including driving a signed-in property directly in the browser.
---

# GA4 Report Builder

Most GA4 reporting failures aren't reporting failures. They're modeling mistakes that only surface once someone reads the numbers: a parameter registered with the wrong type, a dimension named after the event instead of the value it actually holds, a filter that silently drops half the dataset. This skill works backwards from the business question to a report that means what the reader assumes it means.

## Workflow

1. Write down the question and the measurement definition behind it. "Active users" and "users who did something" are not the same number, and the gap only shows up later.
2. Read the event dictionary. If there isn't one, build a working version from the property itself (see below). Map the question onto representative event names, categorical parameters, numeric parameters, and whichever built-in dimensions and metrics already cover it.
3. Consult `references/ga4-data-model.md` before proposing any new custom definition.
4. Pull the matching recipe from `references/report-recipes.md` — growth, feature usage, retention, errors.
5. Build the report.
6. Say what the data can't answer. Missing events, missing parameters, wrong parameter scope, a registration date that postdates the range, GA4's own aggregation behavior: any of these can make a report technically correct and practically useless.

For direct browser work, read `references/chrome-ga4-workflow.md` first, then confirm the account and property, inventory what already exists, and verify each save against what GA4 actually displays.

## Building a dictionary when there isn't one

Plenty of teams have no event dictionary, or have one that stopped matching reality two releases ago. You can reconstruct a usable one from the property in a few minutes, as long as you're honest about what it does and doesn't tell you.

`Admin → Data display → Events` lists every event name the property has received, with counts and users over the selected range. This gives you the complete event surface. Extend the range before trusting it — an event fired only by a monthly job won't appear in a 7-day window.

`Admin → Data display → Custom definitions` lists the parameters someone already registered, along with their scope and the underlying parameter name. These are the parameters you can put in a report today without registering anything new.

Between those two pages you get event names and registered parameters. What you don't get is **unregistered parameters** — GA4's admin screens simply don't enumerate them. To see those you need `Admin → DebugView` with a device in debug mode, the app's own tracking code, or a BigQuery export if the property has one linked. Say which method you used, because a dictionary built from DebugView reflects one session on one device, not the whole app.

Two things to record for every event you list: whether it's a GA4 automatic or enhanced-measurement event rather than a custom one, and the registration date of any custom definition attached to it. Both change what a report can show, and both are invisible once you're inside the report editor.

When the reconstructed dictionary has gaps, report the gaps. A dictionary that is quietly 60% complete produces reports that are confidently wrong.

## Modeling

The default answer to "should I register a custom definition?" is no. GA4 gives you `Event name` for free, and custom definitions are capped, non-retroactive, and awkward to unwind.

Register a custom dimension when an existing categorical parameter needs to appear in reports. Register a custom metric when an existing numeric parameter needs aggregating. Nothing else qualifies. In particular, never create a custom dimension just to give an event a friendlier label.

Name definitions after what the parameter means, not after the event that emits it. This matters more than it sounds: a dimension called after `<feature_event>` but bound to `<duration_parameter>` will group rows by duration values, and everyone reading it will assume otherwise.

Some smaller rules that keep coming up:

- Keep the word `count` out of dimension names. Counting is what `Event count`, `Total users`, and `Event count per active user` are for.
- Use a report filter to narrow the report to the events that actually answer the question. Don't rely on the reader to mentally exclude rows.
- `(not set)` is a data-quality signal, not noise. Before excluding it, check the definition's registration date and how completely the parameter is populated.
- Custom definitions don't backfill. Registering one today does nothing for last month's standard reports.
- Never sum daily active users into MAU. Use active users across a 30-day range, or a rolling metric built for it.
- Pin down what retention means before building anything: exact Day N, rolling, cumulative, or simply returning within a window.

## Changing an existing property

Someone else's reports are probably load-bearing. Preserve existing reports, collections, filters, and custom definitions unless replacement or removal was explicitly requested, and check for an equivalent report before creating a new one.

Prefer `Save as a new report` over overwriting.

A confusing custom definition is not grounds for archiving it. Confirm the parameter type is genuinely wrong and that the user authorized the change. The same goes for publishing a collection, replacing `Reports snapshot`, or touching access settings: none of that happens without explicit authorization.

If a request needs tracking that doesn't exist, report the gap. Do not invent a definition to make the report buildable.

## Handling the user's data

Event dictionaries, event and parameter names, parameter values, account and property names, IDs, report results, screenshots, URLs: treat all of it as confidential, and use it only for the task at hand.

None of it should ever end up in this skill, in reusable examples, in public artifacts, logs, filenames, or source-control metadata. When writing anything reusable, substitute placeholders like `<feature_event>`, `<category_parameter>`, `<property_name>`.

Before packaging or publishing, scan every file and archive in it for task-specific names, numeric IDs, URLs, email addresses, dates, and observed metric values.

## English interface labels

All click paths assume the English UI:

```
Admin → Data display → Events
Admin → Data display → Custom definitions
Admin → DebugView
Reports → Library
Explore → Cohort exploration
Create new report → Create detail report
Create new report → Create overview report
Report data → Dimensions
Report data → Metrics
Report filter → Add filter
Summary cards → Create new card
Collections → Create new collection
```

## Before calling a report done

Check that the intended property is selected, that the default dimension matches the question, and that the metrics have compatible scopes. Then look at the output itself: do the filters include exactly the intended events, have the unexplained `(not set)` rows resolved, is the sort order the ranking that was asked for?

Totals need their own moment of attention. GA4's total row deduplicates users; it is not the sum of the rows above it, and reports get misread on this constantly.

Finally, give the report a name and description someone else can act on, wait for GA4's save confirmation, and confirm the report appears in `Library`.

## Explaining the result

A detail report has four moving parts, and describing them in this order tends to land:

> The filter decides which data gets in. The dimension decides how it's grouped. The metric decides what's calculated. Sorting decides what rises to the top.

Before making browser changes, state which property is selected and what will be left untouched. Afterwards, report only saves that were confirmed, and keep finished reports clearly separate from work you're recommending as a follow-up.
