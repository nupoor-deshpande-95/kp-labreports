### Title

Email ID verification flaw leads to full account takeover (ATO)

### OWASP - Vulnerability

OWASP Top 10 2025 - A07 Authentication Failures

### Summary

The application is vulnerable to response tampering during the email id verification step. By capturing the server's ```401 Unauthorized``` error response for an invalid OTP in Burp Suite, the attacker modifies it to match a successful ```201 Created``` registration response pattern. The client-side browser accepts this manipulated data without verifying the true server-side state, setting the authorization token and granting unauthorized access.

### Vulnerable Endpoint

```bash
/api/v1/uwa/auth/otp/verify
```

### Steps to Reproduce

1. Sign up to the application using a nonexistent email and observe that an OTP is sent to the inbox of the applicant.

   <img width="756" height="491" alt="SCR-20261102-rhyn" src="https://github.com/user-attachments/assets/6649936d-5c3e-43a5-b2bd-7724af1e6cb9" />

   <img width="756" height="491" alt="SCR-20261102-riby" src="https://github.com/user-attachments/assets/6c4eadac-3fe3-4d1b-94dc-18ca6177684c" />
    
2. Enter any random otp in the text box and observe the response captured in Burp Suite.

    <img width="756" height="491" alt="SCR-20261102-rjcv" src="https://github.com/user-attachments/assets/54cdaafe-b575-4d4a-8738-3b6d25af5722" />

    <img width="756" height="491" alt="SCR-20261102-rjhv" src="https://github.com/user-attachments/assets/65ba197a-a595-4a35-877b-2c8cc3c70737" />

    Here we can see that it gives a 401 Unauthorized and the same in the message block of the response. Attempt to manipulate it.

3. Manipulate the captured to mimic that of a successful operation as seen below:

    <img width="756" height="491" alt="SCR-20261102-rkni" src="https://github.com/user-attachments/assets/abd63633-f293-410f-9193-310506973339" />
 
    Here, we can see that the browser accepts the modified Burp Suite forwarded response and sets the redirects the user to further registration steps.

### Impact / Risk

1. Malicious actors can completely bypass email id verification controls by manipulating response codes (e.g., converting a `401 Unauthorized` into a  `201 Created`  state), allowing unauthorized account creation or access.
2. Attackers can generate or accept valid session tokens (`Bearer` tokens) without proof of identity, leading to potential unauthorized access to user accounts and sensitive data.
3. Relying on client-side state or browser-enforced validation exposes a critical architectural flaw where security controls are treated as cosmetic rather than authoritative.

### Recommendation

1. Ensure that session tokens and authorization headers are issued exclusively by the backend *only after* verifying that all prerequisite verification steps (such as valid OTP submission) have successfully passed server-side checks.
2. Eliminate any logic where the client application trusts response bodies or status codes provided or modified by the user; all security-critical decisions must be validated on the server.
3. Use securely signed tokens (such as cryptographically verified JWTs) bound to a validated server-side session state to prevent unauthorized token generation or injection.

---
