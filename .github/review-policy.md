# Review policy — automated adjudication

This file is the authoritative blocking policy for the LLM review pipeline.
The adjudicator reads it from the PR's base branch. Changes to this file are
themselves control-integrity changes and block until reviewed.

## Blocks merge (`request_changes`)

1. **security** — missing or weakened authentication/authorization; cross-tenant
   data access (any query or lookup not scoped to the owning company/client);
   secrets, credentials, tokens, or private keys in code or config; injection
   paths (SQL, shell, template); PII written to logs or returned in responses
   beyond what the endpoint requires.
2. **correctness** — clear bugs (unreachable logic, inverted conditions, wrong
   operators, unhandled None/null on a required path); data-loss risk;
   destructive or irreversible data migrations without a rollback path.
3. **control_integrity** — changes that disable or weaken CI workflows,
   required status checks, branch rulesets, this policy file, the review
   pipeline workflows, encryption settings, audit logging, or error reporting.
4. **untested_new_logic** — an entirely untested new endpoint, service,
   workflow, or module containing business logic. (Coverage *gaps* in
   existing code are notes, not blocks.)

## Never blocks (non-blocking notes only)

Style, naming, refactoring suggestions, performance improvements short of an
obvious hot-path problem (e.g. an unbounded N+1 in a request path *is* a
correctness block; a suboptimal loop is a note), documentation, dependency
nitpicks.

## lea-wealth-plugins specifics

Blocking, under the severities above:
- **security**: credential or API-key material in plugin code; requests to
  client systems without TLS; client data written outside the sanctioned
  storage paths.
- **untested_new_logic**: new plugin entry points with no test coverage.
