# README.md

# Jotion - Fullstack Notion Clone

![Jotion Banner](https://iamkartikey.vercel.app/project-screenshots/notion-clone-jotion.png)

**Jotion** is a full-featured, real-time collaborative note-taking application inspired by Notion. Built with a modern tech stack including Next.js 14 (App Router), React, Convex, Tailwind CSS, Clerk, and EdgeStore.

This project provides a robust foundation for a collaborative workspace, featuring real-time updates, rich text editing, hierarchical document organization, and more.

## ✨ Key Features

- **Real-time Database & Collaboration** 🔗: Powered by Convex for seamless updates across users.
- **Notion-style Editor** 📝: Rich text editing capabilities using BlockNote.
- **Light and Dark Mode** 🌓: Theme switching supported via `next-themes`.
- **Infinite Nested Documents** 🌲: Organize notes hierarchically with infinite depth.
- **Trash Can & Soft Delete** 🗑️: Archive and restore documents easily.
- **Authentication** 🔐: Secure user sign-up and login via Clerk.
- **File Uploads** ☁️: Store cover images and editor image uploads using EdgeStore.
- **File Deletion & Replacement** 🔄: Manage uploaded cover images.
- **Document Icons** 🌠: Assign emojis as icons to documents (updates in real-time).
- **Expandable & Collapsable Sidebar** ↔️: Resizable navigation panel for flexible workspace layout.
- **Full Mobile Responsiveness** 📱: Adapts to various screen sizes for a seamless experience on any device.
- **Publish Notes to the Web** 🌐: Share specific documents publicly via a unique URL.
- **Landing Page** 🛬: Attractive marketing page for unregistered users.
- **Cover Images** 🖼️: Add banner images to documents.
- **Document Recovery** 🔄📄: Restore accidentally deleted files from the trash.
- **Search Functionality** 🔍: Quickly find documents across your workspace using a command menu (⌘+K).

## 🚀 Tech Stack

- **Framework:** [Next.js](https://nextjs.org/) 14 (App Router)
- **Language:** [TypeScript](https://www.typescriptlang.org/)
- **Database & Real-time Backend:** [Convex](https://convex.dev/)
- **Authentication:** [Clerk](https://clerk.com/)
- **File Storage:** [EdgeStore](https://edgestore.dev/)
- **Rich Text Editor:** [BlockNote](https://www.blocknotejs.org/)
- **Styling:** [Tailwind CSS](https://tailwindcss.com/)
- **UI Components:** [Shadcn/ui](https://ui.shadcn.com/)
- **Client State Management:** [Zustand](https://github.com/pmndrs/zustand) (for UI state like modals, search)
- **Server State Management:** Convex Hooks (`useQuery`, `useMutation`)
- **Notifications/Toasts:** [Sonner](https://sonner.emilkowal.ski/)
- **Icons:** [Lucide React](https://lucide.dev/)
- **Hooks:** [usehooks-ts](https://usehooks-ts.com/)
- **Utilities:** `clsx`, `tailwind-merge`, `emoji-picker-react`, `react-textarea-autosize`, `react-dropzone`

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
    git clone https://github.com/awesome-kartikey/notion-clone-kartikey.git # Replace with your repo URL
    cd notion-clone-kartikey
    ```

2.  **Install dependencies:**

    ```bash
    npm install
    # or
    yarn install
    ```

3.  **Set up Environment Variables:**
    Create a `.env.local` file in the root of the project and add the following variables obtained from your service dashboards:

    ```env
    # --- Convex ---
    # Deployment used by `npx convex dev` (usually looks like dev:xxxxx)
    # Get from Convex dashboard -> Project Settings -> API Keys
    CONVEX_DEPLOYMENT=
    # Your Convex project URL
    # Get from Convex dashboard -> Project Settings -> API Keys
    NEXT_PUBLIC_CONVEX_URL=

    # --- Clerk ---
    # Get these from your Clerk application dashboard -> API Keys
    NEXT_PUBLIC_CLERK_PUBLISHABLE_KEY=
    CLERK_SECRET_KEY=

    # --- EdgeStore ---
    # Get these from your EdgeStore project dashboard -> API Keys
    EDGE_STORE_ACCESS_KEY=
    EDGE_STORE_SECRET_KEY=
    ```

    **Important Clerk Configuration:**

    - Go to your Clerk Dashboard.
    - Navigate to **JWT Templates**.
    - Create a new template based on the **Convex** preset.
    - Go to **API Keys**.
    - Under **Advanced** -> **Allowed Origins**, add your development URL (`http://localhost:3000`) and your future production URL.
    - Under **Advanced** -> **URL Whitelist**, add the path for Clerk webhooks if needed (not strictly required for this setup but good practice).

4.  **Set up Convex Backend:**
    Run the following command in your terminal. This command links your local project to your Convex deployment, pushes your schema (`convex/schema.ts`) and functions (`convex/*.ts`), and starts a local development server that proxies requests to your Convex backend. Keep this running in a separate terminal during development.

    ```bash
    npx convex dev
    ```

    - Follow the CLI prompts to log in to your Convex account and select the project/deployment if it's your first time running it for this project.

5.  **Run the Development Server:**

    ```bash
    npm run dev
    # or
    yarn dev
    ```

    Open [http://localhost:3000](http://localhost:3000) in your browser to see the application.

## 💻 Usage

1.  **Sign Up / Log In:** Use the Clerk authentication forms accessible from the landing page.
2.  **Create a Note:** Once logged in, click the "New page" button or the "+" icon in the sidebar.
3.  **Edit Content:** Click into the main editor area to start typing. Use BlockNote's slash commands (`/`) or toolbar for formatting.
4.  **Organize Notes:** Drag and drop notes in the sidebar to nest them under other notes.
5.  **Add Icons/Covers:** Hover over the top of a note page to reveal buttons for adding an emoji icon or a cover image.
6.  **Publish:** Open the "Publish" menu in the top-right navbar of a note to make it publicly accessible.
7.  **Search:** Press `⌘+K` (or `Ctrl+K`) to open the search command menu and quickly find notes.
8.  **Trash:** Delete notes using the context menu in the sidebar or the top navbar menu. Access deleted notes via the "Trash" popover in the sidebar to restore or permanently delete them.

## 📜 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 🙏 Acknowledgements

- Inspired by the comprehensive tutorial by [Antonio Erdeljac](https://github.com/AntonioErdeljac) ([YouTube Tutorial](https://www.youtube.com/watch?v=ZbX4Ok9YX94)).
- Thanks to the teams behind Convex, Clerk, EdgeStore, Next.js, BlockNote, Shadcn/ui, and Tailwind CSS for their fantastic tools and libraries.
