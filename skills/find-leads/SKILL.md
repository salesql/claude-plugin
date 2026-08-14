---
name: find-leads
argument-hint: "[business description or URL]"
description: End-to-end lead prospecting with SalesQL. Research a business's ideal customer profile (ICP), translate it into SalesQL search filters, find matching people, and reveal verified emails and phone numbers. Use this whenever the user wants to find leads, prospects, potential customers, or contacts for a business — even if they only give a company description, a website URL, or a vague ask like "who should I sell this to?". Also use it when the user mentions ICP research, building a lead list, or filling a pipeline.
---

# Find Leads

Turn a business description or website URL into a list of qualified leads with verified contact data, using the SalesQL MCP tools.

The user's input is `$ARGUMENTS`: either a description of their business ("we sell payroll software for restaurants in the US") or a URL to research. If `$ARGUMENTS` is empty, ask what business or product they want to find leads for before doing anything else.

## Why this order matters

SalesQL charges credits only when revealing real contact data (`enrich_person`). Everything before that — resolving filters, searching, browsing obfuscated results — is free. So the workflow front-loads all the free research and iteration, and only spends credits at the very end, on leads the user has approved. Never invert this: enriching before the user confirms the list wastes their credits on contacts they may not want.

## Step 1 — Understand the business

If `$ARGUMENTS` contains a URL, fetch it (and, if needed, one or two obvious subpages like /pricing or /customers) to understand what the company sells, to whom, and at what price point. If it's a description, work from that, supplementing with web research only when the description is too thin to infer a customer profile.

From this, draft an ideal customer profile (ICP):

- **Job titles** of the buyer and/or decision maker (include synonyms and seniority variants — "Head of Sales", "VP Sales", "Sales Director")
- **Industries** of the target companies
- **Geography** (countries, and states/cities if the business is regional)
- **Company size** (employee ranges) and, when relevant, **company type** (private, public, nonprofit…)

Present the ICP to the user in a short summary and ask them to confirm or adjust it before searching. This checkpoint is cheap and prevents an entire search built on a wrong assumption.

## Step 2 — Resolve filter values with `lookup`

`search_people` rejects guessed identifiers. Industry values are slugs, and country/region values are opaque UUIDs — they cannot be invented or translated. Resolve every non-free-text filter through `lookup` first:

| ICP dimension | `lookup` type | `search_people` field(s) |
| --- | --- | --- |
| Industry | `industry` | `industries`, `organization_industries` |
| Country | `country` | `countries`, `organization_countries` |
| State / city | `region` | `states_or_cities`, `organization_states_or_cities` |
| Company size | `company_size` | `organization_employee_ranges` |
| Company type | `company_type` | `organization_company_types` |

Use the `q` parameter for fuzzy search on the dynamic types (`industry`, `country`, `region`). Always pass the returned `value` to search filters — never the `label`. Job titles are free text and need no lookup; pass the title variants directly to `job_titles`.

`lookup` consumes no credits, so resolve everything you need without worrying about cost.

## Step 3 — Search with `search_people`

Build the search from the resolved values. Sensible defaults:

- Filter on the **person's** attributes (`job_titles`, `countries`) for who they are, and the **organization's** attributes (`organization_industries`, `organization_employee_ranges`) for where they work.
- Add `has_emails: true` (or `has_emails_verified: true` when the user cares about deliverability) so results are actually contactable.
- Start with `page: 1` and the default `page_size` of 20.

Results come back with **obfuscated** contact data (e.g. `...@acme.com`) — this is expected and free. `is_total_accurate` is only reliable on page 1.

Iterate on the filters, not on pages:

- **Too few results** (or zero): broaden — drop the narrowest filter, add title synonyms, widen the employee range.
- **Too many / off-target results**: tighten — add industry or size filters, use the `{include, exclude}` form to exclude noise (e.g. exclude "Assistant" titles).
- Spot-check a handful of names and companies against the ICP before accepting the search as good.

## Step 4 — Present the shortlist and get approval

Show the user the candidate leads in a table: name, title, company, location, LinkedIn URL, and what contact data exists (obfuscated). State clearly how many they asked for versus how many the search found.

Then ask which leads to reveal. This is the credit-spending gate — wait for an explicit answer. If the user wants many leads revealed, check `get_account_status` first and tell them how many credits they have before proceeding.

## Step 5 — Reveal contacts with `enrich_person`

For each approved lead, call `enrich_person` with the person's `linkedin_url` from the search result. This is the reliable join key between search and enrichment — fall back to `full_name` + `organization_name` (or `organization_domain`) only when a result has no LinkedIn URL.

Deliver the final list as a table: name, title, company, verified emails (with status), phones, LinkedIn URL. If the user wants a file, write it as CSV.

## Example

**Input:** `/salesql:find-leads we sell an AI code-review tool for mid-size software companies in Spain`

1. ICP: CTOs, VPs of Engineering, Heads of Engineering; industry "software development"; country Spain; 51–200 employees. User confirms.
2. `lookup` industry q="software" → slug; `lookup` country q="spain" → UUID; `lookup` company_size → "51,200" range value.
3. `search_people` with `job_titles: ["CTO", "VP of Engineering", "Head of Engineering"]`, resolved `organization_industries`, `countries`, `organization_employee_ranges`, `has_emails: true`.
4. Present 20 obfuscated matches; user picks 10.
5. `enrich_person` by `linkedin_url` for each; deliver the table with verified emails and phones.
