### Title

Mobile number verification flaw leads to full account takeover (ATO)

### OWASP - Vulnerability

OWASP Top 10 2025 - A07 Authentication Failures

### Summary

The application is vulnerable to response tampering during the mobile verification step. By capturing the server's ```401 Unauthorized``` error response for an invalid OTP in Burp Suite, the attacker modifies it to match a successful ```201 Created``` registration response pattern. The client-side browser accepts this manipulated data without verifying the true server-side state, setting the authorization token and granting unauthorized access.

### Vulnerable Endpoint

```bash
/api/v1/uwa/auth/otp/verify
```

### Steps to Reproduce

1. Sign up to the application using a temporary email and observe that an OTP is sent to the inbox of the applicant.

    <img width="3016" height="1888" alt="image" src="https://github.com/user-attachments/assets/d0ab4118-9d8b-41b6-b80f-a6f2f59aaaf2" />
    
    <img width="3016" height="1888" alt="image" src="https://github.com/user-attachments/assets/19cd57ed-b402-4a7a-878a-4d3a7cc753e8" />
    
    <img width="3016" height="1888" alt="image" src="https://github.com/user-attachments/assets/8c2172fa-6e22-44c4-820b-c5feeda1f656" />
    
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
    
    <img width="3016" height="1888" alt="image" src="https://github.com/user-attachments/assets/99d40067-4a41-43ae-8650-1743bbcd7140" />
    
    <img width="3016" height="1888" alt="image" src="https://github.com/user-attachments/assets/6014ad93-8a5d-4703-a69b-f024d3e938f0" />
    
3. Input an incorrect OTP and capture the request to see how the backend handles it.
    
    <img width="3016" height="1888" alt="image" src="https://github.com/user-attachments/assets/e0a9f9e2-60dd-4b51-9142-04b8ddbc6ba1" />
    
    <img width="3016" height="1888" alt="image" src="https://github.com/user-attachments/assets/da17e026-73ad-4c87-9471-a0775fbda978" />
    

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
    
    <img width="3016" height="1888" alt="image" src="https://github.com/user-attachments/assets/c4a66d42-b72e-43c4-8008-af9247866cd9" />
    
    <img width="3016" height="1888" alt="image" src="https://github.com/user-attachments/assets/93ab8927-fb5f-4c90-a8c5-ed3de2b4b2f8" />
    
    <img width="3016" height="1888" alt="image" src="https://github.com/user-attachments/assets/8c2659eb-4dd1-4edd-b451-e23e996fd4b5" />
    

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
