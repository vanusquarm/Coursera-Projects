Here’s a direct comparison between **Cookies** and **Session Storage** to help you understand their differences:

| Feature                          | **Cookies**                                              | **Session Storage**                              |
| -------------------------------- | -------------------------------------------------------- | ------------------------------------------------ |
| **Storage Location**             | Client-side (browser)                                    | Client-side (browser)                            |
| **Size Limit**                   | \~4KB per cookie                                         | \~5–10MB (varies by browser)                     |
| **Persistence**                  | Can persist across sessions (if `expires`/`max-age` set) | Only persists until browser/tab is closed        |
| **Accessible to JS**             | Yes (`document.cookie`)                                  | Yes (`sessionStorage.getItem()`)                 |
| **Automatically Sent to Server** | **Yes**, with every HTTP request to the same domain      | **No**, never sent to the server automatically   |
| **Use Cases**                    | Auth tokens, tracking IDs, preferences                   | Temporary UI state, form data, tab-specific data |
| **Security Options**             | Can be made `HttpOnly`, `Secure`, `SameSite`             | No built-in security flags                       |

### Summary:

* Use **cookies** when you need **persistence across sessions** or **server access**.
* Use **sessionStorage** for **temporary, tab-specific data** that doesn't need to be shared with the server.
