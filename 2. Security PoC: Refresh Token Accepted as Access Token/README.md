## 2. Security PoC: Refresh Token Accepted as Access Token

### Overview
A critical vulnerability was identified where the system incorrectly accepts a `refreshToken` as a valid `accessToken` for authenticating requests to protected resources. This bypasses the intended security mechanism of having short-lived access tokens.

| Metric | Value | Rationale |
| :--- | :--- | :--- |
| **Severity** | **Critical** | The flaw completely undermines the security principle of token expiry, granting attackers long-term, unauthorized access to user accounts if the refresh token is ever compromised. |
| **CWE** | **CWE-287** | Improper Authentication. The system fails to correctly validate the type and intended purpose of the token being presented. |
| **Risk Rating** | **Critical** | Direct authentication bypass leading to persistent unauthorized access to sensitive user data and functionalities. |

### Impact
This leads to an **Authentication Bypass**. If a `refreshToken` is compromised, an attacker can use it directly to access protected API endpoints, circumventing the intended short lifespan of access tokens and gaining unauthorized, potentially long-term, access to user accounts or system functionalities.

### Affected System
•   **System Type:** API authorization mechanism

•   **Sample Endpoint/URL:** `https://[anonymized-corporate-website.com]/api/protected-resource`

### Steps to Reproduce
1.  Perform a successful login to obtain both an `accessToken` and a `refreshToken` from the authentication response.
2.  Attempt to send a request to a protected API endpoint (e.g., `/api/protected-resource`) using the **`refreshToken`** in the `Authorization: Bearer` header, instead of the `accessToken`.
3.  Observe that the request is successfully processed, indicating the `refreshToken` was accepted as an `accessToken`.

### Proof of Concept
[Screencast 1: Authentication Bypass demonstrating refreshToken usage](https://youtu.be/JNT8FAChJgM)

### Tools Used
•   Postman

### Recommended Remediation
To mitigate this critical authentication flaw, the development team must ensure absolute separation between access tokens and refresh tokens:

1.  **Token Type Validation:** Implement strict server-side validation to explicitly check the **type** of token being presented for resource access. If using JWT, this should be done by checking a specific claim (e.g., `typ` set to `access` or `refresh`).
2.  **Separate Handlers:** Ensure that the API authorization middleware is configured to only accept tokens issued for the purpose of resource access. The refresh token validation logic must be distinct and only applied to the dedicated refresh endpoint (`/api/refresh-token`).
3.  **Distinct Signing Keys/Format:** If possible, sign access tokens and refresh tokens using different cryptographic keys, or use fundamentally different formats/algorithms, making it impossible for the system to confuse them.
4.  **Logging:** Log all failed authentication attempts that involve token validation errors for monitoring and analysis.
