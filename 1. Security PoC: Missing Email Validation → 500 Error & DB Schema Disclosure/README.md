# 1. Security PoC: Missing Email Validation → 500 Error & DB Schema Disclosure

## Overview
A vulnerability was discovered due to the absence of proper email validation during registration or when using the password recovery function. This allowed sending requests with excessively long strings in the email field.

| Metric | Value | Rationale |
| :--- | :--- | :--- |
| **Severity** | **Medium** | Potential for Denial of Service (DoS) and unintentional database schema disclosure. |
| **CWE** | **CWE-20** | Improper Input Validation. |
| **Risk Rating** | **Moderate** | The vulnerability directly affects service stability and leaks sensitive technical information, although it does not grant direct account compromise. |

## Impact
This vulnerability could potentially lead to:

*   **Denial of Service (DoS):** Mass requests with "overloaded" email addresses could cause buffer overflow or other server-side errors, resulting in service disruption.
*   **DB Schema Disclosure:** In some cases, improper handling of such requests could trigger internal server errors (`500 Internal Server Error`) that leaked information about the internal database schema in error messages.

## Affected System
*   **System Type:** Corporate website
*   **Sample Endpoint/URL:** `https://[anonymized-corporate-website.com]/register`

## Steps to Reproduce
1.  Send an HTTP-POST request to the registration or password recovery endpoint.
2.  In the email field, provide a very long string.
3.  Observe the server response: exploitation may result in a `500 Internal Server Error` containing fragments of the database schema.
4.  Repeat the process in bulk to potentially trigger a DoS.

## Proof of Concept
<img width="1590" height="743" alt="chrome_JZjmWvX10c2025" src="https://github.com/user-attachments/assets/57624117-bebc-442d-9ddf-47ab83a11add" />

## Tools Used
*   Postman

## Recommended Remediation

To mitigate this vulnerability, the following actions are necessary:

1.  **Strict Server-Side Input Validation:** Implement robust server-side validation for all email input fields (registration, password recovery).
2.  **Enforce Limits:** The system **must enforce a maximum length limit** (e.g., 254 characters, per RFC standards).
3.  **Regex Matching:** Ensure the input string **conforms to a standard RFC 5322 email Regular Expression** to prevent injection of excessively long strings and invalid characters.
4.  **Error Handling:** Configure the web server to use **generic error pages** (e.g., custom 500 error pages) to prevent the leakage of database schema or internal server path details.
