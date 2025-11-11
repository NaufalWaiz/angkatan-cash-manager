# Tech Stack Document for angkatan-cash-manager

## Frontend Technologies

- **Next.js (App Router)**  
  Provides a unified framework for building both pages and API routes. It enables server-side rendering for faster initial load and client-side navigation for a smooth user experience.
- **React & TypeScript**  
  Ensures a modular, component-based UI with type safety. TypeScript reduces runtime errors and improves developer productivity by catching mistakes early.
- **Tailwind CSS v4**  
  A utility-first CSS framework that makes styling fast and consistent. It allows building responsive layouts without writing custom CSS from scratch.
- **shadcn/ui**  
  A collection of prebuilt, accessible UI components (forms, buttons, cards, tables). These components integrate seamlessly with Tailwind to accelerate UI development and maintain a polished look.
- **Server & Client Components**  
  Next.js lets us fetch sensitive data on the server while keeping interactive elements (like forms and charts) on the client. This split improves performance and security.

## Backend Technologies

- **Next.js API Routes**  
  Built-in backend endpoints under `/app/api` for handling business logic (payment submissions, approvals, expense queries). Keeps front- and back-end in one codebase for simplicity and ease of deployment.
- **Drizzle ORM**  
  A TypeScript-first ORM for PostgreSQL. Provides type-safe database queries and migrations. Ensures data integrity and prevents common SQL mistakes.
- **PostgreSQL (via Supabase)**  
  A reliable, scalable relational database. Supabase offers a managed PostgreSQL instance with built-in authentication, real-time updates, and UI dashboard.
- **Go (Optional)**  
  If a separate backend is preferred, a Go API can replace Next.js API routes. Go offers high performance and a small memory footprint, suitable for backend microservices.

## Infrastructure and Deployment

- **Version Control: Git & GitHub**  
  Tracks changes, supports collaborative development, and integrates with CI/CD tools.
- **Hosting: Vercel (Recommended)**  
  Optimized for Next.js, provides automatic deployments on every push, global CDN, and serverless functions for API routes.
- **CI/CD: GitHub Actions**  
  Automates testing, linting, and deployments. Ensures code quality by running tests on every pull request and deploys only passing changes.
- **Environment Management**  
  Uses environment variables for sensitive credentials (database URLs, Clerk keys). Vercel or GitHub Actions secrets keep them secure.

## Third-Party Integrations

- **Clerk**  
  A drop-in authentication service for sign-up, sign-in, and user management. Offers ready-made UI components and hooks (`<ClerkProvider>`, `useUser`) for role-based access control.
- **Supabase**  
  Provides the PostgreSQL database, authentication, and storage. Simplifies setup compared to self-hosted databases, with a GUI for managing schemas and data.
- **Optional Analytics**  
  Can integrate tools like Vercel Analytics or Google Analytics to track user behavior and performance metrics.

## Security and Performance Considerations

- **Authentication & Authorization**  
  - Clerk enforces secure sign-in flows and session management.  
  - Role-based checks (student vs. treasurer) in API routes and page middleware prevent unauthorized access.
- **Data Protection**  
  - HTTPS always encrypts data in transit.  
  - Environment variables keep secrets out of the codebase.
- **Type Safety**  
  Drizzle ORM and TypeScript ensure that only valid queries run against the database, reducing runtime errors and injection risks.
- **Performance Optimizations**  
  - Server Components fetch data at build or request time, reducing bundle size.  
  - Client Components handle interactivity without blocking the server.  
  - Tailwind’s utility classes minimize CSS footprint.  
  - Vercel’s global CDN and edge caching deliver assets quickly to users worldwide.

## Conclusion and Overall Tech Stack Summary

This project combines modern web technologies to deliver a secure, performant, and user-friendly treasury management app:

- **Next.js** unites frontend and backend into one framework.  
- **React + TypeScript** deliver a robust, type-safe UI.  
- **Tailwind CSS & shadcn/ui** accelerate the creation of responsive, accessible interfaces.  
- **Drizzle ORM + Supabase (PostgreSQL)** ensure reliable data management with type-checked queries.  
- **Clerk** provides seamless authentication and role-based access control.  
- **Vercel + GitHub Actions** automate deployment and maintain high availability.

Together, these technologies support rapid development, easy maintenance, and a polished experience for both Treasurers and Students. This stack is flexible enough to add new features—like detailed reporting or external payment processors—while maintaining security and performance at every step.