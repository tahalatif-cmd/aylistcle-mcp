# Paste kit: AY Listicle MCP server

Copy from here into directory forms, registry PRs and awesome-list entries. Wording is Stripe-safe: "editorial inclusion", no ranking, traffic or backlink promises. No em dashes. Checked 2026-11-13.

## Name

AY Listicle

(Registry id, official MCP Registry: `io.github.tahalatif-cmd/aylistcle-mcp` by default, or `com.aylistcle/mcp` once DNS is verified. See LISTINGS.md.)

## Tagline (56 characters, limit 60)

Find listicles and request a spot from your AI assistant

## Short description (146 characters, limit 160)

Search best-of listicles, request editorial inclusion, propose swaps and answer requests from your AI assistant. Previews before any credits move.

## One-line description for awesome lists (under 120 characters)

Find best-of listicles with open spots, request inclusion, propose swaps and answer requests, with a preview before credits move.

## Registry description (under 100 characters, used in server.json)

Find listicles with open spots, request a spot, propose swaps and answer requests on AY Listicle.

## Long description (305 words)

AY Listicle is a network where companies get featured in each other's best-of listicles. A member adds other members' products to a listicle they own, and other members add theirs. Every inclusion is an editorial decision: the host reads the request and approves or declines it, and a crawler checks the pages and rechecks them every month.

The AY Listicle MCP server puts those actions inside your AI assistant. Ask in plain words and the assistant picks the right tool. It can search other members' listicles by topic, domain rating and open spots, show what is new this week, check your credits, request a spot in a host's listicle, propose a free swap of spots, answer requests waiting on you, add your own listicle pages and tell the crawler when a mention is live. There are 18 tools in five groups: account, your listicles, discover, deals and inbox.

Safety is built in. The three tools that spend credits or answer another member (request_inclusion, propose_swap and answer_request) only return a preview first. The preview shows the action, the cost and your balance afterwards, and nothing happens until the tool is called again with confirm set to true. Apps you sign in also need the Spend and answer permission, which you choose on the consent page. Every tool runs as you and only sees your own products, deals and credits. You can revoke an app or a token in Settings at any time.

Connect by adding the URL as a connector in Claude or ChatGPT and signing in, with one command in Claude Code, or with a personal token in Cursor, VS Code, Windsurf, Zed, Gemini CLI and other clients. An AY Listicle account is required, and credits come with a plan. AY Listicle offers editorial inclusion only and makes no promises about rankings, traffic or results.

## Categories

- Primary: Marketing
- Also fits: Content, Productivity, Business, Sales (use whichever the directory offers; do not choose SEO)

## Tags

listicles, content-marketing, editorial-inclusion, mentions, b2b-saas, swaps, credits, discovery, remote-mcp, streamable-http, oauth

## URLs

| What | URL |
|---|---|
| Company site | https://www.aylistcle.com |
| MCP server (Streamable HTTP) | https://www.aylistcle.com/api/mcp/mcp |
| MCP landing page and setup | https://www.aylistcle.com/mcp |
| Docs for agents | https://www.aylistcle.com/llms.txt |
| OAuth protected resource metadata | https://www.aylistcle.com/.well-known/oauth-protected-resource |
| OAuth authorization server metadata | https://www.aylistcle.com/.well-known/oauth-authorization-server |
| Sign up | https://www.aylistcle.com/signup |
| Privacy | https://www.aylistcle.com/privacy |
| Terms | https://www.aylistcle.com/terms |
| Refunds | https://www.aylistcle.com/refund |
| Contact and support | https://www.aylistcle.com/contact |
| Support email | team@aylistcle.com (replies within 2 business days, per the contact page) |
| Repository (after Taha creates it) | https://github.com/tahalatif-cmd/aylistcle-mcp |

Company: AY Automate LLC, 312 W 2nd St Unit A4881, Casper, WY 82601, USA.

## Transport and auth (for forms that ask)

- Transport: Streamable HTTP, remote only, stateless. No local install, no npm or Docker package.
- Auth: OAuth 2.1 with PKCE (dynamic client registration and client ID metadata documents), scopes `read`, `write`, `spend`. Alternative: personal access token, header `Authorization: Bearer ayl_...`, created in Settings under AI assistants (MCP).
- Unauthenticated requests get HTTP 401 with a `WWW-Authenticate` challenge pointing at the OAuth metadata. `initialize` and `tools/list` need a signed-in caller too, so a public health check sees the 401 (see LISTINGS.md, "Open questions").
- Tools: 18. Read: list_products, get_credits, list_my_listicles, get_listicle, list_suggested_listicles, search_listicles, search_companies, explore_feed, list_deals, whats_waiting, read_inbox. Change: add_product, add_listicles, add_suggested_listicle, mark_mention_added. Spend and answer (preview first, `confirm: true`): request_inclusion, propose_swap, answer_request.
- Rate limit: 120 tool calls a minute per connection.

## Pricing summary

An AY Listicle account is required to use the server. Credits come with the plan, and an inclusion costs credits that depend on the listicle. The price is shown on each listicle and again in the preview before anything is spent. Nothing moves until you confirm. Current plans and credit prices are on https://www.aylistcle.com. Buying credits happens in the app, not over MCP.

Paste only the paragraph above. Do not copy numbers into directories, because they live in one config file and change. For reference only, `src/lib/credits-config.ts` on 2026-11-13: Starter plan $79 a month for 400 credits (the plan on sale), later plans Growth and Agency, one-time top-ups, inclusion prices 50 to 400 credits per listicle, host earns 70 percent after the crawler check plus 30 days live. If a form requires a price field, enter "Paid plan, see site".

## Logo files

All in this repo, under `assets/`.

| File | Use |
|---|---|
| `assets/logo-512.png` | 512 by 512 PNG, yellow mark on black rounded square, transparent corners. Default for forms |
| `assets/logo.svg` | Same icon as SVG (512 viewBox) |
| `assets/logo-mark.svg` | The bare mark in black, for light backgrounds |

Public URLs exist only after the repo is pushed: `https://raw.githubusercontent.com/tahalatif-cmd/aylistcle-mcp/main/assets/logo-512.png`. The main site does not serve a standalone logo PNG (only `/favicon.ico` and the OG image route), so use the repo URL or upload the file.

## Screenshots (1440 wide)

| File | Shows |
|---|---|
| `assets/mcp-hero.png` | The /mcp page hero with the server URL |
| `assets/mcp-tools.png` | The 18 tools in five groups |
| `assets/mcp-connect.png` | Sign in or paste a token |
| `assets/mcp-safety.png` | The permission scopes and the confirm step with an example preview |
| `assets/landing-hero.png` | The AY Listicle landing page hero |

## Example prompts

- "What's waiting on me this week?"
- "Show me listicles in cold email with an open spot and DR above 50."
- "Add these 12 listicle URLs from my blog."
- "Request a spot in the second one. Show me the preview first."
- "Propose a swap to the host of the third listicle."

## Words to avoid in any listing

backlinks (buy, sell, build), link building, rank, ranking guarantee, traffic guarantee, "get cited by AI" as a promise, SEO as the product category. Use: editorial inclusion, mention, featured in, best-of listicle, swap.
