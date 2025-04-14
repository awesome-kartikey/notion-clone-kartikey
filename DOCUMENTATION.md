## Jotion: Technical Documentation

### 1. Overview

Jotion is a full-stack, real-time collaborative document management application engineered as a functional equivalent to Notion. It utilizes a modern technology stack designed for performance, scalability, and developer experience.

**Core Technologies:**

- **Frontend Framework:** Next.js 13 (App Router) with React & TypeScript
- **Backend & Database:** Convex (Real-time Database & Serverless Functions)
- **Authentication:** Clerk (User Management & Authentication Provider)
- **File Storage:** EdgeStore (Managed Object Storage for Assets)
- **Rich Text Editor:** BlockNote (Block-based Editor Component)
- **UI:** Tailwind CSS with Shadcn/ui (Component Library)
- **State Management:** Zustand (Client-side UI state), Convex (Server state & data fetching)

This document provides a technical deep dive into the application's architecture, core features implementation, data model, and development guidelines.

### 2. System Architecture

Jotion employs a client-server architecture with a real-time backend.

- **Frontend (Client):** A Next.js application responsible for UI rendering, user interaction, and client-side state management. It communicates directly with Convex for data operations and Clerk for authentication.
- **Backend (Convex):** A serverless platform providing:
  - **Real-time Database:** Stores application data (documents, user associations).
  - **Serverless Functions:** Hosts backend logic (queries and mutations) written in TypeScript.
  - **Authentication Integration:** Validates user identity via Clerk JWTs.
- **Authentication Provider (Clerk):** Manages user sign-up, sign-in, session handling, and provides JWTs to authenticate requests to the Convex backend.
- **File Storage (EdgeStore):** A dedicated service for storing binary assets (e.g., cover images) referenced by URLs within the Convex database.

**Data Flow (Real-time):**

1.  The Next.js client subscribes to Convex queries using the `useQuery` hook.
2.  When data is modified in the Convex database (via a mutation triggered by any client), Convex automatically pushes updates to all subscribed clients.
3.  The `useQuery` hook receives the updated data, triggering a re-render in the relevant React components with minimal developer intervention.

### 3. Core Features & Implementation Details

#### 3.1 Real-time Document Collaboration

- **Mechanism:** Leverages Convex's real-time database subscriptions.
- **Implementation:** Client components use `useQuery` (e.g., `useQuery(api.documents.getById, { documentId })`) to fetch and subscribe to document data. Mutations (`api.documents.update`) modify the data, and Convex propagates changes automatically to all subscribed clients.

#### 3.2 Rich Text Editor (BlockNote)

- **Component:** `components/editor.tsx` (dynamically imported to optimize bundle size).
- **State Management:** `useBlockNote` hook manages editor state.
- **Persistence:** On content change (`onEditorContentChange`), the editor state (as `PartialBlock[]`) is serialized to a JSON string and saved to the `content` field of the corresponding document via the `api.documents.update` mutation.
- **File Uploads:** The `uploadFile` handler within `useBlockNote` utilizes `useEdgeStore` to upload files directly to EdgeStore, returning the URL for embedding within the document content.

#### 3.3 Document Hierarchy & Navigation

- **Data Structure:** The `documents` table includes an optional `parentDocument` field (type `Id<"documents">`).
- **Implementation:**
  - The `Navigation` component (`app/(main)/_components/navigation.tsx`) manages the sidebar structure.
  - The `DocumentList` component recursively fetches child documents using `useQuery(api.documents.getSidebar, { parentDocument: id })`.
  - The `Item` component renders individual documents, handling expansion state (`useState`) and triggering navigation (`useRouter`).

#### 3.4 Archival and Recovery (Trash)

- **Mechanism:** Soft delete pattern using the `isArchived` boolean flag in the `documents` table.
- **Implementation:**
  - `api.documents.archive`: Sets `isArchived: true` recursively for the document and its descendants.
  - `api.documents.getTrash`: Queries documents where `userId` matches and `isArchived` is true.
  - `api.documents.restore`: Sets `isArchived: false` recursively. Includes logic to reset `parentDocument` if the original parent remains archived.
  - `api.documents.remove`: Performs a hard delete from the database.
  - `TrashBox` component (`app/(main)/_components/trash-box.tsx`) provides the UI for viewing, restoring, or permanently deleting archived items.

#### 3.5 Document Publishing

- **Mechanism:** Controlled by the `isPublished` boolean flag in the `documents` table.
- **Implementation:**
  - `Publish` component (`app/(main)/_components/publish.tsx`) allows toggling the `isPublished` flag via `api.documents.update`.
  - Public route `app/(public)/(routes)/preview/[documentId]/page.tsx` renders published documents.
  - `api.documents.getById` query contains authorization logic: returns the document if `isPublished: true` and `isArchived: false`, otherwise verifies authenticated user ownership.

#### 3.6 Search Functionality

- **Component:** `SearchCommand` (`components/search-command.tsx`) utilizes `cmdk` library for a command palette interface.
- **Implementation:**
  - `useSearch` (Zustand store) manages modal visibility.
  - Fetches all non-archived documents for the current user via `useQuery(api.documents.getSearch)`.
  - Filters results client-side based on user input.

#### 3.7 Cover Images & Icons

- **Storage:** Cover image URLs (from EdgeStore) and icon emojis (strings) are stored directly in the `documents` table (`coverImage`, `icon` fields).
- **Implementation:**
  - `Cover` component (`components/cover.tsx`) displays the image and provides options (via `CoverImageModal`) to change/remove it.
  - `Toolbar` component (`components/toolbar.tsx`) uses `IconPicker` to select/remove icons.
  - Mutations `api.documents.update`, `api.documents.removeCoverImage`, `api.documents.removeIcon` handle database updates.
  - `CoverImageModal` (`components/modals/cover-image-modal.tsx`) orchestrates the EdgeStore upload/replacement process via `useEdgeStore`.

#### 3.8 Theming (Dark/Light Mode)

- **Library:** `next-themes`.
- **Implementation:**
  - `ThemeProvider` (`components/providers/theme-provider.tsx`) wraps the root layout.
  - `ModeToggle` component (`components/mode-toggle.tsx`) provides UI for switching themes.
  - Tailwind CSS dark mode variant (`dark:`) is used extensively for styling. Configuration in `tailwind.config.ts`.

### 4. Codebase Structure

- **`app/`:** Next.js App Router implementation.
  - **Route Groups (`(groupName)`):** Organize routes by section (marketing, main authenticated app, public) and apply specific layouts without affecting URL paths.
  - **Layouts (`layout.tsx`):** Define shared UI structure for route segments. Root layout initializes providers.
  - **Pages (`page.tsx`):** Define UI unique to a specific route.
  - **`_components`:** Convention for co-locating components specific to a route group/segment.
  - **`api/`:** Server-side API route handlers (e.g., EdgeStore endpoint).
- **`components/`:** Shared UI elements across the application.
  - **`ui/`:** Base components sourced from Shadcn/ui (Button, Input, Dialog, etc.).
  - **`providers/`:** Application-wide context providers (Convex, Theme, Modal, EdgeStore).
  - **`modals/`:** Dialog components for specific workflows (Settings, Cover Image, Confirmation).
  - **Higher-level Components:** `Editor`, `Toolbar`, `Cover`, `SearchCommand`, etc.
- **`convex/`:** Backend logic and database schema.
  - **`schema.ts`:** Defines database tables, fields, types, and indexes using Convex's API. The source of truth for the data model.
  - **`documents.ts`:** Contains all queries and mutations related to document operations. Includes authentication and authorization logic.
  - **`auth.config.js`:** Configures the Clerk authentication provider for Convex.
  - **`_generated/`:** Auto-generated files by Convex containing typesafe API clients and server utilities based on the schema and functions. **Do not edit directly.**
- **`hooks/`:** Custom React hooks encapsulating reusable logic or state.
  - **Zustand Stores (`useSearch`, `useSettings`, `useCoverImage`):** Manage global client-side UI state.
  - **Utility Hooks (`useOrigin`, `useScrollTop`):** Provide browser-specific functionality safely.
- **`lib/`:** Utility functions and configurations.
  - **`utils.ts`:** General utility functions (e.g., `cn` for merging Tailwind classes).
  - **`edgestore.ts`:** Initializes and exports the typesafe EdgeStore client hook (`useEdgeStore`).

### 5. Data Model (`convex/schema.ts`)

The primary table is `documents`:

```typescript
// convex/schema.ts excerpt
defineTable({
  title: v.string(), // Document title
  userId: v.string(), // Identifier of the owner user (from Clerk)
  isArchived: v.boolean(), // Flag for soft deletion (true if in trash)
  parentDocument: v.optional(v.id("documents")), // ID of the parent document for hierarchy
  content: v.optional(v.string()), // Serialized BlockNote editor content (JSON string)
  coverImage: v.optional(v.string()), // URL of the cover image (from EdgeStore)
  icon: v.optional(v.string()), // Emoji icon string
  isPublished: v.boolean(), // Flag for public accessibility (true if published)
})
  .index("by_user", ["userId"]) // Index for querying documents by user
  .index("by_user_parent", ["userId", "parentDocument"]); // Index for querying children of a parent for a specific user
```

### 6. Authentication & Authorization

- **Flow:**
  1.  User interacts with Clerk UI (`SignInButton`, etc.) on the Next.js frontend.
  2.  Clerk authenticates the user and issues a JWT.
  3.  `ConvexProviderWithClerk` makes the JWT available to Convex hooks/requests.
  4.  Convex backend functions (`query`, `mutation`) use `ctx.auth.getUserIdentity()` to validate the JWT against the configured Clerk issuer (`convex/auth.config.js`).
  5.  If valid, `getUserIdentity()` returns user information (including `subject`, used as `userId`).
- **Authorization:** Convex functions typically perform checks:
  - Verify `identity` exists (user is authenticated).
  - Compare `identity.subject` (current user's ID) with the `userId` field on the document being accessed/modified.

### 7. State Management Strategy

- **Server State (Convex):** All core application data (documents, hierarchy, user associations) is managed by Convex. The `useQuery` hook provides reactive data fetching and subscriptions, acting as the primary server state management mechanism. `useMutation` handles state modifications.
- **Client UI State (Zustand):** Used for ephemeral, global UI state not persisted in the database, such as the open/closed status of modals (`useSearch`, `useSettings`, `useCoverImage`) and related temporary data (e.g., URL for cover image replacement).

### 8. Setup and Deployment

#### 8.1 Prerequisites

- Node.js >= 18.x.x
- npm or yarn
- Accounts: Convex, Clerk, EdgeStore

#### 8.2 Environment Configuration

- Create `.env.local` in the project root.
- Populate with keys/URLs from Convex, Clerk, and EdgeStore dashboards as specified in `README.md`.
- **Critical:** Configure Clerk JWT Template for Convex and set `NEXT_PUBLIC_CONVEX_URL`. Add `http://localhost:3000` (and deployment URLs) to Clerk's Allowed Origins.

#### 8.3 Local Development

1.  `npm install` or `yarn install`
2.  Run `npx convex dev` in a separate terminal (links project, syncs schema/functions, provides local proxy).
3.  Run `npm run dev` or `yarn dev` for the Next.js application.
4.  Access at `http://localhost:3000`.

#### 8.4 Deployment Strategy

- **Frontend (Next.js):** Deploy to Vercel (recommended). Configure all environment variables from `.env.local` in Vercel project settings. Add the Vercel deployment URL to Clerk Allowed Origins.
- **Backend (Convex):** Promote the development deployment to production using `npx convex deploy`. Update `CONVEX_DEPLOYMENT` and `NEXT_PUBLIC_CONVEX_URL` environment variables in Vercel to point to the _production_ Convex deployment URL.
- **EdgeStore:** Ensure production API keys are configured in the Vercel environment variables.

### 9. Development Guide

#### 9.1 Extending the Schema

1.  Modify `convex/schema.ts` to add/change fields or tables. Define necessary types using `v.*`.
2.  Add relevant indexes if needed for query performance.
3.  Run `npx convex dev` to apply schema changes and regenerate types (`convex/_generated/`).
4.  Update relevant Convex functions (`query`, `mutation`) in `convex/*.ts` to utilize the new schema elements.
5.  Update frontend components (`app/`, `components/`) to interact with the new data structure.

#### 9.2 Adding New Features

1.  Define necessary data model changes in `convex/schema.ts`.
2.  Implement backend logic (queries, mutations) in `convex/*.ts`, including authentication/authorization.
3.  Develop frontend components in `app/` or `components/`, using `useQuery` and `useMutation` to interact with the Convex backend.
4.  Manage any necessary client-side UI state using `useState`, `useReducer`, or Zustand for global state.
5.  Ensure appropriate routing is set up within the `app/` directory.

### 10. Troubleshooting Common Issues

- **Authentication Failure:** Verify Clerk JWT Template (Convex), Clerk `NEXT_PUBLIC_CONVEX_URL` setting, Allowed Origins, and accuracy of `.env.local` / deployment environment variables. Check Convex function logs for auth errors.
- **Convex Function Errors:** Inspect logs via `npx convex logs` or the Convex dashboard. Ensure function arguments match client calls and schema definitions are synced (`npx convex dev`).
- **Data Not Updating (Real-time):** Confirm `npx convex dev` is running. Ensure components use `useQuery`. Check network connection and Convex dashboard for connectivity status. Verify mutation logic is correct.
- **File Upload/Display Issues:** Validate EdgeStore API keys. Check the network tab in browser developer tools for failed upload requests. Ensure `next.config.js` includes `files.edgestore.dev` in image domains. Verify URLs stored in Convex are correct.

---
