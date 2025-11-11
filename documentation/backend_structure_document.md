# Backend Structure Document

This document outlines the backend setup for the angkatan-cash-manager application. It explains how components fit together, what technologies we’re using, and how the system stays fast, secure, and easy to maintain.

## 1. Backend Architecture

Overall, our backend lives inside the same Next.js project that powers the frontend. We use Next.js API Routes to implement server-side logic. Here’s how it all fits:

• Monorepo structure: frontend pages, shared components, and API routes in one codebase—but clearly separated.
• Next.js App Router:
  – Server Components fetch data from the database securely.  
  – Client Components handle interactive UI elements (forms, tables, charts).
• Drizzle ORM manages database access in a type-safe way.  
• Clerk handles user authentication and session management.

By running our API as serverless functions (for example, on Vercel), we get automatic horizontal scaling. Each function spins up as needed, so adding users or spikes in traffic doesn’t slow us down. The modular design (API routes as controllers, Drizzle schemas as models) keeps code organized and easy to extend or fix.

## 2. Database Management

We store all data in PostgreSQL via Supabase. Key points:

• Database Type: Relational (SQL)  
• Provider: Supabase (managed PostgreSQL)
• ORM: Drizzle (TypeScript-friendly, ensures compile-time checking of queries)

Data is organized into tables (users, payments, expenses). We define schemas and migrations in the code—Supabase runs them under the hood. Drizzle’s migrations tools let us evolve the schema safely over time. We also rely on Supabase for daily backups, point-in-time recovery, and replication to ensure data durability.

## 3. Database Schema

Human-Readable Overview:

• **Users**
  – id: unique user identifier  
  – email: login email  
  – name: display name  
  – role: ‘student’ or ‘treasurer’  
  – created_at: timestamp

• **Payments**
  – id: unique payment record ID  
  – user_id: references the student who submitted it  
  – amount: payment amount  
  – date_submitted: when the student uploaded proof  
  – status: ‘pending’, ‘approved’, or ‘rejected’  
  – proof_url: link to uploaded receipt or screenshot  
  – approved_at: timestamp when treasurer acted

• **Expenses**
  – id: unique expense record ID  
  – title: description of expense  
  – amount: expense amount  
  – description: optional details  
  – date_added: when expense was logged  
  – added_by: references the treasurer user

PostgreSQL (Drizzle) DDL Example:
```
CREATE TABLE users (
  id UUID PRIMARY KEY,
  email TEXT UNIQUE NOT NULL,
  name TEXT,
  role TEXT NOT NULL DEFAULT 'student',
  created_at TIMESTAMP WITH TIME ZONE DEFAULT now()
);

CREATE TABLE payments (
  id UUID PRIMARY KEY,
  user_id UUID REFERENCES users(id),
  amount NUMERIC(10,2) NOT NULL,
  date_submitted TIMESTAMP WITH TIME ZONE DEFAULT now(),
  status TEXT NOT NULL DEFAULT 'pending',
  proof_url TEXT,
  approved_at TIMESTAMP WITH TIME ZONE
);

CREATE TABLE expenses (
  id UUID PRIMARY KEY,
  title TEXT NOT NULL,
  amount NUMERIC(10,2) NOT NULL,
  description TEXT,
  date_added TIMESTAMP WITH TIME ZONE DEFAULT now(),
  added_by UUID REFERENCES users(id)
);
```  

## 4. API Design and Endpoints

We follow RESTful design with Next.js API Routes. All routes live under `/app/api`.

Key Endpoints:

• POST /api/payments  
  – Purpose: Students submit a new payment record and upload proof.  
  – Access: authenticated students only.

• GET /api/payments?status=pending  
  – Purpose: Treasurer fetches unapproved payments.  
  – Access: treasurer only.

• PATCH /api/payments/:id  
  – Purpose: Treasurer approves or rejects a payment (update status and approved_at).  
  – Access: treasurer only.

• GET /api/expenses  
  – Purpose: Fetch all recorded class expenses.  
  – Access: any authenticated user.

• POST /api/expenses  
  – Purpose: Treasurer logs a new expense.  
  – Access: treasurer only.

• GET /api/dashboard-stats  
  – Purpose: Calculate and return summary stats (total collected, total spent, current balance).  
  – Access: authenticated users.

All endpoints validate input, check user roles via Clerk’s session tokens, and then run Drizzle queries against Supabase.

## 5. Hosting Solutions

We use a combination of Vercel and Supabase:

• Vercel (Next.js)  
  – Hosts both the frontend and API Routes as serverless functions.  
  – Built-in CDN for static assets and edge caching.  
  – Automatic scaling, zero-config deployments.

• Supabase (Database & Auth)  
  – Managed PostgreSQL with built-in auth, storage, and real-time features.  
  – Free tier for small classes, easy to upgrade.

Benefits:

• Reliability: global edge network with automatic failover.  
• Performance: serverless functions scale with demand, assets served from CDN.  
• Cost-effectiveness: pay-as-you-go, generous free tiers.

## 6. Infrastructure Components

• Load Balancer / Edge Network:
  – Vercel’s edge network routes requests to the nearest serverless function.  
  – Automatically balances traffic and reduces latency.

• CDN:
  – Vercel CDN caches static assets (CSS, JS, images) close to users.  
  – Speeds up page loads and reduces origin server load.

• Caching:
  – Next.js ISR (Incremental Static Regeneration) for pages that change infrequently (e.g., public expense list).  
  – SWR or React Query on the client for data-level caching and background revalidation.

• Storage:
  – Supabase Storage for hosting uploaded payment proofs (images, PDFs).  

## 7. Security Measures

• Authentication:
  – Clerk handles signup, login, password resets, and session management.  
  – All API routes require a valid Clerk token.

• Authorization:
  – Role checks in middleware ensure only Treasurers can access sensitive endpoints.  
  – Students can only create payments and view public data.

• Encryption:
  – HTTPS/TLS encryption in transit for all traffic (Vercel + Supabase).  
  – At-rest encryption on Supabase by default.

• Data Validation:
  – Server-side input validation to prevent SQL injection or malformed data.  
  – File type and size checks on uploads.

• Environment Variables:
  – Secrets (`NEXT_PUBLIC_SUPABASE_URL`, `SUPABASE_SECRET_KEY`, `CLERK_API_KEY`) stored securely in Vercel’s environment settings.

## 8. Monitoring and Maintenance

• Logging & Errors:
  – Vercel provides real-time logs for function invocations and errors.  
  – Optional integration with Sentry or Logflare for advanced monitoring.

• Performance Metrics:
  – Vercel Analytics for frontend and serverless function latency.  
  – Supabase Dashboard for query performance and slow query logging.

• Maintenance:
  – Drizzle migrations track schema changes and can be rolled back if needed.  
  – Regular dependency updates via automated tools (Dependabot).  
  – Periodic security audits of dependencies and configuration.

## 9. Conclusion and Overall Backend Summary

Our backend uses Next.js API Routes and Drizzle ORM to deliver a scalable, maintainable financial application. Clerk secures user authentication and roles, Supabase manages data reliably, and Vercel hosts everything with global performance. Together, these components meet the project’s goals: a transparent, secure, and easy-to-extend class treasury management system that lets students submit payments and Treasurers approve them efficiently.

Unique aspects:

• Single codebase for frontend and backend streamlines development.  
• Type-safe database queries catch errors at compile time.  
• Serverless architecture means minimal ops overhead—focus on features, not servers.

By following this structure, any developer—even without deep technical background—can understand how the backend works and where to make changes.