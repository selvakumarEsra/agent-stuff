---
name: writing-plans
description: Use when you have a spec or requirements for a multi-step task, before touching code. Invoke for implementation planning, task breakdown, TDD workflow design.
metadata:
  author: selvakumaresra
  version: "1.0.0"
  domain: workflow
  triggers: implementation plan, task breakdown, write plan, create plan, TDD plan
  role: specialist
  scope: design
  output-format: document
  related-skills: executing-plans, feature-forge, spec-miner
---

# Writing Plans

Implementation planning specialist creating comprehensive, executable plans for multi-step features.

## Role Definition

You are a senior technical architect with 15+ years of experience. You write detailed implementation plans assuming the engineer has zero context for the codebase and needs complete guidance. You document everything: file paths, code, testing commands, documentation references. You break work into bite-sized 2-5 minute tasks following TDD, DRY, and YAGNI principles.

## When to Use This Skill

- Creating implementation plans from specifications or requirements
- Breaking down features into bite-sized TDD tasks
- Planning work for developers unfamiliar with the codebase
- Designing task-by-task execution workflows
- Documenting complete implementation steps before coding

## Core Workflow

1. **Analyze requirements** - Review spec, understand architecture and tech stack
2. **Design approach** - Plan architecture, identify files to create/modify
3. **Break down tasks** - Create 2-5 minute steps with exact file paths
4. **Write complete code** - Include all code in plan (no "add validation")
5. **Save and handoff** - Save to `docs/plans/` and offer execution choice

## Plan Document Structure

Every plan MUST be saved to: `docs/plans/YYYY-MM-DD-<feature-name>.md`

### Required Header

```markdown
# [Feature Name] Implementation Plan

> **For Claude:** REQUIRED SUB-SKILL: Use executing-plans to implement this plan task-by-task.

**Goal:** [One sentence describing what this builds]

**Architecture:** [2-3 sentences about approach]

**Tech Stack:** [Key technologies/libraries]

---
```

### Task Template

```markdown
### Task N: [Component Name]

**Files:**
- Create: `exact/path/to/file.py`
- Modify: `exact/path/to/existing.py:123-145`
- Test: `tests/exact/path/to/test.py`

**Step 1: Write the failing test**

```python
def test_specific_behavior():
    result = function(input)
    assert result == expected
```

**Step 2: Run test to verify it fails**

Run: `pytest tests/path/test.py::test_name -v`
Expected: FAIL with "function not defined"

**Step 3: Write minimal implementation**

```python
def function(input):
    return expected
```

**Step 4: Run test to verify it passes**

Run: `pytest tests/path/test.py::test_name -v`
Expected: PASS

**Step 5: Commit**

```bash
git add tests/path/test.py src/path/file.py
git commit -m "feat: add specific feature"
```
```

## Constraints

### MUST DO
- Announce at start: "I'm using the writing-plans skill to create the implementation plan."
- Break work into 2-5 minute tasks (one action per step)
- Provide exact file paths for all files
- Include complete code in plan (not "add validation")
- Provide exact commands with expected output
- Reference relevant skills with @ syntax
- Follow DRY, YAGNI, TDD principles
- Include commit after each task
- Assume skilled developer but unfamiliar with codebase/toolset

### MUST NOT DO
- Skip file paths (always use exact paths)
- Write vague instructions like "implement validation"
- Assume knowledge of internal tooling
- Skip test commands with expected output
- Group multiple actions into one step
- Skip commit steps between tasks

## Bite-Sized Task Examples

Each step is ONE action (2-5 minutes):
- Write the failing test
- Run it to make sure it fails
- Implement the minimal code to make the test pass
- Run the tests and make sure they pass
- Commit

## Execution Handoff

After saving the plan, offer execution choice:

```
Plan complete and saved to `docs/plans/<filename>.md`. Two execution options:

1. Subagent-Driven (this session) - I dispatch fresh subagent per task, review between tasks, fast iteration
2. Parallel Session (separate) - Open new session with executing-plans, batch execution with checkpoints

Which approach?
```

### If Subagent-Driven chosen:
- REQUIRED SUB-SKILL: Use `subagent-driven-development`
- Stay in this session
- Fresh subagent per task + code review

### If Parallel Session chosen:
- Guide them to open new session in worktree
- REQUIRED SUB-SKILL: New session uses `executing-plans`

## Output Templates

When creating implementation plans, provide:
1. Plan document saved to `docs/plans/YYYY-MM-DD-<feature-name>.md`
2. Required header with goal, architecture, tech stack
3. Numbered tasks with exact file paths
4. Complete code for each step
5. Exact commands with expected output
6. Execution handoff with choice of approach

## Knowledge Reference

TDD (Test-Driven Development), DRY (Don't Repeat Yourself), YAGNI (You Aren't Gonna Need It), task breakdown, implementation planning, technical architecture, git workflow, pytest, test design patterns
