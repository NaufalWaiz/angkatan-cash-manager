# Security Guidelines for angkatan-cash-manager

This document outlines security best practices and controls tailored to your angkatan-cash-manager class treasury application. It aligns with industry standards and the core security principles of security by design, least privilege, defense in depth, and secure defaults.

---

## 1. Authentication & Access Control

1.1. Role-Based Access Control (RBAC)
- Define two roles: **`treasurer`** and **`student`**.  
- Store `role` in the users table (Drizzle schema).  
- Enforce role checks server-side in every API route and page.
  - E.g., a middleware that rejects non-treasurer access to `/api/payments?status=pending`.

1.2. Clerk Integration
- Use Clerk’s Next.js SDK for authentication and session management.  
- Wrap the root layout with `<ClerkProvider>`.  
- Use Clerk hooks (`useUser`, `withAuth`) to obtain user identity & roles.

1.3. Session Security
- Ensure Clerk session cookies have `Secure`, `HttpOnly`, and `SameSite=Lax` or stricter.  
- Configure idle and absolute timeouts in Clerk settings.  
- Provide explicit logout endpoints to terminate sessions server-side.

1.4. Multi-Factor Authentication (MFA)
- Enable MFA in Clerk for treasurer accounts.  
- Require at least one second factor (OTP or push) for high-privilege actions (e.g., approving payments).

---

## 2. Input Handling & Processing

2.1. Server-Side Validation
- Never trust client input.  
- Use Zod or Joi to validate all request bodies, query params, path params in Next.js API routes.

2.2. Prevent Injection Attacks
- Use Drizzle ORM’s parameterized queries for all database operations.  
- Avoid string interpolation in SQL.  
- Sanitize any dynamic fields used in raw queries.

2.3. Prevent Cross-Site Scripting (XSS)
- Escape and encode all user-supplied text in React components.  
- Use React’s default escaping for JSX.  
- If rendering HTML, run it through a sanitizer like DOMPurify.

2.4. Secure File Uploads (Payment Proofs)
- Restrict file types to images/PDF only.  
- Enforce maximum file size (e.g., 5 MB).  
- Store uploads in an isolated bucket (e.g., Supabase Storage) with signed URLs.  
- Scan uploads for malware (integrate with a scanning API).

---

## 3. Data Protection & Privacy

3.1. Data in Transit
- Enforce HTTPS (TLS≥1.2) via Next.js custom server or hosting platform settings.  
- Redirect all HTTP traffic to HTTPS.

3.2. Data at Rest
- Supabase automatically encrypts PostgreSQL data at rest.  
- Consider column-level encryption for highly sensitive fields (e.g., payment details).

3.3. Secrets Management
- Do not hardcode keys in source.  
- Store Supabase URL/keys and Clerk API keys in environment variables.  
- Use a secrets management solution (e.g., Vercel Env, AWS Secrets Manager) in CI/CD.

3.4. Logging & Masking
- Mask PII (emails, payment references) in logs.  
- Do not log stack traces or database errors to client responses.  
- Centralize logs in a secure logging service (e.g., Datadog, LogDNA).

---

## 4. API & Service Security

4.1. HTTPS & CORS
- Enforce HTTPS on all API endpoints.  
- Configure CORS to allow only your frontend origin.

4.2. Rate Limiting & Throttling
- Implement rate limiting middleware (e.g., `express-rate-limit` or Next.js Edge Middleware) on critical endpoints:
  - Login, registration, payment submission, approval endpoints.

4.3. API Versioning
- Prefix routes with `/api/v1/…`.  
- Plan for backward-compatible changes.

4.4. Least Privilege for Database
- Create a Supabase database user with only necessary privileges:
  - `SELECT`/`INSERT` on payments for students.  
  - `UPDATE` on payments only for treasurer.  
  - `SELECT` on expenses for all roles.

---

## 5. Web Application Security Hygiene

5.1. Security Headers
- Content-Security-Policy: restrict scripts/styles to self and trusted CDNs.  
- Strict-Transport-Security: max-age=31536000; includeSubDomains; preload.  
- X-Frame-Options: DENY.  
- X-Content-Type-Options: nosniff.  
- Referrer-Policy: no-referrer-when-downgrade.

5.2. CSRF Protection
- For any state-changing form/API, implement CSRF tokens (e.g., NextAuth’s CSRF or custom synchronizer tokens).

5.3. Secure Cookies
- Set cookies with `Secure`, `HttpOnly`, and `SameSite=Strict` (or Lax for cross-page embeds).

5.4. Subresource Integrity (SRI)
- Apply SRI hashes when including any third-party scripts or styles.

---

## 6. Infrastructure & Configuration Management

6.1. Server Hardening
- Disable unused services on the hosting environment.  
- Keep OS and runtime dependencies up to date.

6.2. Environment Segregation
- Use separate Supabase projects for dev, staging, and production.  
- Enforce different API keys and restrict origins per environment.

6.3. Secret Rotation & Revocation
- Rotate Supabase and Clerk keys quarterly or after suspected compromise.  
- Revoke unused credentials immediately.

---

## 7. Dependency Management & CI/CD

7.1. Secure Dependencies
- Maintain and audit `package-lock.json`.  
- Integrate SCA tools (e.g., Snyk, Dependabot) to detect vulnerable packages.

7.2. Minimal Footprint
- Only install required packages (avoid unnecessary utilities).  
- Review transitive dependencies regularly.

7.3. CI/CD Pipeline
- Run linting, type checking, and unit/integration tests on every PR.  
- Fail builds on high/critical vulnerabilities or test coverage drops.

---

## 8. Error Handling & Monitoring

8.1. Fail Securely
- Catch and handle exceptions in API routes; return generic error messages.  
- Do not expose stack traces or internal details in production.

8.2. Monitoring & Alerts
- Integrate application performance monitoring (APM).  
- Set alerts for error rate spikes, high latency, or rate limit breaches.

8.3. Audit Trails
- Log all payment creation, approval, and rejection actions with user ID and timestamp.  
- Store logs in an append-only, access-controlled store.

---

## 9. Ongoing Security Practices

- Schedule quarterly security reviews and dependency audits.  
- Conduct penetration testing before major releases.  
- Update documentation and train team members on secure coding practices.

---

*By following this layered, security-by-design approach, the angkatan-cash-manager treasury application will maintain resilience against common threats while ensuring data integrity, confidentiality, and availability.*