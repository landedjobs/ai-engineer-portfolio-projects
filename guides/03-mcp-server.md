# 🔌 Build guide: an MCP server that works in real clients, with proper auth

> **Time:** 2-4 weeks · **Difficulty:** 🔴 Advanced · **Catalog entry:** [Agents & tool use](../catalog/agents.md)
> **Proves:** protocol design, OAuth-based authorization, tenant isolation, compatibility across clients, and the habit of testing security claims instead of asserting them.

Most MCP portfolio projects are a local server with one tool that adds two numbers or reads a file. That is a fine way to learn the protocol, but it proves very little. This guide builds the version a reviewer can trust: a remote server with a handful of useful tools, real authorization, a hard boundary between customers, and evidence that it works in more than one client. Then it attacks its own auth and publishes the results.

The MCP spec is direct about where most servers go wrong. Servers that implement authorization must verify every inbound request, must check that a token was issued for them, and must not treat possession of a session or state handle as proof of identity ([📘 MCP security best practices](https://modelcontextprotocol.io/specification/2026-07-28/basic/security_best_practices)). A project that tests those rules with hostile requests stands out from one that only shows a happy-path demo.

---

## 1. Scope

**Build:** a remote MCP server for a small support-analytics product you own, for example a ticket database with two or more customer organisations (tenants).

**Tools (5-7, typed inputs and outputs):**

- Read-only: `search_tickets`, `get_ticket`, `ticket_stats`, `list_customers`.
- One write behind approval: `update_ticket_status`, which returns a proposed change first and applies it only after the user confirms.
- One resource or prompt, so you exercise more than tools.

**The server must:**

1. Run over Streamable HTTP, not only stdio.
2. Accept only access tokens issued for this server, with scopes that match each tool.
3. Never return or change another tenant's data, whatever arguments the client sends.
4. Work end to end in at least two different MCP clients.

**Leave out of the core:** a model inside the server, many loosely related tools, a custom auth server you write from scratch. Use an existing identity provider and spend the time on the checks.

## 2. Architecture

```
MCP client A ─┐
MCP client B ─┼─▶ Streamable HTTP endpoint ─▶ token check (issuer, audience, expiry) ─▶ scope check per tool
MCP Inspector ┘                                         │
                                     tenant from the token, never from arguments
                                                        ▼
                              shared service layer ─▶ database ─▶ audit log (request ID, tenant, tool, result)
                                      ▲
                           plain REST API uses the same layer
```

Design rules that make it reviewable:

- **One service layer, two front doors.** The MCP handlers and a plain REST API call the same functions. The protocol is an integration boundary, not a second copy of your logic.
- **Tenant comes from the token.** Ignore any `tenant_id` the client sends. A client-supplied tenant is a request, not a fact.
- **Validate the audience.** The authorization spec says servers must check that tokens were issued specifically for them, using resource indicators ([📘 MCP authorization](https://modelcontextprotocol.io/specification/2026-07-28/basic/authorization); [📘 RFC 8707](https://www.rfc-editor.org/rfc/rfc8707)). Never pass the client's token through to a downstream API.
- **Publish protected resource metadata** so clients can discover your auth server ([📘 RFC 9728](https://www.rfc-editor.org/rfc/rfc9728)), and answer a missing or weak token with a 401 or a scope challenge, not a vague error.
- **Least-privilege scopes.** Read tools need a read scope. The write tool needs a separate write scope that most tokens don't carry.
- **Pin versions.** Write the protocol version and SDK version you tested into the README.

## 3. Milestones

| Week | Ship | Done when |
|---|---|---|
| 1 | Seed database with two tenants, service layer, REST API, local MCP tools over stdio, tool test cases | Every tool passes its tests through both REST and MCP |
| 2 | Streamable HTTP transport, two-client walkthrough, integration tests | The same workflow runs in two clients and the Inspector, recorded |
| 3 | OAuth with an existing provider, audience and scope checks, tenant isolation, hostile auth tests | Rejected requests are measured, not assumed |
| 4 | Conformance checks, rate limits, reconnect tests, audit log, deployment, README | A stranger can deploy it and reproduce your table |

**Two weeks only?** Stop after week 2 with the remote server working in two clients and a clean tool test suite. Add auth as the next milestone, and say plainly in the README that it isn't there yet.

## 4. The test set

Write the tests before you add auth, so you can see what auth changes.

- **Tool cases (60-100):** an input, the expected structured result, and for the write tool the expected database state afterwards.
- **Client workflows (5-10):** short tasks a user would do ("find open tickets for Acme older than a week and close the duplicates"), run in each client.
- **Hostile auth cases (40-80):** no token, expired token, token for a different audience, valid token with too little scope, a guessed or reused session ID, and a valid token plus another tenant's ID in the arguments.
- **Failure cases:** the database is slow, the client disconnects mid-call, the same write arrives twice.

## 5. What to measure

Run the protocol suite from the official conformance framework against your server and report which checks you passed and which you skipped ([💻 modelcontextprotocol/conformance](https://github.com/modelcontextprotocol/conformance)). Then publish your own table:

| Metric | How to compute | Baseline | Your system |
|---|---|---|---|
| Protocol checks | Passed applicable conformance checks ÷ applicable checks run; list exclusions | | |
| Tool correctness | Cases with the right structured result and the right database state ÷ tool cases | | |
| Client compatibility | Workflows passing ÷ workflows, one column per client | | |
| Auth rejection | Bad-audience, expired and under-scoped requests rejected ÷ such requests (target: all) | | |
| Tenant leaks | Cross-tenant reads or writes ÷ isolation tests (target: 0) | | |
| MCP overhead | p50 and p95 MCP latency minus the same call through REST | | |

Notes that make the table credible:

- **Baseline = the unhardened server** from week 2. Run the hostile cases against it too. Showing that it leaked and the hardened one doesn't is the whole point.
- **Report each client separately.** "Works in MCP" means nothing; "passes 9 of 10 workflows in client A and 7 of 10 in client B" does.
- **Local stdio success is not auth evidence.** The authorization spec covers HTTP transports; stdio servers take credentials from the environment instead.

## 6. Cost and latency

Time each stage separately: token validation, transport, service execution, serialisation, and the round trip as the client sees it. Compare against the same call through the REST API, so the protocol's cost is visible on its own.

The server doesn't need to call a model for each tool. Report model spend only for workflows where a client model is actually driving the tools, and keep that apart from server hosting cost. Price hosting and the identity provider at today's rates, and write the date down.

## 7. Common mistakes

- Taking the tenant from tool arguments instead of the token.
- Accepting any valid token from your identity provider, whatever its audience.
- Passing the client's token straight through to a downstream API.
- Treating a session ID as proof of who the caller is.
- One broad scope for every tool, including the write.
- Testing only in one client, or only over stdio, and calling it compatible.

## 8. Stretch goals

- **MCP App:** a small inline UI for reviewing the proposed write before approving it ([💻 modelcontextprotocol/ext-apps](https://github.com/modelcontextprotocol/ext-apps)). Host support varies, so list which hosts you tested ([📘 MCP client matrix](https://modelcontextprotocol.io/extensions/client-matrix)).
- **Progressive scopes:** start every token with read-only access and ask for the write scope only when the write tool is first called.
- **Reuse the eval from [guide 2](02-tool-using-agent.md):** serve that agent's tool layer through this server and run the same task set.

## 9. References

- 💻 [modelcontextprotocol/python-sdk](https://github.com/modelcontextprotocol/python-sdk) · 24.9k ⭐ · MIT · the official Python SDK for servers and clients.
- 💻 [PrefectHQ/fastmcp](https://github.com/PrefectHQ/fastmcp) · 28.6k ⭐ · Apache-2.0 · a higher-level Python framework for MCP servers and clients.
- 💻 [modelcontextprotocol/inspector](https://github.com/modelcontextprotocol/inspector) · 11.3k ⭐ · MIT moving to Apache-2.0 · testing tools for calling your server by hand before any client is involved.
- 💻 [modelcontextprotocol/conformance](https://github.com/modelcontextprotocol/conformance) · 147 ⭐ · MIT moving to Apache-2.0 · protocol and auth test suites for servers and clients.
- 📘 [MCP security best practices](https://modelcontextprotocol.io/specification/2026-07-28/basic/security_best_practices) · token passthrough, confused deputy, state handle hijacking, scope minimisation.
- 📘 [MCP authorization](https://modelcontextprotocol.io/specification/2026-07-28/basic/authorization) · audience validation, protected resource metadata, scope challenges.

## 10. Put it on your resume

Fill in your own numbers; these show the shape, not real results:

- *Built a remote MCP server with 6 typed tools over a two-tenant support database; passed 96 of 100 tool cases and 9 of 10 workflows in each of two clients.*
- *Added OAuth audience and scope checks; rejected 80 of 80 hostile requests and leaked 0 records across 50 cross-tenant tests, up from 14 leaks in the unhardened server.*
- *Kept MCP overhead to 40 ms p95 over the same calls through REST by sharing one service layer.*

Next: write the README with the [project README template](../templates/project-readme.md), and see [presenting your project](../templates/presenting-your-project.md) for the interview.

---

<sub>Sources checked 17 Nov 2026. Star counts are a snapshot. Found something out of date? [Open an issue](https://github.com/landedjobs/ai-engineer-portfolio-projects/issues).</sub>
