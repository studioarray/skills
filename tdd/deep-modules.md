# Deep Modules

A deep module has a small interface and substantial behavior hidden behind it.

```text
+-------------------+
|  Small Interface  |  Few methods, simple parameters
+-------------------+
|                   |
| Deep Behavior     |  Complex rules hidden inside
|                   |
+-------------------+
```

A shallow module has a broad or noisy interface and little behavior behind it.

```text
+-----------------------------+
|  Large Interface            |  Many methods, complex parameters
+-----------------------------+
|  Thin Implementation        |  Mostly pass-through code
+-----------------------------+
```

When implementing a slice, look for chances to deepen modules naturally:

- Reduce the number of public methods
- Simplify parameters
- Move domain rules behind a domain-shaped API
- Keep tests on public behavior, not helpers

Do this during refactor, not while RED.
