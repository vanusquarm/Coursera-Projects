## Handling Errors in Http Calls (Part 1)
- if (!response.IsSuccessStatusCode) return false;

This code checks if the HTTP response was not successful (i.e., the status code is not in the range 200-299).
If the response was not successful, it returns false.

- response.EnsureSuccessStatusCode();

This method throws an exception if the HTTP response was not successful.
It ensures that the code execution stops and an error is raised if the response status code indicates a failure.

### When to Use Each Approach
if (!response.IsSuccessStatusCode) return false;: Use this when you want to handle the failure case gracefully without throwing an exception. This is useful in scenarios where you might want to log the error or take some alternative action without stopping the execution flow.
response.EnsureSuccessStatusCode();: Use this when you want to enforce that the response must be successful, and any failure should be treated as an exception. This is useful in scenarios where a failed response is considered critical and should halt further execution.
