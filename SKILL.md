---
name: pension-benchmark-comparison
description: Compares a public pension or retirement system's funding, investment, cost, and governance metrics against national and state/regional peer benchmarks, and presents the result as a structured chat summary (not an auto-generated file) so the user can decide how to disseminate it. Use this whenever the user asks to benchmark, compare, or evaluate a pension fund, retirement system, or public pension plan against peers, national averages, state averages, or similar systems -- including requests like "how does our plan compare to others", "benchmark this pension fund", "is our funded ratio good", "compare us to NCPERS", "how do we stack up against other plans in our state", or "update our peer comparison with the new quarterly report" -- even if the user doesn't name a specific benchmark source. Also use this to update a previously-run comparison with newer source documents (e.g. a new quarterly performance report or a new actuarial valuation).
---

# Pension / Retirement System Benchmark Comparison

## What this skill does

Takes a specific pension or retirement system's own source documents (financial statements, actuarial valuations, investment performance reports) and compares its key metrics against two tiers of peer data: a **national** benchmark (an industry-wide study) and a **state or regional** benchmark (a government oversight body's aggregated data, where one exists). The output is a well-organized chat response, not a file -- let the user ask separately if they want it turned into a doc, deck, or spreadsheet.

This skill generalizes a process that was developed comparing a Florida municipal police & fire pension plan against the NCPERS national study and the Florida Division of Retirement's statewide database. Don't assume those exact sources apply to a new request -- the plan type, country, state, and available benchmarks will vary. Treat the sources below as a template for the *kind* of thing to look for, not a fixed list.

## Step 1: Pin down the plan and gather its own numbers first

Before looking at any benchmark, get a solid picture of the plan itself. Ask the user for (or look for uploaded/linked):
- The plan's most recent **actuarial valuation** (funded ratio, discount rate, amortization method/period, contribution requirements, asset allocation target)
- The plan's most recent **audited financial statements** (GASB net pension liability, investment returns, expenses, contributions actually received)
- The plan's most recent **investment performance report** from its consultant (trailing returns over multiple periods, current asset allocation, benchmark comparisons)

If the user hasn't provided these, ask which plan and request the documents or links rather than guessing. If they've provided some but not others, proceed with what's available and note the gaps rather than blocking on completeness.

A pension plan reports the same underlying reality in more than one way depending on the standard used (e.g. actuarial-value-of-assets funded ratio vs. GASB market-value funded ratio; smoothed vs. market investment returns). Pull out **both** where available and keep them clearly labeled -- collapsing them into one number loses information the board or reader will want.

## Step 2: Find the right benchmark sources

**National benchmark.** For US public pensions, NCPERS (National Conference on Public Employee Retirement Systems) publishes an annual "Public Retirement Systems Study" with national aggregate statistics broken out by fiscal period and often by plan type (general/public safety/education) -- search for the current edition by name. For other plan types (corporate, multi-employer, non-US), search for the analogous industry-wide survey (e.g. Milliman's Public Pension Funding Index, a national actuarial society's survey, a country-specific regulator's aggregate report). Don't assume NCPERS is always the right source -- confirm it covers the plan's category before using it.

**State or regional benchmark.** Many US states have a pension oversight office that aggregates data across all plans it regulates and republishes it (sometimes as one-page "fact sheets" per plan, sometimes as a giant statewide appendix workbook). Search for "[state] department of [management services / retirement / financial services] local government retirement systems annual report" or similar. These offices often also publish a per-plan comparison sheet -- check the plan's own website's "Links" or "Resources" page, since plans are frequently required to link to their state oversight fact sheet directly. If no equivalent state/regional body exists (private-sector or non-US plans, for instance), skip this tier and say so rather than forcing a comparison that doesn't exist.

Always web-search for the *current* edition/year of these sources rather than assuming a URL pattern still works -- oversight bodies restructure their sites and publish new annual editions.

## Step 3: Watch for these specific traps

These come up often enough with government pension data that they're worth calling out explicitly:

- **Robots.txt blocks.** Some official document servers (state pension "fact sheet" hosts in particular) block automated fetching even though the document is public. If `web_fetch` returns a robots-disallowed error, don't give up on the source -- give the user the direct URL to open themselves, and offer to build the comparison from whatever they paste back, or search for a mirrored/alternate copy on a fetchable domain (e.g. the same document sometimes lives on both a legacy domain and a current one).
- **Huge statewide datasets get truncated on fetch.** A state's full multi-year appendix covering hundreds of plans is often too large for a single fetch to return in full, even with a high token limit. When this happens: fetch what you can, save it to a file, and compute statistics with Python (regex-parse rows, filter to the relevant plan type, compute mean/median) rather than eyeballing hundreds of rows -- eyeballing is where transcription errors creep in. Be upfront with the user about partial coverage (e.g. "this covers plans A through L alphabetically, roughly 45% of the state's police/fire plans") rather than presenting a partial sample as the full population.
- **Column misalignment in extracted PDF text.** Wide data tables extracted from PDFs sometimes have columns shift for individual rows (especially where a cell is empty or a text field varies in length). Cross-validate anything you're about to rely on heavily: if the plan's own actuarial valuation states its payroll growth assumption is 1.14%, and the state's extracted row shows two candidate numbers that could be payroll growth or investment return, use the plan's own document to disambiguate rather than trusting positional parsing blindly. When you can't cross-validate, say so and flag the number as approximate.
- **Different methodologies produce different "right answers."** A plan can look "well above average" on one funded-ratio methodology and "right at the median" on another, simply because the benchmark source computed the ratio differently (smoothed actuarial assets vs. market value; different liability measure; different peer sample). This is a genuine, reportable finding, not an error to reconcile away -- surface it and explain why the two numbers differ rather than picking one and hiding the other.
- **Fiscal year misalignment.** Different plans and different benchmark sources use different fiscal year-end dates. Use whichever period is closest to the plan's own reporting period, state the vintage explicitly, and flag when a same-year comparison isn't possible.

## Step 4: Compute the comparison

For each metric that's available on both sides, work out the plan's own figure and the peer figure(s), and characterize the gap in plain terms (well above average / roughly in line / below average), not just the raw numbers. Typical metrics to cover, when available:

- Funded ratio (on each basis the sources report)
- Discount rate / investment return assumption
- Investment returns over multiple trailing periods (1yr, 5yr, 10yr, 20yr or whatever periods the sources share)
- Asset allocation (equities / fixed income / alternatives / cash)
- Investment + administrative expenses (in basis points if possible)
- Contribution rates (employee / employer / total, as % of payroll)
- Amortization period and method
- Cost-of-living adjustment (COLA) policy, if applicable
- Any other metric that stood out during document review as unusual or noteworthy for this specific plan

## Step 5: Present the result as a chat summary

Default to a well-organized **conversational response**, not a file:

1. A short headline comparison table (metric / peer benchmark / this plan / assessment)
2. A section per metric or metric group with 2-4 sentences of plain-English interpretation -- why the number is what it is, and what it means for the plan
3. A bottom-line synthesis paragraph
4. A methodology note covering data vintage, coverage caveats, and any places where sources used different definitions

Do not proactively create a Google Doc, Word doc, PDF, or other artifact. The point of a chat summary is that the user decides how (or whether) to disseminate it -- copy it into an email, paste it into their own template, or come back and explicitly ask for a formatted document if that's what they want. If they do ask for a document afterward, that's a separate, explicit step -- build it from the same underlying numbers rather than re-deriving anything.

## Updating a previous comparison

If the user wants to refresh a comparison with a newer quarterly report, valuation, or benchmark edition, re-run Steps 1-4 with the new documents rather than assuming last quarter's extracted numbers still hold -- funded ratios, returns, and assumptions all change. Reuse the general benchmark data only if the underlying study/report hasn't been updated to a newer edition; check whether a newer edition exists before reusing old benchmark figures.

## Running this for a different plan

Nothing above is specific to any one plan, state, or country. To run this for a new plan: repeat Step 1 with that plan's own documents, and repeat Step 2 to find the appropriate national and state/regional benchmarks for that plan's category and jurisdiction -- don't reuse a previous plan's benchmark sources without checking they're the right peer group.
