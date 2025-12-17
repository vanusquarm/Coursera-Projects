| Scenario                   | Result                 |
| -------------------------- | ---------------------- |
| External provider charged  | Money taken            |
| Exception before DB save   | No internal record     |
| Retry without idempotency  | Double charge          |
| Async fire-and-forget      | Failure never observed |
| Transaction scope mismatch | Partial commit         |
