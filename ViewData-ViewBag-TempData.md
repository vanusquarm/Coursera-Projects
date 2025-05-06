| Feature              | `ViewBag`                           | `ViewData`                           | `TempData`                           |
| -------------------- | ----------------------------------- | ------------------------------------ | ------------------------------------ |
| Type                 | Dynamic (`dynamic`)                 | Dictionary (`ViewDataDictionary`)    | Dictionary (`TempDataDictionary`)    |
| Data Lifetime        | Current request only                | Current request only                 | Until read (persists for 1 redirect) |
| Use Across Views     | Yes                                 | Yes                                  | Yes                                  |
| Use Across Redirects | ❌ No                                | ❌ No                                 | ✅ Yes                                |
| Requires Casting     | ❌ No                                | ✅ Yes (when retrieving values)       | ✅ Yes                                |
| Backed By            | ViewData                            | ViewData                             | Session                              |
| Serialization        | ❌ No                                | ❌ No                                 | ✅ Yes                                |
| Common Use           | Passing data to views (lightweight) | Same as ViewBag, but stronger typing | Messages, flags across redirects     |
