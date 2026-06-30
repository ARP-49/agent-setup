---
name: software-engineer
description: Senior software engineer discipline — test-driven development, clean architecture, surgical refactoring, and frontend best practices for React/TypeScript. Always consult during planning phases.
---

# Software Engineer — TDD, Clean Code, Frontend Craft

A senior engineering discipline that governs how code is planned, written, tested, and maintained. This skill is **mandatory reading** during planning phases to ensure every task embeds testability, correctness, and craftsmanship from the start.

---

## 1. Gherkin Feature Descriptions

Every feature, user story, or behavior change should be described in **Gherkin** before any code is written. Gherkin is the human-readable contract — it bridges the gap between "what the user expects" and "what the tests verify."

### Why Gherkin First

- Forces you to think about **behavior from the user's perspective** before implementation details
- Creates a shared language between planning, testing, and code
- Maps directly to test cases — each `Scenario` becomes one or more tests
- Catches missing edge cases and ambiguous requirements early

### Gherkin Format

```gherkin
Feature: <Feature name>
  As a <role>
  I want <capability>
  So that <benefit>

  Background:
    Given <shared precondition across scenarios>

  Scenario: <Happy path description>
    Given <initial state>
    When <action taken>
    Then <expected result>
    And <additional assertion>

  Scenario: <Edge case or error path>
    Given <initial state>
    When <invalid action>
    Then <error handling behavior>
```

### Rules for Writing Gherkin

1. **One behavior per scenario.** If you need "And When", it's probably two scenarios.
2. **Declarative, not imperative.** Describe *what*, not *how*. "Given the user is logged in" not "Given the user navigates to /login and types..."
3. **Use domain language.** "Given a quiz with 5 questions" not "Given a QuizConfig object with length=5."
4. **Cover the unhappy paths.** At least one error/edge scenario per feature.
5. **Background for shared setup.** Don't repeat the same Given in every scenario.

### Example: Quiz Scoring

```gherkin
Feature: Quiz scoring
  As a learner
  I want to see my score after completing a quiz
  So that I know how well I understood the material

  Background:
    Given the user has started a quiz
    And the quiz has 10 questions

  Scenario: Perfect score
    When the user answers all 10 questions correctly
    Then the score should be 100%
    And a streak bonus should be applied
    And confetti animation should play

  Scenario: Partial score
    When the user answers 7 questions correctly and 3 incorrectly
    Then the score should be 70%
    And incorrect answers should be shown with corrections

  Scenario: Quiz abandoned midway
    When the user answers 4 questions and navigates away
    Then the quiz should be saved as incomplete
    And no XP should be awarded

  Scenario: Empty quiz (edge case)
    Given the quiz has 0 questions due to a filter error
    Then an error message should be shown
    And the user should be redirected to the quiz selection
```

### Gherkin in Task Specs

When planning tasks, include a `#### Gherkin` section in each task that adds user-facing behavior:

```markdown
### Task <N>: <Title>
**Files**: ...

#### Gherkin
\```gherkin
Feature: ...
  Scenario: ...
\```

#### Tests
- [ ] Unit test mapping to Scenario 1: ...
- [ ] Unit test mapping to Scenario 2: ...
```

The Gherkin scenarios drive the test list — each scenario should map to at least one test.

---

## 2. Agent Behavior & Code Modification Discipline

These rules govern how an AI coding agent should interact with the codebase. They are non-negotiable.

### Priority Hierarchy

1. **User directives are supreme.** A direct command from the user overrides all other rules.
2. **Verify facts with tools.** Don't rely on internal knowledge for version-specific, time-sensitive, or API-specific details — use tools to find the current answer.
3. **Follow these principles** in the absence of a direct user override.

### Code Generation Philosophy

- **Simplest solution first.** Solve the problem with the least code and complexity. No premature optimization.
- **Standard library and patterns first.** Only add third-party libraries if they are the industry standard or truly necessary.
- **No elaborate cleverness.** Prioritize readability and maintainability over "clever" approaches.
- **Focus on the request.** Don't add features, edge cases, or handling the user didn't ask for.
- **Explain the "why".** Briefly justify choices — the reasoning is more valuable than the code itself.

### Surgical Code Modification

- **The codebase is the source of truth.** Respect its structure, style, and conventions.
- **Minimal necessary changes.** Alter the absolute minimum code to implement the change.
- **No unsolicited refactoring.** Only modify, refactor, or delete code the user explicitly targeted.
- **Integrate, don't replace.** Add new logic into existing structure rather than rewriting entire functions.
- **Declare intent before acting.** State what you're about to do and why, concisely, before making changes.

### Tool Usage

- **Use tools when they're the right answer.** Don't avoid them when they're essential.
- **Edit directly when asked.** Don't generate code snippets for the user to paste — apply changes directly.
- **Purposeful action.** Every tool call must directly serve the user's stated goal. No drive-by modifications.

---

## 3. Test-Driven Development Lifecycle

TDD is the default development rhythm. Every feature, bugfix, and refactor starts with a test.

### The Red-Green-Refactor Cycle

```
1. RED    — Write a failing test that defines the desired behavior
2. GREEN  — Write the minimum code to make it pass
3. REFACTOR — Clean up while keeping all tests green
4. REPEAT — Next slice of behavior
```

### TDD Rules

1. **Never write production code without a failing test first.** If there's no test, the behavior is undefined.
2. **One behavior per test.** Tests should be small, focused, and named after the behavior they verify.
3. **Tests are first-class code.** Apply the same quality standards — no magic strings, no duplication, descriptive names.
4. **Tests document intent.** A well-named test suite is the best living documentation.
5. **Fast feedback.** Tests must run in milliseconds. Mock external dependencies, isolate units.

### Test Naming Convention

```typescript
// Pattern: describe WHAT, it SHOULD when CONDITION
describe('calculateXP', () => {
  it('should return 0 for an empty quiz', () => { ... });
  it('should award bonus XP when streak exceeds 5', () => { ... });
  it('should cap daily XP at the configured maximum', () => { ... });
});
```

### What to Test

| Layer | Test Type | What to Assert |
|-------|-----------|----------------|
| Utility functions | Unit | Pure input → output |
| Custom hooks | Unit (renderHook) | State transitions, returned values |
| React components | Integration | Renders correct UI, responds to interactions |
| API handlers | Integration | Request → response shape, status codes, error cases |
| Services | Unit | Business logic, edge cases, error paths |
| Zod schemas | Unit | Valid inputs pass, invalid inputs rejected with correct errors |

### What NOT to Test

- Implementation details (private methods, internal state shape)
- Third-party library internals
- Trivial getters/setters with no logic
- CSS / visual layout (use visual regression tools instead)

### Testing Patterns for This Project

```typescript
// React component test (Vitest + Testing Library)
import { render, screen, fireEvent } from '@testing-library/react';
import { describe, it, expect, vi } from 'vitest';

describe('TextInput', () => {
  it('should call onSubmit with the text when submitted', () => {
    const onSubmit = vi.fn();
    render(<TextInput onSubmit={onSubmit} />);

    fireEvent.change(screen.getByRole('textbox'), { target: { value: 'Test message' } });
    fireEvent.click(screen.getByRole('button', { name: /submit/i }));

    expect(onSubmit).toHaveBeenCalledWith('Test message');
  });

  it('should not call onSubmit when input is empty', () => {
    const onSubmit = vi.fn();
    render(<TextInput onSubmit={onSubmit} />);

    fireEvent.click(screen.getByRole('button', { name: /submit/i }));

    expect(onSubmit).not.toHaveBeenCalled();
  });
});

// Hook test
import { renderHook, act } from '@testing-library/react';

describe('useQuizTimer', () => {
  it('should decrement every second when started', () => {
    vi.useFakeTimers();
    const { result } = renderHook(() => useQuizTimer(30));

    act(() => result.current.start());
    act(() => vi.advanceTimersByTime(3000));

    expect(result.current.remaining).toBe(27);
    vi.useRealTimers();
  });
});

// API function test
describe('POST /api/items', () => {
  it('should return 400 for missing name field', async () => {
    const req = createMockRequest({ body: { userId: '123' } });
    const result = await itemHandler(req);

    expect(result.status).toBe(400);
    expect(result.body.error.code).toBe('VALIDATION_ERROR');
  });
});
```

### Test Infrastructure

- **Runner**: Vitest (frontend), Vitest or Jest (backend)
- **Component testing**: `@testing-library/react` — test user behavior, not implementation
- **Mocking**: `vi.fn()`, `vi.mock()` — mock at boundaries, not internals
- **Coverage**: Track but don't chase 100% — focus on critical paths and business logic

---

## 4. Planning With Testability

When decomposing tasks during the planning phase, **every task must include test requirements**.

### Task Test Section Format

Every task in a plan **must** have a `#### Tests` section:

```markdown
### Task <N>: <Title>
**Files**: `path/to/module.ts`, `path/to/module.test.ts`

#### Description
...

#### Tests
- [ ] Unit test: <specific behavior to test>
- [ ] Unit test: <edge case>
- [ ] Integration test: <end-to-end slice if applicable>

#### Verify
- [ ] All new tests pass
- [ ] No existing tests broken
- [ ] TypeScript compiles clean
```

### Planning Checklist (test awareness)

Before finalizing any plan:

- [ ] Every task that writes logic also writes tests
- [ ] Test files are listed in the task's `**Files**` field
- [ ] Edge cases are called out explicitly
- [ ] Interfaces are designed for testability (dependency injection, pure functions)
- [ ] No task is "too simple to test" — if it has logic, it has a test

---

## 5. Clean Code Principles

### Functions

- **Single responsibility.** One function does one thing. If you need "and" to describe it, split it.
- **Small.** Under 30 lines ideal, never above 50. If it's longer, extract.
- **Pure when possible.** Same input → same output, no side effects. Easier to test, easier to reason about.
- **Early returns.** Avoid deep nesting. Check invalid conditions first and bail out.
- **Descriptive names.** `calculateStreakBonus` not `calc` or `process`. The name IS the documentation.

```typescript
// BAD: nested, unclear, untestable
function handle(data: any) {
  if (data) {
    if (data.type === 'quiz') {
      if (data.answers) {
        let score = 0;
        for (let i = 0; i < data.answers.length; i++) {
          if (data.answers[i].correct) score++;
        }
        return score / data.answers.length;
      }
    }
  }
  return 0;
}

// GOOD: flat, typed, testable
function calculateQuizScore(answers: readonly QuizAnswer[]): number {
  if (answers.length === 0) return 0;
  const correctCount = answers.filter(a => a.correct).length;
  return correctCount / answers.length;
}
```

### Types

- **No `any`.** Ever. Use `unknown` + narrowing if the type is truly dynamic.
- **`interface` for object shapes.** `type` for unions, intersections, and mapped types.
- **`readonly` by default.** Only make mutable what must mutate.
- **Zod at boundaries.** Validate external data (API inputs, storage reads) with Zod schemas. Trust internal types.
- **Discriminated unions over optional fields.** Makes impossible states unrepresentable.

```typescript
// BAD: wide open, anything goes
interface Response {
  status?: string;
  data?: any;
  error?: string;
}

// GOOD: impossible states are unrepresentable
type Response =
  | { success: true; data: ChatMessage }
  | { success: false; error: { code: ErrorCode; message: string } };
```

### Error Handling

- **Never swallow errors.** Catch → log → re-throw or handle meaningfully.
- **Typed errors.** Use discriminated unions or custom error classes, not bare `Error`.
- **Fail fast.** Validate inputs at the boundary. Don't let bad data trickle through.
- **User-facing errors are different from developer errors.** Map internal errors to user-friendly messages at the UI layer.

### Naming

| Element | Convention | Example |
|---------|-----------|---------|
| Variables/functions | camelCase, descriptive | `itemId`, `calculateScore` |
| Constants | SCREAMING_SNAKE or camelCase | `MAX_RETRIES`, `defaultConfig` |
| Types/interfaces | PascalCase | `Item`, `Result` |
| Booleans | `is`/`has`/`should` prefix | `isLoading`, `hasError` |
| Event handlers | `on`/`handle` prefix | `onSubmit`, `handleClick` |
| Files | kebab-case or PascalCase (components) | `item-utils.ts`, `DashboardView.tsx` |

---

## 6. React & Frontend Craft

### Component Architecture

- **Small components.** Under 200 lines. If it's growing, extract sub-components.
- **Smart vs dumb.** Container components (hooks, state, logic) wrap presentational components (just UI + props).
- **Custom hooks for logic.** If a component has non-trivial state management, extract a `useXyz` hook. This makes the logic testable independently.
- **Props over context.** Pass data explicitly until prop drilling becomes genuinely painful (3+ levels of the same prop).

```typescript
// GOOD: logic in hook, component is just UI
function DashboardView() {
  const { items, score, addItem, isComplete } = useDashboard();

  if (isComplete) return <DashboardComplete score={score} />;
  return <DashboardItem items={items} onAdd={addItem} />;
}
```

### State Management

- **Local state first.** `useState` and `useReducer` for component-scoped state.
- **Shared state management.** Use the project's existing state management solution (Context, Redux, Signals, etc.).
- **Derived state.** Don't store what you can compute. Compute in render or `useMemo`.
- **Minimal state.** Store the source of truth only. Derive everything else.

### Performance

- **Measure before optimizing.** Don't sprinkle `useMemo`/`useCallback` everywhere "just in case."
- **Memoize when**: a child component re-renders expensively, or a computation is genuinely costly.
- **Code split** large views with `React.lazy` + `Suspense` when bundle size matters.
- **Virtualize** long lists (>100 items).

### Accessibility (a11y)

- **Semantic HTML first.** Use `<button>`, `<nav>`, `<main>`, `<section>` — not div soup.
- **ARIA only when needed.** If semantic HTML covers it, skip ARIA.
- **Keyboard navigation.** Every interactive element must be focusable and operable via keyboard.
- **Focus management.** When content changes dynamically (modals, route changes), manage focus.
- **Color is not the only signal.** Use icons + text + color together.

```tsx
// BAD
<div onClick={handleClick} className="button">Send</div>

// GOOD
<button onClick={handleClick} aria-label="Send message">
  <SendIcon aria-hidden />
  <span>Send</span>
</button>
```

### CSS / Tailwind

- **Utility-first.** Compose Tailwind classes. Extract `@apply` only for highly reused patterns.
- **Design tokens.** Use CSS variables / Tailwind theme for colors, spacing, fonts. Never hardcode.
- **Responsive by default.** Mobile-first breakpoints.
- **No inline styles** unless truly dynamic (e.g., computed positions).

---

## 7. Refactoring Discipline

### When to Refactor

- **Before adding a feature.** If the code you're about to change is messy, clean it first (separate commit).
- **During code review.** Small improvements as you go.
- **When tests are green.** Never refactor without a passing test suite as your safety net.
- **Never during a feature sprint without tests.** If there are no tests, write them first, then refactor.

### Refactoring Protocol

```
1. Ensure tests pass (or write them if missing)
2. Make ONE small structural change
3. Run tests — still green?
4. Commit
5. Repeat
```

### Common Refactors

| Smell | Refactor | Impact |
|-------|----------|--------|
| Long function (>50 lines) | Extract focused sub-functions | Readability, testability |
| Duplicated logic | Extract shared utility/hook | DRY, single source of truth |
| God component (>200 lines) | Extract sub-components + hook | Maintainability |
| Primitive obsession | Introduce value objects / types | Type safety |
| Shotgun surgery (change touches 10 files) | Colocate related logic | Cohesion |
| Feature envy (using another module's internals) | Move logic to the owning module | Encapsulation |
| Boolean parameters | Replace with descriptive options object | Readability |

### Refactoring Golden Rules

1. **Behavior stays the same.** If you're changing behavior, it's not a refactor — it's a feature.
2. **Tests are your safety net.** No tests? Write them first. Non-negotiable.
3. **Small steps.** Each step should be independently committable and correct.
4. **One concern per commit.** Don't mix refactoring with feature work.

---

## 8. Architecture Principles

### Separation of Concerns

```
UI Layer        → Components render state. No business logic.
Hook Layer      → Custom hooks manage state and side effects.
Service Layer   → API clients, data transformation, business rules.
Type Layer      → Interfaces and schemas. The contract between layers.
```

### Dependency Direction

Dependencies flow **inward** — UI depends on hooks, hooks depend on services, services depend on types. Never the reverse.

```
Components → Hooks → Services → Types
     ↓          ↓         ↓
  (renders)  (manages)  (processes)
```

### Design for Testability

- **Inject dependencies.** Pass services/clients as parameters, don't import singletons.
- **Pure functions for logic.** Side effects at the edges, pure computation in the core.
- **Thin boundaries.** API handlers validate input → call service → return result. The handler is glue, the service is logic.
- **Interface-driven.** Define the contract first, implement second. This is how TDD works naturally.

---

## 9. Tailwind CSS v4 & Styling Standards

This project uses **Tailwind CSS v4** with the Vite plugin. The rules below prevent common mistakes.

### Setup (v4 — what's different)

- **No `tailwind.config.js`.** Configuration is CSS-first using `@theme` in `index.css`.
- **No `postcss.config.js`.** The `@tailwindcss/vite` plugin handles everything.
- **No `@tailwind` directives.** Use `@import "tailwindcss";` in the main CSS file.
- **Automatic content detection.** No need to specify `content` paths.

### Canonical Class Names (enforced by linter)

| Old / Non-canonical | Canonical (use this) |
|---------------------|---------------------|
| `bg-gradient-to-b` | `bg-linear-to-b` |
| `bg-gradient-to-br` | `bg-linear-to-br` |
| `flex-shrink-0` | `shrink-0` |

### CSS Custom Properties (short form)

Use `property-(--variable)` not `property-[var(--variable)]`:

```tsx
// ❌ Verbose
className="bg-[var(--primary-600)] text-[var(--text-900)]"

// ✅ Short form
className="bg-(--primary-600) text-(--text-900)"
```

### Design Tokens

Use the project's defined design tokens (colors, spacing, fonts) via CSS variables. Never hardcode hex values or pixel measurements. Reference the project's stylesheet to see what's available.

### Styling Rules

- **Utility-first.** Compose Tailwind classes. Extract `@apply` only for highly reused patterns.
- **Design tokens only.** Use theme variables for colors, spacing, fonts. Never hardcode hex values.
- **Responsive by default.** Mobile-first breakpoints.
- **No inline styles** unless truly dynamic (e.g., computed positions).
- **Custom theme via `@theme`** in CSS, not a JavaScript config file.

### Common Mistakes to Avoid

```css
/* ❌ OLD v3 style — do not use */
@tailwind base;
@tailwind components;
@tailwind utilities;

/* ✅ v4 style */
@import "tailwindcss";
```

```javascript
// ❌ Do not create tailwind.config.js in v4
// ❌ Do not create postcss.config.js when using @tailwindcss/vite
```

---

## 10. Code Review Checklist

Before any code is considered complete:

- [ ] Gherkin scenarios written for user-facing behavior
- [ ] All new logic has corresponding tests (mapped from Gherkin scenarios)
- [ ] Tests are descriptive and test behavior, not implementation
- [ ] No `any` types
- [ ] Functions are under 50 lines
- [ ] Components are under 200 lines
- [ ] Error cases are handled explicitly
- [ ] No `console.log` left behind
- [ ] Names are descriptive and consistent
- [ ] No duplicated logic
- [ ] Accessibility basics covered (semantic HTML, keyboard nav, ARIA labels)
- [ ] If using Tailwind: canonical class names and design token usage
- [ ] No hardcoded values — uses project design tokens for colors, spacing, fonts
- [ ] Only minimal, surgical changes made — no unsolicited refactoring
- [ ] TypeScript compiles with zero errors
- [ ] All existing tests still pass
