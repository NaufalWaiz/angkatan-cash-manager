# Project Requirements Document (PRD)

## 1. Project Overview
**Paragraph 1**  
The “angkatan-cash-manager” is a web application designed to streamline and modernize how a class treasury collects, approves, and reports financial transactions. It solves the common problems of manual payment tracking, lack of transparency, and cumbersome approval workflows by offering a digital dashboard, role-based access, and a structured database. Students can submit payment proofs, and Treasurers can quickly approve or reject them, all within a single, cohesive interface.

**Paragraph 2**  
This project is being built to increase accountability, reduce administrative overhead, and provide clear, real-time insights into class funds. Key success criteria include secure user authentication, accurate role-based permissions, reliable data storage in Supabase, and an intuitive user interface that works well on both desktop and mobile browsers. The first version will demonstrate end-to-end flow: student submissions, treasurer approvals, and public expense visibility.

## 2. In-Scope vs. Out-of-Scope

**In-Scope (Version 1)**
- User registration and login via Clerk (authentication and user management).
- Two roles: **Student** and **Treasurer**, enforced across UI and API.
- Protected dashboard showing:
  - For Students: total collected amount and summary of class expenses.
  - For Treasurers: a queue of pending payment submissions in a data table.
- Payment submission page with form (receipt upload, amount, description).
- Treasurer approval interface: approve/reject buttons on each payment.
- Public expenses page listing all approved class expenditures.
- Database integration with Supabase using Drizzle ORM (PostgreSQL).
- Basic styling and layout using Tailwind CSS v4 and shadcn/ui.

**Out-of-Scope (Phase 1)**
- Mobile-native apps (Android/iOS).
- Push or email notifications on payment status changes.
- Advanced reporting (charts beyond summary cards) or CSV exports.
- Multi-class or multi-group support.
- Offline mode or PWA features.
- Separate Go backend (all logic lives in Next.js API routes).

## 3. User Flow
**Paragraph 1**  
A **new user** lands on the public homepage and chooses to sign up via Clerk. After email verification, they log in and land on the dashboard: a top navbar shows their name and a logout button; a left sidebar holds links to “Dashboard,” “Submit Payment,” and “Expenses.” Students see summary cards (total collected, total expenses) and can click “Submit Payment” to open a form. They fill in amount, upload receipt image, add notes, and click “Submit.” The form calls `POST /api/payments`, stores data in Supabase, and returns to the dashboard with a success message.

**Paragraph 2**  
A **Treasurer** logs in the same way but sees the dashboard populated with a DataTable of pending payments. Each row has “Approve” and “Reject” buttons. Clicking one sends a `PATCH /api/payments/:id` request. The table refreshes to remove processed items. The Treasurer can also click “Expenses” to view a public list of all approved class expenses, fetched via `GET /api/expenses`. Throughout, role checks in middleware ensure Students cannot access Treasurer screens and vice versa.

## 4. Core Features
- **Authentication & User Management**  
  - Clerk integration for sign-up, login, and session handling.
  - Role assignment (Student, Treasurer) stored in user metadata.
- **Role-Based Access Control (RBAC)**  
  - Middleware that checks `user.role` on protected pages and API routes.
- **Dashboard**  
  - Summary cards (total collected funds, total expenses).
  - DataTable for pending payments (Treasurer view).
- **Payment Submission**  
  - Form with amount, description, file upload component.
  - `POST /api/payments` endpoint for new payments.
- **Payment Approval Workflow**  
  - `PATCH /api/payments/:id` to update status (approved/rejected).
  - Immediate UI update in DataTable.
- **Expense Tracking**  
  - `GET /api/expenses` endpoint for approved payments.
  - Public-facing DataTable with pagination and search.
- **Database Schema**  
  - `users` table with `role` column.
  - `payments` table (id, userId, amount, receiptUrl, status, createdAt).
  - `expenses` view or table (approved payments summary).
- **UI Components**  
  - Reusable SectionCards, ChartAreaInteractive (for future charts).
  - `shadcn/ui` Form, Input, Button components.
- **API Routes**  
  - Next.js API routes under `/app/api` for payments and expenses.

## 5. Tech Stack & Tools
- **Frontend**: Next.js (App Router), React 18, TypeScript.
- **UI & Styling**: Tailwind CSS v4, shadcn/ui component library.
- **Authentication**: Clerk Next.js SDK (server and client integration).
- **Backend/API**: Next.js API Routes (Node.js), TypeScript.
- **Database**: Supabase (PostgreSQL) with Drizzle ORM for type-safe queries.
- **File Storage**: Supabase Storage (for receipt uploads).

## 6. Non-Functional Requirements
- **Performance**: API response under 200ms for common queries; SSR for dashboard summary.
- **Security**:
  - All routes served over HTTPS.
  - Role checks in middleware before page render or API logic.
  - Validate and sanitize file uploads; enforce 5MB max size.
- **Reliability**: 99.9% uptime assumed on Supabase; retry logic for transient DB errors.
- **Scalability**: Modular component design; can add features without major refactor.
- **Usability**: Responsive design (mobile, tablet, desktop); clear error/success messaging.

## 7. Constraints & Assumptions
- **Supabase** project and credentials must be provisioned before development.
- **Clerk** account and API keys are available for auth integration.
- Users will be manually assigned “Treasurer” role via Clerk dashboard or admin API.
- Environment supports Node.js 18+, Next.js 14+.
- No third-party notification or payment gateway integrations in v1.

## 8. Known Issues & Potential Pitfalls
- **API Rate Limits**: Supabase free tier may throttle under heavy use—monitor and upgrade if needed.
- **File Upload Size**: Large receipt images could impact performance—restrict size and use client-side compression.
- **Race Conditions**: Two Treasurers approving the same payment—use row-level locking or check current status before updating.
- **Clerk Quotas**: Excessive sign-ups could hit auth limits—plan for fallback.
- **Time Zones**: Storing and displaying `createdAt` consistently—use UTC everywhere.

---
*This PRD is the definitive guide for the AI model to generate detailed technical documents and code structure for angkatan-cash-manager without ambiguity.*