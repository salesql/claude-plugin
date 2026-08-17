# SalesQL for Claude Code

Official [SalesQL](https://salesql.com) plugin for [Claude Code](https://code.claude.com). Prospect, enrich, and reveal B2B contacts without leaving your terminal: search for leads, get verified emails and phone numbers, and enrich people and companies through the SalesQL MCP server.

## Installation

Add the community marketplace (if you haven't already) and install the plugin:

```
/plugin marketplace add anthropics/claude-plugins-community
/plugin install salesql@claude-plugins-community
```

The plugin ships disabled by default (it connects to an external service). If the `/salesql:` commands don't show up after installing, enable it from `/plugins`.

## Authentication

The plugin connects to the SalesQL MCP server over HTTPS and authenticates with OAuth — no API keys to copy or paste.

1. In Claude Code, run `/mcp` and select **salesql**.
2. Your browser opens the SalesQL login page.
3. Sign in and authorize the connection. Done.

## Skills

The plugin ships guided workflows as skills, invokable as slash commands:

| Skill | What it does |
| --- | --- |
| `/salesql:find-leads <business description or URL>` | End-to-end prospecting: researches your ideal customer profile, finds matching people, and reveals verified emails and phones for the leads you approve |

Example:

```
/salesql:find-leads we sell an AI code-review tool for mid-size software companies in Spain
```

Claude can also trigger skills automatically when your request matches — e.g. "help me find leads for my business".

## Usage

Once connected, just ask Claude in natural language. The plugin exposes these tools:

| Tool | What it does |
| --- | --- |
| `search_people` | Find leads by title, company, industry, location, and more |
| `enrich_person` | Get verified emails, phones, and profile data for a person |
| `enrich_person_bulk` | Enrich up to 100 people in one request |
| `enrich_organization` | Get firmographic data for a company |
| `enrich_organization_bulk` | Enrich up to 100 companies in one request |
| `lookup` | Resolve valid filter values (industry, country, company size…) before searching — free, no credits |
| `get_account_status` | Check your SalesQL plan and remaining credits |

Example prompts:

```
Find 20 heads of sales at SaaS companies in Spain with 50-200 employees
```

```
Get me a verified email and phone for this LinkedIn profile: <url>
```

```
Enrich acme.com — industry, size, location, and tech stack
```

```
How many SalesQL credits do I have left this month?
```

## Resources

- [SalesQL documentation](https://help.salesql.com)
- [SalesQL MCP](https://salesql.com/mcp)

## License

[MIT](LICENSE)
