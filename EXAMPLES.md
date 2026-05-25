# Examples

Concrete walkthroughs of the search, filter, fetch workflow. Each shows the tool calls and the token-cost reasoning behind them.

All examples assume the claude-mem MCP is installed and the `search`, `timeline`, and `get_observations` tools are available.

---

## 1. Find recent bugfixes

**Scenario**: You hit an error that feels familiar and want to know whether it was fixed before.

**Step 1, search the index** (cheap, filter on type and obs_type):

```text
search(query="auth token", type="observations", obs_type="bugfix", limit=20, project="my-project")
```

Returns a table of about 50 to 100 tokens per row:

```text
| ID     | Time     | Type   | Title                            | Read |
|--------|----------|--------|----------------------------------|------|
| #10942 | 2:15 PM  | bugfix | Fixed auth token expiration race | ~50  |
| #10803 | 11:02 AM | bugfix | Patched refresh-token reuse      | ~50  |
| #10654 | Yesterday| bugfix | Logout did not clear session     | ~50  |
```

**Step 2, fetch only the relevant one**:

```text
get_observations(ids=[10942])
```

**Cost reasoning**: the search scanned 20 candidates for roughly 1000 to 2000 tokens of index. You read the titles, decided only `#10942` matched, and spent one expensive fetch (about 500 to 1000 tokens) on it. Fetching all 20 up front would have cost roughly 10x more for the same answer.

---

## 2. What happened last week

**Scenario**: You return to a project and want a digest of recent work without loading every observation.

**Step 1, search by date range** (no query needed, the date filter does the work):

```text
search(type="observations", dateStart="2026-05-12", dateEnd="2026-05-18", limit=20, project="my-project")
```

Returns the index for that window. Read the titles to reconstruct the arc of the work: what was built, what broke, what got decided.

**Step 2, fetch only the entries you need detail on**:

```text
get_observations(ids=[11201, 11188], orderBy="date_asc")
```

**Cost reasoning**: the index alone often answers "what happened last week" because the titles carry the summary. You fetch full narratives only for the one or two items you actually need to act on. Most of the time you never leave step 1.

---

## 3. Context around a discovery

**Scenario**: Search surfaced one observation that looks like the key decision, but you need the work that led up to it and what followed.

**Step 1, search to find the anchor**:

```text
search(query="switched to server-side rendering", limit=10, project="my-project")
```

Suppose `#11131` is the decision.

**Step 2, timeline around it** (pull the surrounding window in chronological order):

```text
timeline(anchor=11131, depth_before=5, depth_after=5, project="my-project")
```

Returns 11 items, the 5 before, the anchor, and the 5 after, with observations, sessions, and prompts interleaved. This shows the reasoning that preceded the decision and the changes that implemented it.

**Step 3, fetch full detail for the two or three that matter**:

```text
get_observations(ids=[11129, 11131, 11134])
```

**Cost reasoning**: `timeline` is a single windowed call, much cheaper than fetching each neighboring observation in full just to see whether it is relevant. You spend full-fetch tokens only after the timeline tells you which neighbors matter.

---

## 4. Batch fetch

**Scenario**: After filtering, you have three IDs worth reading in full.

**Wrong** (N requests):

```text
get_observations(ids=[11131])
get_observations(ids=[10942])
get_observations(ids=[10855])
```

**Right** (one request):

```text
get_observations(ids=[11131, 10942, 10855], orderBy="date_desc")
```

**Cost reasoning**: the token cost of the payload is the same either way, but three separate calls add three round-trips of overhead and three tool-call framings. One batched call returns the same observations in a single request. Always batch when you have two or more IDs.

---

## A note on cost

The workflow trades one or two extra steps up front for a large saving on the payload. Search is cheap and you run it first; timeline is a single windowed call you run only when you need context; the expensive `get_observations` runs last and only on IDs you already decided to keep.

The failure mode is skipping straight to a broad fetch because it feels faster. It is faster to type and slower in tokens: you pay full price for results you then discard. On a project with a large memory, that is the difference between a cheap recall and one that crowds out the rest of your context window.

## Further reading

- [claude-mem](https://github.com/thedotmack/claude-mem): the memory system these tools query
- [`CLAUDE.md`](CLAUDE.md): the portable instruction artifact with the full parameter reference
- [`vibe-engineer-skills`](https://github.com/HermeticOrmus/vibe-engineer-skills): guidelines for directing AI codegen well
