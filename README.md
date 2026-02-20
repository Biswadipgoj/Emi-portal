# TelePoint EMI Management Portal

A production-ready EMI management system built with Next.js 14 + Supabase.

---

## 🚀 Quick Setup

### 1. Install Dependencies
```bash
npm install
```

### 2. Environment Variables
Copy `.env.example` to `.env.local` and fill in your Supabase credentials:
```bash
cp .env.example .env.local
```

```env
NEXT_PUBLIC_SUPABASE_URL=https://your-project.supabase.co
NEXT_PUBLIC_SUPABASE_ANON_KEY=your-anon-key
SUPABASE_SERVICE_ROLE_KEY=your-service-role-key
```

### 3. Run SQL Migration
Go to your Supabase project → SQL Editor → paste the contents of `migrations/001_initial.sql` and run it.

### 4. Create Super Admin User in Supabase
In Supabase Dashboard → Authentication → Users → Add User:
- **Email:** `telepoint@admin.local`
- **Password:** `TELEBISWAJITPOINT`
- **Auto-confirm email:** ✅ Yes

Then in SQL Editor, run:
```sql
INSERT INTO profiles (user_id, role)
SELECT id, 'super_admin'
FROM auth.users
WHERE email = 'telepoint@admin.local';
```

### 5. Run Development Server
```bash
npm run dev
```

### 6. Deploy to Vercel
```bash
# Connect your repo to Vercel, then add environment variables in Vercel dashboard
vercel deploy
```

---

## 🔐 Login Credentials

| Role | Username | Password |
|------|----------|----------|
| Super Admin | `TELEPOINT` | `TELEBISWAJITPOINT` |
| Retailer | *(set by admin)* | *(set by admin)* |

---

## 📋 Feature Summary

### Super Admin (`/admin`)
- ✅ Live customer search (name/IMEI/Aadhaar) — privacy rule (no auto-load)
- ✅ Create/edit/delete customers with full fields
- ✅ Record payments directly (instantly approved)
- ✅ Mark customers as COMPLETE (remark mandatory)
- ✅ Manage retailers (create/edit/delete/activate/deactivate)
- ✅ Configure fine settings (default ₹450)
- ✅ Export CSV reports
- ✅ Audit log on all actions

### Admin Approvals (`/admin/approvals`)
- ✅ Live search pending payment requests
- ✅ Approve → EMIs turn APPROVED (blue)
- ✅ Reject → EMIs revert to UNPAID (with reason, audit trail)

### Retailer (`/retailer`)
- ✅ Search own customers only
- ✅ View EMI schedule + due breakdown
- ✅ 1st EMI charge popup (if pending)
- ✅ Fine popup (if overdue)
- ✅ Submit payment request (password confirmation as PIN)
- ✅ UPI QR code generation
- ✅ Cannot edit COMPLETE customers
- ✅ View recent payment requests + receipts

### Receipt (`/receipt/[id]`)
- ✅ Full payment receipt with breakdown
- ✅ Shows EMI, fine, 1st charge, total, mode, status, timestamps
- ✅ Print/PDF support

---

## 🗄 Database Tables

| Table | Purpose |
|-------|---------|
| `profiles` | User roles (super_admin / retailer) |
| `retailers` | Retailer records linked to auth users |
| `customers` | Customer master with all EMI config |
| `emi_schedule` | Auto-generated EMI payment schedule |
| `payment_requests` | Payment submissions with status |
| `payment_request_items` | Individual EMIs in each request |
| `audit_log` | Admin action audit trail |
| `fine_settings` | Configurable fine amount (default ₹450) |

### Key DB Function
`get_due_breakdown(customer_id)` — single source of truth for:
- Next unpaid EMI details
- Fine due (if overdue)
- 1st EMI charge due
- Total payable
- Popup flags

---

## 🔒 Security

- **RLS enabled** on all tables
- Retailers can only access their own customers/requests
- Service role key used **only** in server-side API routes
- Never exposed to browser
- Password re-auth for retailer payment submission
