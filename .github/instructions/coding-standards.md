---
description: 'Central coding standards covering comments, documentation, and TypeScript conventions'
---

# Coding Standards

This file documents the core coding standards for Tailspin Toys, ensuring consistency and clarity across the codebase.

## Comments and Documentation

### Comment Philosophy: Intent, Not Mechanics

**Comments should explain *why* code exists or the reasoning behind a non-obvious decision, not restate what the code already says.** Comments that merely paraphrase the line below are maintenance debt — they go stale and mislead.

- ✅ **Good**: `// Deterministic rating ensures static builds produce consistent output across runs`
- ❌ **Bad**: `// Initialize the rating variable` or `// Check if id is present`

Only comment:
- **Design decisions and trade-offs** — why this approach over alternatives
- **Non-obvious intent or problem being solved** — what problem does this code prevent?
- **Complex algorithms or business logic** — how does it work?
- **Workarounds for limitations** — link to issues or docs explaining the quirk
- **Deprecation or migration guidance** — why is this old? What replaces it?

**Treat outdated comments as bugs.** Update or delete them whenever you touch the related code.

### JSDoc/TSDoc for Exported Functions

Every **exported function** in `db/` and `src/lib/` must have a **JSDoc/TSDoc comment** describing:
- What the function does (one-line summary)
- Each parameter (with type if not obvious from signature)
- Return value (with type if not obvious from signature)

For **data-access helpers**, document the injectable `db` argument so the testing pattern stays clear:

```ts
/**
 * Fetches all games, ordered alphabetically by title for determinism in static builds.
 * @param db - The database instance (injectable for testing)
 * @returns Array of games with publisher and category relations
 */
export async function getAllGames(db: Database): Promise<Game[]> {
  // Implementation...
}
```

### Astro Component Props Documentation

Each **reusable `.astro` component** must document its `Props` interface with a JSDoc/TSDoc comment to make the component API self-explanatory:

```astro
---
/**
 * Displays a game card in a grid with title, description, and CTA.
 * @param game - The game object with title, description, and other metadata
 */
interface Props {
  game: Game;
}

const { game } = Astro.props;
---
```

## TypeScript Conventions

### Explicit Types

All **function parameters and return types** must be explicitly annotated. Do not rely on inference for public APIs.

- ✅ **Good**: `export async function getGameById(db: Database, id: number): Promise<Game | null>`
- ❌ **Bad**: `export async function getGameById(db, id) { ... }`

This applies to:
- All exported functions in `db/` and `src/lib/`
- Component `Props` interfaces
- Test fixtures and helpers
- API responses and serialized data

### Imports and Module Organization

- Import types using `import type` to ensure they don't end up in the bundle
- Group imports: external packages, then relative paths, then types
- Use absolute imports via path aliases where configured

### Type Safety

- Use strict mode (`tsconfig.json` includes `"strict": true`)
- Avoid `any` — use `unknown` with narrowing if needed
- Define interfaces for domain objects (Game, Publisher, Category)
- Use discriminated unions for multi-variant types

## Code Quality

### Testability

- **Interactive elements**: Include `data-testid` attributes (see `ui.instructions.md`)
- **Data layer**: Helpers use **injectable `db`** so tests pass in-memory databases
- **Transforms**: Keep pure and side-effect-free for easy unit testing
- **Determinism**: Seed-derived values (ratings, timestamps) must produce identical output across builds

### Linting and Formatting

- ESLint enforces TypeScript and Astro code quality
- Run `npm run lint` before committing
- The `quality-checks` skill wraps linting with debugging guidance
- Fix ESLint errors; don't disable rules without documented justification

### Code Organization

- Keep components focused on a single responsibility
- Reusable logic belongs in `src/lib/` or `db/`, not duplicated across pages
- Data-access helpers live in `src/lib/games.ts` and are used by pages
- Transforms (pure functions) live in `db/transforms.ts` and are tested independently

## Review Checklist

Before committing or opening a PR:

- [ ] Comments explain *why*, not *what*
- [ ] Exported functions have JSDoc/TSDoc with parameters and return types
- [ ] Component `Props` interfaces are documented
- [ ] All function signatures use explicit types
- [ ] No outdated comments remain
- [ ] Linting passes: `npm run lint`
- [ ] Tests pass: `npm run test:unit` and `npm run test:e2e` (via quality-checks)
- [ ] Build succeeds: `npm run build`

## See Also

- [`astro.instructions.md`](astro.instructions.md) — Astro pages, layouts, and components
- [`drizzle.instructions.md`](drizzle.instructions.md) — Data layer and Drizzle patterns
- [`unit-tests.instructions.md`](unit-tests.instructions.md) — Unit testing with Vitest
- [`ui.instructions.md`](ui.instructions.md) — UI principles and component strategies
- [`style.instructions.md`](style.instructions.md) — Tailwind CSS and styling
