In Keycloak (or any OpenID Connect-compliant IdP), the **Token Endpoint** and **Introspection Endpoint** serve different purposes in handling authentication and authorization.

---

## **1. Token Endpoint (`/protocol/openid-connect/token`)**
The **Token Endpoint** is used to **obtain, refresh, or exchange tokens**. It’s the primary endpoint for **clients** to authenticate users and get an access token.

### **Use Cases**
- Getting an **access token** using authorization code, client credentials, or password grant.
- Refreshing an expired token.
- Exchanging tokens (e.g., client credentials grant or token exchange).

### **Request Example (Authorization Code Flow)**
```http
POST /realms/{realm}/protocol/openid-connect/token
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&
code={auth_code}&
client_id={client_id}&
client_secret={client_secret}&
redirect_uri={redirect_uri}
```

### **Response Example**
```json
{
  "access_token": "eyJhbGciOiJI...",
  "refresh_token": "eyJhbGciOiJI...",
  "id_token": "eyJhbGciOiJI...",
  "expires_in": 3600,
  "token_type": "Bearer"
}
```

---

## **2. Introspection Endpoint (`/protocol/openid-connect/token/introspect`)**
The **Introspection Endpoint** is used to **validate and inspect an access token**. It allows a **resource server (API)** to check if a token is **valid, expired, or revoked**.

### **Use Cases**
- **Resource servers (APIs)** verify tokens presented by clients before processing requests.
- Checking the **expiration time**, **active status**, or **permissions** of a token.

### **Request Example**
```http
POST /realms/{realm}/protocol/openid-connect/token/introspect
Content-Type: application/x-www-form-urlencoded
Authorization: Basic {base64(client_id:client_secret)}

token={access_token}
```

### **Response Example**
If the token is valid:
```json
{
  "active": true,
  "exp": 1712754000,
  "iat": 1712750000,
  "client_id": "some-client",
  "username": "user@example.com",
  "scope": "openid profile email"
}
```
If the token is **invalid or expired**:
```json
{
  "active": false
}
```

---

## **3. Key Differences**
| Feature                | Token Endpoint  | Introspection Endpoint |
|------------------------|----------------|------------------------|
| **Purpose**           | Issue new tokens | Validate existing tokens |
| **Used By**           | Clients (Apps) | Resource servers (APIs) |
| **Requires Client Credentials** | Yes | Yes |
| **Returns**           | Access token, refresh token, ID token | Token metadata (active, expiry, scopes, etc.) |
| **Who Calls It?**     | Frontend, Backend | API before granting access |

---

## **4. When to Use Each**
| **Scenario** | **Endpoint to Use** |
|-------------|------------------|
| A user logs in, and the app needs an access token | **Token Endpoint** |
| An API needs to verify if an access token is valid before processing a request | **Introspection Endpoint** |
| A client needs to refresh an access token | **Token Endpoint** |
| Checking token expiry before allowing access to a protected resource | **Introspection Endpoint** |

---

### **Which One Should You Use?**
- If you **need a new token**, use the **Token Endpoint**.
- If you **need to check an existing token’s validity**, use the **Introspection Endpoint**.

🚀
