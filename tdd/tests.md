# Good and Bad Tests

## Good Tests

Good tests verify observable behavior through public interfaces.

```typescript
test("user can checkout with a valid cart", async () => {
  const cart = createCart();
  cart.add(product);

  const result = await checkout(cart, paymentMethod);

  expect(result.status).toBe("confirmed");
});
```

Good tests:

- Exercise behavior users or callers care about
- Use the same interface production callers use
- Survive internal refactors
- Describe what the system does
- Keep assertions focused on one behavior

## Bad Tests

Bad tests couple themselves to implementation details.

```typescript
test("checkout calls paymentService.process", async () => {
  const mockPayment = jest.mock(paymentService);

  await checkout(cart, payment);

  expect(mockPayment.process).toHaveBeenCalledWith(cart.total);
});
```

Red flags:

- Mocking internal collaborators
- Calling private methods
- Asserting call counts or internal order
- Naming tests after implementation details
- Failing after a refactor that did not change behavior
- Verifying state through a back door

Prefer verification through another public interface:

```typescript
// Bad: bypasses the public interface to verify storage.
test("createUser saves to database", async () => {
  await createUser({ name: "Alice" });

  const row = await db.query("SELECT * FROM users WHERE name = ?", ["Alice"]);

  expect(row).toBeDefined();
});

// Good: proves the externally observable behavior.
test("createUser makes the user retrievable", async () => {
  const user = await createUser({ name: "Alice" });

  const retrieved = await getUser(user.id);

  expect(retrieved.name).toBe("Alice");
});
```
