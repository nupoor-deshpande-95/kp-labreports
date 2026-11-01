# Day 1 - Burp Suite for Beginners
## Overview

- Started with a quick course overview
    
    ```
    1. Burp Suite for Beginners
    2. Environment Setup
    3. Subdomain Enumeration
    4. Network Reconnaissance
    5. Technology-Based CVE Hunting
    6. IIS Windows Server Reconnaissance
    7. Content Discovery
    8. Historical Analysis
    9. JavaScript Reconnaissance
    10. Hidden Parameters
    11. Creating an End-to-End Methodology
    12. Cross Site Scripting
    13. Open Redirect
    14. Race Condition
    15. Cross-Site Request Forgery
    16. Authentication Bypass
    17. Business Logic Errors
    18. Improper Access Control
    19. SQL Injection Fundamentals
    20. Server-Side Request Forgery
    21. XML External Entity
    22. Source Code Review
    23. Career & Next Steps
    ```
    
- Went over labs platform and certain recommended tools and extensions
    
    ```
    # Labs
    https://pwnedbox.site/
    
    # Tools - Encoder Decoder
    	https://www.base64encode.org/
    	https://www.base64decode.org/
    	https://www.urlencoder.org/
    	https://www.urldecoder.org/
    
    # Tools - Comparer
    	https://meldmerge.org/
    	brew install --cask meld # Mac command-line install
    	
    # Extension
    	Bot Bypass Detection by Zakhar Fedotkin, Portswigger
    	
    # Temp Mail resource
    	https://tempmail.cfd/
    ```
    

---

## Refresher

- Status Codes
    
    
    | **Category** | **Type** | **Purpose** | **Common Examples** |
    | --- | --- | --- | --- |
    | 1xx | Informational | The server received the request and processing continues. | 100 Continue, 103 Early Hints |
    | 2xx | Success | The action was successfully received, understood, and accepted. | 200 OK, 201 Created, 204 No Content |
    | 3xx | Redirection | Further action or a different URL is required to complete the request. | 301 Moved Permanently, 304 Not Modified |
    | 4xx | Client Error | The request contains bad syntax or cannot be fulfilled by the client. | 400 Bad Request, 401 Unauthorized, 404 Not Found |
    | 5xx | Server Error | The server failed to fulfill an otherwise valid request. | 500 Internal Error, 502 Bad Gateway, 503 Unavailable |

---

## Practical

---

### Title

OTP bypass leading to unauthorised access on target.

### OWASP - Vulnerability

OWASP Top 10 2025 - A07 Authentication Failures

### Summary

The application improperly trusts client-side responses, allowing attackers to manipulate OTP failure statuses into success states and completely bypass authentication.

### Vulnerable Endpoint

```bash
/api/v1/uwa/auth/otp/verify
```

### Steps to Reproduce

1. Sign up to the application using a temporary email and observe that an OTP is sent to the inbox of the applicant.
    <image src="./images/day1/Screenshot 2026-10-30 at 10.41.29 PM.png">    
    <image src="./images/day1/Screenshot 2026-10-30 at 10.52.53 PM.png">
    <image src="./images/day1/Screenshot 2026-10-30 at 10.53.02 PM.png">
The third image above gives us an idea about how the backend processes the OTP - especially the `201 Created response`.
    
```bash
    HTTP/2 201 Created
    ...
    {
        "contact":"<email>",
        "verifiedAt":"<timestamp>"
    }
```
    
2. Enter an appropriate mobile number as per the country within which the application functions and observe the response
    <image src="./images/day1/Screenshot 2026-10-30 at 10.53.02 PM.png">
    <image src="./images/day1/Screenshot 2026-10-30 at 11.07.29 PM.png">

3. Input an incorrect OTP and capture the request to see how the backend handles it.
    <image src="./images/day1/Screenshot 2026-10-30 at 10.53.02 PM.png">
    <image src="./images/day1/Screenshot 2026-10-30 at 11.09.19 PM.png">
Here we can see that it gives a 401 Unauthorized and the same in the message block of the response. Attempt to manipulate it.

```bash
    HTTP/2 401 Unauthorized
    ...
    {
        "statusCode":401,
        "message":"OTP invalid or expired"
    }
```

4. Attempt the manipulate the response captured in Burp Suite to match the pattern above and forward the response.
    <image src="./images/day1/Screenshot 2026-10-30 at 11.09.37 PM.png">
    <image src="./images/day1/Screenshot 2026-10-30 at 11.15.59 PM.png">
    <image src="./images/day1/Screenshot 2026-10-30 at 11.10.35 PM.png">

Here, we can see that the browser accepts the modified Burp Suite forwarded response and sets the `Authorization: Bearer <token>`. This implies that the browser accepts client side modified data and proceeds without verifying if the server side state is modified or not.

### Impact / Risk

1. Malicious actors can completely bypass OTP verification controls by manipulating response codes (e.g., converting a `401 Unauthorized` into a  `201 Created`  state), allowing unauthorized account creation or access.
2. Attackers can generate or accept valid session tokens (`Bearer` tokens) without proof of identity, leading to potential unauthorized access to user accounts and sensitive data.
3. Relying on client-side state or browser-enforced validation exposes a critical architectural flaw where security controls are treated as cosmetic rather than authoritative.

### Recommendation

1. Ensure that session tokens and authorization headers are issued exclusively by the backend *only after* verifying that all prerequisite verification steps (such as valid OTP submission) have successfully passed server-side checks.
2. Eliminate any logic where the client application trusts response bodies or status codes provided or modified by the user; all security-critical decisions must be validated on the server.
3. Use securely signed tokens (such as cryptographically verified JWTs) bound to a validated server-side session state to prevent unauthorized token generation or injection.

---

## Template
```text
# Vulnerability Report Template

## Title
[Insert Vulnerability Title]

## OWASP - Vulnerability
[Insert OWASP Category / Year]

## Summary
[Provide a brief, 1-2 sentence overview of the security issue and its core mechanism.]

## Vulnerable Endpoint
`[Insert Endpoint Path or URL]`

## Steps to Reproduce
1. [Prerequisite / Initial action]
2. [Trigger condition or input payload]
3. [Observe server response]
4. [Exploitation / Manipulation step]

## Impact / Risk
1. [Describe impact point 1]
2. [Describe impact point 2]
3. [Describe impact point 3]

## Recommendation
1. [Remediation step 1]
2. [Remediation step 2]
3. [Remediation step 3]
```