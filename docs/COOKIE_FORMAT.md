# Cookie and Session Token Format Reference

This document explains how to configure session tokens for different authentication scenarios.

---

## Basic Formats

### Standard Cookie

```
Cookie: session=abc123def456
```

### Multiple Cookies

```
Cookie: session=abc123; csrf_token=xyz789; preference=dark
```

### Custom Header

```
X-Auth-Token: abc123def456
```

### Authorization Bearer Token

```
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

### API Key

```
X-API-Key: sk_live_abc123def456
```

---

## Multiple Headers

You can specify multiple headers, one per line:

```
Authorization: Bearer eyJhbGciOiJIUzI1NiIs...
X-CSRF-Token: abc123
Cookie: preference=dark; theme=light
```

---

## Common Authentication Patterns

### Session Cookie

```
Cookie: PHPSESSID=abc123def456ghi789
```

```
Cookie: JSESSIONID=ABC123DEF456.node1
```

```
Cookie: ASP.NET_SessionId=abc123def456
```

### JWT in Cookie

```
Cookie: token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c
```

### JWT in Authorization Header

```
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c
```

### Basic Authentication

```
Authorization: Basic dXNlcm5hbWU6cGFzc3dvcmQ=
```

### API Key Authentication

```
X-API-Key: your-api-key-here
```

```
Api-Key: your-api-key-here
```

### Custom Token Header

```
X-Auth-Token: abc123def456
```

```
X-Access-Token: abc123def456
```

---

## Auto-Fix Feature

BOLAMatrix automatically converts simple formats:

| You Enter | Converted To |
|-----------|--------------|
| `session=abc123` | `Cookie: session=abc123` |
| `token=xyz789` | `Cookie: token=xyz789` |

So you can simply paste:
```
session=abc123
```

And it becomes:
```
Cookie: session=abc123
```

---

## Juice Shop Example Tokens

OWASP Juice Shop uses JWT `Authorization: Bearer` headers:

| Role | Session Data |
|------|--------------|
| **Unauth** | *(leave empty)* |
| **admin** | `Authorization: Bearer <admin JWT from Burp>` |
| **Jim** | `Authorization: Bearer <jim JWT from Burp>` |
| **accountant** | `Authorization: Bearer <accountant JWT from Burp>` |

To obtain JWTs: log in to Juice Shop (`http://localhost`) as each user through Burp proxy, find any authenticated request in Proxy History, and copy the full `Authorization: Bearer eyJ...` header.

---

## Tips

### Getting Tokens from Burp

1. Log in as the target user in your browser
2. Find a request in Burp Proxy History
3. Look for `Cookie:` or `Authorization:` headers
4. Copy the relevant header(s)
5. Paste into BOLAMatrix role configuration

### Multi-Factor Authentication

If your app uses MFA tokens that change:
1. Complete MFA in browser
2. Capture the resulting session token
3. Use that stable session token in BOLAMatrix

### Token Expiration

If tokens expire during testing:
1. Re-authenticate in browser
2. Update the session data in the role
3. Continue testing

### Testing Without Authentication

For the "unauthenticated" role:
- Leave the session data field **completely empty**
- BOLAMatrix will send requests with no auth headers

---

## Troubleshooting

### 401 for All Requests

- Verify token is correct (copy fresh from Burp)
- Check if token has expired
- Ensure header format is correct (includes `Cookie:` or `Authorization:`)

### Inconsistent Results

- Token may have changed mid-test
- Check for CSRF tokens that need updating
- Some apps tie sessions to IP/User-Agent

### Cookie Not Being Sent

- Ensure format is `Cookie: name=value`
- Check for typos in header name
- Verify no extra whitespace
