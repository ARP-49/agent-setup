---
name: tdd
description: Test-Driven Development workflow — Red-Green-Refactor cycle, Gherkin scenario authoring, test list generation, and task-level test specifications for Python (pytest) and TypeScript (Vitest/Jest). Use when planning or implementing any new feature, bugfix, or refactor to ensure tests are written before production code.
---

# TDD — Test-Driven Development

## Quick Start

For every task, before writing a single line of production code:

1. Write a Gherkin scenario describing the behavior
2. Translate each scenario into a failing test
3. Write the minimum code to turn it green
4. Refactor — keep all tests green

---

## Workflow

### Step 1 — Write Gherkin First

```gherkin
Feature: <behavior name>
  As a <role>
  I want <capability>
  So that <benefit>

  Scenario: <happy path>
    Given <precondition>
    When  <action>
    Then  <expected result>

  Scenario: <error/edge case>
    Given <precondition>
    When  <invalid action>
    Then  <error handling>
```

Rules:
- One behavior per scenario
- Declarative ("Given the user is authenticated"), not imperative
- Cover at least one error/edge path per feature

---

### Step 2 — Derive the Test List

Convert each Gherkin scenario to a named test before opening the implementation file.

**Python (pytest):**
```python
# test_<module>.py
def test_<behavior>_<condition>():
    ...  # RED: write assertion first, implementation after

def test_<behavior>_raises_when_<condition>():
    with pytest.raises(SomeError):
        ...
```

**TypeScript (Vitest / Jest):**
```typescript
describe('<unit>', () => {
  it('should <behavior> when <condition>', () => { ... });
  it('should throw when <condition>', () => { ... });
});
```

---

### Step 3 — Red-Green-Refactor

```
RED      Write the failing test. Run it. Confirm it fails for the right reason.
GREEN    Write the minimum production code to pass. No extras.
REFACTOR Clean up duplication, naming, structure. All tests stay green.
REPEAT   Next scenario from the list.
```

Hard rules:
- Never write production code before a failing test exists
- Never add logic not required by a currently failing test
- Mock all I/O and external dependencies; keep unit tests < 10 ms

---

### Step 4 — Task Spec Format

When specifying tasks in a PRD, include a `#### Tests` section:

```markdown
### Task N: <Title>

#### Gherkin
\```gherkin
Feature: ...
  Scenario: happy path
    ...
  Scenario: error path
    ...
\```

#### Tests
- [ ] `test_<behavior>_<happy_path>` — asserts <expected>
- [ ] `test_<behavior>_raises_<error>` — asserts <error type>
- [ ] `test_<behavior>_<edge_case>` — asserts <boundary>

#### Verify
- [ ] All tests listed above pass
- [ ] No production code exists without a corresponding test
- [ ] Coverage for this module ≥ 80%
```

---

## Test Structure Cheat Sheet

| Layer | Tool | Assert |
|---|---|---|
| Pure functions / logic | pytest / Vitest | Return values, raised exceptions |
| Data transforms | pytest | Input → output shape and values |
| API handlers | pytest + httpx/TestClient | Status codes, response bodies |
| ML model outputs | pytest | Output shape, value ranges, no NaN |
| React components | Vitest + Testing Library | Render, user events, accessibility |

---

## Anti-Patterns

- ❌ Writing tests after the code is working ("test-after")
- ❌ Testing implementation details (private methods, internal state)
- ❌ Tests that require a live database, network, or file system (use fixtures/mocks)
- ❌ Vague test names: `test_works`, `test_ok`, `test_1`
- ❌ Single monolithic test covering multiple behaviors
