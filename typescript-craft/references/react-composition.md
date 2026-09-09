# React composition

## Give state one owner

Keep state near the components that use it. Components at any level may own state. When several components must stay synchronized, use their nearest appropriate common parent or the repository's existing shared store/provider.

A custom hook shares logic. Separate calls that create local state have independent state. A hook may expose shared state by reading the same context or store, or run once in a common parent whose values are passed down.

For example, calling a locally stateful `useDraft()` separately in an editor and preview will not synchronize them. Put the draft in a common owner and give each component the data and events it needs.

Use a reducer or named workflow hook when it clarifies related transitions. Introduce context when consumers need shared access; keep ordinary local state local.

## Derive values during rendering

Calculate values from current props and state during render. A local `const total = items.length` needs no view-model module. Extract a pure transformation when its complexity, reuse, or domain meaning warrants a separate name or module.

Avoid maintaining a second state value for data that can be derived. Keep rendering pure. Use memoization when there is a concrete performance or identity requirement, following the repository's tooling and conventions.

## Use Effects for external synchronization

Put interaction-specific work in event handlers. Use Effects to synchronize with external systems, declare their reactive dependencies, and clean up subscriptions or resources. Cleanup should tolerate setup running again in development.

For asynchronous work, handle failure and prevent obsolete results from overwriting current state. Abort obsolete requests when supported, or ignore their results. Follow the framework's existing data-loading and caching approach before adding a fetching Effect.

## Compose around product responsibilities

Separate independently meaningful UI areas when that makes ownership clearer. Small private components can stay in the same file. Follow the framework's route and server/client conventions; a page/hook/view-model/component stack is optional.

When props describe distinct modes, use discriminated props or focused variants. Independent options can remain booleans. Use children or slots for real extension points. Compound components and providers are useful when they coordinate shared behavior; simple named components often suffice.

## Test what users can do

Use the repository's UI test tools to exercise visible behavior through roles, labels, text, and realistic user actions. Cover relevant loading, error, and interaction states. Prefer assertions about rendered results over private hook calls or component wiring.

Choose fixtures at the boundary being tested. A view-model fixture can isolate presentation, but it cannot verify fetching or transport integration. Include those paths when their behavior is part of the change.

## Sources

- [React, sharing state](https://react.dev/learn/sharing-state-between-components)
- [React, custom hooks](https://react.dev/learn/reusing-logic-with-custom-hooks)
- [React, deriving values and avoiding unnecessary Effects](https://react.dev/learn/you-might-not-need-an-effect)
- [React, synchronizing with Effects](https://react.dev/learn/synchronizing-with-effects)
- [Testing Library, guiding principles](https://testing-library.com/docs/guiding-principles/)
