# FAQ.md

# Frequently Asked Questions (FAQ)

## General

**Q: What is Jotion?**
A: Jotion is a full-stack web application designed as a clone of the popular productivity tool, Notion. It allows users to create, organize, and collaborate on notes and documents in real-time, featuring a rich text editor, hierarchical organization, file uploads, publishing, and more.

**Q: What technologies is Jotion built with?**
A: Jotion uses a modern tech stack:
_ **Frontend:** Next.js 14 (App Router), React, TypeScript
_ **Backend & Database:** Convex
_ **Authentication:** Clerk
_ **File Storage:** EdgeStore
_ **UI:** Tailwind CSS, Shadcn/ui
_ **Editor:** BlockNote \* **State Management:** Zustand (UI), Convex (Server)

**Q: Is Jotion free to use?**
A: The Jotion codebase itself is open-source under the MIT license. However, running your own instance requires accounts with Convex, Clerk, and EdgeStore, which have their own free and paid tiers depending on usage.

**Q: How does Jotion compare to the real Notion?**
A: Jotion implements many core features of Notion, such as the block-based editor, nested documents, real-time collaboration hints (via Convex), publishing, and dark mode. However, Notion is a mature product with a vastly larger feature set (databases, templates, integrations, etc.). Jotion serves as an excellent example of building a complex, real-time application with modern tools.

## Setup & Development

**Q: What environment variables do I need?**
A: You need to create a `.env.local` file in the project root with the following variables:
_ `CONVEX_DEPLOYMENT`: Your Convex deployment identifier (e.g., `dev:xxxxx`).
_ `NEXT_PUBLIC_CONVEX_URL`: Your Convex project URL.
_ `NEXT_PUBLIC_CLERK_PUBLISHABLE_KEY`: Your Clerk frontend API key.
_ `CLERK_SECRET_KEY`: Your Clerk backend API key.
_ `EDGE_STORE_ACCESS_KEY`: Your EdgeStore access key.
_ `EDGE_STORE_SECRET_KEY`: Your EdgeStore secret key.
See the README for instructions on where to find these keys in each service's dashboard.

**Q: How do I set up the Convex backend?**
A: 1. Sign up for a Convex account. 2. Create a new project in the Convex dashboard. 3. Install the Convex CLI if you haven't (`npm install -g convex`). 4. Run `npx convex dev` in your project's root directory. 5. Follow the prompts to link your local project to your Convex deployment. This command syncs your schema and functions (`convex/`) and provides a local proxy for development. Keep it running in a separate terminal.

**Q: How do I configure Clerk?**
A: 1. Sign up for a Clerk account and create an application. 2. Obtain your Publishable Key and Secret Key. 3. In the Clerk dashboard, navigate to **JWT Templates**. 4. Create a new template using the **Convex** preset. This ensures Clerk JWTs are compatible with Convex authentication. 5. Go to **API Keys** -> **Advanced** -> **Allowed Origins** and add `http://localhost:3000` (and your production URL later).

**Q: Where is the backend code located?**
A: All backend logic (database schema, queries, mutations) resides within the `convex/` directory.
_ `schema.ts`: Defines the database tables and fields.
_ `documents.ts`: Contains query and mutation functions related to document operations. \* `auth.config.js`: Configures Convex to use Clerk for authentication.

**Q: How is real-time collaboration implemented?**
A: Real-time functionality is primarily handled by Convex.
_ Frontend components use the `useQuery` hook from `convex/react` to subscribe to data.
_ When any client triggers a mutation (using `useMutation`) that modifies data relevant to a query, Convex automatically pushes the updated data to all clients subscribed to that query. \* React components automatically re-render with the new data provided by `useQuery`.

**Q: Why was Convex chosen for the backend?**
A: Convex offers several advantages for this type of application:
_ **Real-time by Default:** Built-in WebSocket subscriptions make real-time features easy to implement.
_ **Serverless:** No need to manage servers; scales automatically.
_ **Integrated Database and Functions:** Simplifies the backend architecture.
_ **TypeScript End-to-End:** Provides type safety from the database schema through backend functions to the frontend client. \* **Strong Consistency:** Ensures data integrity.

## Usage & Features

**Q: How do I create nested documents (sub-pages)?**
A: You can create nested documents in two ways: 1. **Sidebar:** Click the "+" icon that appears when hovering over a document item in the sidebar. The new document will be created as a child of that item. 2. **Dragging:** Drag and drop documents in the sidebar to reorder or nest them under others.

**Q: How does publishing work?**
A: Each document has an `isPublished` flag (defaulting to `false`). 1. In the document view, click the "Publish" button in the top navbar. 2. Confirm publishing in the popover. This sets the `isPublished` flag to `true` via a Convex mutation. 3. A public URL (`/preview/[documentId]`) becomes accessible. This route fetches the document but only renders it if `isPublished` is true and `isArchived` is false. Authentication is not required to view published documents.

**Q: Can I recover notes I accidentally deleted?**
A: Yes. Deleting a note initially moves it to the "Trash" (soft delete) by setting its `isArchived` flag to `true`. 1. Click the "Trash" item in the sidebar to open the Trash Box popover. 2. Find the note you want to recover. 3. Click the "Restore" (undo) icon next to the note title. This sets `isArchived` back to `false`. 4. Alternatively, you can permanently delete notes from the Trash Box.

**Q: How does the search functionality (⌘+K) work?**
A: 1. The `SearchCommand` component (`components/search-command.tsx`) uses the `cmdk` library. 2. It fetches all non-archived documents for the logged-in user using the `api.documents.getSearch` Convex query. 3. As the user types in the command input, it filters the fetched documents client-side based on the title. 4. Selecting a document navigates the user to that document's page.

## Troubleshooting

**Q: I'm getting authentication errors or cannot log in.**
A: Check the following:
_ **Environment Variables:** Ensure `NEXT_PUBLIC_CLERK_PUBLISHABLE_KEY` and `CLERK_SECRET_KEY` are correctly set in your `.env.local` file.
_ **Clerk JWT Template:** Verify you have created the Convex JWT template in your Clerk dashboard.
_ **Allowed Origins:** Make sure `http://localhost:3000` is listed in Clerk's Allowed Origins.
_ **Convex Auth Config:** Check that `convex/auth.config.js` points to the correct Clerk application domain. \* **Browser Console/Network Tab:** Look for specific error messages from Clerk or Convex.

**Q: My changes are not updating in real-time for other users (or browser tabs).**
A: Check these points:
_ **`npx convex dev`:** Ensure the Convex development process is running in a separate terminal.
_ **`useQuery`:** Verify that the components displaying the data are using the `useQuery` hook to subscribe to changes.
_ **Mutations:** Confirm that data modifications are being performed using `useMutation`.
_ **Network Connection:** Ensure a stable internet connection. \* **Convex Dashboard:** Check the Convex dashboard for any deployment issues or function errors.

**Q: Cover images or images in the editor are not loading/uploading.**
A:
_ **EdgeStore Keys:** Double-check `EDGE_STORE_ACCESS_KEY` and `EDGE_STORE_SECRET_KEY` in `.env.local`.
_ **EdgeStore API Route:** Ensure the API route at `app/api/edgestore/[...edgestore]/route.ts` is correctly set up.
_ **`next.config.js`:** Verify that `files.edgestore.dev` (or your EdgeStore domain) is included in the `images.domains` array in `next.config.js`.
_ **Browser Console/Network Tab:** Check for errors during the file upload request to EdgeStore.
