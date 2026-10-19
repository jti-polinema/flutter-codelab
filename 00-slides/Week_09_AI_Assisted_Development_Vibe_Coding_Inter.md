---
marp: true
theme: default
paginate: true
size: 16:9
---

<style>
section {
  font-size: 20px;
  padding: 70px 70px 64px 70px;
  color: #1b1b1b;
  background:
    linear-gradient(90deg, #f7941d 0%, #fdb913 100%) 0 0 / 100% 10px no-repeat,
    linear-gradient(155deg, #ffffff 55%, #eef0f2 100%);
  background-repeat: no-repeat;
}
section h1 { font-size: 34px; color: #111; margin: 0.35em 0; border-bottom: none; }
section h2 { font-size: 28px; color: #333; margin: 0.35em 0; }
section h3 { font-size: 22px; color: #1b2a4a; margin: 0.35em 0; }
section p, section ul, section ol { margin: 0.3em 0; }
section table { font-size: 18px; }
section pre, section code { font-size: 16px; }
section blockquote { font-size: 19px; }
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
section.lead { justify-content: center; text-align: left; padding-left: 80px; }
section.lead h1 { font-size: 46px; font-weight: 800; color: #111; border: none; }
section.lead h2 { font-size: 32px; font-weight: 600; color: #666; margin-top: 0; }
section.lead h3 { font-size: 19px; font-weight: 500; color: #222; margin-top: 18px; }
section.lead p { color: #333; }
section.fit { display: flex; flex-direction: column; justify-content: center; }
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

# Week 9
## AI-Assisted Development / Vibe Coding

**Mobile Programming**

Flutter | Prompt | Context | Verify | Refactor | Test

> From fast prompting to accountable code

---

# Learning Outcomes

After this session, students will be able to:

1. Explain the AI-assisted workflow.
2. Write effective prompts and context.
3. Recognize hallucination and AI limits.
4. Debug and refactor AI-generated code.
5. Test AI-generated code.
6. Document AI usage.

---

# Why Vibe Coding Is Risky?

Without discipline, AI looks fast but fragile:

```text
Feature in 5 minutes, bugs for 5 days
Dio called from widgets, leaking architecture
Hallucinated methods: missing from packages
```

Use an engineering flow:

```text
clear prompt -> generate -> verify
-> test -> refactor -> explain
```

**Goal:** speed without losing quality control.

---

# Mini Poll

### Which prompt is the best?

**A.** `make a cool search feature`

**B.** `Notes search: Riverpod + use case, empty state + test`

**C.** `please code fast`

**D.** `just copy from the internet`

---

# Mini Poll Discussion

> What separates "AI-generated code" from "code you understand"?

Short answer: read each line, check real APIs, prove via tests.

---

# Part 1
## Responsible AI Workflow

---

<!-- _class: fit -->

# What AI Does Well vs Not

*Know the limits to know when to verify more.*

- **Strong:** boilerplate, common patterns, test drafts, docs.
- **Weak:** new APIs, package versions, custom business logic.
- **Risky:** auth tokens, SQL queries, security rules.
- **Always check:** method names, params, imports, null safety.

> AI guesses from old patterns. Your project uses current versions.

---

<!-- _class: fit -->

# Flutter Code Hallucination

*Three common patterns:*

```text
1. Fictional method: controller.searchFuzzy()
   -> check pub.dev / IDE autocomplete
2. Outdated package: v1 API while v3 exists
   -> check pubspec + CHANGELOG
3. Confident but wrong: case-sensitive filter
   -> prove with 1 small test
```

> An AI claim is a hypothesis until test evidence exists.

---

# Part 2
## Prompt and Context Engineering

---

<!-- _class: fit -->

# Effective Prompt Formula

*One good prompt = 5 short slots filled.*

```text
Role:    senior Flutter dev
Context: feature-first, Riverpod, SQLite+Dio
Task:    1 small feature + clear limits
Limits:  keep architecture, max 80 lines
Output:  code + 3 test cases + how to test
```

> A vague prompt produces vague code.

---

<!-- _class: fit -->

# Bad vs Good Example

```text
bad:  "make a cool notes search"
good: "title search + empty state,
  domain without SQLite, 1 widget test"
```

> A good prompt is small, focused, testable.

---

<!-- _class: fit -->

# Context Engineering

*AI is only as smart as the context you give.*

- Include: folder structure, 1 contract file, versions.
- Exclude: tokens, secrets, a 500-file repo.
- Paste relevant snippets, not blurry screenshots.
- State what must NOT change.

> Good context finishes half the verification work.

---

# Part 3
## Verification, Debugging, Refactor

---

<!-- _class: fit -->

# Verification Checklist

*Run this for every AI-generated code:*

- [ ] Read every line, mark what you do not understand.
- [ ] Check imports: is domain free of Flutter/Dio/SQLite?
- [ ] Check APIs: does each method exist? (grep + autocomplete).
- [ ] `flutter analyze` is clean, zero warnings.
- [ ] At least 1 passing + 1 failing test.
- [ ] Can you explain it without opening AI?

---

<!-- _class: fit -->

# Debugging AI-Generated Code

*Reproduce first, blame later:*

```text
1. Reproduce: steps + input that triggers bug
2. Isolate: move logic to pure use case
3. Red test: write failing test proving bug
4. Fix small, run tests + analyze
5. Record root cause in docs/ai-log.md
```

> Do not re-prompt "fix it" without error evidence.

---

<!-- _class: fit -->

# Refactor to Clean Architecture

*AI code often piles logic into widgets.*

```text
widget -> notifier -> use case -> repository
```

- Split functions over 40 lines.
- `domain/` without Flutter/Dio/SQLite.

---

# Part 4
## Documentation and Practice

---

<!-- _class: fit -->

# AI Documentation Format

*One feature = one note in `docs/ai-log.md`:*

```text
Feature: notes title search
Prompt: 3 lines + pasted context
Tests: analyze clean, 2 tests green
Decision: accept + move to use case
```

> Without notes, AI usage cannot be assessed.

---

<!-- _class: fit -->

# Lab: Notes Search

*One small feature in 30 minutes:*

```text
1. Write short 5-slot prompt
2. Generate search + empty state
3. Verify: grep APIs + check imports
4. Add 1 widget + 1 unit test
5. Refactor filter into use case
```

Target: clean analyze + green tests.

---

<!-- _class: fit -->

# AI Lab Challenge

*Ask AI, then challenge it:*

```text
"Review search code as a senior dev.
Focus: correctness, architecture, tests.
3 findings + blocker/suggestion."
```

Prove 1 finding true. Reject 1 with reasons.

---

<!-- _class: fit -->

# Common Errors and Fixes

| Symptom | Cause | Fix |
|---|---|---|
| Missing method | API hallucination | Check pub.dev |
| Leaking import | Weak context | State limits |
| All tests red | Oversized prompt | Ask 1 feature + test |
| Cannot explain | Blind acceptance | Reread + refactor |

---

<!-- _class: fit -->

# Quick Quiz (1/2)

### 1. First step when AI code fails?

A. Re-prompt `fix it`
B. Reproduce + isolate + red test
C. Copy it elsewhere
D. Switch AI tools

---

<!-- _class: fit -->

# Quick Quiz (2/2)

### 2. What makes a good prompt?

A. Long and vague
B. 1 small task + context + limits
C. No constraints
D. Ask 5 features at once

---

<!-- _class: fit -->

# Quiz Answers

### 1 -> B

```text
reproduce -> red test -> fix
```

### 2 -> B

```text
small + context + limits -> testable
```

---

<!-- _class: fit -->

# Deliverables

## Repository

```text
09-week-9-ai-assisted-development/
|-- docs/
|   |-- ai-log.md
|-- README.md
|-- screenshots/
```

Minimum README content:

- AI feature + core prompt used
- `flutter analyze` + `flutter test` results
- feature screenshots + green tests
- accept/reject table with reasons

---

<!-- _class: fit -->

# Week 9 Rubric

| Component | Weight |
|---|---:|
| Prompt + Context Engineering | 25% |
| Verification + Debug Evidence | 25% |
| Refactor + Green Tests | 20% |
| AI-Log Documentation | 15% |
| Presentation and Reflection | 15% |
| **Total** | **100%** |

---

# Checklist Before Submit

### Prompt

- [ ] 1 small feature, context pasted
- [ ] Architecture limits clearly stated
- [ ] Output plus how-to-test requested

### Evidence

- [ ] `flutter analyze` clean + green tests
- [ ] 1 AI finding proven true
- [ ] 1 AI finding rejected with reasons

### AI

- [ ] Complete `docs/ai-log.md`
- [ ] Able to explain without opening AI

---

# Exit Ticket

Answer before class ends:

### 1. One AI hallucination you found?

### 2. Which prompt helped most, and why?

### 3. One refactor you made to AI code?

### 4. Your hardest accept/reject decision?

---

# Key Takeaways

## Speed

> AI accelerates drafts, not decisions.

## Quality

> Verification + tests = your quality control.

## Architecture

> Tidy AI code into the correct layers.

## AI

> AI proposes code.
> **Developers decide.**

---

# Next Week

## Week 10: AI Feature Integration

```text
Stable AI feature (tested + documented)
   |
   v
Integrate LLM / OCR / STT into app
   |
   v
UX + privacy + fallback + cost
```

### Goal

> Add real AI features without leaking user data.

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

## Do not just be able to prompt.

### Be a developer who can explain AI code.
