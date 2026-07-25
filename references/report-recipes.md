# Report recipes

Four reports cover most of what product teams ask for. Each one below lists what to configure and, more usefully, how the result gets misread.

## User growth and activity

A detail report on `Date`, with `New users`, `Active users`, and `Total users`. Add `DAU / MAU` if the audience wants it. Line chart plus bar chart. No filter.

Daily `Active users` is your DAU. For MAU, set a 30-day range and read active users over that range — do not add up the daily values, since users active on multiple days would be counted once per day.

If you include the ratio metric, warn the reader that its total-row aggregation is close to meaningless even though the daily rows are fine.

## Feature usage

Default dimension `Event name`. Metrics: `Total users`, `Event count`, `Event count per active user`. Filter down to the events that actually represent the features in question.

Build that filter from the event names supplied in the current task, and don't copy them back into this file. The shape:

```regex
^(<feature_event_1>|<feature_event_2>|<feature_event_3>)$
```

Custom dimensions belong here only as optional drill-downs: `<result_category_dimension>`, `<trigger_category_dimension>`, `<subtype_category_dimension>`, `<interaction_category_dimension>`.

One case comes up often enough to plan for. When two business features emit the same event name, split them with a registered categorical parameter. When no such parameter exists, say plainly that the report cannot separate them — don't approximate.

## Error ranking

Default dimension `<error_category_dimension>`, filtered to error events only.

Useful secondary dimensions: `Event name`, `<error_code_dimension>`, `<error_source_dimension>`, `<error_stage_dimension>`, plus `App version` and `Device model` when you need to know whether a spike is a release problem.

Metrics are `Event count` and `Total users`, optionally `Event count per active user`. Sort by `Event count` for the loudest errors and by `Total users` for the ones hitting the most people — these two rankings often disagree, and the second is usually the one worth acting on.

Resolve each `<..._dimension>` placeholder against the custom definitions registered in the property you're working in. As with the filter events, neither the dimension names nor the event names get written back here.

```regex
^(<error_event_1>|<error_event_2>|<error_event_3>)$
```

Don't quietly drop `(not set)`. Quantify it and explain where it came from: data collected before the definition was registered, or a parameter that isn't being sent on every error event.

## Retention

Use GA4's native `Retention overview` for Day 1, Day 7, and Day 30. When the inclusion or return criteria need to be something else, go to `Explore → Cohort exploration`.

Either way, state four things: the inclusion criterion (usually first touch or `first_open`), the return criterion (any event, or one specific active event), the granularity, and whether the calculation is standard, rolling, or cumulative.

A custom detail report does not reproduce cohort retention. Don't claim it does unless the GA4 interface genuinely supports the cohort semantics being asked for.

## Assembling an overview

Build summary cards from detail reports you've already validated, then collect them into an overview. A reasonable set:

1. Daily new users and DAU
2. Feature users
3. Feature event count
4. Top errors by occurrence
5. Top errors by affected users
6. The native retention cards

Add the reports to a collection first. Custom summary cards won't all show up as overview candidates until you do.
