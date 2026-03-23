# YMN AI CHATBOT — Complete Project Documentation

## Table of Contents

1. [Project Overview](#1-project-overview)
2. [Technology Stack](#2-technology-stack)
3. [Project Architecture](#3-project-architecture)
4. [File Structure](#4-file-structure)
5. [Detailed Code Analysis](#5-detailed-code-analysis)
    - [HTML Structure](#51-html-structure)
    - [CSS Styling](#52-css-styling)
    - [JavaScript — Core Logic](#53-javascript--core-logic)
6. [Knowledge Base Files](#6-knowledge-base-files)
7. [Data Flow — How a User Query is Answered](#7-data-flow--how-a-user-query-is-answered)
8. [Security Features](#8-security-features)
9. [PWA (Progressive Web App) Features](#9-pwa-progressive-web-app-features)
10. [Admin Panel](#10-admin-panel)
11. [API Integration (Groq)](#11-api-integration-groq)
12. [Deployment](#12-deployment)
13. [Developers](#13-developers)

---

## 1. Project Overview

**Project Name:** YMN AI CHATBOT (College AI Assistant)
**Purpose:** An intelligent, PWA-ready, single-page web application that serves as a smart AI assistant for students, faculty, and visitors of **Yeshwant Mahavidyalaya, Nanded**. It provides instant answers to queries about courses, admissions, fees, faculty, hostel, and campus life.

**Key Highlights:**

-   Single-file architecture — the entire application lives inside one `index.html` file (~1658 lines).
-   Uses a local knowledge base stored in `clg_info/` folder (10 text files with curated college data).
-   Integrates with **Groq API** (LLM — Llama 3.3 70B) for intelligent response generation.
-   Falls back to **web scraping** of the official college website (`https://ymnnanded.in/`) if local data is insufficient.
-   Fully responsive, mobile-first design (max-width 500px, like a phone app).
-   Installable as a PWA on mobile and desktop.
-   Dark mode support.
-   Admin dashboard for API key management and document upload.
-   All data stored in the browser using **IndexedDB** — no backend server required.

**Developed by:** Sanika Kumbhkarn & Samiksha Padalwar
**College:** Yeshwant Mahavidyalaya, Nanded (Affiliated to S.R.T.M. University)
**Admin Password (Default):** `get_admin_password` (SHA-256 hashed in code)

---

## 2. Technology Stack

| Category           | Technology                            | Purpose                                    |
| ------------------ | ------------------------------------- | ------------------------------------------ |
| Frontend           | HTML5, CSS3, JavaScript (Vanilla)     | Entire UI & Logic                          |
| AI/LLM             | Groq API (Llama 3.3-70b-versatile)    | Natural language response generation       |
| Markdown Rendering | Marked.js (CDN)                       | Renders AI markdown responses into HTML    |
| PDF Parsing        | PDF.js v3.11.174 (CDN)                | Extracts text from uploaded PDF documents  |
| DOCX Parsing       | Mammoth.js v1.6.0 (CDN)               | Extracts text from uploaded DOCX documents |
| Icons              | Font Awesome v6.4.0 (CDN)             | UI icons (send, gear, moon, trash, etc.)   |
| Local Database     | IndexedDB (Browser API)               | Stores documents, API keys, and config     |
| PWA                | Service Worker (dynamically injected) | Offline capability & installability        |
| Web Scraping       | AllOrigins CORS Proxy                 | Client-side scraping of college website    |

---

## 3. Project Architecture

```
┌──────────────────────────────────────────────────────────────────┐
│                         USER (Browser)                          │
│                                                                  │
│  ┌─────────────────────────────────────────────────────────┐    │
│  │                    index.html                            │    │
│  │  ┌──────────┐  ┌───────────┐  ┌───────────────────────┐ │    │
│  │  │  Chat UI │  │ Admin UI  │  │   PWA Install Banner  │ │    │
│  │  └─────┬────┘  └─────┬─────┘  └───────────────────────┘ │    │
│  │        │              │                                   │    │
│  │  ┌─────▼──────────────▼─────────────────────────────┐    │    │
│  │  │              JavaScript Engine                    │    │    │
│  │  │  ┌─────────────┐  ┌────────────────────┐         │    │    │
│  │  │  │ Search      │  │ Document Processor │         │    │    │
│  │  │  │ Engine      │  │ (PDF/TXT/DOCX)     │         │    │    │
│  │  │  └──────┬──────┘  └────────┬───────────┘         │    │    │
│  │  │         │                  │                       │    │    │
│  │  │  ┌──────▼──────────────────▼─────┐                │    │    │
│  │  │  │       IndexedDB               │                │    │    │
│  │  │  │  ┌───────────┐ ┌───────────┐  │                │    │    │
│  │  │  │  │ documents │ │  config   │  │                │    │    │
│  │  │  │  └───────────┘ └───────────┘  │                │    │    │
│  │  │  └───────────────────────────────┘                │    │    │
│  │  └───────────────────────────────────────────────────┘    │    │
│  └─────────────────────────────────────────────────────────┘    │
│                                                                  │
└─────────────────────┬──────────────────────┬─────────────────────┘
                      │                      │
         ┌────────────▼──────┐    ┌──────────▼───────────┐
         │   Groq API        │    │  College Website     │
         │ (LLM Processing)  │    │ (Fallback Scraping)  │
         └───────────────────┘    └──────────────────────┘
```

---

## 4. File Structure

```
YMN AI ChatBot/
│
├── index.html                      # Main application file (HTML + CSS + JS — everything)
├── README.md                       # Project readme with setup instructions
├── PROJECT_DOCUMENTATION.md        # This file — complete technical documentation
├── .gitignore                      # Excludes secret config from Git
│
├── assets/
│   └── clg-logo.png                # College logo (used as watermark in chat background)
│
├── config/                         # Configuration files
│   ├── default_config.json         # SECRET — API key, admin hash, defaults (gitignored)
│   └── default_config.template.json # Template for config (committed to Git)
│
├── clg_info/                       # Knowledge Base — Pre-loaded text files
│   ├── files.json                  # Manifest listing all knowledge base filenames (auto-loaded)
│   ├── college data.txt            # College overview, departments, courses, syllabus
│   ├── common_questions.txt        # 50+ FAQ with answers (admissions, fees, exams, etc.)
│   ├── faculty_details.txt         # Department-wise faculty listing (77 total)
│   ├── fees_details.txt            # Fee structure (admission, exam, hostel, scholarships)
│   ├── final questions.txt         # 20 curated Q&A pairs for common queries
│   ├── general.txt                 # Full website crawl dump from ymnnanded.in
│   ├── imp_anouncements.txt        # Important announcements & PDF extractions
│   ├── must_details.txt            # Priority documents list & availability status
│   ├── principal_details.txt       # Principal's full bio-data & achievements
│   └── principals_desk.txt         # Principal's welcome message (verbatim)
│
└── report_docs/
    ├── A Project Report Preparation Guidelines.pdf  # Report formatting guide
    └── YMN_AI_CHATBOT_Project_Report.html           # Complete project report (HTML)
```

---

## 5. Detailed Code Analysis

### 5.1 HTML Structure

The `index.html` file is structured as follows:

#### `<head>` Section (Lines 1–627)

-   **Meta tags:** UTF-8 charset, mobile viewport with `user-scalable=no`.
-   **Title:** "College AI Assistant"
-   **CDN Libraries:**
    -   `marked.min.js` — Markdown to HTML converter for bot responses.
    -   `pdf.min.js` (PDF.js) — Client-side PDF text extraction.
    -   `mammoth.browser.min.js` — Client-side DOCX text extraction.
    -   Font Awesome 6.4.0 — Icon library.
-   **Dynamic PWA Manifest:** A `<script>` block dynamically creates a JSON manifest blob and injects it as a `<link rel="manifest">`. This avoids needing a separate `manifest.json` file.
-   **CSS Styles:** ~580 lines of embedded CSS covering the full UI.

#### `<body>` Section (Lines 628–755)

The HTML body contains a single `<div id="app">` container with:

| Element         | ID/Class           | Purpose                                                           |
| --------------- | ------------------ | ----------------------------------------------------------------- |
| Toast           | `#toast`           | Popup notification (e.g., "Configuration saved!")                 |
| Header          | `<header>`         | App title "YMN AI CHATBOT" + theme toggle + admin gear button     |
| Install Banner  | `#install-banner`  | PWA install prompt (hidden by default)                            |
| Chat View       | `#chat-view`       | Main chat interface (messages + input area)                       |
| Chat Area       | `#chat-area`       | Scrollable message container with watermark background            |
| Input Container | `#input-container` | Textarea + send button + clear/export actions + character counter |
| Admin View      | `#admin-view`      | Hidden admin panel (login + dashboard)                            |
| Admin Login     | `#admin-login`     | Password input + login button                                     |
| Admin Dashboard | `#admin-dashboard` | API config form + document upload + document list                 |

---

### 5.2 CSS Styling

#### CSS Variables (Light & Dark Mode)

```
:root { ... }        — Light mode colors
.dark-mode { ... }   — Dark mode overrides
```

| Variable        | Light Mode | Dark Mode | Purpose                  |
| --------------- | ---------- | --------- | ------------------------ |
| `--bg-body`     | `#f3f4f6`  | `#111827` | Page background          |
| `--bg-app`      | `#ffffff`  | `#1f2937` | App container background |
| `--text-main`   | `#1f2937`  | `#f9fafb` | Primary text color       |
| `--primary`     | `#4f46e5`  | `#6366f1` | Primary accent (indigo)  |
| `--bubble-user` | `#4f46e5`  | `#6366f1` | User message bubble      |
| `--bubble-bot`  | `#f3f4f6`  | `#374151` | Bot message bubble       |

#### Key UI Styling Decisions

-   **Mobile-first:** `#app` is `max-width: 500px`, centered, full-height.
-   **Chat watermark:** `#chat-area::before` uses `clg-logo.png` at `opacity: 0.03` as a subtle background.
-   **Custom scrollbar:** Thin 6px scrollbar with theme-aware colors.
-   **Animations:** `fadeIn` animation on messages, `blink` animation on loading dots.
-   **Suggestion bubbles:** Outlined buttons with primary color border, hover fills.
-   **Drag-drop upload area:** Dashed border file upload zone.

---

### 5.3 JavaScript — Core Logic

The entire application logic is in a single `<script>` block (Lines 756–1658). Below is a detailed breakdown of every function and module:

---

#### Module 1: Core Configuration & Security (Lines 764–810)

| Item                  | Details                                                                                                                                                 |
| --------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `ADMIN_HASH`          | SHA-256 hash of the admin password. Initially set to a fallback value, then overridden from `config/default_config.json` at startup.                    |
| `DEFAULT_CONFIG`      | Object holding default `groq_api_key`, `groq_model_id`, and `college_website_url`. Populated from the secret config file at startup.                    |
| `loadDefaultConfig()` | Async function that fetches `config/default_config.json` via HTTP, overrides `ADMIN_HASH`, and stores API defaults in `DEFAULT_CONFIG`. Fails silently. |
| Context menu block    | `contextmenu` event prevented on desktop (allows on mobile for paste).                                                                                  |
| DevTools block        | `F12` and `Ctrl+Shift+I` keyboard shortcuts are prevented.                                                                                              |
| `sha256(message)`     | Async function using `crypto.subtle.digest('SHA-256', ...)` to hash the admin password input for verification.                                          |

---

#### Module 2: PWA Service Worker & Install Prompt (Lines 798–853)

| Function/Variable                         | What It Does                                                                                             |
| ----------------------------------------- | -------------------------------------------------------------------------------------------------------- |
| `swCode`                                  | String containing Service Worker JavaScript (cache-first strategy with `CACHE_NAME = 'college-bot-v1'`). |
| `swBlob` / `swUrl`                        | Creates a Blob URL from the SW code string and registers it — avoids needing a separate `sw.js` file.    |
| `navigator.serviceWorker.register(swUrl)` | Registers the dynamically-created service worker on page load.                                           |
| `deferredPrompt`                          | Stores the `beforeinstallprompt` event for later use.                                                    |
| `installBanner` / `installBtn`            | Shows a banner "Install App for easier access" with an Install button.                                   |
| `installBtn.click` handler                | Triggers `deferredPrompt.prompt()` to show the native PWA install dialog.                                |

---

#### Module 3: IndexedDB Database Wrapper (Lines 855–925)

| Function                      | What It Does                                                                                                                                                |
| ----------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `initDB()`                    | Opens/creates the `CollegeBotDB` database (version 1) with two object stores: `documents` (keyPath: `id`) and `config` (keyPath: `key`). Returns a Promise. |
| `dbOp.get(storeName, key)`    | Reads a single record from a store by key.                                                                                                                  |
| `dbOp.put(storeName, item)`   | Inserts or updates a record in a store.                                                                                                                     |
| `dbOp.getAll(storeName)`      | Retrieves all records from a store.                                                                                                                         |
| `dbOp.delete(storeName, key)` | Deletes a single record by key.                                                                                                                             |

**Database Schema:**

| Store       | Key                                                | Fields                            |
| ----------- | -------------------------------------------------- | --------------------------------- |
| `documents` | `id` (auto-generated timestamp or `auto_filename`) | `name`, `content`, `type`, `date` |
| `config`    | `key` (`groqKey`, `modelId`, `websiteUrl`)         | `value`                           |

---

#### Module 4: UI Logic & State (Lines 927–990)

| Item                                     | What It Does                                                                                       |
| ---------------------------------------- | -------------------------------------------------------------------------------------------------- |
| `ui` object                              | Caches all DOM element references (chat area, inputs, buttons, admin elements) for fast access.    |
| `pdfjsLib.GlobalWorkerOptions.workerSrc` | Sets the PDF.js web worker URL for background PDF processing.                                      |
| `showToast(message)`                     | Displays a green toast notification at the top for 3 seconds.                                      |
| `initTheme()`                            | Reads `localStorage.theme` and applies `dark-mode` class if set.                                   |
| `ui.themeToggle.click` handler           | Toggles `dark-mode` class on body, saves preference to localStorage, swaps icon (moon ↔ sun).      |
| `ui.adminToggle.click` handler           | Toggles between Chat view and Admin view. Uses `history.pushState` to update URL hash to `#admin`. |
| `back-to-chat-btn.click` handler         | Returns to chat view from admin panel, cleans URL hash.                                            |
| `popstate` listener                      | Handles browser back button to switch between views correctly.                                     |

---

#### Module 5: Admin Logic (Lines 1000–1055)

| Function                         | What It Does                                                                                                                                                                        |
| -------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `checkAdminSession()`            | Checks if `localStorage.adminSession` exists and is within 30 minutes. If valid, shows dashboard; otherwise calls `logout()`.                                                       |
| `ui.loginBtn.click` handler      | Hashes the entered password with `sha256()`, compares with `ADMIN_HASH`. On match: saves session timestamp to localStorage, shows dashboard, calls `showToast("Login successful")`. |
| `logout()`                       | Removes `adminSession` from localStorage, hides dashboard, shows login form.                                                                                                        |
| `loadConfig()`                   | Reads `groqKey`, `modelId`, and `websiteUrl` from IndexedDB `config` store and populates the admin form fields.                                                                     |
| `ui.saveConfigBtn.click` handler | Saves the three config values (`groqKey`, `modelId`, `websiteUrl`) to IndexedDB. Shows toast "Configuration saved!".                                                                |

---

#### Module 6: Document Processor (Lines 1057–1145)

| Function                      | What It Does                                                                                                                                                                                                             |
| ----------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `ui.docUpload.change` handler | Main file upload handler. Detects file extension (`.txt`, `.pdf`, `.docx`). Extracts text using the appropriate parser. Cleans text (collapses whitespace). Saves to IndexedDB `documents` store with `id = Date.now()`. |
| `parsePDF(file)`              | Reads a PDF file using PDF.js. Iterates through all pages, extracts text content items, and concatenates them. Returns a single string.                                                                                  |
| `parseDOCX(file)`             | Reads a DOCX file using Mammoth.js. Calls `mammoth.extractRawText()` and returns the raw text string.                                                                                                                    |
| `renderDocList()`             | Fetches all documents from IndexedDB. Renders each as a card in the admin dashboard showing name, date, content preview (30 chars), and a delete button.                                                                 |
| `window.deleteDoc(id)`        | Global function. Confirms deletion, removes document from IndexedDB, re-renders the list.                                                                                                                                |

---

#### Module 7: Search Engine & Web Scraping (Lines 1147–1275)

##### `searchLocalDocuments(query, docs)` — The Core Search Algorithm

This is the heart of the application's intelligence. It performs keyword-based search with synonym expansion across all stored documents.

**Step-by-step:**

1. **Stop Words Filtering:** Removes common filler words (the, is, are, what, how, college, tell, me, etc.) from the query.
2. **Keyword Extraction:** Splits query into words > 2 characters long that aren't stop words.
3. **Fallback:** If all words are stop words, uses the original query words.
4. **Synonym Expansion:** Expands keywords using a predefined synonym dictionary:
    - `faculty` → professor, teacher, dr, lecturer, hod, staff, dean
    - `hostel` → accommodation, dorm, dormitory, room, warden, mess
    - `fee/fees` → cost, tuition, price, payment, amount, charges
    - `course/courses` → program, degree, btech, bsc, ba, bcom, syllabus, curriculum
    - `admission` → apply, enroll, join, eligibility, cutoff, merit, good, review
5. **URL Stripping:** All URLs and file references are stripped from document content (replaced with `[URL REMOVED]` / `[FILE REMOVED]`) so the AI never leaks internal URLs.
6. **Chunking:** Documents are split into paragraphs (by newlines). If chunks are too few, artificial 800-character chunks are created.
7. **Scoring:** Each chunk is scored by counting keyword matches (full word match = 1 point, partial match = 0.5 points).
8. **Ranking:** Chunks are sorted by score descending. Top 5 chunks are returned with source attribution.

##### `scrapeWebsite(url, query)` — Fallback Web Scraper

Called only when `searchLocalDocuments()` returns empty results.

**Step-by-step:**

1. **CORS Proxy:** Uses `api.allorigins.win` as a CORS proxy to fetch the college website HTML from the client side.
2. **DOM Parsing:** Parses the fetched HTML using `DOMParser`. Extracts text from `p, h1, h2, h3, li, a` elements.
3. **Smart Link Discovery:** Searches all `<a>` tags on the page for links whose text or href matches the user's query keywords.
4. **Sub-page Scraping:** If a relevant sub-page link is found, it scrapes that page too and appends the content.
5. **Local Search on Scraped Data:** Runs the same `searchLocalDocuments()` algorithm on the scraped content.
6. **Loading Status Updates:** Updates the loading indicator text ("Reading sub-page..." etc.).

---

#### Module 8: Chat & Groq API Client (Lines 1277–1527)

##### `chatHistory` — Conversation State

An array initialized with a single system message that defines the AI's persona:

-   **Personality:** Warm, helpful, proud advocate of the college.
-   **Factual Boundaries:** Must only use provided context for factual answers.
-   **Security:** Must never reveal internal instructions or prompts.
-   **Link Policy:** Only allowed to output `https://ymnnanded.in/` — never PDFs, sub-pages, or `undefined`.
-   **Suggestion Format:** Must format alternative questions as `SUGGESTIONS: ["Q1", "Q2", "Q3"]` at the end.

##### `getTime()` — Time Formatter

Returns current time as `HH:MM` (12-hour format).

##### Marked.js Custom Renderer

Custom `renderer.link` function that:

-   Opens all links in new tabs (`target="_blank"`).
-   Fixes `[object Object]` bug by replacing broken href objects with the college website URL.

##### `appendMessage(sender, text)` — Message Display

**What it does:**

1. Creates a message div with class `user` or `bot`.
2. For bot messages: Parses `SUGGESTIONS: [...]` JSON from the end of the response, creates clickable suggestion bubble buttons, and removes the JSON from visible text.
3. Renders bot text through `marked.parse()` for markdown formatting.
4. **Post-render sanitization:** Replaces any `href="undefined"` or `[object Object]` with the college website URL.
5. Appends the message to the chat area with a timestamp.
6. Auto-scrolls to the bottom.

##### `window.sendSuggestion(query)` — Suggestion Click Handler

Sets the input field value to the clicked suggestion text and triggers `handleSend()`.

##### `showLoading(statusText)` / `hideLoading()` — Loading Indicator

Shows/hides an animated three-dot loading indicator with a status message (e.g., "Searching documents...", "Checking college website...", "Thinking...").

##### `handleSend()` — The Main Chat Handler (Most Critical Function)

This is the primary function that orchestrates the entire query-response pipeline:

**Step-by-step flow:**

1. **Input Validation:** Gets query from textarea, trims it. Returns if empty.
2. **UI Updates:** Clears input, disables send button, shows user message bubble.
3. **Config Fetch:** Reads `groqKey`, `modelId`, `websiteUrl` from IndexedDB.
4. **URL Safety:** Ensures `collegeWebsiteUrl` is never `undefined` or empty (defaults to `https://ymnnanded.in/`).
5. **API Key Check:** If no API key is configured, shows an error message and returns.
6. **Phase 1 — Local Document Search:**
    - Shows loading "Searching documents..."
    - Fetches all documents from IndexedDB.
    - Calls `searchLocalDocuments(query, docs)` to find relevant context.
7. **Phase 2 — Website Scraping (Fallback):**
    - Only triggered if local search returned empty.
    - Shows loading "Checking college website..."
    - Calls `scrapeWebsite(collegeWebsiteUrl, query)`.
8. **Phase 3 — AI Processing:**
    - Shows loading "Thinking..."
    - Constructs prompt: `CONTEXT:\n{context}\n\nUSER QUESTION:\n{query}`
    - Builds API message array: system prompt + chat history + new user message.
9. **Phase 4 — Groq API Call:**
    - POST request to `https://api.groq.com/openai/v1/chat/completions`
    - Headers: `Authorization: Bearer {apiKey}`, `Content-Type: application/json`
    - Body: `{ model, messages, temperature: 0.5 }`
10. **Phase 5 — Response Processing:**
    - Parses JSON response, extracts `choices[0].message.content`.
    - **Sanitization:** Replaces all `undefined` references with college website URL.
    - **Nuclear Fallback:** If response doesn't mention `ymnnanded.in` at all, appends the link.
11. **Phase 6 — Display & History:**
    - Hides loading indicator.
    - Calls `appendMessage('bot', botReply)`.
    - Adds user query and bot reply to `chatHistory` (stores original query without context).
    - Trims history to last 10 messages + system prompt to prevent token overflow.
12. **Error Handling:** Catches any errors, displays them as bot messages with troubleshooting hints.

---

#### Module 9: Input Events & Chat Tools (Lines 1529–1574)

| Function/Event               | What It Does                                                                                                                                  |
| ---------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------- |
| `sendBtn.click`              | Triggers `handleSend()`.                                                                                                                      |
| `userInput.keypress` (Enter) | Triggers `handleSend()` on Enter (without Shift).                                                                                             |
| `updateCharCounter()`        | Updates the `0 / 1000` counter. Enables/disables send button. Auto-resizes textarea height up to 120px.                                       |
| `clearChatBtn.click`         | Confirms with user, resets `chatHistory` to system prompt only, clears chat area, shows "Chat history cleared" message.                       |
| `exportChatBtn.click`        | Iterates all `.message` elements, extracts text and timestamps, generates a `.txt` file, and triggers download via a temporary `<a>` element. |

---

#### Module 10: Auto-Load Local Files — Dynamic Manifest (Lines 1606–1660)

| Item                  | Details                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| --------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `clg_info/files.json` | A JSON manifest file containing an array of all knowledge base filenames. The app fetches this file at startup instead of having filenames hardcoded in JavaScript. To add new knowledge base files, you only need to drop the `.txt` file in `clg_info/` and add its name to `files.json` — no code changes required in `index.html`.                                                                                                                              |
| `preloadLocalFiles()` | First fetches `clg_info/files.json` to get the list of filenames. Then loops through each file, fetches it via `fetch('./clg_info/' + filename, { cache: 'no-store' })`, cleans the text, and saves to IndexedDB with id `auto_clg_info/filename`. The `auto_` prefix and fixed ID ensures files overwrite themselves on reload instead of duplicating. Logs success/failure to console. If `files.json` fails to load, no files are loaded and an error is logged. |

**Important Note:** This fetch only works when the app is served via HTTP (Live Server, Vercel, GitHub Pages). Opening `index.html` directly as a file (`file://`) will fail due to browser CORS restrictions.

---

#### Module 11: Boot / Initialization (Lines 1632–1700)

| Function                   | What It Does                                                                                                                                                                                                                     |
| -------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `boot()`                   | The main entry point. Calls `initTheme()`, then `loadDefaultConfig()` (secret file), then `initDB()`, then `seedDefaultsFromConfig()`, then `preloadLocalFiles()`. Also checks if URL hash is `#admin` to auto-open admin panel. |
| `seedDefaultsFromConfig()` | Seeds IndexedDB with defaults from the secret config file. Only writes API key, model ID, and website URL if they don't already exist in IndexedDB (admin overrides take priority).                                              |

---

## 6. Knowledge Base Files

The `clg_info/` folder contains 10 curated text files that form the chatbot's primary knowledge source:

| File                    | Lines | Content Summary                                                                                                                                              |
| ----------------------- | ----- | ------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `college data.txt`      | 166   | College overview, department list, UG/PG courses with intake, CS department faculty, academic schedule, course syllabi (Data Science, Software Testing)      |
| `common_questions.txt`  | 205   | 50+ FAQ answers covering admissions, fees, scholarships, courses, exams, campus life, faculty, placements, general rules                                     |
| `faculty_details.txt`   | 121   | Department-wise faculty listing — Principal, Zoology, Physics, English, Mathematics, Chemistry, Sociology, Public Administration, Library (77 total faculty) |
| `fees_details.txt`      | 111   | Admission fees, university exam fees (M.Sc., M.A., M.Com.), scholarships (Reliance Foundation), girls hostel fees (₹32,200), other fees                      |
| `final questions.txt`   | ~80   | 20 curated Q&A pairs for most common student queries (courses, admission process, eligibility, fee range, scholarships, faculty, location)                   |
| `general.txt`           | 157   | Full website crawl dump from ymnnanded.in — homepage content, news & events, pre-semester timetable, academic timetable, department pages, 60+ PDF list      |
| `imp_anouncements.txt`  | 123   | Extracted content from important PDF announcements — practical exam timetables, youth festival schedule, tender details, scholarship details, admission ads  |
| `must_details.txt`      | 137   | Priority document availability checklist — prospectus, departments, hostel policy, library, NAAC reports, IQAC documents, NSS/NCC, missing items             |
| `principal_details.txt` | 123   | Principal Dr. Ganeshchandra Shinde's complete profile — awards, international experience, 36 years teaching, 22 PhD candidates, 101 international papers     |
| `principals_desk.txt`   | ~40   | Verbatim principal's welcome message (emphasis on ICT, LMS, skill programs, NAAC accreditation)                                                              |

---

## 7. Data Flow — How a User Query is Answered

```
User types question
        │
        ▼
┌─────────────────────┐
│ 1. handleSend()     │
│    Captures query    │
└────────┬────────────┘
         │
         ▼
┌─────────────────────────────┐
│ 2. Fetch all documents      │
│    from IndexedDB           │
│    (auto-loaded + uploaded) │
└────────┬────────────────────┘
         │
         ▼
┌──────────────────────────────────┐
│ 3. searchLocalDocuments()        │
│    - Remove stop words           │
│    - Expand synonyms             │
│    - Score & rank document chunks │
│    - Return top 5 relevant chunks │
└────────┬─────────────────────────┘
         │
    ┌────▼────┐
    │ Found?  │
    └────┬────┘
    YES  │  NO
    │    │
    │    ▼
    │  ┌─────────────────────────┐
    │  │ 4. scrapeWebsite()      │
    │  │    - Fetch via CORS     │
    │  │      proxy              │
    │  │    - Parse homepage     │
    │  │    - Find relevant      │
    │  │      sub-page link      │
    │  │    - Scrape sub-page    │
    │  │    - Search scraped text│
    │  └────────┬────────────────┘
    │           │
    ▼           ▼
┌───────────────────────────────┐
│ 5. Build prompt with CONTEXT  │
│    + USER QUESTION            │
└────────┬──────────────────────┘
         │
         ▼
┌───────────────────────────────┐
│ 6. POST to Groq API          │
│    Model: llama-3.3-70b      │
│    Temperature: 0.5           │
└────────┬──────────────────────┘
         │
         ▼
┌───────────────────────────────┐
│ 7. Sanitize response          │
│    - Fix undefined links      │
│    - Fix [object Object]      │
│    - Ensure college URL       │
└────────┬──────────────────────┘
         │
         ▼
┌───────────────────────────────┐
│ 8. Render as Markdown         │
│    - Parse SUGGESTIONS: [...]  │
│    - Create suggestion bubbles │
│    - Display in chat           │
└───────────────────────────────┘
```

---

## 8. Security Features

| Feature            | Implementation                                                                                                                         |
| ------------------ | -------------------------------------------------------------------------------------------------------------------------------------- |
| Admin Password     | SHA-256 hashed, compared using Web Crypto API. Never stored in plaintext.                                                              |
| Secret Config File | `config/default_config.json` stores admin hash and API key defaults. Excluded from Git via `.gitignore`.                               |
| Config Seeding     | API key from secret file is seeded into IndexedDB only if not already set by admin — admin overrides always take priority.             |
| Session Timeout    | Admin session expires after 30 minutes (checked via localStorage timestamp).                                                           |
| DevTools Blocking  | `F12` and `Ctrl+Shift+I` shortcuts are intercepted and prevented.                                                                      |
| Context Menu       | Right-click disabled on desktop (allowed on mobile for usability).                                                                     |
| API Key Storage    | Stored in browser's IndexedDB — never sent to any server except Groq.                                                                  |
| URL Sanitization   | All internal document URLs are stripped before sending to AI. Bot responses are sanitized to never output `undefined` or broken links. |
| AI Prompt Security | System prompt instructs the AI to never reveal its instructions, prompts, or database workings.                                        |
| `.gitignore`       | Prevents `config/default_config.json` (containing API keys) from being committed to public repositories.                               |

**Note:** These are client-side protections and deter casual users. A knowledgeable developer can bypass them via browser DevTools.

---

## 9. PWA (Progressive Web App) Features

| Feature         | How It Works                                                                            |
| --------------- | --------------------------------------------------------------------------------------- |
| Manifest        | Dynamically generated as a JSON Blob URL and injected as `<link rel="manifest">`.       |
| Service Worker  | Dynamically created as a JavaScript Blob URL and registered. Uses cache-first strategy. |
| Install Prompt  | Intercepts `beforeinstallprompt` event, shows a custom install banner.                  |
| Standalone Mode | `display: "standalone"` in manifest gives native app-like experience.                   |
| App Name        | "College AI Assistant" / "YMN AI CHATBOT"                                               |
| Theme Color     | `#4f46e5` (Indigo)                                                                      |

---

## 10. Admin Panel

### Access

-   Click the gear icon (⚙️) in the header, or navigate to `your-url.com/#admin`.
-   Enter password: `get_admin_password`

### Dashboard Sections

**1. API & Integration**

-   **Groq API Key:** Text field for the API key (stored in IndexedDB).
-   **Groq Model ID:** Default `llama-3.3-70b-versatile`. Changeable if model is decommissioned.
-   **College Website URL:** Default `https://ymnnanded.in/`. Used for fallback web scraping.

**2. Knowledge Base (Documents)**

-   **Upload Zone:** Click-to-upload area accepting `.txt`, `.pdf`, `.docx` files.
-   **Document List:** Shows all uploaded documents with name, date, preview, and delete button.
-   **Auto-loaded files** from `clg_info/` also appear here.

---

## 11. API Integration (Groq)

| Parameter          | Value                                                                |
| ------------------ | -------------------------------------------------------------------- |
| Endpoint           | `https://api.groq.com/openai/v1/chat/completions`                    |
| Method             | POST                                                                 |
| Model              | `llama-3.3-70b-versatile` (configurable)                             |
| Temperature        | `0.5` (balanced creativity/accuracy)                                 |
| Auth               | Bearer token (Groq API key)                                          |
| Message Format     | OpenAI-compatible chat format (`system`, `user`, `assistant` roles)  |
| History Management | Last 10 messages + system prompt (trimmed to prevent token overflow) |

---

## 12. Deployment

The application can be deployed on:

1. **GitHub Pages** — Push to a repo, enable Pages.
2. **Vercel** — Connect repo, auto-deploys.
3. **Local Server** — VS Code Live Server extension.

**Requirements:**

-   `index.html`, `clg_info/` folder (with `files.json`), and `config/` folder must be in the same directory.
-   A valid Groq API key must be configured via the admin panel or the secret config file.
-   HTTPS is recommended for PWA features and Service Worker.

---

## 13. Developers

-   **Sanika Kumbhkarn** — Developer
-   **Samiksha Padalwar** — Developer
-   **College:** Yeshwant Mahavidyalaya, Nanded
-   **Guided by:** Department of Computer Science, Yeshwant Mahavidyalaya

---

_This documentation covers every function, module, and design decision in the YMN AI CHATBOT project._
