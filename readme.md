# LakeSiders Armory — IIM Udaipur Sports Equipment & Circulation Portal

A centralized, library-style sports equipment inventory and live custody tracking web application built for **LakeSiders — The Sports Committee of IIM Udaipur** in a custom **Red (`#DC2626`), Stark White (`#FFFFFF`), and Carbon Black (`#0A0A0B`)** industrial-athletic design system.

## Architectural Overview

| Layer | Technology | Purpose |
| :--- | :--- | :--- |
| **Interactive Frontend App** | `lakesiders_equipment_portal.html` | Custom Swiss-grid Red/White/Black UI with live stock meters, instant self-issue/return workflows, active borrower ledger, and admin desk console |
| **Authentication & Domain Guard** | `auth.ts` (NextAuth.js v5 + Google OAuth) | Restricts login strictly to `@iimu.ac.in` Google Workspace accounts (`hd: "iimu.ac.in"`) and grants automatic `ADMIN` privileges to `sports@iimu.ac.in` |
| **Serverless Backend API** | `route.ts` (Next.js App Router on Vercel) | Handles atomic equipment check-outs, stock validation, self check-ins, and `sports@iimu.ac.in` Admin catalog mutations |
| **Database Layer** | `schema.sql` (Vercel Postgres / Supabase) | Relational schema for `equipment` SKUs and `circulation_loans` with `@iimu.ac.in` email constraints and pre-seeded LakeSiders inventory |

## Step-by-Step Deployment on Vercel

### 1. Instant Static Preview Deployment (Zero-Config)
To deploy the interactive prototype (`lakesiders_equipment_portal.html`) immediately to a live `*.vercel.app` URL:
1. Place `lakesiders_equipment_portal.html` inside a project folder and rename/copy it to `index.html`.
2. Run the Vercel CLI from that directory:
   ```bash
   npx vercel --prod
   ```

### 2. Full Multi-User Production Setup (Next.js + Vercel Postgres + Google OAuth)
1. **Initialize Next.js & Install Dependencies**:
   ```bash
   npx create-next-app@latest lakesiders-armory --typescript
   cd lakesiders-armory
   npm install next-auth@beta @vercel/postgres
   ```
2. **Provision Database (`schema.sql`)**:
   - In your Vercel Dashboard, open **Storage → Create Database → Postgres (Neon)** (or connect Supabase).
   - Paste and execute `schema.sql` in the SQL query runner to create the `equipment` and `circulation_loans` tables and seed the initial LakeSiders inventory.
3. **Configure Google Workspace OAuth (`auth.ts`)**:
   - In Google Cloud Console (**APIs & Services → Credentials**), create an **OAuth 2.0 Client ID** and set the Authorized Redirect URI to:
     `https://<your-vercel-domain>.vercel.app/api/auth/callback/google`
   - Place `auth.ts` in your project root and `route.ts` at `app/api/inventory/route.ts`.
4. **Set Vercel Environment Variables**:
   Add the following in **Vercel Project Settings → Environment Variables**:
   ```ini
   GOOGLE_CLIENT_ID=your_google_oauth_client_id
   GOOGLE_CLIENT_SECRET=your_google_oauth_client_secret
   AUTH_SECRET=your_random_32_byte_secret
   ```
5. **Deploy to Production**:
   ```bash
   npx vercel --prod
   ```
