# API Security Checklist

## Authentication
- [ ] Consider not using `Basic Auth`. Use standard authentication (e.g. [JWT](https://jwt.io/), [OAuth](https://oauth.net/)).
- [ ] Don't reinvent the wheel in `Authentication`, `token generation`, `password storage`. Use the standards.
- [ ] Use `Max Retry` and jail features in Login.
- [ ] Use encryption on all sensitive data.

### JWT (JSON Web Token)
- [ ] Use a random complicated key (`JWT Secret`) to make brute forcing the token very hard.
- [ ] Don't extract the algorithm from the payload. Force the algorithm in the backend (`HS256` or `RS256`).
- [ ] Make token expiration (`TTL`, `RTTL`) as short as possible.
- [ ] Don't store sensitive data in the JWT payload, it can be decoded [easily](https://jwt.io/#debugger-io).
- [ ] Avoid storing too much data. JWT is usually shared in headers and they have a size limit.

### OAuth
- [ ] Always validate `redirect_uri` server-side to allow only whitelisted URLs.
- [ ] Always try to exchange for code and not tokens (don't allow `response_type=token`).
- [ ] Use `state` parameter with a random hash to prevent CSRF on the OAuth authentication process.
- [ ] Define the default scope, and validate scope parameters for each application.

## Access
- [ ] Limit requests (Throttling) to avoid DDoS / brute-force attacks.
- [ ] Use HTTPS on server side to avoid MITM (Man in the Middle Attack).
- [ ] Use `HSTS` header with SSL to avoid SSL Strip attack.
- [ ] For private APIs, allow access only from safelisted IPs/hosts.

## Input
- [ ] Use the proper HTTP method according to the operation: `GET (read)`, `POST (create)`, `PUT/PATCH (replace/update)`, and `DELETE (to delete a record)`, and respond with `405 Method Not Allowed` if the requested method isn't appropriate for the requested resource.
- [ ] Validate `content-type` on request Accept header (Content Negotiation) to allow only your supported format (e.g. `application/xml`, `application/json`, etc.) and respond with `406 Not Acceptable` response if not matched.
- [ ] Validate `content-type` of posted data as you accept (e.g. `application/x-www-form-urlencoded`, `multipart/form-data`, `application/json`, etc.).
- [ ] Validate user input to avoid common vulnerabilities (e.g. `XSS`, `SQL-Injection`, `Remote Code Execution`, etc.).
- [ ] Don't use any sensitive data (`credentials`, `Passwords`, `security tokens`, or `API keys`) in the URL, but use standard Authorization header.
- [ ] Use an API Gateway service to enable caching, Rate Limit policies (e.g. `Quota`, `Spike Arrest`, or `Concurrent Rate Limit`) and deploy APIs resources dynamically.
- [ ] Use only server-side encryption.

## Processing
- [ ] Check if all the endpoints are protected behind authentication to avoid broken authentication process.
- [ ] User own resource ID should be avoided. Use `/me/orders` instead of `/user/654321/orders`.
- [ ] Don't auto-increment IDs. Use `UUID` instead.
- [ ] If you are parsing XML files, make sure entity parsing is not enabled to avoid `XXE` (XML external entity attack).
- [ ] If you are parsing XML files, make sure entity expansion is not enabled to avoid `Billion Laughs/XML bomb` via exponential entity expansion attack.
- [ ] Use a CDN for file uploads.
- [ ] If you are dealing with huge amount of data, use Workers and Queues to process as much as possible in background and return response fast to avoid HTTP Blocking.
- [ ] Do not forget to turn the DEBUG mode OFF.

## Output
- [ ] Send `X-Content-Type-Options: nosniff` header.
- [ ] Send `X-Frame-Options: deny` header.
- [ ] Send `Content-Security-Policy: default-src 'none'` header.
- [ ] Remove fingerprinting headers - `X-Powered-By`, `Server`, `X-AspNet-Version`, etc.
- [ ] Force `content-type` for your response, if you return `application/json` then your response `content-type` is `application/json`.
- [ ] Don't return sensitive data like `credentials`, `Passwords`, or `security tokens`.
- [ ] Return the proper status code according to the operation completed. (e.g. `200 OK`, `400 Bad Request`, `401 Unauthorized`, `405 Method Not Allowed`, etc.).

Source: [API Security Checklist](https://github.com/shieldfy/API-Security-Checklist)

====================================================================================================================================================================================

# OWASP API Security Top 10 2023

### [API1:2023 - Broken Object Level Authorization](https://owasp.org/API-Security/editions/2023/en/0xa1-broken-object-level-authorization/)

APIs tend to expose endpoints that handle object identifiers, creating a wide attack surface of Object Level Access Control issues. Object level authorization checks should be considered in every function that accesses a data source using an ID from the user.

### [API2:2023 - Broken Authentication](https://owasp.org/API-Security/editions/2023/en/0xa2-broken-authentication/)

Authentication mechanisms are often implemented incorrectly, allowing attackers to compromise authentication tokens or to exploit implementation flaws to assume other user’s identities temporarily or permanently. Compromising a system’s ability to identify the client/user, compromises API security overall.

### [API3:2023 - Broken Object Property Level Authorization](https://owasp.org/API-Security/editions/2023/en/0xa3-broken-object-property-level-authorization/)

This category combines [API3:2019 Excessive Data Exposure](https://owasp.org/API-Security/editions/2019/en/0xa3-excessive-data-exposure/) and [API6:2019 - Mass Assignment](https://owasp.org/API-Security/editions/2019/en/0xa6-mass-assignment/), focusing on the root cause: the lack of or improper authorization validation at the object property level. This leads to information exposure or manipulation by unauthorized parties.

### [API4:2023 - Unrestricted Resource Consumption](https://owasp.org/API-Security/editions/2023/en/0xa4-unrestricted-resource-consumption/)

Satisfying API requests requires resources such as network bandwidth, CPU, memory, and storage. Other resources such as emails/SMS/phone calls or biometrics validation are made available by service providers via API integrations, and paid for per request. Successful attacks can lead to Denial of Service or an increase of operational costs.

### [API5:2023 - Broken Function Level Authorization](https://owasp.org/API-Security/editions/2023/en/0xa5-broken-function-level-authorization/)

Complex access control policies with different hierarchies, groups, and roles, and an unclear separation between administrative and regular functions, tend to lead to authorization flaws. By exploiting these issues, attackers can gain access to other users’ resources and/or administrative functions.

### [API6:2023 - Unrestricted Access to Sensitive Business Flows](https://owasp.org/API-Security/editions/2023/en/0xa6-unrestricted-access-to-sensitive-business-flows/)

APIs vulnerable to this risk expose a business flow - such as buying a ticket, or posting a comment - without compensating for how the functionality could harm the business if used excessively in an automated manner. This doesn’t necessarily come from implementation bugs.

### [API7:2023 - Server Side Request Forgery](https://owasp.org/API-Security/editions/2023/en/0xa7-server-side-request-forgery/)

Server-Side Request Forgery (SSRF) flaws can occur when an API is fetching a remote resource without validating the user-supplied URI. This enables an attacker to coerce the application to send a crafted request to an unexpected destination, even when protected by a firewall or a VPN.

### [API8:2023 - Security Misconfiguration](https://owasp.org/API-Security/editions/2023/en/0xa8-security-misconfiguration/)

APIs and the systems supporting them typically contain complex configurations, meant to make the APIs more customizable. Software and DevOps engineers can miss these configurations, or don’t follow security best practices when it comes to configuration, opening the door for different types of attacks.

### [API9:2023 - Improper Inventory Management](https://owasp.org/API-Security/editions/2023/en/0xa9-improper-inventory-management/)

APIs tend to expose more endpoints than traditional web applications, making proper and updated documentation highly important. A proper inventory of hosts and deployed API versions also are important to mitigate issues such as deprecated API versions and exposed debug endpoints.

### [API10:2023 - Unsafe Consumption of APIs](https://owasp.org/API-Security/editions/2023/en/0xaa-unsafe-consumption-of-apis/)

Developers tend to trust data received from third-party APIs more than user input, and so tend to adopt weaker security standards. In order to compromise APIs, attackers go after integrated third-party services instead of trying to compromise the target API directly.


