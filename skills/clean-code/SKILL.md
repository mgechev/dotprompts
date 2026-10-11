---
name: clean-code
description: >-
  Enforces code readability, maintainability, and clean architecture principles
  on every coding task. Activate this skill whenever writing, editing,
  refactoring, reviewing, generating, or modifying source code in any
  programming language. Also activate when creating new files, modules,
  classes, functions, tests, or entire projects. Do not use for
  non-code tasks such as prose writing, data analysis, or DevOps
  configuration that contains no application logic.
---

# Clean Code & Software Craftsmanship

Apply every principle below when writing or modifying code. These are
non-negotiable defaults. Deviate only when the user explicitly asks to,
or when a project-level rule or style guide contradicts a specific point.

---

## 1 — Structure & Size Limits

### 1.1 File Length
- Keep every source file under **300 lines**. Up to 400 lines is acceptable
  only when splitting would hurt cohesion (e.g., a single state machine).
  Never exceed 400 lines.
- When a file approaches the limit, extract cohesive groups of declarations
  into new modules/files.

### 1.2 Function / Method Length
- A function or method must not exceed **50 lines** (excluding blank lines
  and comments).
- If a function grows beyond 50 lines, extract helper functions that each
  operate at a single level of abstraction.

### 1.3 No God Classes
- Every class, struct, or module must have a **single, well-defined
  responsibility** (Single Responsibility Principle).
- If a class name needs the word "And" or "Manager" to describe what it does,
  it is doing too much — split it.
- Prefer many small, focused classes over a few large ones.

---

## 2 — Abstraction & Encapsulation

### 2.1 Correct Level of Abstraction
- Each unit (function, class, module) must operate at **one level of
  abstraction**. High-level orchestration and low-level details must not
  coexist in the same function.
- Extract low-level steps into clearly named helpers so that the calling
  function reads like a summary of the algorithm.

### 2.2 Encapsulation
- Hide internal state. Expose only the minimal public API needed by
  consumers.
- Fields should be private by default. Provide accessor methods or
  properties only when external access is genuinely needed.
- Modules should export the narrowest possible surface area.

### 2.3 Loose Coupling
- Units should depend on **abstractions** (interfaces, protocols, type
  aliases), not on concrete implementations, whenever the dependency is
  likely to change or needs to be testable in isolation.
- Apply **Dependency Injection**: pass collaborators in rather than
  constructing them internally.
- Follow the **Law of Demeter**: a function should only call methods on its
  direct dependencies, not reach through chains of objects
  (`a.getB().getC().doThing()` → inject `C` directly or expose a
  domain method on `A`).

### 2.4 Composability
- Design small units that **compose** to form larger behaviors.
- Prefer building pipelines / chains of simple transformations over
  monolithic procedures.

---

## 3 — Composition Over Inheritance

- Default to **composition** (has-a) when combining behaviors. Use
  interfaces/protocols to define contracts and inject collaborators.
- Use inheritance **only** when there is a genuine "is-a" relationship
  **and** you need to share implementation — not just a contract.
- Prefer **delegation** and the **Strategy pattern** over deep class
  hierarchies. Inheritance depth should rarely exceed 2 levels beyond
  the base.

---

## 4 — Reducing Conditionals

### 4.1 Polymorphism Over Branching
- When a conditional (if/else, switch/case) selects behavior based on
  **type or category**, replace it with **polymorphism**: define an
  interface and implement each branch as a separate class/function.
- Use **Strategy**, **State**, or **Command** patterns to eliminate
  conditional cascades that select algorithms at runtime.

### 4.2 Null / Optional Handling
- Avoid returning or accepting `null`/`nil`/`None` where the language
  provides **Option/Maybe** types, **Null Object** pattern, or
  **default values**.
- This eliminates defensive `if (x != null)` checks scattered across
  call sites.

### 4.3 Lookup Tables
- Replace multi-branch `if/else if` or `switch` blocks that map **values
  to values** with a dictionary / map / hash lookup.

### 4.4 Guard Clauses (See §5 — Nesting)
- Convert nested `if` blocks into flat **guard clauses** with early
  returns at the top of the function.

---

## 5 — Minimize Nesting

### 5.1 Early Returns / Guard Clauses
- Handle error cases, edge cases, and precondition violations **first**
  with early `return`, `throw`, or `continue`.
- The main ("happy path") logic should run at the shallowest indentation
  level.

### 5.2 Max Nesting Depth
- Strive for a maximum nesting depth of **2 levels** inside any function.
  If deeper nesting is needed, extract the inner block into a named
  helper.

### 5.3 Avoid `else` After Early Return
- When a guard clause returns early, do **not** wrap the remaining body in
  an `else` block — it is already unreachable on the guarded path.

**Example (before):**
```
function process(input) {
  if (input) {
    if (isValid(input)) {
      // ... 30 lines of logic
    } else {
      throw new Error("invalid");
    }
  } else {
    throw new Error("missing");
  }
}
```

**Example (after):**
```
function process(input) {
  if (!input) throw new Error("missing");
  if (!isValid(input)) throw new Error("invalid");

  // ... 30 lines of logic at depth 1
}
```

---

## 6 — Immutability & State Management

### 6.1 Immutable by Default
- Treat all data as **immutable** unless mutation is required for a
  proven performance-critical hot path.
- Declare variables with `const`, `final`, `readonly`, `val`, or the
  language-equivalent immutable binding wherever possible.

### 6.2 Never Mutate Arguments
- Functions must **not** modify (mutate) their input arguments.
  Return new values instead.
- If a function transforms a collection, return a new collection
  rather than modifying the original in place.

### 6.3 Contain Mutable State
- When mutation is necessary (performance hot path, I/O buffers),
  **confine** it to the smallest possible scope — ideally a single
  function — and document why mutability is needed.

### 6.4 Pure Functions
- Prefer **pure functions** (deterministic, no side effects) for all
  business logic and transformations.
- Push side effects (I/O, network, database) to the **boundaries**
  of the system (entry points, adapters, controllers), keeping the
  core logic pure and testable.

---

## 7 — Naming & Readability

### 7.1 Intention-Revealing Names
- Variables, functions, classes, and modules must have names that
  describe **what** they represent or **what** they do — not how.
- Avoid abbreviations unless they are universally understood in the
  domain (`id`, `url`, `http`).

### 7.2 Consistent Vocabulary
- Use one word per concept across the entire codebase. Do not mix
  `fetch` / `retrieve` / `get` / `load` for the same operation.

### 7.3 Boolean Naming
- Prefix boolean variables and functions with `is`, `has`, `can`,
  `should`, or equivalent to make conditionals read naturally.

### 7.4 No Magic Numbers or Strings
- Extract literals into well-named constants. The name should convey
  the **business meaning**, not the value.

---

## 8 — Error Handling

### 8.1 Fail Fast
- Validate inputs at the boundary. Reject invalid data as early as
  possible with clear, actionable error messages.

### 8.2 Typed / Domain Errors
- Define domain-specific error types rather than throwing generic
  exceptions or returning raw error strings.
- This enables callers to handle specific failure modes without
  string matching.

### 8.3 No Silent Swallowing
- Never write an empty `catch` / `except` / `rescue` block. At
  minimum, log the error. Prefer re-throwing or returning an
  error value.

---

## 9 — Testing

### 9.1 Behavioral Tests First
- Test **observable behavior** (inputs → outputs, side effects,
  public API contracts), not implementation details (private
  methods, internal state, call counts on mocks).
- A correctly written test must **still pass after an internal
  refactoring** that preserves external behavior.

### 9.2 Arrange-Act-Assert
- Structure every test case with clear **Arrange** (setup),
  **Act** (execute), **Assert** (verify) sections.
- One logical assertion per test. Multiple `assert` statements
  are fine when they verify different facets of the **same**
  behavior.

### 9.3 Test Naming
- Name tests to describe the **scenario and expected outcome**:
  `should return empty list when no items match filter`.
- Avoid names like `test1` or `testGetUser`.

### 9.4 Minimal Mocking
- Mock only **external boundaries** (network, database, filesystem,
  clock).
- Do not mock the unit under test's own collaborators unless
  absolutely necessary — prefer real implementations or
  lightweight fakes.
- Over-mocking couples tests to implementation and makes them
  brittle on refactoring.

### 9.5 Coverage Is Not the Goal
- Aim for **meaningful coverage** of business-critical paths, edge
  cases, and error scenarios — not a numeric percentage.
- Every new behavior or bug fix must include a corresponding test.

---

## 10 — Dependency & Module Hygiene

### 10.1 Acyclic Dependencies
- Module/package dependency graphs must be **acyclic**. If a cycle
  is detected, break it by extracting a shared interface or an
  intermediate module.

### 10.2 Cohesive Modules
- Group related types, functions, and constants together in the
  same module/file. Do not scatter related logic across unrelated
  files.

### 10.3 Explicit Exports
- Export only the public API. Keep helpers, internal types, and
  implementation details unexported/private.

---

## 11 — Comments & Documentation

### 11.1 Code Over Comments
- Prefer self-documenting code (clear names, small functions,
  obvious structure) over explanatory comments.
- Write comments only to explain **why** — not **what**. If a
  comment restates the code, delete it.

### 11.2 Public API Docs
- All public functions, classes, and modules must have a concise
  doc comment describing purpose, parameters, return values, and
  thrown errors.

### 11.3 TODO / FIXME
- Use `TODO(username):` or `FIXME(username):` with a brief
  explanation. Never leave anonymous TODOs.

---

## 12 — Application Checklist

Before completing any code generation or modification task, verify:

- [ ] No file exceeds 400 lines.
- [ ] No function exceeds 50 lines.
- [ ] Every class/module has a single clear responsibility.
- [ ] No deep nesting — guard clauses and early returns are used.
- [ ] Conditionals on type/category use polymorphism, not if/else chains.
- [ ] Composition is preferred over inheritance.
- [ ] Arguments are never mutated; data is immutable by default.
- [ ] Side effects are at boundaries; core logic is pure.
- [ ] Tests verify behavior, not implementation details.
- [ ] Names reveal intent; no magic numbers or strings.
- [ ] Public APIs have doc comments; no "what" comments.
- [ ] Dependencies flow in one direction (no cycles).
