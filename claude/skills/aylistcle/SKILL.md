---
name: aylistcle
description: Use the AY Listicle MCP server to find best-of listicles with open spots, request editorial inclusion for a product, propose free swaps and answer incoming requests, always previewing before any credits move. Use when the user wants to get their product featured in listicles, check what is waiting on them on AY Listicle, or review requests.
---

# AY Listicle: find listicles and request inclusion safely

AY Listicle is a network where companies get featured in each other's best-of listicles. A host adds a member's product to a listicle they own (an editorial inclusion), and the host approves every request. This skill drives the AY Listicle MCP server (`https://www.aylistcle.com/api/mcp/mcp`). If the tools are not connected, tell the user to add the server and sign in (see the README), then stop.

## Ground rules

- Never promise rankings, traffic, citations or any other result. A spot is an editorial inclusion that a host approves. Describe it that way.
- Three tools change things between members: `request_inclusion` (holds credits), `propose_swap` (emails the other member, no credits) and `answer_request` (accepts, declines or cancels). Each returns a preview first and does nothing until called again with `confirm: true`.
- Always show the user the preview (cost, balance after, who is told, the blurb that will be sent) and wait for a clear yes before calling with `confirm: true`. A yes to one request is not a yes to the next one.
- Never invent ids. Take `listicle_id`, `product_id` and request ids from tool results.
- Do not bulk-request. Suggest a short list, let the user pick, then do them one at a time.
- Without a plan, other members' listicles come back locked (numbers only, no name or link). Say so and point the user to https://www.aylistcle.com instead of trying to work around it.

## Workflow: find listicles and ask for a spot

1. `list_products` to see the user's products and ids. If there is more than one, ask which one. Read the product's field and one-liner so the blurb you write is accurate.
2. `get_credits` so you know the spendable balance before suggesting anything.
3. Find candidates:
   - `explore_feed` for open spots, new this week and companies to watch.
   - `search_listicles` with `query`, `fields`, `min_dr`, `open_only: true`, `sort` (`dr`, `impressions`, `newest`, `spots`, `growth`). Use `search_companies` to group by company.
4. `get_listicle` on the two or three best fits. Check that the listicle is relevant to the product, that a spot is open, and the price in credits. Skip any that do not fit the product honestly.
5. Choose the route:
   - `request_inclusion` costs credits (held until the host answers).
   - `propose_swap` is free but only works when the host turned on `open_to_swaps` and you can offer a spot in one of your own listicles. Both spots must be open.
6. Draft a one-line `blurb` (the line the host will paste). Plain, factual, no superlatives, within the length limit.
7. Call the tool without `confirm`. Present the preview to the user. Only after they agree, call again with `confirm: true`.
8. Afterwards, `list_deals` or `whats_waiting` shows the status.

## Workflow: answer what is waiting

1. `whats_waiting` (most urgent first), or `read_inbox` for updates and failed checks.
2. For each request to answer, show who asked, which listicle and spot, the blurb, and what the user earns or gives. Ask accept, decline or leave for later.
3. `answer_request` with `answer: accept | decline | cancel`, previewed first, then `confirm: true` after the user agrees.
4. After the user adds a partner's product to their page, call `mark_mention_added` so the crawler checks the page. Call it again after a fix if a check failed.

## Workflow: add the user's own listicles

- `list_suggested_listicles` shows listicles found on the user's own site. Add one with `add_suggested_listicle`.
- `add_listicles` takes up to 50 page URLs. Confirm the list of URLs with the user first. Duplicates and bad links are reported per URL.
- New pages are read in the background, so numbers fill in after a minute or so.

## When something fails

- A 403 insufficient scope means the app was not allowed to do that. Tell the user to reconnect AY Listicle and allow "Spend and answer" (or "Change" for add tools).
- Rate limit: 120 tool calls a minute per connection. Slow down.
- Anything else: report the tool's message as it is. Do not retry a confirmed action without asking, because it may already have happened. Check `list_deals` first.
