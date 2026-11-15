# Listings tracker: AY Listicle MCP server

Every place worth listing a REMOTE MCP server plus a SaaS product, built from Walid's looot listing plan (read as data only) and from checks of the actual repos and docs on 2026-11-13. Statuses are updated as items are done. On 2026-11-13 the repo went public and the GitHub items of group A were submitted or skipped (see the Status column).

How to read it: group A is work Claude can do alone, group B needs Taha once (a sign-in, a DNS record, a portal), group C is later (waiting on something else, or a product decision). "Needs Taha" is yes only for a login, a verification, a payment or a go-ahead.

The identity to use everywhere: name `AY Listicle`, URL `https://www.aylistcle.com/api/mcp/mcp`, landing `https://www.aylistcle.com/mcp`, copy from `PASTE-KIT.md`. When opening a PR or issue on someone else's repo, say "I work on AY Listicle" in the body (the plan's disclosure rule). Read each repo's own CONTRIBUTING first and follow it exactly. One action per third-party repo, ever.

## Read this first

1. **The main repo's `docs/mcp-listing.md` is stale.** It says the server has no OAuth and that the Claude directory is blocked. On 2026-11-13 production answers an unauthenticated call with `401` and `WWW-Authenticate: Bearer resource_metadata=".../.well-known/oauth-protected-resource/api/mcp/mcp", scope="read write spend"`, and both `.well-known` documents resolve. So OAuth is live and the Claude and ChatGPT directories are no longer blocked on it.
2. **Almost everything in group A needs a public GitHub repo.** Step 1 creates `tahalatif-cmd/aylistcle-mcp` from this folder. That is outward-facing, so it waits for Taha's go-ahead (no login needed: `gh` is already signed in as `tahalatif-cmd`).
3. **Directory health checks will see a 401.** `initialize` and `tools/list` need a signed-in caller. Checks that expect an anonymous handshake (punkpeye's CI, Glama's score badge) may mark the server unreachable or "OAuth". Unverified. See "Open questions".
4. **Never buy a fast lane** (mcp.so $39, MCP Market $29 to $69, mcpservers.org $39, claudemarketplaces $4). The plan skipped them all.
5. **PR hygiene from the plan:** vet that a repo merged an outside PR in the last 14 days, one-line diff in the right section matching the list's format, answer reviewers the same day, one nudge after 7 days and never a second, declined means done, never sign a CLA. The plan also commits third-party PRs without a Co-Authored-By line, so leave that trailer off commits that go into other people's repos.

## Official MCP Registry: the two name variants

The registry reads `server.json`. Schema: `https://static.modelcontextprotocol.io/schemas/2025-12-11/server.schema.json` (validated locally against it, both files pass). `description` must stay at or under 100 characters. A published version cannot be edited, so bump `version` for every later publish.

| | Variant 1 (default, in `server.json`) | Variant 2 (`variants/server.com.aylistcle.json`) |
|---|---|---|
| Name | `io.github.tahalatif-cmd/aylistcle-mcp` | `com.aylistcle/mcp` |
| Proves | You control the GitHub account `tahalatif-cmd` | You control the DNS of aylistcle.com |
| Needs from Taha | GitHub device login, about 2 minutes | One TXT record at Namecheap, about 10 minutes plus DNS wait |
| Command | `mcp-publisher login github` then `mcp-publisher publish` | `mcp-publisher login dns --domain aylistcle.com --private-key ...` then `mcp-publisher publish` |
| Good because | Fast, no DNS, no key to keep | The brand name, shows as the company's own listing |

Publish one name, not both, so directories do not show two entries. Start with variant 1. Move to variant 2 later if the brand namespace matters (publishing under the new name is a new server entry, not a rename).

**Variant 1 steps.** `brew install mcp-publisher`, then in this folder: `mcp-publisher login github` (it prints a code and a github.com/login/device URL, Taha enters the code), `mcp-publisher validate`, `mcp-publisher publish`. The `repository` field points at `https://github.com/tahalatif-cmd/aylistcle-mcp`, so create that repo first.

**Variant 2 steps (DNS at Namecheap).** HTTP verification does not work for us (the registry does not follow redirects and `aylistcle.com` answers every path with a 308 to `www`). Use DNS. Needs OpenSSL 3 (macOS built-in LibreSSL lacks Ed25519), so `brew install openssl@3`:

```bash
openssl genpkey -algorithm Ed25519 -out key.pem   # keep key.pem private, never commit it
PUBLIC_KEY="$(openssl pkey -in key.pem -pubout -outform DER | tail -c 32 | base64)"
echo "aylistcle.com. IN TXT \"v=MCPv1; k=ed25519; p=${PUBLIC_KEY}\""
# Namecheap: Domain List, Manage, Advanced DNS, add a TXT record on host "@" with the value
#   v=MCPv1; k=ed25519; p=<PUBLIC_KEY>      (keep the existing SPF and Google verification TXT records)
PRIVATE_KEY="$(openssl pkey -in key.pem -noout -text | grep -A3 "priv:" | tail -n +2 | tr -d ' :\n')"
mcp-publisher login dns --domain aylistcle.com --private-key "${PRIVATE_KEY}"
mcp-publisher validate && mcp-publisher publish
```

(From the main repo's `docs/mcp-listing.md`. The Namecheap click path is my own description and not checked against their current UI.)

The `Authorization` header in `server.json` is `isRequired: false` because OAuth now works with no header. The main repo's own `server.json` still says `isRequired: true` and is out of date; this kit's file replaces it.

## Group A: Claude can do alone (PR, issue, auto-index, no-login form)

Order is the order to do them in. All wait on row A1 unless the note says otherwise.

| # | Place | URL | How to submit | Needs Taha | What exactly is needed | Status |
|---|---|---|---|---|---|---|
| A1 | GitHub repo (gate for most rows) | https://github.com/tahalatif-cmd/aylistcle-mcp | CLI (`gh repo create`, push) | Yes, a go-ahead only | Create public repo from this folder, add topics `mcp`, `mcp-server`, `remote-mcp`, `model-context-protocol`, `claude-code-plugin`, `gemini-cli-extension`, `listicles`. README, LICENSE, SECURITY.md and manifests are ready | done 2026-11-13 (repo is public) |
| A2 | mcp.so | https://mcp.so/submit?type=remote-server | Web form | No (unverified: the form may ask for sign-in) | Name, URL, description from PASTE-KIT. Free queue only, skip the $39 fast lane. Works without A1 | todo |
| A3 | mcpservers.org (also the only route into wong2/awesome-mcp-servers, which says "we do not accept PRs") | https://mcpservers.org/submit | Web form | No | PASTE-KIT fields. Free queue, skip the $39 lane. Works without A1 | todo |
| A4 | TensorBlock MCP Index and awesome list | https://github.com/TensorBlock/awesome-mcp-servers/issues/new?template=add-mcp-server.yml | Issue form (a bot drafts the PR) | No | Name, URL, category `Marketing, Sales & CRM` (file `docs/marketing-sales--crm.md`), one-line description. 1,074 stars, active | issue opened 2026-11-13: https://github.com/TensorBlock/awesome-mcp-servers/issues/5944 (form has no marketing category, used Other) |
| A5 | MobinX/awesome-mcp-list | https://github.com/MobinX/awesome-mcp-list | PR, one README bullet | No | Needs a public repo (A1) and accepts a repo that carries the config for a hosted remote server (precedent: makernice-plugin). Use the exact bullet template in its CONTRIBUTING, pick one category | PR opened 2026-11-13: https://github.com/MobinX/awesome-mcp-list/pull/1266 |
| A6 | mcpHQ/awesome-mcp-servers | https://github.com/mcpHQ/awesome-mcp-servers | PR editing `data/servers.json` | No | One object with the fields in its CONTRIBUTING (category, provider, tags, transport `streamable-http`, auth `oauth`). README is generated from the JSON. 117 stars, active | PR opened 2026-11-13: https://github.com/mcpHQ/awesome-mcp-servers/pull/814 |
| A7 | VoltAgent/official-mcp-servers | https://github.com/VoltAgent/official-mcp-servers | PR, one README line | No | Add at the end of **Emerging and Independent** (small team): `- **[AY Listicle](https://www.aylistcle.com/mcp)** - short description`. Links the vendor docs page, so no repo needed. 422 stars | skipped 2026-11-13: CONTRIBUTING wants real community usage and rejects brand new projects, plus a description of 10 words or fewer. Retry once there is real usage |
| A8 | jaw9c/awesome-remote-mcp-servers | https://github.com/jaw9c/awesome-remote-mcp-servers | PR adding one table row | No | Row: `AY Listicle | Marketing | https://www.aylistcle.com/api/mcp/mcp | OAuth2.1 | [AY Automate](https://www.aylistcle.com)`. Rules: OAuth 2.0 only, officially supported, production ready. Risk: the plan's looot PR here (#1241) was closed unmerged, so expect a possible no | PR opened 2026-11-13: https://github.com/jaw9c/awesome-remote-mcp-servers/pull/2297 (said plainly there is no track record yet) |
| A9 | Docker MCP Catalog | https://github.com/docker/mcp-registry | PR adding a remote-server entry (no image to build) | No | Follow its "Adding a Remote MCP Server" section: transport `streamable-http`, OAuth. 584 stars. The plan's looot PR here went quiet for weeks, so expect a slow review | PR opened 2026-11-13: https://github.com/docker/mcp-registry/pull/7084 (remote entry, no oauth block, task validate not run) |
| A10 | ToolHive catalog (Stacklok) | https://github.com/stacklok/toolhive-catalog | Issue first, wait for the `approved-for-pr` label, then PR with DCO sign-off | No (DCO sign-off is a `-s` flag, not a CLA) | Uses `server.json`-style `remotes` entry. Wants a security contact: `SECURITY.md` is already in this kit. Needs A1 | skipped 2026-11-13: registry criteria want CI, tests, pinned actions, releases and a changelog; this repo is config and docs only, so it would be dropped. Revisit if that changes |
| A11 | skills.sh (Vercel skills CLI) | https://skills.sh | Auto-index after an install | No | After A1, run `npx skills add tahalatif-cmd/aylistcle-mcp` once (pin the CLI version) so the skill in `skills/aylistcle/` is indexed. Recheck the next day | done 2026-11-13: ran `npx skills@1.8.1 add tahalatif-cmd/aylistcle-mcp` once in a temp dir (installed the aylistcle skill, cleaned up). Recheck skills.sh next day |
| A12 | Gemini CLI extensions gallery | https://geminicli.com/extensions | Auto-index | No | Needs A1: public repo, topic `gemini-cli-extension`, `gemini-extension.json` at root (ready). The plan could not confirm indexing from a script because the page is JS-only | todo |
| A13 | claude-plugins.dev | https://claude-plugins.dev | Auto-index of public GitHub repos | No | Needs A1 and `.claude-plugin/plugin.json` (ready). Took about a week to appear for looot | todo |
| A14 | ToolSDK MCP Registry | https://github.com/toolsdk-ai/toolsdk-mcp-registry | PR adding one JSON file (per plan) | No | Read `docs/CONTRIBUTING.md` first: whether it takes a remote Streamable HTTP entry with no npm package is unverified. 234 stars, active | PR opened 2026-11-13: https://github.com/toolsdk-ai/toolsdk-mcp-registry/pull/1445 (remote entry, validator passed; repo not starred) |
| A15 | Kilo Marketplace | https://github.com/Kilo-Org/kilo-marketplace | PR | No | Entry for a remote server. Rules not read, vet the repo first | PR opened 2026-11-13: https://github.com/Kilo-Org/kilo-marketplace/pull/718 |
| A16 | mcp.directory | https://mcp.directory | Free form | No | PASTE-KIT. Queue, reviews are slow (looot waited since 10-08) | todo |
| A17 | MCP Server Spot | https://www.mcpserverspot.com/submit | Free form | No | PASTE-KIT | todo |
| A18 | MCP Collection | https://mcpcollection.com/submit | Free form | No | PASTE-KIT | todo |
| A19 | MCP Market | https://mcpmarket.com/submit | Free form, free queue (4 to 6 weeks) | No | Form asks for a GitHub repo, so needs A1. Skip the $29 and $69 lanes | todo |
| A20 | MCP Pub | https://mcppub.com | Editorial queue form | No | PASTE-KIT. Form location not confirmed | todo |
| A21 | MCP Server Hub | https://mcpserverhub.com | Tally form | No | PASTE-KIT | todo |
| A22 | AISO Tools | https://aisotools.com/submit | Free form | No | PASTE-KIT | todo |
| A23 | Infrabase.ai | https://infrabase.ai | Free form | No | PASTE-KIT. Find the submit link on the site | todo |
| A24 | TechPluto | https://www.techpluto.com/submit-a-star/submit | Free form | No | PASTE-KIT. May send a confirm email Taha must click, then it moves to B | todo |
| A25 | AI Agents Directory | https://aiagentsdirectory.com | Free form | No | PASTE-KIT. Find the submit link on the site | todo |

## Group B: needs Taha once

| # | Place | URL | How to submit | Needs Taha | What exactly is needed | Status |
|---|---|---|---|---|---|---|
| B1 | Official MCP Registry (feeds PulseMCP, VS Code, many directories) | https://registry.modelcontextprotocol.io | CLI `mcp-publisher` | Yes | Variant 1: GitHub device login (needs A1 first). Variant 2: one TXT record at Namecheap. See the section above. Claude then runs validate and publish | todo |
| B2 | Glama (connector page and server index) | https://glama.ai/mcp/connectors | Web form "Add Server" | Yes | Sign in (GitHub) and solve a captcha, then claim the listing. Glama's health check may need a test token because the server needs sign-in (unverified). Unlocks the punkpeye remote list (C1) | todo |
| B3 | Claude connectors directory (Anthropic) | https://claude.com/docs/connectors/building/submission | Developer portal form | Yes | Paid Claude plan, reviewer test account with credits on an AY Listicle plan, privacy policy sign-off (/privacy exists). OAuth is live, so no longer blocked. Needs a decision on how reviewers get credits | todo |
| B4 | Claude plugin directory | https://clau.de/plugin-directory-submission | Form in the Claude admin portal | Yes | Submit this repo as the plugin (needs A1). `plugin.json` already has icon, documentationUrl, supportUrl, privacyPolicyUrl and termsOfServiceUrl | todo |
| B5 | ChatGPT apps directory (also reaches Codex) | https://platform.openai.com/apps-manage | OpenAI submission portal | Yes | Verified organisation, domain verification token, demo video, test cases, a test account. About an hour. The plan's first looot submission was rejected, so read the criteria first | todo |
| B6 | cursor.directory | https://cursor.directory/plugins/new | Web form, GitHub sign-in | Yes | Sign in with GitHub, submit the repo (needs A1). Required before the Cursor Marketplace (C5) | todo |
| B7 | ClawHub (OpenClaw skills) | https://clawhub.ai | CLI `clawhub skill publish` | Yes | GitHub login and token. Publishes the one skill. Unlocks VoltAgent/awesome-openclaw-skills (53k stars, only takes skills already on ClawHub) | todo |
| B8 | mcpi.app | https://mcpi.app | Web form | Yes | Account plus ownership proof (DNS TXT or a well-known file). DNS is easier | todo |
| B9 | MCPBundles | https://www.mcpbundles.com/mcp-analysis?intent=publish | Web form | Yes | Account plus ownership proof | todo |
| B10 | AIBase MCP | https://mcp.aibase.com | Web form | Yes | Account, then submit the server URL | todo |
| B11 | MCP Server Space | https://mcpserver.space/submit | Web form | Yes | Sign in, fill the form | todo |
| B12 | claudemarketplaces.com | https://claudemarketplaces.com/submit | Web form | Yes (the plan flags it as needing a person) | Needs the plugin repo (A1). Free, skip the $4 lane | todo |

## Group C: later

| # | Place | URL | How to submit | Needs Taha | What exactly is needed | Status |
|---|---|---|---|---|---|---|
| C1 | punkpeye/awesome-remote-mcp-servers (the right punkpeye list for a remote-only server, 3,145 stars) | https://github.com/punkpeye/awesome-remote-mcp-servers | PR, add `🤖🤖🤖` to the PR title for the fast lane | No | After B2 (Glama connector badge is required, CI checks it). Star the repo first (required). Section `Marketing` (alphabetical). Three-line entry with 🔐 (OAuth), one sentence up to 120 chars. Endpoint must answer an MCP `initialize` (we return 401, see Open questions) | todo |
| C2 | PulseMCP | https://www.pulsemcp.com | Auto-index from the official registry (submissions closed) | No | Wait after B1. Recheck in about a week | todo |
| C3 | GitHub MCP Registry (github.com/mcp) | https://github.com/mcp | Onboarding discussion in github/github-mcp-server, then email partnerships@github.com | No (Claude can post once B1 is live) | Hand-curated, needs B1 first. Find the current onboarding discussion (the main repo notes #1257, the plan used #3607). Posting is outward-facing, get a go-ahead | todo |
| C4 | LobeHub MCP | https://lobehub.com/mcp | Auto-index, then claim with GitHub | Yes, for the claim | Appears after B1 or A1. Claiming needs a GitHub sign-in (3 minutes) | todo |
| C5 | Cursor Marketplace | https://cursor.com/marketplace/publish | Publisher form | Yes | Only after B6. The plan was rejected with "use cursor.directory first". Plugins must be open source (this repo is) | todo |
| C6 | Smithery | https://smithery.ai/new | GitHub login, claim | Yes | Conflict in sources: the main repo notes say Smithery stopped hosted connections on 2026-10-31 and has no self-serve submit; the plan lists it live via the registry API with a claim. Check the site after B1 before spending time. No `smithery.yaml` is needed for a remote server | todo |
| C7 | hesreallyhim/awesome-claude-code (57,941 stars) | https://github.com/hesreallyhim/awesome-claude-code | Web UI issue form only (`recommend-resource.yml`) | No | Rule: resource must be 14 days old with continued commits, or have 100 stars; one resource at a time. Wait about 2 weeks after A1. Use the form, anything else risks a ban from the repo | todo |
| C8 | Raycast MCP registry | https://github.com/raycast/extensions | PR (one JSON entry) | No | Marginal reach. After B1 | todo |
| C9 | Claude Code plugin marketplace (self-hosted) | `/plugin marketplace add tahalatif-cmd/aylistcle-mcp` | None, it works once A1 is public | No | Not a submission. Test `/plugin install aylistcle@aylistcle` after A1 and mention it in outreach | todo |
| C10 | Product Hunt | https://www.producthunt.com/posts/new | Maker account, launch day | Yes | A launch day is a separate decision | todo |
| C11 | Show HN | https://news.ycombinator.com/submit | HN account | Yes | Must be something people can try. Needs a signed-in account with a plan | todo |
| C12 | SaaSHub | https://www.saashub.com/services/submit | Web form, login | Yes | Account, list as an alternative to named tools | todo |
| C13 | AlternativeTo | https://alternativeto.net/manage-item/ | Web form, account | Yes | Account, one product page | todo |
| C14 | G2 | https://sell.g2.com/create-a-profile | Free vendor profile | Yes | Business email, then reviews | todo |
| C15 | Capterra, GetApp, Software Advice | https://digitalmarkets.gartner.com/ | Free vendor profile | Yes | One Gartner Digital Markets account covers all three | todo |
| C16 | TrustRadius | https://www.trustradius.com/vendors | Free vendor profile | Yes | Account | todo |
| C17 | Crunchbase | https://www.crunchbase.com/add-new | Account | Yes | Company profile for AY Automate LLC | todo |
| C18 | StackShare | https://stackshare.io/tools/new | Account | Yes | One product page | todo |
| C19 | Indie Hackers | https://www.indiehackers.com/products/new | Account | Yes | One product page | todo |
| C20 | SaaSworthy | https://www.saasworthy.com/list-your-software | Free form, account | Yes | Listing form | todo |
| C21 | open-saas-directory/awesome-saas-directory and brandonhimpfen/awesome-saas | https://github.com/open-saas-directory/awesome-saas-directory | PR, one line | No | The plan marked both "maybe". Check fit and recent merges before opening | todo |
| C22 | Reddit r/mcp and r/ClaudeAI | https://www.reddit.com/r/mcp/submit | Post | Yes | Account and strict self-promo rules. Do after real usage exists | todo |
| C23 | The Rundown (Supertools) | https://www.therundown.ai/submit | Email support@therundown.ai | Yes (a person sends) | Draft: name, URL, category, one line. Claude saves a draft, Taha sends | todo |
| C24 | Perplexity, Mistral Le Chat connectors | api@perplexity.ai, https://mistral.ai/contact | Email | Yes | No public store, contact only. Low priority, after B3 and B5 | todo |
| C25 | Meta Muse connectors | https://platform.muse.ai | Account, questionnaire, test account | Yes | In the plan as in review for looot. Fit unclear | todo |

### Awesome lists and their rules (checked 2026-11-13)

| Repo | Stars, last push | Right place for a remote MCP | Contribution rules | Our row |
|---|---|---|---|---|
| punkpeye/awesome-remote-mcp-servers | 3,145, 2026-11-06 | `Marketing` section, alphabetical, three-line entry with 🔐 | Must be a Glama connector (badge required, CI-checked), star the repo, endpoint must answer `initialize`, public sign-up OK, one sentence up to 120 chars, `🤖🤖🤖` in the title for agents | C1 (after B2) |
| punkpeye/awesome-mcp-servers (97,685 stars) | 2026-11-06 | Not for us: its CONTRIBUTING says remote-only servers belong in the remote list | Needs a public GitHub repo you install and run | Not applicable now |
| jaw9c/awesome-remote-mcp-servers | 1,186, 2026-11-09 | Add a row to the table in the README | OAuth 2.0 only, official, production ready, active maintenance | A8 |
| wong2/awesome-mcp-servers | 4,409, 2026-07-13 | None | "We do not accept PRs", submit at mcpservers.org/submit | A3 |
| appcypher/awesome-mcp-servers | 5,837, 2026-05-06 | None | Archived | Not applicable now |
| TensorBlock/awesome-mcp-servers | 1,074, 2026-11-13 | `docs/marketing-sales--crm.md` | Issue form or docs PR, alphabetical, one server per line | A4 |
| mcpHQ/awesome-mcp-servers | 117, 2026-11-11 | `data/servers.json` entry | Publicly available with docs or a registry listing, maintained, not a mirror | A6 |
| MobinX/awesome-mcp-list | 909, 2026-11-04 | One bullet in the best-fit README category | Public GitHub repo required, exact bullet template, exactly one bullet | A5 |
| VoltAgent/official-mcp-servers | 422, 2026-11-05 | End of "Emerging and Independent" | Official servers only, link vendor docs if no repo | A7 |
| hesreallyhim/awesome-claude-code | 57,941, 2026-11-13 | Plugins or skills resource, via the issue form | 14 days old with active commits or 100 stars, one resource at a time, issue form only | C7 |
| modelcontextprotocol/servers | 91,973, 2026-11-12 | None | Reference servers only, points to the registry | Not applicable now |

## Not applicable now

Skipped on purpose: package registries and SDKs, browser extensions, automation-platform apps, and anything that needs code we do not have.

| Place or family | Reason |
|---|---|
| npm, PyPI, Maven Central, NuGet, crates.io, RubyGems, Packagist, pub.dev, Hex, JSR, pkg.go.dev, Swift Package Index, Homebrew, Docker Hub images, GitHub Marketplace Actions, libraries.io, deps.dev, socket.dev | Package registries. The server is remote only and there is no package to publish. Do not write an `npx` install line anywhere until a package exists |
| SDK and framework registries: Vercel AI SDK, LangChain, LlamaIndex, Mastra, GPT Researcher, CrewAI, Agno, OpenHands, Portkey, google/adk-docs, Haystack | Need an SDK or integration package we do not have |
| Browser extensions: Chrome Web Store, Firefox Add-ons, Edge Add-ons | No extension |
| IDE extension marketplaces: VS Code Marketplace, Open VSX, JetBrains, Zed extensions | Need extension code. The README covers VS Code, Windsurf and Zed by config snippet instead |
| Automation-platform apps: n8n, Dify, Zapier, Make, Activepieces, Pipedream, Langflow, Power Automate, Node-RED, HubSpot, Attio, Twenty, Google Sheets, Clay, Instantly, Pabbly, IFTTT, Albato, Windmill, Latenode | Need built apps and, mostly, reviewer scenarios |
| Agent platform catalogs that need code: Hermes plugin catalog, Goose extensions, Amp skills, xAI plugin marketplace | Need a plugin or extension in their formats. Revisit if the Claude plugin proves useful |
| API hubs: RapidAPI, Postman Public API Network, apis.io, public-apis lists | There is no public REST API yet. The main repo's `docs/openapi.yaml` is an unbuilt draft contract ("Nothing here is built yet") |
| Open-source alternative lists (saas-alternatives, foss-alternatives and similar) | No open-source app to list |
| punkpeye/awesome-mcp-servers, appcypher/awesome-mcp-servers, modelcontextprotocol/servers | Local-install only, archived, or reference-only (see the awesome-list table) |
| Cline MCP Marketplace | `cline/mcp-marketplace` was last pushed 2025-06-24, so the repo looks abandoned |
| github/awesome-copilot, Copilot CLI marketplace | The plan's looot entry was rejected by hand as a funnel to a prepaid connector with no trial credit. Same risk for us. Revisit only if a free trial exists |
| Archestra catalog | Maintainer closed every "Add" issue as not planned |
| Enterprise and cloud marketplaces: Microsoft 365 Agent Store, Gemini Enterprise gallery, Salesforce AgentExchange, AWS, Vercel Marketplace, Cloudflare partners | Partner programs with business verification and security review. Not now |
| Consumer assistants with no connector store or no listing route: Gemini app, Grok, Genspark, Manus, Notion AI, Copilot Studio, Poe, Character.ai, Alexa+, Apple Intelligence | Users add a URL themselves (covered by the README) or there is no route |
| Launch directories with paid, badge or login-only listings (There's An AI For That, Toolify, Futurepedia, Serchen, TinyLaunch, Turbo0, Launch MCP, Launch Llama, BetaList) | Paid fast lanes, backlink badges or months-long queues. The plan skipped them |
| Bulk launch-site queue (about 1,290 rows in the plan) | Low value per minute. Revisit only after the MCP and review listings land |

## Open questions (things not verified)

- Whether the registry, Glama and punkpeye's CI accept a server whose `initialize` returns 401 with an OAuth challenge. Test `mcp-publisher publish` and Glama's check before relying on them.
- Whether Glama needs a test token for its health checks and how to supply one for an OAuth-only server.
- Whether Gemini CLI auto-discovers OAuth for `httpUrl` servers without an `oauth` block (the docs show the block only with a client ID). The extension manifest omits it. Fallback: `gemini mcp add` with a header token.
- Exact ChatGPT, Zed, Windsurf and VS Code menu and key names in the README were written from memory of those tools' docs and not run against live apps.
- Smithery's current state (conflicting sources, see C6) and whether claude-plugins.dev, skills.sh and the Gemini gallery index from a brand new repo.
- The submit URLs for MCP Pub, Infrabase.ai and AI Agents Directory are the home pages from the plan, not confirmed form addresses.
- Whether mcp.so, MCP Server Spot and MCP Collection need sign-in (the plan marks them no-login).

## Web form round, 2026-11-15
| Place | Result |
|---|---|
| mcpservers.org | Submitted, free (review within 2 weeks). Remote URL + OAuth set |
| mcp.directory | Submitted (review within 24 hours) |
| MCP Server Spot | Submitted |
| MCP Collection | Submitted (endpoint handshake passed) |
| MCP Market | Already in their free queue (auto-discovered) |
| MCP Server Hub | Submitted (Tally form) |
| AISO Tools | Live (free, nofollow) |
| Infrabase.ai | Submitted |
| mcp.so | Skipped: remote-server listing is paid only ($39). Needs Taha's decision |
| MCP Pub | Skipped: site down |
| TechPluto | Skipped: free form removed, paid reviews only |
| AI Agents Directory | Needs sign-in (moved to group B) |
