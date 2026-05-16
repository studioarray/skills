# Composition Architecture

Use this guide when a slice touches UI/component structure, state orchestration, or code that is drifting toward one large mixed-responsibility file.

## When to split

Split after a green test when one file or component owns several unrelated reasons to change:

- data loading or external adapters
- workflow orchestration or state transitions
- domain rules or validation
- rendering branches for multiple modes
- reusable presentation
- formatting, mapping, or view-model shaping

Keep simple code together when the behaviour is small and cohesive. Composition should reduce future change cost, not create ceremony.

## Better shapes

- Put orchestration in a container, controller, route, hook, reducer, or state machine.
- Keep presentational components focused on rendering and interaction events.
- Move domain rules behind a small public interface with domain-shaped inputs and outputs.
- Wrap external systems in specific adapters instead of generic fetchers with conditional behaviour.
- Prefer composition APIs over boolean prop soup: children, slots, focused subcomponents, compound components, or render props when they genuinely clarify extension points.
- Let reusable pieces earn extraction through repeated or independently changing behaviour.

## React cues

React code needs composition pressure when a component has many unrelated hooks, large inline mapping logic, broad boolean props, or conditionals for several flows. Prefer a small orchestration component plus focused children/hooks over a single component that loads data, mutates state, validates input, renders every mode, and knows every variant.

## Testing

Keep tests on public behaviour. Test the workflow through the route/component/API that a user or caller exercises. Test extracted domain modules directly only when they are intentional public interfaces. Avoid tests that merely prove private components were called.
