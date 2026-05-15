# Interface Design for Testability

Good interfaces make behavior easy to test without exposing internals.

## Accept Dependencies

Accept dependencies rather than creating them inside the behavior.

```typescript
function processOrder(order, paymentGateway) {
  return paymentGateway.charge(order.total);
}
```

Harder to test:

```typescript
function processOrder(order) {
  const gateway = new StripeGateway();
  return gateway.charge(order.total);
}
```

## Return Results

Return meaningful results when that is natural for the domain.

```typescript
function calculateDiscount(cart): Discount {
  return discountFor(cart);
}
```

Harder to reason about:

```typescript
function applyDiscount(cart): void {
  cart.total -= discountFor(cart).amount;
}
```

Side effects are sometimes correct, especially at application boundaries. Keep them behind a public interface that tests can exercise directly.

## Keep the Surface Small

A small public surface usually needs fewer tests and less setup:

- Fewer methods
- Simpler parameters
- Domain-shaped return values
- Complexity hidden behind the interface
