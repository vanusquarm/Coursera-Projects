While JWT is often used in conjunction with OAuth2 for token-based authentication and authorization, it can also be used independently for simpler use cases where you don't need the full complexity of OAuth2.

### How JWT Works Without OAuth2

JWT can serve as a compact, URL-safe means of representing claims that are transmitted between parties. A JWT typically consists of three parts:

1. **Header**: Specifies the algorithm used for signing the token (e.g., `HS256`).
2. **Payload**: Contains the claims or data. For example, user information or metadata like expiration (`exp`), subject (`sub`), etc.
3. **Signature**: Ensures the token has not been tampered with and that it came from a trusted source.

You can use JWT for service-to-service authentication or for securing endpoints in your application without using OAuth2 by manually handling the creation, signing, and validation of the JWT.

### Typical Flow Without OAuth2

1. **JWT Generation**: A backend service (or a user authentication endpoint) generates a JWT and signs it with a secret or private key. The payload could contain user-specific information, roles, or claims that identify and authorize the user or service.
   
2. **JWT Usage**: The client (e.g., another service or frontend) includes the JWT in the `Authorization` header as a Bearer token in the HTTP request when accessing protected resources.

3. **JWT Validation**: The receiving service validates the token by checking the signature with a secret or public key and verifying claims like expiration (`exp`) or issuer (`iss`).

4. **Access Control**: After validation, if the JWT is valid, the service grants access to the requested resource.

### Example of Using JWT Without OAuth2

Let’s say we have a scenario where Service A wants to authenticate itself when calling Service B using JWT tokens, and we do not want to use OAuth2.

#### 1. **Generating the JWT Token (Service A)**

Service A can generate a JWT token that includes claims, like the user’s identity or service-specific metadata, and sign it with a secret key.

Here is a simple example in C# for generating a JWT:

```csharp
using System;
using System.IdentityModel.Tokens.Jwt;
using Microsoft.IdentityModel.Tokens;
using System.Text;
using System.Security.Claims;

public class JwtGenerator
{
    public string GenerateToken(string username)
    {
        var claims = new[]
        {
            new Claim(JwtRegisteredClaimNames.Sub, username),
            new Claim(JwtRegisteredClaimNames.Jti, Guid.NewGuid().ToString())
        };

        var key = new SymmetricSecurityKey(Encoding.UTF8.GetBytes("your_secret_key_here"));
        var creds = new SigningCredentials(key, SecurityAlgorithms.HmacSha256);

        var token = new JwtSecurityToken(
            issuer: "your_service",
            audience: "your_audience",
            claims: claims,
            expires: DateTime.Now.AddHours(1),
            signingCredentials: creds);

        return new JwtSecurityTokenHandler().WriteToken(token);
    }
}
```

In this example:
- The **`GenerateToken`** method creates a JWT token for a user identified by the `username`.
- A **secret key** (`"your_secret_key_here"`) is used to sign the token.

#### 2. **Sending the JWT Token (Service A to Service B)**

When Service A calls Service B, it will send the JWT token in the `Authorization` header:

```csharp
using System.Net.Http;

public class ServiceAClient
{
    private static readonly HttpClient client = new HttpClient();

    public async Task CallServiceB(string token)
    {
        var request = new HttpRequestMessage(HttpMethod.Get, "https://service-b/endpoint");
        request.Headers.Authorization = new System.Net.Http.Headers.AuthenticationHeaderValue("Bearer", token);

        var response = await client.SendAsync(request);
        // Handle the response from Service B
    }
}
```

Here, the `Authorization` header is populated with the JWT token from Service A.

#### 3. **Validating the JWT Token (Service B)**

Service B receives the request and needs to validate the JWT token sent by Service A.

```csharp
using System;
using System.IdentityModel.Tokens.Jwt;
using Microsoft.IdentityModel.Tokens;
using System.Text;

public class JwtValidator
{
    public bool ValidateToken(string token)
    {
        var key = new SymmetricSecurityKey(Encoding.UTF8.GetBytes("your_secret_key_here"));
        var tokenHandler = new JwtSecurityTokenHandler();

        try
        {
            var principal = tokenHandler.ValidateToken(token, new TokenValidationParameters
            {
                IssuerSigningKey = key,
                ValidateIssuer = false, // You can enable issuer validation if needed
                ValidateAudience = false, // You can enable audience validation if needed
                ValidateLifetime = true, // Validate token expiration
                ClockSkew = TimeSpan.Zero // Optional: Adjust for skew in expiration time
            }, out var validatedToken);

            // You can access the claims in principal and authorize based on them
            var username = principal.Identity.Name; // Example: Get the username from the token
            return true; // Token is valid
        }
        catch (Exception)
        {
            return false; // Token is invalid
        }
    }
}
```

In this code:
- **`ValidateToken`** checks the validity of the JWT by verifying the signature with the secret key.
- **`ValidateLifetime`** checks whether the token has expired.
- Additional validation like **issuer** and **audience** can be enabled if needed.

#### 4. **Using the Validator in Service B**

In Service B, you would integrate the validation logic into your HTTP request pipeline (e.g., middleware) to ensure all incoming requests have valid JWT tokens.

```csharp
public class JwtMiddleware
{
    private readonly RequestDelegate _next;

    public JwtMiddleware(RequestDelegate next)
    {
        _next = next;
    }

    public async Task InvokeAsync(HttpContext context)
    {
        var token = context.Request.Headers["Authorization"].ToString().Replace("Bearer ", "");

        var validator = new JwtValidator();
        if (validator.ValidateToken(token))
        {
            // Proceed with the request, token is valid
            await _next(context);
        }
        else
        {
            context.Response.StatusCode = 401; // Unauthorized
        }
    }
}
```

Here, the JWT token from the `Authorization` header is extracted, validated, and the request proceeds if the token is valid.

### Summary

You can use **JWT** without OAuth2 for simple service-to-service authentication and authorization. The flow involves generating a signed JWT token, sending it with requests, and validating the token on the receiving side. OAuth2 is a common framework for managing and exchanging tokens, but if your application doesn’t require its full capabilities (e.g., authorization server, token revocation, scopes, etc.), you can use JWT on its own without needing to implement OAuth2.
