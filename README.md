# College AI Assistant (YMN AI CHATBOT)

An intelligent, PWA-ready web application designed to serve as a smart assistant for college students, faculty, and visitors. It provides instant answers to queries about courses, admissions, fees, and campus life using a locally stored knowledge base and the powerful Groq API.

## 🚀 How It Works

1.  **Knowledge Base**: The app pre-loads information from text files located in the `clg_info/` directory. A manifest file (`clg_info/files.json`) lists all the knowledge base filenames — the app reads this manifest at startup and automatically fetches every file listed in it.
2.  **Local Storage**: Extracted text from these documents is stored locally in your browser using **IndexedDB**. This ensures fast access and privacy.
3.  **Semantic Search**: When you ask a question, the app performs a keyword-based search (with synonym expansion) across the local documents to find the most relevant context.
4.  **Web Scraping (Fallback)**: If the information isn't found locally, the app can optionally scrape the configured college website for real-time information.
5.  **AI Processing**: The gathered context is sent to the **Groq API** (using models like Llama 3) to generate a professional, helpful, and accurate response.
6.  **PWA Support**: The app can be installed on your mobile or desktop device for a native-like experience.
7.  **Secret Config File**: The app loads default API keys and settings from `config/default_config.json` at startup, so end users can use the chatbot without needing admin access.

## 🏁 How to Start

### Option A: Quick Start (Using Secret Config File — Recommended)

1.  **Set up the secret config**: Copy `config/default_config.template.json` to `config/default_config.json`.
2.  **Fill in your details**: Open `default_config.json` and add your Groq API key. Example:
    ```json
    {
        "admin_password_reference": "get_admin_password",
        "admin_hash": "aff64f01a5484076e06d72c255498dd7da423b8c7485b4e113e11e31700975cf",
        "groq_api_key": "gsk_your_actual_api_key_here",
        "groq_model_id": "llama-3.3-70b-versatile",
        "college_website_url": "https://ymnnanded.in/"
    }
    ```
3.  **Host the app**: Deploy on GitHub Pages, Vercel, or use VS Code Live Server.
4.  **Start Chatting**: Open the app — it works immediately! No admin login needed for end users.

### Option B: Manual Setup (Via Admin Panel)

1.  **Login to Admin Panel**: Open the app and click the **Gear Icon** (top right) or go to `your-url.com/#admin`. Enter the admin password (default is `get_admin_password`).
2.  **Provide Groq API Key**: In the Admin Dashboard, paste your **Groq API Key** into the required field and click **"Save Configuration"**.
3.  **Add Knowledge Base**: Upload your college's text, PDF, or DOCX files in the **"Knowledge Base"** section. The app will automatically process and store them locally.
4.  **Start Chatting**: Go back to the chat view and start asking questions!

## 🔐 Secret Config File (`config/default_config.json`)

This file stores default configuration that the app auto-loads at startup:

| Field                      | Purpose                                                                            |
| -------------------------- | ---------------------------------------------------------------------------------- |
| `admin_password_reference` | Your admin password in plain text (for YOUR reference only — app never reads this) |
| `admin_hash`               | SHA-256 hash of the admin password (used for login verification)                   |
| `groq_api_key`             | Default Groq API key (auto-loaded so users don't need admin access)                |
| `groq_model_id`            | Default LLM model ID (default: `llama-3.3-70b-versatile`)                          |
| `college_website_url`      | Fallback website URL for web scraping                                              |

**How it works:**

-   On app startup, `index.html` fetches `config/default_config.json` via HTTP.
-   The admin hash is loaded from this file (users still need to enter the password to access admin panel).
-   API key, model ID, and website URL are seeded into IndexedDB as defaults (only if not already set by admin).
-   If the secret file is missing or fails to load, the app falls back to built-in defaults.
-   Admin panel overrides always take priority over secret file defaults.

**⚠️ IMPORTANT**: This file is listed in `.gitignore` so it is NOT pushed to public GitHub repos. Only the template file (`default_config.template.json`) is committed.

## 🔑 How to Generate a Groq API Key

To use this assistant, you need a Groq API key. Follow these steps:

1.  **Go to the Groq Console**: Visit [https://console.groq.com/](https://console.groq.com/).
2.  **Sign Up / Log In**: Create an account or sign in using your existing credentials.
3.  **Navigate to API Keys**: On the left sidebar, click on the **"API Keys"** section.
4.  **Create a New Key**:
    -   Click the **"Create API Key"** button.
    -   Give your key a name (e.g., "CollegeGPT").
    -   **Copy the key immediately!** You won't be able to see it again for security reasons.
5.  **Configure the App**:
    -   **Method 1 (Recommended):** Paste the key into `config/default_config.json` in the `groq_api_key` field.
    -   **Method 2:** Open the Admin Panel, enter password `get_admin_password`, paste the key, and click "Save Configuration".

## 💳 How to Upgrade Groq Billing Plan

To use models without usage limits (and avoid rate limits), follow these steps:

1.  **Go to Billing Plans**: Visit [https://console.groq.com/settings/billing/plans](https://console.groq.com/settings/billing/plans).
2.  **Upgrade Plan**: Under the **"Developer plan"**, click **"Upgrade"**.
3.  **Enter Details**: Add your **Name and Address** and click **"Continue to payment"**.
4.  **Add Payment Method**: Select **"Card"** and add your **Card Number, Expiration Date, and CVC**.
5.  **Save Info**: Click **"Save my info"** and provide your **Contact Number and Name** again.
6.  **Confirm**: Click **"Upgrade"** to complete the process.

## 🌟 Why This is Useful

-   **Instant Information**: No more digging through complex websites or PDF handbooks. Get answers to "How much is the B.Tech fee?" or "Who is the HOD of Computer Science?" in seconds.
-   **24/7 Availability**: The assistant is always online to help prospective students and parents, even outside college office hours.
-   **User-Friendly**: Provides interactive suggestion bubbles to help users discover what they can ask.
-   **Privacy-Focused**: Your documents and API keys are stored locally in your browser's database, not on a central server.
-   **Customizable**: Administrators can easily update the knowledge base by uploading new PDF, TXT, or DOCX files directly through the dashboard.

## 🛠️ Setup & Deployment

### Local Development

1.  Clone this repository.
2.  Copy `config/default_config.template.json` → `config/default_config.json`.
3.  Add your Groq API key to `default_config.json`.
4.  Open with VS Code **Live Server** (or any local HTTP server).
5.  Ensure the `clg_info/` and `config/` folders are in the same directory as `index.html`.
6.  When adding new knowledge base files, drop the `.txt` file into `clg_info/` and add the filename to `clg_info/files.json`.

### 🌐 GitHub Pages Hosting (Free & Public)

To host this chatbot publicly so anyone can access it via a URL:

1.  **Create a GitHub Repository**:

    -   Go to [https://github.com/new](https://github.com/new).
    -   Name it (e.g., `ymn-ai-chatbot`).
    -   Set visibility to **Public** (required for free GitHub Pages).
    -   Click **Create repository**.

2.  **Push your code**:

    ```bash
    cd "YMN AI ChatBot"
    git init
    git add .
    git commit -m "Initial commit"
    git branch -M main
    git remote add origin https://github.com/YOUR_USERNAME/ymn-ai-chatbot.git
    git push -u origin main
    ```

    > ⚠️ The `.gitignore` will automatically exclude `config/default_config.json` (your secrets).

3.  **Enable GitHub Pages**:

    -   Go to your repo → **Settings** → **Pages**.
    -   Under **Source**, select **Deploy from a branch**.
    -   Select **main** branch and **/ (root)** folder.
    -   Click **Save**.
    -   Wait 1-2 minutes. Your site will be live at: `https://YOUR_USERNAME.github.io/ymn-ai-chatbot/`

4.  **Add the secret config to the live site**:

    -   Since `default_config.json` is gitignored, you need to add it **manually** after deployment.
    -   Go to your repo on GitHub → Click **Add file** → **Create new file**.
    -   Name it: `config/default_config.json`
    -   Paste your config JSON with the real API key.
    -   Commit directly to `main`.
    -   ⚠️ **NOTE**: On a public repo, this file will be visible to anyone. See the "Security Note" below.

5.  **Share the link**: `https://YOUR_USERNAME.github.io/ymn-ai-chatbot/`

### 🔒 Security Note for GitHub Pages

GitHub Pages only works with **public** repositories (on free plans). This means:

-   Your `index.html`, `clg_info/` files, and knowledge base are visible to anyone — this is fine.
-   Your `config/default_config.json` (with API key) would also be visible if committed.
-   **Recommended approach**: Use a **private repo** with GitHub Pages (requires GitHub Pro/Team — $4/month), OR use **Vercel/Netlify** which support private repos for free.

**Alternative (Free & Private):**

-   Use **Vercel**: Connect your GitHub repo (can be private) → auto-deploys → free HTTPS URL.
-   Use **Netlify**: Same as Vercel — drag & drop deploy, free tier, supports private repos.
-   Both services let you set **environment variables** for API keys instead of committing them.

### Files & Read-Only

On GitHub, by default:

-   Only YOU (the repo owner) can modify files via push/commit.
-   Others can view/fork the repo but cannot edit your files directly.
-   The hosted site (`github.io`) is always **read-only** for visitors — they can only use the chatbot, not modify anything.

---

_Built with ❤️ for better campus accessibility._
