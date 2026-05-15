# Mocking

Mock at system boundaries only.

Good mock candidates:

- External APIs such as payment, email, maps, AI providers, or partner services
- Time and randomness
- File system boundaries
- Databases when the repo does not support a reliable test database

Do not mock:

- Internal modules you own
- Domain objects
- Private helpers
- Collaborators that can be exercised cheaply through the public interface

## Boundary Design

At system boundaries, design interfaces that are easy to fake without leaking implementation details into tests.

Prefer dependency injection:

```typescript
function processPayment(order, paymentClient) {
  return paymentClient.charge(order.total);
}
```

Avoid constructing external clients inside the behavior under test:

```typescript
function processPayment(order) {
  const client = new StripeClient(process.env.STRIPE_KEY);
  return client.charge(order.total);
}
```

Prefer specific SDK-style boundary methods over one generic fetcher with conditional behavior:

```typescript
const billingApi = {
  getUser: (id) => fetch(`/users/${id}`),
  getOrders: (userId) => fetch(`/users/${userId}/orders`),
  createOrder: (data) => fetch("/orders", { method: "POST", body: data }),
};
```

Specific boundary methods keep mocks simple:

- Each fake returns one shape
- No conditional logic is needed in test setup
- Test setup shows which external operations are exercised
- Types can describe each operation precisely
