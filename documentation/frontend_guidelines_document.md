# Frontend Guidelines Document for angkatan-cash-manager

This document describes the frontend setup, architecture, and best practices for the angkatan-cash-manager application. It covers the technologies, design choices, and patterns used to build a scalable, maintainable, and performant class treasury management web app.

## 1. Frontend Architecture

### Frameworks and Libraries
- **Next.js (App Router)**: Provides file-based routing, server and client components, and built-in support for performance optimizations like streaming and incremental static regeneration.  
- **React**: Powers interactive UI components.  
- **Tailwind CSS v4**: Utility-first CSS framework for rapid styling.  
- **shadcn/ui**: A lightweight component library built on top of Tailwind CSS to standardize UI elements like buttons, inputs, modals, and cards.  
- **Clerk**: Manages user authentication and role-based access control (replacing Better Auth).  
- **Drizzle ORM**: Type-safe ORM that connects to a PostgreSQL database (Supabase) for managing users, payments, and expenses.  

### Scalability, Maintainability, Performance
- **Modular file structure** (`/app`, `/components`, `/ui`, `/db`): Makes it easy to find and update features independently.  
- **Server Components**: Fetch sensitive data on the server, reducing client bundle size.  
- **Client Components**: Contain interactive logic (forms, buttons) and use React hooks for local state.  
- **Utility-first styling** (Tailwind): Keeps CSS size minimal by purging unused classes in production.  
- **Incremental adoption**: You can add new pages or UI elements without restructuring the entire app.  

## 2. Design Principles

### Usability
- Clear, consistent UI patterns (buttons, forms, tables) using shadcn/ui.  
- Simple page layouts: dashboards with cards and tables, submission forms with clear labels and feedback.  

### Accessibility
- Use semantic HTML elements (`<button>`, `<table>`, `<form>`, `<label>`).  
- All interactive controls receive focus states and aria attributes where needed.  
- High color-contrast palette for readability.  

### Responsiveness
- Mobile-first design: Tailwind’s responsive utilities (`sm:`, `md:`, `lg:`) adapt layouts for small to large screens.  
- Flexible grid and flex layouts for cards and tables.  

## 3. Styling and Theming

### Approach
- **Utility-first CSS**: Tailwind CSS for most styling through class names.  
- **Component styles**: `shadcn/ui` provides pre-styled, customizable React components.  
- **No separate SASS or BEM**: Tailwind eliminates the need for naming conventions.  

### Theming
- Configure Tailwind’s `theme.extend` in `tailwind.config.js` to define custom colors and font families.  
- Use CSS variables (via `:root`) if you need dynamic theming (dark mode toggle).  

### Visual Style
- **Style**: Modern flat design with subtle shadows (glassmorphism touches on cards).  
- **Font**: Inter (system-UI fallback) – clean, professional, optimized for the web.  

### Color Palette
- **Primary**: #4F46E5 (Indigo)  
- **Secondary**: #10B981 (Emerald)  
- **Accent**: #F59E0B (Amber)  
- **Background**: #F3F4F6 (Gray-100)  
- **Surface (cards, panels)**: #FFFFFF (White)  
- **Text (primary)**: #111827 (Gray-900)  
- **Text (secondary)**: #6B7280 (Gray-500)  
- **Error**: #EF4444 (Red-500)  
- **Success**: #22C55E (Green-500)  

## 4. Component Structure

### Organization
- **/app**: Page routes and layouts (server components by default).  
- **/components**: Feature-specific components (e.g., `PaymentSubmissionForm`, `DashboardStats`, `ExpenseTable`).  
- **/ui**: Generic UI primitives from shadcn/ui (buttons, inputs, modals) that can be wrapped or extended.  

### Reusability
- Small, focused components (atoms) combined into larger ones (molecules/pages).  
- Shared hooks and utilities for data fetching or formatting under `/lib` or `/hooks`.  
- Co-located styles via Tailwind classes—no separate CSS files.  

### Benefits of Component-Based Architecture
- Encapsulation: Each component manages its own markup, styles, and logic.  
- Consistency: Shared UI primitives ensure a uniform look.  
- Testability: Smaller units are easier to test in isolation.  

## 5. State Management

### Approach
- **Server state**: Fetched in server components using Next.js data fetching APIs (`fetch` in server components).  
- **Client state**: Local component state with React hooks (`useState`, `useEffect`).  
- **Auth state**: Provided by Clerk’s React context and hooks (`useUser`, `SignedIn`, `SignedOut`).  

### Shared State Patterns
- For cross-component data (e.g., user profile, theme), use React Context or a lightweight library (e.g., Zustand or Jotai) if needed.  
- Data-fetching caches: Consider SWR or React Query for richer caching features and background refetching.  

## 6. Routing and Navigation

### File-Based Routing
- **/app** folder with `layout.tsx` for shared UI (navbar, footer) and nested `page.tsx` for each route.  
- Route groups (e.g., `/(auth)/`, `/dashboard`, `/expenses`, `/submit-payment`) organize related pages.  

### Navigation
- Use Next.js `<Link>` component for client-side transitions.  
- Active link styling to highlight current page.  
- Programmatic navigation with `useRouter` (e.g., redirect after successful form submission).  

## 7. Performance Optimization

### Code Splitting and Lazy Loading
- Next.js automatically splits code by route.  
- Use `next/dynamic` to lazy load heavy components or chart libraries.  

### Asset Optimization
- Optimize images with Next.js `<Image>` component and automatic format selection.  
- Purge unused CSS classes via Tailwind’s Purge feature.  

### Caching and SSR
- Cache API responses with `revalidate` or middleware caching strategies.  
- Use server components to avoid shipping data-fetching logic to the client.  

### Other Strategies
- Pre-fetch critical data during build or on hover (`prefetch` links).  
- Minimize bundle size by eliminating unused dependencies.  

## 8. Testing and Quality Assurance

### Unit Testing
- **Jest** + **React Testing Library** for testing individual components and hooks.  
- Mock Clerk’s context for auth-based tests.  

### Integration Testing
- Combine multiple components/pages in tests to ensure flows work (e.g., login → dashboard).  

### End-to-End Testing
- **Cypress** or **Playwright** to simulate user interactions (form submission, role-based access).  

### Linting and Formatting
- **ESLint** with Next.js plugin for code consistency.  
- **Prettier** for automatic code formatting.  
- Tailwind CSS plugin for sorting classes.  

### CI/CD Integration
- Run tests and lint checks on every pull request (GitHub Actions or similar).  

## 9. Conclusion and Overall Frontend Summary

This frontend setup uses Next.js, React, and Tailwind CSS to deliver a modern, scalable, and maintainable treasury management app. By following these guidelines:
- You get a clear architecture that separates server and client logic.  
- You maintain a consistent, accessible, and responsive design powered by utility-first styling and shadcn/ui components.  
- You ensure data integrity and type safety with Drizzle ORM and Supabase.  
- You support robust authentication and role-based access control with Clerk.  
- You optimize performance through server components, code splitting, and asset optimization.  
- You guarantee quality with a full testing strategy and automated checks.

Following these principles will help your team build features quickly, maintain code quality, and deliver a reliable experience for both Treasurers and Students.