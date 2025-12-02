## 4. Security PoC: PostgreSQL UUID Parsing Error – Internal Server Crash

### Overview
A critical vulnerability was identified where supplying malformed UUIDs to an API endpoint expecting UUID input leads to a PostgreSQL parsing error. This error, in turn, causes an internal server crash, making the service unavailable.

| Metric | Value | Rationale |
| :--- | :--- | :--- |
| **Severity** | **High** | The vulnerability allows any unauthenticated user to easily render the service or specific high-traffic functionalities unavailable, posing a direct threat to service availability. |
| **CWE** | **CWE-20** | Improper Input Validation. The application fails to validate the format of the path parameter (UUID string) before passing it to the database driver. |
| **Risk Rating** | **High** | The flaw enables a highly effective Denial of Service (DoS) attack, directly impacting business continuity.

### Impact
This vulnerability creates a **Denial of Service (DoS) opportunity**. An attacker can trigger a server crash by repeatedly sending malformed UUIDs, effectively rendering the service or specific functionalities unavailable to legitimate users.

### Affected System
•   **System Type:** Backend system interacting with PostgreSQL, processing UUID inputs.
•   **Sample Endpoint/URL:** `https://[anonymized-corporate-website.com]/api/resource/{uuid_id}`

### Steps to Reproduce
1.  Identify an API endpoint that expects a UUID as part of its parameters (e.g., a path parameter like `.../items/{item_uuid}`).
2.  Send an HTTP request (e.g., GET, PUT, DELETE) to this endpoint, providing a malformed string instead of a valid UUID (e.g., `.../items/invalid-uuid-string` or `.../items/123`).
3.  Observe the server response: a `500 Internal Server Error` indicating a database parsing issue, or potentially a complete server crash.
4.  Repeat the process with mass requests to trigger a full Denial of Service.

### Proof of Concept
<img width="1013" height="592" alt="chrome_Bgg0p0TmhU2025" src="https://github.com/user-attachments/assets/04d3fb19-ca05-4c17-b83c-a4bf0f2aa7c9" />

### Tools Used
•   Postman

### Recommended Remediation
To mitigate this Denial of Service vulnerability, robust validation must be enforced at the application layer:

1.  **Application-Level Validation (Must-Have):** Implement strict validation on the API gateway or controller layer to ensure the path parameter conforms to the standard UUID format (32 hexadecimal characters, optionally separated by hyphens) **before** making the database call. If the format is invalid, return a standard `400 Bad Request` instantly.
2.  **Generic Error Handling:** Ensure that the server does not expose detailed internal information (like database driver or SQL error messages) when a `500 Internal Server Error` occurs. Use generic error pages.
3.  **Resource Limits:** Implement rate limiting (throttling) on the API endpoint to mitigate the DoS threat from mass repeated requests.
