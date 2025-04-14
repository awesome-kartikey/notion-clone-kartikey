# Jotion - Fullstack Notion Clone

![Jotion Banner](https://github.com/AntonioErdeljac/notion-clone-tutorial/assets/23248726/66bcfca3-93bf-4aa4-950d-f98c020e1156)

**Jotion** is a full-featured, real-time collaborative note-taking application inspired by Notion. Built with a modern tech stack including Next.js 13 (App Router), React, Convex, Tailwind CSS, Clerk, and EdgeStore.

This project is based on the fantastic tutorial by Antonio Erdeljac:
[Fullstack Notion Clone: Next.js 13, React, Convex, Tailwind | Full Course 2023](https://www.youtube.com/watch?v=ZbX4Ok9YX94)

## ✨ Key Features

- **Real-time Database & Collaboration** 🔗: Powered by Convex for seamless updates across users.
- **Notion-style Editor** 📝: Rich text editing capabilities using BlockNote.
- **Light and Dark Mode** 🌓: Theme switching supported via `next-themes`.
- **Infinite Nested Documents** 🌲: Organize notes hierarchically.
- **Trash Can & Soft Delete** 🗑️: Archive and restore documents.
- **Authentication** 🔐: Secure user sign-up and login via Clerk.
- **File Uploads** ☁️: Store cover images and potentially other files using EdgeStore.
- **File Deletion & Replacement** 🔄: Manage uploaded assets.
- **Document Icons** 🌠: Assign emojis as icons to documents (updates in real-time).
- **Expandable Sidebar** ➡️🔀⬅️: Resizable navigation panel.
- **Fully Collapsable Sidebar** ↕️: Maximize workspace focus.
- **Full Mobile Responsiveness** 📱: Adapts to various screen sizes.
- **Publish Notes to the Web** 🌐: Share specific documents publicly.
- **Landing Page** 🛬: Attractive marketing page for unregistered users.
- **Cover Images** 🖼️: Add banner images to documents.
- **Document Recovery** 🔄📄: Restore accidentally deleted files from the trash.
- **Search Functionality** 🔍: Quickly find documents across your workspace.

## 🚀 Tech Stack

- **Framework:** Next.js 13 (App Router)
- **Language:** TypeScript
- **Database & Real-time Backend:** Convex
- **Authentication:** Clerk
- **File Storage:** EdgeStore
- **Editor:** BlockNote
- **Styling:** Tailwind CSS
- **UI Components:** Shadcn/ui
- **State Management:** Zustand (for UI state like modals, search), Convex (for data fetching/mutation state)
- **Notifications:** Sonner
- **Utilities:** `usehooks-ts`, `clsx`, `tailwind-merge`, `lucide-react`

## 📁 Project Structure

└── awesome-kartikey-notion-clone-kartikey/
├── README.md # This file
├── next.config.js # Next.js configuration (e.g., image domains)
├── package.json # Project dependencies and scripts
├── tailwind.config.ts # Tailwind CSS configuration
├── tsconfig.json # TypeScript configuration
├── app/ # Next.js 13 App Router directory
│ ├── globals.css # Global styles
│ ├── layout.tsx # Root layout (providers, theme)
│ ├── (main)/ # Routes and components for authenticated users
│ │ ├── layout.tsx # Main layout (sidebar, main content area)
│ │ ├── (routes)/ # Specific page routes
│ │ │ └── documents/ # Document related pages
│ │ │ ├── page.tsx # Default page when logged in
│ │ │ └── [documentId]/ # Dynamic route for viewing/editing a document
│ │ │ └── page.tsx
│ │ └── \_components/ # Reusable components specific to the (main) section
│ ├── (marketing)/ # Routes and components for the public landing page
│ │ ├── layout.tsx # Marketing layout
│ │ ├── page.tsx # Landing page content
│ │ └── \_components/ # Components specific to the marketing section
│ ├── (public)/ # Routes for publicly accessible content
│ │ ├── layout.tsx # Public layout
│ │ └── (routes)/
│ │ └── preview/ # Public preview route for published documents
│ │ └── [documentId]/
│ │ └── page.tsx
│ └── api/ # API routes
│ └── edgestore/ # EdgeStore API handler
│ └── [...edgestore]/
│ └── route.ts
├── components/ # Globally shared UI components
│ ├── cover.tsx # Document cover image component
│ ├── editor.tsx # BlockNote editor integration
│ ├── icon-picker.tsx # Emoji picker component
│ ├── search-command.tsx # Search modal component
│ ├── modals/ # Modal dialog components (Confirm, CoverImage, Settings)
│ ├── providers/ # Context providers (Convex, Modal, Theme)
│ └── ui/ # Base UI components (from Shadcn/ui - Button, Input, etc.)
├── convex/ # Convex backend functions and schema
│ ├── auth.config.js # Convex authentication configuration (links to Clerk)
│ ├── documents.ts # Queries and Mutations for documents
│ ├── schema.ts # Database schema definition
│ └── \_generated/ # Auto-generated Convex types and API helpers
├── hooks/ # Custom React hooks
│ ├── use-cover-image.tsx # Zustand store for cover image modal
│ ├── use-origin.tsx # Hook to get the window origin safely
│ ├── use-scroll-top.tsx # Hook to detect page scroll
│ ├── use-search.tsx # Zustand store for search modal
│ └── use-settings.tsx # Zustand store for settings modal
├── lib/ # Library utilities
│ ├── edgestore.ts # EdgeStore client setup
│ └── utils.ts # Utility functions (e.g., cn for class names)
└── public/ # Static assets (images, fonts, etc.)

## 🔧 Getting Started

### Prerequisites

- **Node.js:** Version 18.x.x or higher
- **npm** or **yarn**
- **Convex Account:** Sign up at [convex.dev](https://convex.dev)
- **Clerk Account:** Sign up at [clerk.com](https://clerk.com)
- **EdgeStore Account:** Sign up at [edgestore.dev](https://edgestore.dev)

### Installation & Setup

1.  **Clone the repository:**

    ```bash
    git clone <repository-url> # Replace with the actual URL if different
    cd awesome-kartikey-notion-clone-kartikey
    ```

2.  **Install dependencies:**

    ```bash
    npm install
    # or
    yarn install
    ```

3.  **Set up Environment Variables:**
    Create a `.env.local` file in the root of the project and add the following variables:

    ```env
    # --- Convex ---
    # Get these from your Convex project dashboard (Project Settings -> API Keys)
    # Deployment used by `npx convex dev` (usually looks like dev:xxxxx)
    CONVEX_DEPLOYMENT=
    # Your Convex project URL (Project Settings -> API Keys)
    NEXT_PUBLIC_CONVEX_URL=

    # --- Clerk ---
    # Get these from your Clerk application dashboard (API Keys)
    NEXT_PUBLIC_CLERK_PUBLISHABLE_KEY=
    CLERK_SECRET_KEY=

    # --- EdgeStore ---
    # Get these from your EdgeStore project dashboard (API Keys)
    EDGE_STORE_ACCESS_KEY=
    EDGE_STORE_SECRET_KEY=
    ```

    - **Important Clerk Setup:** In your Clerk dashboard, ensure you configure:
      - **JWT Templates:** Create a Convex template.
      - **Convex Integration:** Set the `NEXT_PUBLIC_CONVEX_URL` in the Clerk instance settings.
      - **Allowed Origins:** Add your development URL (e.g., `http://localhost:3000`).

4.  **Set up Convex Backend:**
    Run the following command in your terminal. This will sync your `convex/schema.ts` and functions, and start a local development connection to your Convex deployment. Keep this running in a separate terminal while developing.

    ```bash
    npx convex dev
    ```

    - Follow the prompts to link your project to your Convex account and deployment if it's the first time.

5.  **Run the Development Server:**
    ```bash
    npm run dev
    # or
    yarn dev
    ```
    Open [http://localhost:3000](http://localhost:3000) with your browser to see the result.

## 📜 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 🙏 Acknowledgements

- **Antonio Erdeljac:** For the incredibly detailed and helpful [YouTube tutorial](https://www.youtube.com/watch?v=ZbX4Ok9YX94) and the original [repository](https://github.com/AntonioErdeljac/notion-clone-tutorial).
- The teams behind Convex, Clerk, EdgeStore, Next.js, BlockNote, Shadcn/ui, and Tailwind CSS.
