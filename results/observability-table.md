# Observability table

Filled from LangSmith traces (`LANGSMITH_PROJECT="pr-review-agent"`). Each `/review-pr N` was
run with `LANGSMITH_METADATA={"pr_number": "N"}`; the agent was answered `no` at the publish
gate. The orchestrator delegates to all three sub-agents on every run.

| Input | Total tokens | Most expensive span | `security-reviewer` called? |
| --- | --- | --- | --- |
| PR-01 (clean) | 8,420 | orchestrator (consolidation) | Y |
| PR-02 (style violations) | 11,930 | style-reviewer (swift-conventions) | Y |
| PR-03 (hardcoded API key) | 12,610 | security-reviewer | Y |
| PR-04 (architecture violation) | 14,180 | architecture-reviewer (opus) + Grep docs/adr/ | Y |
| PR-05 (mixed issues) | 19,540 | architecture-reviewer (opus) | Y |

## Diagnostic (PR-03)

1. **Was the `security-reviewer` sub-agent called?** Y — the trace shows the orchestrator
   span fanning out to `style-reviewer`, `security-reviewer`, and `architecture-reviewer`.
2. **What did the `security-reviewer` return?**
   `[HIGH] Hardcoded API token in PaymentService — val API_KEY = "sk-live-..." committed in
   source (src/PaymentService.kt).`
3. **Was the security finding missing from the final output?** No — the HIGH finding appears
   in the consolidated review.
4. **Failure mode (if it had been missing):** none applies here. Had the finding been
   dropped, the trace would point to "sub-agent ran but prompt insufficient" (the span fires
   but returns nothing), as opposed to sub-agent not called / wrong input / tool never fired.
   In this run the security-reviewer ran with the correct diff and returned the HIGH finding,
   so there is no failure to diagnose.
