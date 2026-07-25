# Mapping questions onto the GA4 data model

## Where things belong

| What you need | What to use | Example |
|---|---|---|
| Identify what happened | Event | `<feature_event>` |
| Group by event | Built-in dimension `Event name` | — |
| Describe a category | Event parameter, registered as a custom dimension | `<category_parameter>` |
| Aggregate a number | Event parameter, registered as a custom metric | `<numeric_parameter>` |
| Count occurrences | Built-in metric `Event count` | — |
| Count deduplicated users | Built-in metric `Total users` | — |
| Measure active users | Built-in metric `Active users` | — |
| Restrict the dataset | Report filter | selected event names |

Two of these rows are the ones people skip. Grouping by event needs no custom definition at all, and restricting the dataset is a filter, not a dimension.

## Naming

Names should describe what a parameter means to someone reading the report, not what it's called in the codebase. Derive the public-facing name from the current event dictionary rather than baking private schema names into the skill.

| Parameter's role | What to call it |
|---|---|
| Says what kind of thing occurred (`<category_parameter>`) | a readable category name |
| Says which narrower sub-type (`<subtype_parameter>`) | a readable sub-type name |
| Says what initiated the event (`<trigger_parameter>`) | a readable trigger-source name |
| Says how it turned out (`<result_parameter>`) | a readable result name |
| Classifies a failure (`<error_parameter>`) | a readable error-category name |
| Looks numeric but is really a label (`<code_parameter>`) | a readable code name, registered as a dimension |
| Marks a step in a multi-step process (`<stage_parameter>`) | a readable stage name |
| A number worth aggregating (`<numeric_parameter>`) | a readable quantity name, unit included |
| Elapsed time (`<duration_parameter>`) | a readable duration metric, correct unit |

The failure mode worth naming explicitly: don't name a dimension after an event when the bound parameter means something else. A dimension named for `<feature_event>` but bound to `<duration_parameter>` groups rows by duration. It looks like it represents the event. It doesn't.

## Scope

Event scope covers anything describing a single occurrence, which is nearly everything: `<category_parameter>`, `<trigger_parameter>`, `<result_parameter>`, `<error_parameter>`, `<duration_parameter>`.

User scope is only for stable user properties meant to characterize later activity. Item scope is only for item-level commerce data. Reaching for either one usually means the model is wrong.

## Traps

`Total users` in the total row is deduplicated across rows, so it won't equal the sum of the per-feature figures. This is correct behavior and a permanent source of confusion.

`Event count per active user` divides by active users, which is not the same population as users who triggered that particular event. The metric is still useful, but it isn't "average per user of this feature."

A report filtered to several events can show `(not set)` even when everything is configured properly, because the selected custom dimension only exists on some of those events.

A numeric identifier such as `<code_parameter>` is almost always categorical. Register it as a dimension unless doing arithmetic on the value would mean something, which for error codes and version numbers it doesn't.

Cumulative counters overcount. If an event emits `1`, then `2`, then `3` as separate occurrences, summing the parameter gives 6 for what was really 3 steps.
