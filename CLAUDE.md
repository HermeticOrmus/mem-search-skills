# CLAUDE.md

Operational instructions for searching claude-mem's cross-session memory. Drop this file into a project so Claude knows how to recall past work without burning tokens. Merge with project-specific instructions as needed.

**Requires the claude-mem MCP.** This file assumes the `search`, `timeline`, and `get_observations` MCP tools are available. If they are not, claude-mem is not installed and none of this applies. Install claude-mem first, then this file tells Claude how to use it well.

**Tradeoff**: These instructions bias toward filtering before fetching. The discipline costs one or two extra tool calls up front and saves roughly 10x the tokens on recall.

## When to use it

Use the memory tools when the user asks about PREVIOUS sessions, not the current conversation:

- "Did we already fix this?"
- "How did we solve X last time?"
- "What happened last week?"
- "What was the context around that decision?"

If the answer is in the current conversation, just answer. Memory search is for work that scrolled out of context or happened in another session.

## The 3-layer workflow (always follow)

Never fetch full details without filtering first. The whole point is token savings: the search index is cheap, full observations are expensive, so you narrow with cheap calls and spend the expensive call only on the few results that matter.

1. Search to get an index of IDs and titles.
2. Timeline (optional) to get context around an interesting result.
3. Fetch full details only for the IDs you kept.

### Step 1: search, get the index

The `search` tool returns a compact table of IDs, timestamps, types, and titles, roughly 50 to 100 tokens per result. Read the titles, decide what is relevant, keep the IDs.

```text
search(query="authentication", limit=20, project="my-project")
```

Returns a table like:

```text
| ID     | Time    | Type   | Title                        | Read |
|--------|---------|--------|------------------------------|------|
| #11131 | 3:48 PM | obs    | Added JWT authentication     | ~75  |
| #10942 | 2:15 PM | bugfix | Fixed auth token expiration  | ~50  |
```

Parameters:

- `query` (string) search term
- `limit` (number) max results, default 20, max 100
- `project` (string) project name filter
- `type` (string, optional) "observations", "sessions", or "prompts"
- `obs_type` (string, optional) comma-separated: bugfix, feature, decision, discovery, change
- `dateStart` (string, optional) YYYY-MM-DD or epoch ms
- `dateEnd` (string, optional) YYYY-MM-DD or epoch ms
- `offset` (number, optional) skip N results
- `orderBy` (string, optional) "date_desc" (default), "date_asc", "relevance"

### Step 2: timeline, get context around a result

When a single result looks relevant but you need the work that surrounded it, use `timeline` to pull a window of items in chronological order, with observations, sessions, and prompts interleaved around an anchor.

```text
timeline(anchor=11131, depth_before=3, depth_after=3, project="my-project")
```

Or let it find the anchor from a query:

```text
timeline(query="authentication", depth_before=3, depth_after=3, project="my-project")
```

Returns `depth_before + 1 + depth_after` items around the anchor.

Parameters:

- `anchor` (number, optional) observation ID to center on
- `query` (string, optional) find the anchor automatically when no anchor is given
- `depth_before` (number, optional) items before the anchor, default 5, max 20
- `depth_after` (number, optional) items after the anchor, default 5, max 20
- `project` (string) project name filter

### Step 3: fetch, full details for the kept IDs only

Review the titles from step 1 and the context from step 2. Pick the IDs that matter, discard the rest, then fetch. Always batch two or more IDs into a single `get_observations` call: one request instead of N.

```text
get_observations(ids=[11131, 10942])
```

Parameters:

- `ids` (array of numbers, required) observation IDs to fetch
- `orderBy` (string, optional) "date_desc" (default), "date_asc"
- `limit` (number, optional) max observations to return
- `project` (string, optional) project name filter

Returns complete observation objects with title, subtitle, narrative, facts, concepts, and files, roughly 500 to 1000 tokens each.

## Why filter before fetch

- Search index: about 50 to 100 tokens per result.
- Full observation: about 500 to 1000 tokens each.
- Batch fetch: one HTTP request instead of N individual requests.
- The result is roughly 10x token savings by filtering before fetching.

A naive recall fetches everything that matched, then reads it. That spends the expensive call on results you would have discarded anyway. Searching first, then fetching only the kept IDs, spends the expensive call once on exactly what you need.

---

**Source**: This file packages the claude-mem search workflow as a portable instruction artifact. Full repo, with examples and a Cursor rule, at https://github.com/HermeticOrmus/mem-search-skills.

**License**: MIT, use it, fork it, merge it into your own.
