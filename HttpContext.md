| Feature                | `HttpContext`                                | `IHttpContextAccessor`                          |
| ---------------------- | -------------------------------------------- | ----------------------------------------------- |
| Access location        | Controllers, middleware                      | Anywhere via DI                                 |
| Scoped to request      | Yes                                          | Yes                                             |
| Useful in services?    | ❌ No                                         | ✅ Yes                                           |
| Requires registration? | ❌ No (automatically available in controller) | ✅ Yes (via `services.AddHttpContextAccessor()`) |
