# React TypeScript Composition

Use this reference when React TypeScript touches component structure, hooks, view models, component APIs, local UI state, or UI tests.

## Ownership

React code stays maintainable when each layer has a clear owner.

- Pages, routes, and containers own orchestration.
- Providers adapt broad app state into focused UI contracts.
- Hooks own meaningful state workflows, subscriptions, effects, or reusable interaction logic.
- View-model modules derive render-ready data from raw snapshots or DTOs.
- Components render focused UI and emit events.
- Leaf components may own leaf-local state such as disclosure, draft text, focus, or hover state.

If a component fetches data, transforms it, validates it, handles workflow state, renders several product areas, and formats details, it is carrying too many reasons to change.

## Component Files

Prefer product-area files over a single large component file once the areas are independently meaningful.

Examples of natural splits:

- shell
- status area
- conversation feed
- feed item variants
- prompt composer
- connection screen
- theme control
- empty, blocked, or error states

Small private components are fine while they serve one local concept. When a file becomes a private-function village for multiple product areas, split it.

## Props And Composition

Prefer composition over configuration.

Warning signs:

- many optional props
- several boolean mode props
- prop names that only matter in one branch
- conditionals for unrelated flows
- one component that knows every variant

Better shapes:

- explicit variant components
- discriminated props for real modes
- children or slots for extension points
- small compound components for shared context
- focused hooks for reusable workflows
- view-model types that match product modes

Do not create a compound-component framework unless the component actually has reusable internal coordination. Simple named components are often better.

## State

Keep state as close to its owner as possible.

- Leaf-local UI state can stay local.
- Shared UI state belongs in a focused provider, parent, store, or hook.
- Derived render data belongs in a view model or selector-like function.
- Domain or application rules do not belong in React state code.

When local state starts coordinating several product areas, promote the workflow into a named hook, reducer, state machine, or view-model module.

## Tests

React tests should assert visible behavior and user affordances.

Prefer:

- rendered text, roles, labels, and disabled/enabled states
- user events through public UI
- product modes expressed through realistic fixtures
- focused fixtures that hide irrelevant raw state

Avoid:

- testing private component call structure
- asserting implementation-only hook calls
- rebuilding huge snapshots in every test
- faking transport or protocol behavior inside UI tests when a store/view-model fixture would do
