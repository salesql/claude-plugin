# SalesQL for Claude

The official [SalesQL](https://salesql.com) plugin for Claude. You can use it in Claude Code, Claude chat and Cowork. Find B2B leads, reveal their emails (with verification status) and phone numbers, check whether addresses accept mail, and enrich people and companies, all from your conversation. The plugin connects Claude to the SalesQL MCP server and adds a guided **Find Leads** workflow.

## What the plugin contains and what it sends

- **One remote MCP server**: `https://mcp.salesql.com/mcp` (declared in [`.mcp.json`](.mcp.json)). All data the plugin reads or sends goes to and from this server, which SalesQL operates. Claude sends it your search filters and the people, companies or email addresses you ask about. The server returns results from your SalesQL account.
- **One skill**: [`find-leads`](skills/find-leads/SKILL.md). It contains instructions only. It runs no code, fetches nothing on its own and stores nothing.
- No local commands, hooks, scripts or API keys.

## Installation

**From the Claude Directory:** once the listing is live, open SalesQL in the Directory and add it. It works in Claude chat, Cowork and Claude Code.

**In Claude Code, from this repository:**

```
/plugin marketplace add SalesQL/claude-plugin
/plugin install salesql@salesql
```

## Authentication

The plugin signs in to SalesQL with OAuth, so there are no API keys to copy or paste.

1. In Claude Code, run `/mcp` and select **salesql**. In Claude chat or Cowork, select **Connect** on the SalesQL connector.
2. Your browser opens the SalesQL login page.
3. Sign in and approve the connection.

You need a SalesQL account. The MCP server works on every SalesQL plan, including Free. For other clients, a prompt library and troubleshooting, see the [SalesQL MCP docs](https://salesql.com/docs/mcp).

## Skills

| Skill | What it does |
| --- | --- |
| `/salesql:find-leads <business description or URL>` | End-to-end prospecting. It drafts your ideal customer profile, resolves the search filters, finds matching people, shows you a shortlist, and reveals contact details only for the leads you approve. |

Example:

```
/salesql:find-leads we sell an AI code-review tool for mid-size software companies in Spain
```

Claude also uses the skill on its own when your request matches, for example "help me find leads for my business".

## Tools

Once you're connected, ask Claude in plain language. The SalesQL MCP server provides these tools:

| Tool | What it does | Credits |
| --- | --- | --- |
| `lookup` | Resolves valid filter values (industry, country, region, company size, company type) | Free |
| `search_people` | Finds professionals by title, company, industry, location and more. Contact data comes back masked. | Free |
| `search_organizations` | Finds companies by name, industry, location, size, type and founding year | Free |
| `enrich_person` | Reveals one person's professional profile, emails and phone numbers | Up to 1 enrichment credit |
| `enrich_person_bulk` | Reveals up to 100 people in one request | Up to 1 enrichment credit per person found |
| `enrich_organization` | Gets one company's firmographic profile | Free |
| `enrich_organization_bulk` | Enriches up to 100 companies in one request | Free |
| `verify_email` | Checks whether one email address accepts mail | 1 verification credit |
| `verify_emails_bulk` | Starts a bulk verification job for up to 500 addresses | 1 verification credit per unique address |
| `get_email_verification_job` | Reads the progress and results of a bulk verification job | Free |
| `get_account_status` | Shows the connected account's plan, credits and limits | Free |

Example prompts:

```
Find 20 heads of sales at SaaS companies in Spain with 51-200 employees
```

```
Reveal the work email and phone number for this LinkedIn profile: <url>
```

```
What can you tell me about stripe.com? Company size, type, founding year and LinkedIn page.
```

```
Before I send this campaign, check which of these email addresses accept mail: <addresses>
```

## Privacy

SalesQL is for B2B professional use only. Don't use it to locate private individuals or to get anyone's contact details for personal, non-business purposes. Read the [privacy policy](https://salesql.com/legal/privacy). People can opt out of SalesQL's database at the [privacy center](https://salesql.com/privacy-center).

## Resources

- [SalesQL MCP docs](https://salesql.com/docs/mcp): connection guides, prompt library, tools and troubleshooting
- [SalesQL developer docs](https://salesql.com/docs): API and MCP
- [SalesQL help center](https://salesql.com/help)

## License

[MIT](LICENSE)
