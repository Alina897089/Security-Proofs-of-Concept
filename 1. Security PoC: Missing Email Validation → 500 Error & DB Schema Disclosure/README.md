# 1. Security PoC: Missing Email Validation → 500 Error & DB Schema Disclosure

## Overview
A vulnerability was discovered due to the absence of proper email validation during registration or when using the password recovery function. This allowed sending requests with excessively long strings in the email field.

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
<img width="1590" height="743" alt="chrome_JZjmWvX10c2025" src="https://github.com/user-attachments/assets/223f3516-e630-467e-bfd2-dfae7a016385" />

## Tools Used
*   Postman
