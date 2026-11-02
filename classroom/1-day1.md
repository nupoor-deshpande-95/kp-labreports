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

## Template

```
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