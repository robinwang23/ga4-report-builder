# GA4 Report Builder

A portable agent skill for building GA4 custom reports that mean what they appear to mean.

It's plain Markdown. No runtime, no dependencies, no install step beyond putting the folder somewhere your assistant reads skills from. If you'd rather do the clicking yourself, it also works as a straightforward GA4 playbook.

## The problem it solves

GA4 makes it easy to build a report that is technically valid and quietly wrong. A parameter registered as a metric when it should have been a dimension. A custom dimension named after an event but bound to a duration. Daily active users summed into a monthly figure that's inflated by every returning user. Rows collapsing into `(not set)` because the selected dimension only exists on some of the filtered events.

None of these throw an error. They just produce numbers that people act on.

This skill starts from the business question, decides what belongs in the data model before anything gets registered, and checks the output against the ways GA4 reports are commonly misread.

## What's in it

Four report recipes (user growth, feature usage, error ranking, retention), each with its dimension, metric set, filter shape, and the specific misreading it invites.

A decision table for the modeling step: what deserves a custom dimension, what deserves a custom metric, and what GA4 already gives you for free. The default answer is "you don't need a custom definition," because definitions are quota-limited and never backfill.

Click paths for the English GA4 interface, usable either as instructions for an agent with browser control or as steps you follow by hand.

## Guardrails

Custom definitions can't be un-registered cleanly and don't apply retroactively, so the skill is deliberately cautious about changing a live property:

- existing reports, collections, filters, and definitions stay untouched unless you ask for them to be replaced
- `Save as a new report` is preferred over overwriting someone else's work
- publishing a collection, replacing `Reports snapshot`, archiving a definition, or changing access all need explicit authorization
- reports are verified in `Library` before being reported as done

If a request needs tracking that doesn't exist, it tells you that instead of inventing a definition to make the report buildable.

## Your data stays yours

Event dictionaries, parameter names, account and property names, IDs, report figures, screenshots, and URLs are treated as confidential and used only for the task at hand. Nothing gets written back into the skill files.

The reference documents use placeholders like `<feature_event>`, `<category_parameter>` and `<error_category_dimension>`, resolved against your own property at run time.

## Install

```bash
git clone https://github.com/<your-username>/ga4-report-builder.git
```

Move or symlink the folder into wherever your assistant loads skills from. The exact location varies by platform, though most follow some variation of a `skills/` directory in the tool's config folder.

No skill mechanism at all? Feed `SKILL.md` in as context, or read it yourself. The modeling rules and click paths stand on their own.

## What you'll need

- Editor access to the GA4 property. Creating reports in `Library` requires it.
- An event dictionary. Failing that, whatever events and custom definitions are already visible in the property.
- The GA4 interface set to English. Every click path assumes English labels.
- For hands-off operation, browser control plus a signed-in session. Without it you get exact click paths to follow manually.

## Layout

```
SKILL.md                            workflow, modeling rules, safety constraints
references/ga4-data-model.md        decision table, naming, scope, traps
references/report-recipes.md        the four recipes, overview assembly
references/chrome-ga4-workflow.md   click paths and save verification
```

## License

MIT — see [LICENSE](LICENSE).
