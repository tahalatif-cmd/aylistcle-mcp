# AY Listicle

AY Listicle is a network where companies get featured in each other's best-of listicles. This extension connects Gemini CLI to your AY Listicle account through the MCP server at https://www.aylistcle.com/api/mcp/mcp. Sign in when Gemini CLI asks (use `/mcp auth aylistcle`).

How to work with it:

1. Start with `list_products` and `get_credits`.
2. Search with `search_listicles` or `explore_feed`. Read a listicle with `get_listicle` before asking for a spot.
3. `request_inclusion`, `propose_swap` and `answer_request` only return a preview first. Show the preview to the user (cost, balance after, who is emailed) and call again with `confirm: true` only after they agree.
4. Never promise rankings, traffic or results. A spot is an editorial inclusion that the host approves.

The full workflow is in skills/aylistcle/SKILL.md.
