# Context Doc Spec

File: `.architect-context.md` — project root. He wrote it. He reads it first.
Purpose: his memory. Cuts token use. No re-scanning. No re-reading files.

---

## Format rules

Ultra caveman throughout. Every entry max 2 lines. No fluff. No sentences
that don't carry information. No "this module handles". Just: what, where, how.

Technical words spelled fully — no abbreviations in the doc itself
(abbreviations are for conversation, not the permanent record).
Exception: standard technical acronyms that ARE the name (API, HTTP, SQL, JWT,
UUID, CLI, etc.) — use them.

---

## File structure

```
# ARCHITECT CONTEXT — [project name] — last updated [YYYY-MM-DD]

## META
[5 lines max. What project does. Language. Framework. Database. Entry point.]

## ARCHITECTURE PATTERN
[Named pattern if one exists: MVC, Clean Architecture, Hexagonal, Layered, etc.]
[Layer list with what belongs in each. One line per layer.]

## FOLDER MAP
[Every folder. One line each. What lives there. What does not.]

## MODULES
[Every significant module/package. Name, responsibility, dependencies. 2 lines max each.]

## NAMING CONVENTIONS
[Variable naming style. Function naming style. File naming style. Class naming style.]
[One rule per line. Concrete examples in brackets.]

## ERROR HANDLING CONTRACT
[How errors are handled. One pattern or list of patterns. Where caught. How propagated.]

## LOGGING CONTRACT
[Log library. Format. Log levels used. What fields always present.]

## DATA FLOW
[How data moves: request in → where it goes → transforms → response out.]
[One line per major path if multiple.]

## DEPENDENCIES — EXTERNAL
[Every external package that matters. Name + what it does. One line each.]

## DEPENDENCIES — INTERNAL
[Cross-module dependencies that are non-obvious. Which module imports which.]

## TEST STRATEGY
[Test framework. What is tested. What is not. File naming pattern for tests.]

## KNOWN DEBT
[Technical debt he has seen. No judgment (yet). Just facts. One line each.]

## CHANGE LOG
[Last 10 changes made during his sessions. Date + what changed. Newest first.]
```

---

## Scan procedure — first run

Walk in this order:

1. **Root** — read `package.json` / `pyproject.toml` / `go.mod` / `Cargo.toml`
   / `pom.xml` / `composer.json` — whatever exists. Extract: project name, language,
   framework, key dependencies, entry point.

2. **Folder structure** — list all folders. Infer purpose from names and contents.
   Note any that break expected pattern.

3. **Entry points** — find `main`, `index`, `app`, `server`, `wsgi`, `asgi` etc.
   Trace call chain from entry to first real logic.

4. **Configuration** — read config files (`.env.example`, `config/`, `settings.py`,
   etc.). Note: database type, cache layer, external services wired up.

5. **Module by module** — for each module/package:
   - What does it export/expose?
   - What does it import?
   - What layer does it belong to?
   - Does it follow a consistent pattern?

6. **Naming audit** — scan 20+ identifiers across multiple files. Extract the pattern.
   Note deviations if any.

7. **Error handling scan** — find 5+ error handling sites. Extract the pattern.

8. **Test scan** — find test files. Note framework, structure, what gets tested.

9. **Write context doc** — write `.architect-context.md`. Caveman format throughout.
   Each section. No skipping sections (write "none found" if truly empty).

10. **Announce** — "Context doc written. [path]. Read it."

Do not ask permission to scan. Do not narrate while scanning. Scan. Write. Announce.

---

## Update procedure — after session

Run after any session where codebase was touched. Do not ask. Do.

Rules:
- Update only sections that changed
- Append to CHANGE LOG (newest first, keep last 10 only)
- Update "last updated" date in header
- Do not reformat unchanged sections
- If new module added: add entry to MODULES and FOLDER MAP
- If new dependency added: add to correct DEPENDENCIES section
- If naming convention changed: update NAMING CONVENTIONS and note the change
- If debt was resolved: remove from KNOWN DEBT
- If new debt introduced: add to KNOWN DEBT (he will not be happy about this)

---

## What goes in vs what stays out

**IN:**
- Every folder and what it holds
- Every module with real responsibility
- Every naming pattern
- Every external dependency that affects architecture
- Every error handling site pattern
- The single data flow path (or multiple if genuinely different)
- Actual technical debt seen in the code

**OUT:**
- File contents (no copy-pasting code into context doc)
- Explanations of what things mean
- History before his sessions began
- Opinions (those go in the review, not the doc)
- Anything he could re-derive in 5 seconds from the folder map

---

## Example entry — module

```
## MODULES

### auth
Handles JWT issue and validation. Stateless. No session.
Depends: config, database (user lookup only). Used by: all route middleware.

### user
User entity, repository, service. CRUD plus role management.
Depends: database, auth (for ID extraction). Used by: admin routes, profile routes.
```

---

## Example entry — naming conventions

```
## NAMING CONVENTIONS

Files: kebab-case [user-service.ts, auth-middleware.ts]
Classes: PascalCase [UserService, AuthMiddleware]
Functions: camelCase [getUserById, validateToken]
Variables: camelCase, descriptive [rawPayload, parsedUser, tokenExpiry]
Constants: SCREAMING_SNAKE [MAX_RETRY_COUNT, DEFAULT_TIMEOUT_MS]
Tests: [filename].test.ts alongside source file
```

---

## Example entry — change log

```
## CHANGE LOG

2025-06-01 — Added payment module. Stripe integration. See modules/payment.
2025-05-30 — Moved email logic from user-service to new notifications module.
2025-05-28 — Added rate limiting middleware. Uses Redis. See middleware/rate-limit.
```

---

## Size budget

Context doc should stay under 200 lines. If growing beyond that:
- Entries are too verbose. Cut them.
- Too many modules listed that are trivial. Merge minor ones.
- Change log has too many entries. Keep last 10 only.

Over 200 lines = he is writing essays. He does not write essays. He writes facts.
