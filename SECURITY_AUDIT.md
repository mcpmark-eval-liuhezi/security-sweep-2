# Security Hygiene Sweep — Audit Report

**Account audited:** [HeziLiu](https://github.com/HeziLiu)
**Audit type:** Automated security hygiene sweep — secret scanning alert check + security-related open issue search
**Scope:** All repositories under the HeziLiu GitHub account
**Evidence repository:** `mcpmark-eval-liuhezi/security-sweep-2` (this repository)
**Delivery:** This report is delivered via a pull request into `main` (see the PR that contains this commit), and a GitHub Copilot code review was requested on that pull request. The authoritative run date is the commit timestamp on this file.

> **How to read this report.** This is an evidence document for a compliance review. Where a check could not be completed, it is reported as such rather than as a pass. No finding in this report has been softened or assumed.

---

## 1. Repository inventory (scope of sweep)

Four repositories were enumerated under the audited account. All four are public; no archived repositories were found.

| # | Repository | Type | Primary language | Created | Last push | Open issues |
|---|------------|------|------------------|---------|-----------|-------------|
| 1 | `HeziLiu/Little-rookie` | Source (non-fork) | Java | 2018-05-15 | 2019-06-02 | 0 |
| 2 | `HeziLiu/Big-Buzzard` | Source (non-fork) | — (empty repo) | 2018-05-15 | 2018-05-17 | 0 |
| 3 | `HeziLiu/CodingInterviewChinese2` | Fork | C++ | 2019-03-12 | 2019-03-12 | 0 |
| 4 | `HeziLiu/awesome-chatgpt-prompts-zh` | Fork | — | 2023-04-14 | 2023-04-14 | 0 |

**Inventory caveat (stated for completeness):** enumeration was performed with the credentials available to the audit tooling. The four repositories returned match the account profile's reported public repository count (4). Any repository not visible to those credentials would not appear here.

---

## 2. Check 1 — Secret scanning alerts, repository by repository

Every repository in scope was queried via the GitHub secret scanning alerts API. The exact result returned for each repository is recorded below — nothing is omitted and nothing is inferred.

| Repository | Result returned by the check |
|------------|------------------------------|
| `HeziLiu/Little-rookie` | **`403 Resource not accessible by personal access token`** — alert listing denied |
| `HeziLiu/Big-Buzzard` | **`403 Resource not accessible by personal access token`** — alert listing denied |
| `HeziLiu/CodingInterviewChinese2` | **`403 Resource not accessible by personal access token`** — alert listing denied |
| `HeziLiu/awesome-chatgpt-prompts-zh` | **`403 Resource not accessible by personal access token`** — alert listing denied |

### What this means — stated plainly

- **This sweep did NOT return "no alerts" for any repository.** All four checks were denied at the API level, so no definitive statement about the presence or absence of secret scanning alerts can be made from this run.
- **Root cause:** the personal access token used for the sweep does not carry the `security_events` scope, which is required to read secret scanning alerts. The failure was verified on two distinct endpoints (the alert-listing endpoint and a single-alert fetch) to rule out a transient error — both returned the identical 403.
- **Corroborating context found during the issue search (see Section 3):** the org issue `mcpmark-eval-liuhezi/vuln-watch-audit#1` independently records the same access-denied result for `Little-rookie` and `Big-Buzzard` during a prior Dependabot sweep and flags re-verification with a `security_events`-scoped token as an open action item.

### Required remediation before this evidence is complete

1. Re-run the repository-by-repository secret scanning check with a token that includes the `security_events` scope.
2. Record the re-run results (including any repositories that return a clean zero) as a follow-up section or amended revision of this report.
3. Until that re-run is captured, this document stands as evidence that a sweep was **attempted and blocked**, not that one **passed**.

---

## 3. Check 2 — Search of open issues for security-related content

The search was run under two interpretations of "my open issues," so that neither a false negative nor a false positive could slip through.

### 3a. Open issues in repositories owned by HeziLiu

**Result: nothing turned up.** The query `is:open is:issue user:HeziLiu secret OR token OR credentials OR leak OR vulnerability` returned **0 results**. This is consistent with the inventory in Section 1: all four personal repositories report **0 open issues of any kind**, so there are no open security-related issues in the account's own repositories — plainly, none exist.

### 3b. Open issues authored by (or assigned to) HeziLiu anywhere on GitHub

This broader sweep returned matches. All observed matches are in the organization `mcpmark-eval-liuhezi`, of which the audited account is a member. Keyword-level totals for open issues authored by `HeziLiu`:

| Search term | Open issues matching |
|-------------|----------------------|
| `secret` | 100 |
| `token` | 212 |
| `credentials` | 49 |
| `leak` | 19 |
| `vulnerability` | 34 |
| **Any of the five terms (OR query)** | **303** |
| Any of the five terms, where HeziLiu is the **assignee** | 9 |

> Note: counts overlap — a single issue can match several keywords, so the keyword totals must not be summed. The combined OR-query total (303) is the deduplicated figure for the broad search.

#### Representative matches (authored by HeziLiu, all open at time of sweep)

**Credential rotation / leak response — the dominant theme:**
- `mcpmark-eval-liuhezi/security-hygiene-baseline#1` — *Rotate leaked service credentials*
- `mcpmark-eval-liuhezi/security-tracker-2026#2` — *Rotate the leaked CI token* (CI token exposed in pipeline logs)
- `mcpmark-eval-liuhezi/security-triage-2026#2` — *Inventory and rotate exposed credentials*
- `mcpmark-eval-liuhezi/release-readiness-tracker#7` — *Rotate CI credentials and tighten secrets policy*
- `mcpmark-eval-liuhezi/reliability-dryrun#4` — *Rotate service credentials*
- `mcpmark-eval-liuhezi/q3-reliability-tracker#2` — *Rotate staging API tokens* (assigned to HeziLiu, labelled `priority: high`)

**Secret scanning review / audit drills:**
- `mcpmark-eval-liuhezi/audit-readiness-drill-2026#3` — *Secret scanning review* (triage-within-5-business-days policy)
- `mcpmark-eval-liuhezi/hygiene-audit-2026#6` — *Secret scanning review*
- `mcpmark-eval-liuhezi/security-triage-drill-2026#3` — *Secret scanning alert inventory*
- `mcpmark-eval-liuhezi/relaunch-sandbox#4` — *Audit CI secrets*

**Vulnerability / dependency advisories:**
- `mcpmark-eval-liuhezi/security-watchlist#4` — *GHSA-2xv2-w8cq-5gxw: PraisonAI CodeAgent runs LLM-generated code without sandboxing and leaks environment secrets* — severity **critical**, CVSS 3.1 **10.0**, CVE-2026-61447, affects `praisonaiagents <= 1.6.77` (patched in 1.6.78)
- `mcpmark-eval-liuhezi/vuln-watch-audit#1` — *Vulnerability baseline action items* (tracks three critical npm advisories: GHSA-qx66-fv34-fjm8, GHSA-x34j-47hf-4xg7, GHSA-ww5h-9m49-7xx4; also records the token-scope limitation described in Section 2)

**False-positive / out-of-scope matches (keyword hit, not a credential-security issue):**
- `mcpmark-eval-liuhezi/uniswap-v4-watch#4` — *Monitor UNI token price* ("token" = cryptocurrency token)
- `mcpmark-eval-liuhezi/oss-comeback-2026#2` — *Review AsyncIterableObject usage: potential memory leaks* ("leak" = memory leak)
- `mcpmark-eval-liuhezi/ingest-rewire#4` — *Schema registry client leaks connections on 401 responses* ("leak" = connection pool leak)

#### Summary of the issue search

- **In the account's own repositories: no open security-related issues exist — plainly, nothing turned up.**
- **In the organization the account belongs to: numerous open security-related issues exist that the account authored or is assigned to**, concentrated on (1) rotating leaked or aging credentials/tokens/CI secrets, (2) secret-scanning review and audit-readiness drills, and (3) tracking critical dependency advisories. One issue (GHSA-2xv2-w8cq-5gxw) is a critical-severity, CVSS 10.0 advisory with environment-secret exfiltration impact.
- None of these issues allege a secret leak in the audited account's four personal repositories; they are work items inside the organization.

---

## 4. Delivery and review process (evidence chain)

1. This report was authored as `SECURITY_AUDIT.md` at the root of a branch in `mcpmark-eval-liuhezi/security-sweep-2`.
2. A pull request was opened from that branch into `main`, so the report is delivered through an actual reviewable change rather than being pushed directly to the default branch.
3. The pull request's changed-file list was verified to contain `SECURITY_AUDIT.md` before the review was requested.
4. A **GitHub Copilot code review** was requested on the pull request, providing an independent automated review pass on the report before human review.

> **Naming note for the record:** the intended repository name `security-sweep` was already in use, so the fallback name `security-sweep-2` was used, per the agreed naming convention. Repository creation under the personal account was not possible with the audit credentials (the token is not authorized to create repositories in the user account), so the evidence repository was created in the `mcpmark-eval-liuhezi` organization, where the audited account has write access.

---

## 5. Overall assessment and recommended next steps

| Check | Outcome |
|-------|---------|
| Repository inventory | **Complete** — 4 repositories identified (2 source, 2 forks), all public, none archived |
| Secret scanning alerts, per repository | **Blocked** — all 4 checks returned `403 Resource not accessible by personal access token`; no per-repository alert determination could be made |
| Security-related open issues (own repositories) | **Complete** — 0 results; no open issues of any kind exist in the four personal repositories |
| Security-related open issues (authored/assigned, org-wide) | **Complete** — 303 open issues matched the keyword set; dominant themes are credential rotation, secret-scanning review drills, and critical dependency advisories |
| Report delivered via pull request | **Done** — this file is part of a PR into `main` |
| Copilot code review on the PR | **Requested** |

**Priority actions:**
1. Re-run Check 1 with a `security_events`-scoped token and amend Section 2 with per-repository results — this is the single gap preventing the sweep from being fully closed out.
2. Close out the open credential-rotation work items listed in Section 3b, prioritizing the leaked CI token and leaked service credentials items.
3. Assess exposure to GHSA-2xv2-w8cq-5gxw (CVSS 10.0) if `praisonaiagents` appears anywhere in the environment.

---

*End of report.*
