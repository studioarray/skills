# Refactor Candidates

Only refactor when tests are green.

After a TDD cycle, look for:

- **Duplication**: extract a function, class, helper, or fixture
- **Long methods**: split internals while keeping tests on the public interface
- **Shallow modules**: combine them or move complexity behind a smaller interface
- **Feature envy**: move logic closer to the data or concept it belongs to
- **Primitive obsession**: introduce value objects or domain types when they reduce mistakes
- **Awkward test setup**: simplify the public interface or boundary shape
- **Newly visible debt**: clean up existing code only when it is in the path of the slice

Run tests after each meaningful refactor step.
