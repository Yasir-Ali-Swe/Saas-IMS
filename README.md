# StockPilot — AI-Powered Inventory Management SaaS

StockPilot is a multi-tenant, AI-powered inventory management platform built with the MERN stack. It combines full inventory operations (products, stock, invoicing, purchase orders) with AI features — demand forecasting, reorder suggestions, anomaly detection, business insights, and an NLP chatbot — wrapped in a role-based, subscription-gated SaaS architecture.

---

## ✨ Features

- **Multi-tenant architecture** — each organization's data is fully isolated
- **Role-based access control** — Super Admin, Admin, Manager, Staff, each with scoped permissions
- **Full inventory operations** — products, categories, suppliers, stock in/out with audit trail
- **Invoicing & Purchase Orders** — automatic stock sync, status workflows, PO approval chains
- **AI Demand Forecasting** — predicts stockout timing from sales history
- **AI Reorder Suggestions** — recommends restock quantity and timing based on forecast + supplier lead time
- **AI Anomaly Detection** — flags dead stock, sales spikes, and suspicious adjustments
- **AI Business Insights** — Gemini-generated plain-English performance summaries
- **AI Chatbot (NLP Agent)** — ask questions about your inventory in natural language, powered by function calling (Admin & Super Admin only)
- **Stripe Billing** — Free and Premium subscription tiers, webhook-driven sync
- **Role-specific dashboards** — tailored views for every role, from platform-wide analytics to personal activity stats

---

## 🧱 Tech Stack

**Frontend**

- React
- Tailwind CSS + shadcn/ui
- Redux Toolkit (client state) + Tanstack Query + Axios (server state / API layer)
- React Hook Form + Zod (validation)
- Recharts (charts)

**Backend**

- Node.js + Express.js
- MongoDB + Mongoose
- JWT authentication (access + refresh tokens)
- node-cron (scheduled AI jobs)

**AI**

- Google Gemini API — function calling (chatbot) and text generation (insights)

**Payments**

- Stripe — Checkout Sessions + Webhooks

**File Storage**

- Cloudinary — product images, organization logos, user avatars

---

## 👥 Roles & Permissions

| Role            | Scope                                                                 |
| --------------- | --------------------------------------------------------------------- |
| **Super Admin** | Platform-wide — manages all tenant organizations                      |
| **Admin**       | Full control of their own organization (auto-created at registration) |
| **Manager**     | Operational management — no billing or org-settings access            |
| **Staff**       | Frontline operations — stock handling, invoicing, limited visibility  |

Full permissions matrix available in the project documentation.

---

## 📦 Core Modules

1. **Auth** — Organization + Admin self-registration, login, email verification, password reset, JWT refresh
2. **Super Admin** — Organization management, platform analytics, subscription overrides
3. **Organization & User Management** — Org settings, invoice settings, team invites
4. **Product Foundation** — Categories, Suppliers, Products (auto-generated SKUs)
5. **Stock Management** — Stock in/out with full audit trail (StockLog)
6. **Invoicing** — Auto stock-out on creation, void with stock reversal
7. **Purchase Orders** — Approval workflow, auto stock-in on fulfillment
8. **Dashboard & Reports** — Role-specific dashboards, sales trends, financial reports
9. **AI Forecast & Reorder** — Rule-based demand forecasting and reorder suggestions (nightly cron)
10. **AI Anomaly Detection** — Rule-based anomaly flagging (nightly cron)
11. **AI Insights** — Gemini-generated weekly/monthly business summaries
12. **AI Chatbot** — Gemini function-calling agent across all inventory data (Admin & Super Admin only)
13. **Billing** — Stripe Checkout + webhook-driven subscription management

---

## 🗂️ Database Models

`Organization`, `SubscriptionPlan`, `Subscription`, `User`, `Category`, `Supplier`, `Product`, `StockLog`, `Invoice`, `PurchaseOrder`, `DemandForecast`, `ReorderSuggestion`, `Anomaly`, `AiInsights`, `ChatLog`

---

## 🏗️ Architecture Highlights

- **Multi-tenancy:** every collection is scoped by `organizationId`; uniqueness constraints (SKU, invoice number, PO number, category name) are compound-indexed per organization rather than global
- **Service layer pattern:** stock mutation logic is centralized in a shared service and reused across Stock, Invoice, and Purchase Order controllers — guaranteeing every stock change is captured in the audit trail
- **AI split by design:** forecasting, reorder suggestions, and anomaly detection are rule-based (explainable, no training data required); insights and the chatbot use Gemini for natural language generation and function calling
- **RBAC enforcement:** role checks at the route level, with finer-grained business rules (e.g. a Manager cannot approve their own purchase order) enforced inside controllers
- **Billing integrity:** Stripe webhooks are the source of truth for subscription state; the Super Admin manual override exists only as a support fallback

---

## 🚀 Getting Started

### Prerequisites

- Node.js (v18+)
- MongoDB (local or Atlas)
- Stripe account (test mode)
- Google Gemini API key
- Cloudinary account

### Backend Setup

```bash
cd server
npm install
```

Create a `.env` file:

```env
PORT=5000
NODE_ENV=development
MONGO_URI=your_mongodb_connection_string

JWT_SECRET=your_jwt_secret

GEMINI_API_KEY=your_gemini_api_key

STRIPE_SECRET_KEY=your_stripe_secret_key
STRIPE_WEBHOOK_SECRET=your_stripe_webhook_secret

CLOUDINARY_CLOUD_NAME=your_cloud_name
CLOUDINARY_API_KEY=your_api_key
CLOUDINARY_API_SECRET=your_api_secret

FRONTEND_URL=http://localhost:5173
```

Run the server:

```bash
npm run dev
```

Seed the subscription plans (Free/Premium) before testing billing.

### Frontend Setup

```bash
cd client
npm install
```

Create a `.env` file:

```env
VITE_API_BASE_URL=http://localhost:5000/api/v1
```

Run the app:

```bash
npm run dev
```

### Stripe Webhook (local testing)

```bash
stripe listen --forward-to localhost:5000/api/v1/billing/webhook
```

---

## 📁 Project Structure

```
server/
├── models/
├── controllers/
├── routes/
├── services/        # shared business logic (stock, forecast, reorder, anomaly, insights)
├── jobs/            # node-cron scheduled AI jobs
├── middleware/
└── config/

client/
├── src/
│   ├── api/          # RTK Query API slices
│   ├── store/         # Redux slices (client state)
│   ├── pages/          # route-level pages, organized by role/module
│   ├── components/      # reusable UI components
│   ├── layouts/
│   └── lib/
```

---

## 🔐 Security Notes

- Passwords hashed with bcrypt
- JWT access tokens (short-lived) + httpOnly refresh token cookies
- Tenant isolation enforced at the query level on every request
- Stripe webhook signature verification on all billing events
- AI chatbot restricted to Admin and Super Admin roles; all data queries scoped to the requesting user's organization regardless of AI-generated intent

---

## 📄 License

This project was built as a portfolio/resume project to demonstrate full-stack SaaS development, multi-tenant architecture, and applied AI integration (function calling, LLM-generated insights, rule-based forecasting).
