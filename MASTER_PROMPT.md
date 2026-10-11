You are an expert full-stack software engineer and system architect. Your task is to build a complete, lightweight, production-ready Progressive Web App (PWA) designed to automate hospital cashier payment workflows and eliminate manual reference number checks.

### Project Context & Real-World Problem
In Ethiopian healthcare settings, cashiers experience major operational bottlenecks: patients pay via fragmented digital transfers (Telebirr, CBE Birr, bank mobile apps), forcing cashiers to manually verify text messages, transcribe reference numbers, and compile error-prone end-of-day reports. Cashiers must NEVER have login access to the institution's primary bank accounts. 

To solve this, the application uses **ArifPay** as a payment gateway integration to handle multi-channel checkout securely, leveraging webhooks for instant automated reconciliation.

### Branding & Design System
- **Color Palette:** The hospital's brand colors are **light blue** and **white**. The UI must reflect a clean, trustworthy medical aesthetic featuring light blue primary accents (`#0284c7` or Tailwind `sky-500`/`blue-400`), crisp white backgrounds, clean cards, and high-contrast dark text for readability at busy reception desks.

### Technical Constraints & Stack
- **Architecture:** Monorepo application structure containing a backend and a frontend PWA.
- **Backend:** Node.js with **Express.js**.
- **Database:** **MySQL** using **bare SQL statements** (via `mysql2` promise pool). Do **NOT** use any ORM (like Prisma or Sequelize) to keep the setup lean for shared cPanel/VPS server deployment.
- **Frontend PWA:** **React (Vite)** + Tailwind CSS configured as a Progressive Web App (manifest, service worker, offline support).
- **Package Manager:** Standard `npm` workspaces.

---

### Step-by-Step Deliverables to Generate

Please write clean, fully commented code for the following components:

#### 1. Directory Structure Layout
Provide the high-level project file structure clearly grouping backend, frontend, and shared files.

#### 2. MySQL Database Schema (`schema.sql`)
Write the raw SQL commands to create:
- A `users` table (`id`, `username`, `password_hash`, `role` [cashier/admin], `created_at`).
- An `invoices` table (`id`, `invoice_uuid` [UUID string], `patient_name`, `patient_phone`, `total_amount` [DECIMAL], `status` ['PENDING', 'PAID', 'EXPIRED'], `arifpay_session_id`, `created_by` [foreign key to users], `created_at`).

#### 3. Express.js Backend (`server.js` & `db.js`)
Implement the core server logic using `mysql2` and Express:
- **Database Connection Pool (`db.js`):** Configuration using environment variables.
- **Create Invoice Route (`POST /api/invoices`):** Accepts `patientName`, `patientPhone`, `amount`, and `cashierId`. Inserts a pending record with a generated UUID, calls the ArifPay checkout session creation endpoint conceptually, saves the session reference, and returns the invoice details.
- **ArifPay Webhook Listener (`POST /api/webhook/arifpay`):** Securely receives transaction status payloads from ArifPay. When a payment succeeds, instantly updates the corresponding invoice status to `'PAID'` in MySQL via a raw SQL query.
- **Status Check Route (`GET /api/invoices/:uuid`):** Allows the frontend PWA to poll or query the live payment status of an active invoice.

#### 4. React PWA Frontend (`App.jsx` & Checkout Component styled with Light Blue & White theme)
Create a responsive React component optimized for low-end tablets/devices at hospital reception desks:
- **Cashier Input Form:** Styled with clean white cards, light blue primary buttons, and crisp typography.
- **Live Status Polling & Real-Time Feedback:** Once an invoice is generated, implement a lightweight polling loop or effect that watches `/api/invoices/:uuid`. The moment the backend webhook fires and updates the invoice to `'PAID'`, instantly switch the UI to a prominent **Green Success Screen** with clear confirmation text, eliminating manual checks entirely.
- **PWA Service Worker Setup:** Basic configuration guidelines or Vite plugin settings to ensure the cashier screen loads reliably even with fluctuating hospital network conditions.

Write out the code files clearly with concise setup instructions.
