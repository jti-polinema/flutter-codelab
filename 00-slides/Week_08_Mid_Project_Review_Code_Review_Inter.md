---
marp: true
theme: default
paginate: true
size: 16:9
---

<style>
/* ====== TEMA JTI POLINEMA ====== */
section {
  font-size: 20px;
  padding: 70px 70px 64px 70px;
  color: #1b1b1b;
  /* Bar oranye di atas + latar putih keabu-abuan lembut */
  background:
    linear-gradient(90deg, #f7941d 0%, #fdb913 100%) 0 0 / 100% 10px no-repeat,
    linear-gradient(155deg, #ffffff 55%, #eef0f2 100%);
  background-repeat: no-repeat;
}
section h1 { font-size: 34px; color: #111; margin: 0.35em 0; }
section h2 { font-size: 28px; color: #333; margin: 0.35em 0; }
section h3 { font-size: 22px; color: #1b2a4a; margin: 0.35em 0; }
section p, section ul, section ol { margin: 0.3em 0; }
section table { font-size: 19px; }
section pre, section code { font-size: 17px; }
section blockquote { font-size: 19px; }
section .katex-display { font-size: 1em; margin: 0.3em 0; }
section .katex { font-size: 1.1em; }

/* Logo + teks institusi di pojok kanan atas (semua slide) */
section::before {
  content: "POLITEKNIK NEGERI MALANG\A Department of Information Technology";
  position: absolute;
  top: 26px;
  right: 36px;
  width: 360px;
  text-align: right;
  padding-right: 92px;
  white-space: pre;
  font-size: 12px;
  font-weight: 600;
  line-height: 1.45;
  color: #1b2a4a;
  background: url('jti-polinema-logo.png') no-repeat right center / 96px 48px;
  z-index: 10;
}

/* Footer: blok oranye "jti.polinema.ac.id" + bar navy "D4 Teknik Informatika" + nomor halaman */
section::after {
  content: "D4 Teknik Informatika  ·  " counter(page);
  position: absolute;
  left: 0; right: 0; bottom: 0;
  height: 30px;
  padding-left: 200px;
  display: flex;
  align-items: center;
  justify-content: center;
  color: #ffffff;
  font-size: 13px;
  background:
    url('data:image/svg+xml;utf8,<svg xmlns="http://www.w3.org/2000/svg" width="200" height="30"><text x="100" y="20" text-anchor="middle" font-family="Arial" font-size="13" font-weight="bold" fill="%231b2a4a">jti.polinema.ac.id</text></svg>') no-repeat left top / 200px 30px,
    linear-gradient(to right, #f7941d 0 200px, #1b2a4a 200px 100%);
}

/* ====== SLIDE JUDUL (class: lead) ====== */
section.lead {
  justify-content: center;
  text-align: left;
  padding-left: 80px;
}
section.lead h1 {
  font-size: 48px;
  font-weight: 800;
  color: #111;
  border: none;
}
section.lead h2 {
  font-size: 34px;
  font-weight: 600;
  color: #666;
  margin-top: 0;
}
section.lead h3 {
  font-size: 19px;
  font-weight: 500;
  color: #222;
  margin-top: 18px;
}
section.lead p { color: #333; }

/* Hapus garis bawah default h1 pada theme default */
section h1 { border-bottom: none; }

/* Slide padat: konten auto menyusut & tengah */
section.fit {
  display: flex;
  flex-direction: column;
  justify-content: center;
}
section.fit h1 { font-size: 30px; margin: 0.25em 0; }
section.fit h2 { font-size: 24px; margin: 0.25em 0; }
section.fit h3 { font-size: 20px; margin: 0.25em 0; }
section.fit p, section.fit ul, section.fit ol { margin: 0.25em 0; }
section.fit pre { margin: 0.3em 0; line-height: 1.35; }
section.fit pre, section.fit code { font-size: 15px; }
section.fit blockquote { font-size: 17px; margin: 0.3em 0; }
section.fit table { font-size: 17px; }
</style>

<!-- _class: lead -->

# Week 8
## Mid Project Review & Code Review

**Mobile Programming**

Flutter • Git Flow • Pull Request • Code Review • Static Analysis

> From code that runs to code that passes team review

---

# Learning Outcomes

After this session, students will be able to:

1. Explain Git Flow and branching strategy.
2. Write quality commits and pull requests.
3. Perform constructive code reviews.
4. Run static analysis and linters.
5. Assess mid-semester project readiness.
6. Verify AI-generated review suggestions.

---

# Why Review?

The Week 7 project is tidy, but:

```text
Commit "fix bug" with no context, untraceable
800-line PR with no description, reviewers lost
Mixed styles per file, reviews wasted on spacing
```

Team projects need discipline:

```text
small branch -> small PR -> fast review
clear commits -> readable history
clean analyze -> reviews focus on logic
```

**Goal:** code that is not only working, but collaboration-ready.

---

# Mini Poll

### Which commit is easiest to review?

**A.** `fix bug`
**B.** `feat(notes): validate empty title`
**C.** `update`
**D.** `final fix for real`

### Discussion

> What is the difference between "working code" and "review-ready code"?

---

# Part 1
## Git Flow & Collaboration

---

<!-- _class: fit -->

# Branching Model

*Part 1 - Git Flow: one branch, one purpose.*

```text
main      -> always releasable, protected
develop   -> weekly feature integration
feature/* -> daily work, short-lived
fix/*     -> quick fixes + tests
```

Ground rule:

> Branch from `develop`, PR back to `develop`.
> `main` only via release. No direct commits.

---

<!-- _class: fit -->

# Quality Commits

*One commit = one logical, reviewable change.*

```text
good: feat(notes): validate empty title + test
good: fix(auth): refresh token before one retry
bad: fix bug / update / final fix for real
```

Format: `type(scope): short description`

> `feat`, `fix`, `refactor`, `test`, `docs`, `chore`.

---

<!-- _class: fit -->

# Anatomy of a Pull Request

*Small PRs get reviewed fast. Huge PRs scare reviewers.*

```text
Title: feat(notes): validation + test (one feature max)
Body: problem -> solution -> how to test -> screenshot
Size: < 300 lines, < 3 days of work
Status: analyze + tests green before requesting review
```

> A PR without test steps is not ready.

---

<!-- _class: fit -->

# Issue Tracking

*Every PR starts from a clear issue.*

```text
issue #42 -> branch fix/42-login-crash
  -> small commits -> PR "Closes #42"
  -> review -> merge -> issue closed
```

Checklist for a good issue:

- Reproduction steps + expected result
- Clear labels and priority

---

# Part 2
## Code Review & Static Analysis

---

# What Gets Reviewed?

Order focus from important to trivial:

1. **Correctness** - logic, edge cases, error handling.
2. **Architecture** - dependency direction, layers, contracts.
3. **Readability** - names, small functions, needed comments.
4. **Tests** - tests for new behavior?
5. **Style** - leave to the formatter, not debate.

> Never spend reviews on spacing. Automate it.

---

<!-- _class: fit -->

# 15-Minute Review Checklist

*Find at least the following in a peer review:*

- 2 correctness issues (null, state, error paths)
- 1 architecture violation (cross-layer import)
- 1 test gap (missing success/failure case)
- 1 readability issue (name / oversized function)
- 1 concrete suggestion + reason

> End with a decision: **Approve / Request changes.**

---

<!-- _class: fit -->

# Good Review Comments

*Tough on code, kind to humans.*

```text
weak: "this code is bad, fix it"
good: "This function is 60 lines with 3 jobs.
  Suggestion: extract validation to core/validate.dart.
  Reason: unit-testable without widgets."
```

Pattern: **observation -> impact -> concrete proposal.**

---

<!-- _class: fit -->

# Static Analysis & Linters

*Let machines catch the mechanical issues.*

```text
flutter analyze        # must be clean, zero warnings
dart format --set-exit-if-changed .
flutter test           # green before PR
```

Enforce in CI so dirty PRs are rejected automatically.

> Human reviewers focus on design, not semicolons.

---

<!-- _class: fit -->

# Reading Analyze Output

*Understand the intent, do not memorize rules.*

```text
warning: unused_import -> remove dead imports
info: prefer_const -> use const for cheaper rebuilds
error: must_be_immutable -> widgets must stay immutable
```

Fix errors first, then warnings, then info.

---

# Part 3
## Mid Project Review & Practice

---

<!-- _class: fit -->

# Mid-Semester Definition of Done

*The project may continue to part two when:*

- Feature-first structure + proven dependency rule
- `flutter analyze` clean + green tests
- Small PRs + clear commits + closed issues
- Complete README + screenshots + run guide
- Peer review feedback addressed

> Not "many features", but "a trusted foundation".

---

<!-- _class: fit -->

# Lab: A Simple PR

*Simulate the team flow in 20 minutes:*

```text
1. Create issue: "Validate empty title + test"
2. Branch: git checkout -b feat/notes-validation
3. Small commits + push + open PR to develop
4. Fill template: problem, solution, test steps
5. Ask 1 reviewer, fix, merge
6. Make sure the issue auto-closes
```

One PR per pair, not per class.

---

<!-- _class: fit -->

# Peer Review Simulation

*Swap roles, keep a strict timebox:*

- **Author (7 min):** demo changes + risks + review focus.
- **Reviewer (7 min):** use the shortened checklist.
- **Discussion (6 min):** agree fixes + owner.

> Goal: every PR gets at least 3 meaningful comments.

---

<!-- _class: fit -->

# AI Lab

*Use AI as a Co-Reviewer*

Example prompt:

```text
Review this PR as a senior Flutter dev.
Focus: correctness, architecture, tests.
Ignore style. Give top 5 findings
+ severity (blocker/suggestion) + code fix.
```

### Never accept AI output raw.

---

# AI Verification Challenge

After the AI reviews:

1. **Read** - understand each finding, no copy-paste.
2. **Verify** - cross-check with grep + real tests.
3. **Reject/accept** - reject wrong advice with reasons.
4. **Test** - analyze + tests stay green.
5. **Fix** - apply what is valid, log manual work.
6. **Explain** - demo without reading AI notes.

### Minimum tests

- 2 AI findings proven correct with tests
- 1 AI finding rejected with a reason
- `flutter analyze` + `flutter test`

---

<!-- _class: fit -->

# Review Anti-Patterns

*Avoid:*

### 1. Blind LGTM without reading the diff

```text
approving 800 lines in 1 minute = no review
```

### 2. Nitpicking without priority

```text
spacing debate while logic leaks pass
```

### 3. Reviewing without running

```text
never pull + test, only skim the diff
```

---

# Group Activity

## 15-Minute Code Review

Swap PRs between pairs, find at least:

- 2 correctness issues (null, state, error paths)
- 1 architecture or leaking-DI violation
- 1 missing test case
- 1 exemplary comment (observation + proposal)
- 1 AI decision rejected/accepted + reason

Then answer:

> **Is this project ready for part two? Why?**

---

<!-- _class: fit -->

# Quick Quiz

### 1. Branch for daily work?

A. Commit straight to `main`
B. `feature/*` from `develop`
C. Commit to a friend's branch
D. No branch at all

### 2. What does a good PR look like?

A. 800 lines, many features at once
B. Small, one feature, test steps included
C. No description for speed
D. No tests to stay light

---

<!-- _class: fit -->

# Quiz Answers

### 1 -> B

```text
feature from develop, back via PR
```

### 2 -> B

```text
small + test steps -> fast review
```

### 3 -> Run `flutter analyze` first

```text
machines for style, humans for design
```

---

<!-- _class: fit -->

# Deliverables

## Repository

```text
08-week-8-mid-project-review/
├── docs/
│   ├── review-checklist.md
│   └── ai-review-vs-decision.md
├── README.md
└── screenshots/
```

Minimum README content:

- mid-semester status + issue/PR map
- `flutter analyze` + `flutter test` results
- PR screenshots + review comments
- AI prompt + proposal-vs-decision table

---

<!-- _class: fit -->

# Week 8 Rubric

| Component | Weight |
|---|---:|
| Git Flow + PR + Commits | 25% |
| Peer Code Review | 25% |
| Static Analysis + Green Tests | 15% |
| Mid Review + Follow-up | 15% |
| Responsible AI Usage | 10% |
| Presentation & Reflection | 10% |
| **Total** | **100%** |

---

# Checklist Before Submit

### Collaboration

- [ ] Branch from develop, small one-feature PR
- [ ] Clear commits, issue closed via PR
- [ ] Reviewed by 1 peer + followed up

### Evidence

- [ ] `flutter analyze` clean + green tests
- [ ] PR screenshots + review comments
- [ ] Mid-semester status in README

### AI

- [ ] Prompt + decision table recorded
- [ ] Able to explain findings without AI

---

# Exit Ticket

Answer before class ends:

### 1. What separates a blind LGTM from a responsible approve?

### 2. When is "request changes" required, when is a suggestion enough?

### 3. One AI finding you rejected, and why?

### 4. One thing you fixed after peer review?

---

# Key Takeaways

## Collaboration

> Small branches, small PRs, fast reviews.

## Review

> Tough on code, kind to humans.

## Automation

> Machines for style, humans for design.

## AI

> AI proposes the review.
> **Developers decide.**

---

# Next Week

## Week 9: AI-Assisted Development / Vibe Coding

```text
Structured project (passed review)
   |
   v
Prompt + context + verification
   |
   v
AI generates, developers test + refactor
```

### Goal

> Use AI fast without losing quality control.

---

# Build -> Verify -> Explain

## The Mobile Learning Principle

```text
         AI
           |
           v
        Generate
           |
           v
        Developer
           |
           v
          Verify
           |
           v
          Test
           |
           v
        Refactor
           |
           v
         Explain
```

## Do not just be able to write code.

### Be a developer who welcomes review and can review others.
