# PlayBeat Digital — Deployment Guide

## Architecture Overview

```
┌─────────────────────────────────────────────────────────────┐
│                   Frontend (Vercel)                         │
│  - React + TypeScript                                       │
│  - Static HTML files (index.html, admin.html, unified)      │
│  - Payment gateway UI (logos only, no forms)                │
│  - Domain: playbeat.digital                                 │
└────────────────────┬────────────────────────────────────────┘
                     │ API Calls
                     ▼
┌─────────────────────────────────────────────────────────────┐
│                   Backend (Render)                          │
│  - Node.js + Express                                        │
│  - Payment Gateway Integration                              │
│  - Database: MongoDB                                        │
│  - Domain: api.playbeat.digital                             │
└─────────────────────────────────────────────────────────────┘
```

---

## Frontend Deployment (Vercel)

### Prerequisites
- Vercel account (https://vercel.com)
- GitHub repository connected

### Step 1: Build Frontend
```bash
cd frontend/front
npm install
npm run build
```

This generates a `dist/public/` directory with:
- `index.html` — Home page
- `admin.html` — Admin panel
- `playbeat_unified_fixed.html` — Unified interface
- `shared.css` & `shared.js` — Shared assets

### Step 2: Deploy to Vercel

**Option A: Via GitHub Integration**
1. Go to https://vercel.com/new
2. Select your GitHub repository
3. Set Environment Variables:
   ```
   VITE_API_URL=https://api.playbeat.digital
   VITE_STRIPE_PUBLISHABLE_KEY=pk_test_51TMvD1K7C5jIjluKwjpb2ZJzlZoH4I9hxOwbcZkXr15wNvaLLX7wHpfeTw6Eu09O0aZDCpWo6ptHvoQjhloFApJ200lkcdWYVw
   ```
4. Click "Deploy"

**Option B: Via CLI**
```bash
npm install -g vercel
cd frontend/front
vercel deploy --prod
```

### Step 3: Configure Custom Domain
1. In Vercel Dashboard → Settings → Domains
2. Add `playbeat.digital`
3. Update DNS nameservers to Vercel's:
   - `ns1.vercel-dns.com`
   - `ns2.vercel-dns.com`

---

## Backend Deployment (Render)

### Prerequisites
- Render account (https://render.com)
- GitHub repository connected
- MongoDB Atlas account with connection string

### Step 1: MongoDB Setup

If using MongoDB Atlas:
1. Create cluster at https://www.mongodb.com/cloud/atlas
2. Get connection string:
   ```
   mongodb+srv://admin:A3bE17J0zUPZQ5FE@playbeat.umqpdyx.mongodb.net/?appName=playbeat
   ```

### Step 2: Deploy to Render

**Option A: Using render.yaml**
```bash
cd backend/back
git push origin main
```

Render automatically detects `render.yaml` and deploys.

**Option B: Manual Deployment**
1. Go to https://render.com/dashboard
2. Click "New Web Service"
3. Connect your GitHub repository
4. Configure:
   - **Name**: playbeat-backend
   - **Environment**: Node
   - **Build Command**: `npm install`
   - **Start Command**: `npm start`
   - **Instance Type**: Standard
   - **Region**: Choose closest to users

### Step 3: Set Environment Variables

In Render Dashboard → Environment:
```
NODE_ENV=production
PORT=3000
FRONTEND_URL=https://playbeat.digital
MONGODB_URI=mongodb+srv://admin:A3bE17J0zUPZQ5FE@playbeat.umqpdyx.mongodb.net/?appName=playbeat
SECRET_KEY=RLTJLDFGLDJFGVC
```

**Payment Gateway Credentials:**
```
# Stripe
STRIPE_ACCOUNT_ID=acct_1TMvCGGTlnWXlnXp
STRIPE_SECRET_KEY=***
STRIPE_PUBLISHABLE_KEY=pk_test_***
STRIPE_RESTRICTED_KEY=rk_test_***
STRIPE_WEBHOOK_SECRET=your_webhook_secret

# JazzCash (Pakistan)
JAZZCASH_MERCHANTID=your_id
JAZZCASH_PASSWORD=your_password
JAZZCASH_HASHKEY=your_hash_key
JAZZCASH_MPIN=03318333368

# Bank Alfalah (Pakistan)
ALFAPAY_MERCHANT_USERNAME=your_username
ALFAPAY_MERCHANT_PASSWORD=your_password
ALFAPAY_MERCHANT_HASH=your_hash

# Meezan Bank (Pakistan)
MEZPAY_USERNAME=your_username
MEZPAY_PASSWORD=your_password
```

### Step 4: Configure Custom Domain

1. In Render → Settings → Custom Domains
2. Add `api.playbeat.digital`
3. Update DNS A record:
   ```
   api.playbeat.digital  A  <render-ip>
   ```

---

## Payment Gateway Integration

### 1. Stripe (International)
- **Type**: Debit/Credit Card
- **Currency**: USD, PKR
- **Account**: acct_1TMvCGGTlnWXlnXp
- **Endpoint**: `/api/payments/stripe/create-checkout`

### 2. JazzCash (Pakistan Mobile Wallet)
- **Type**: Mobile Payment
- **Currency**: PKR only
- **Account**: 03318333368
- **Merchant**: PLAYBEAT ARENA MERCHANT ACCOUNT
- **Endpoint**: `/api/payments/jazzcash/initiate`

### 3. Bank Alfalah (Pakistan)
- **Type**: Bank Transfer
- **Currency**: PKR only
- **Account**: 00681011050474
- **Title**: PLAYBEAT DIGITAL (PRIVATE LIMITED)
- **Endpoint**: `/api/payments/alfalah/initiate`
- **User displays**: Bank details for manual transfer

### 4. Meezan Bank (Pakistan)
- **Type**: Bank Transfer
- **Currency**: PKR only
- **IBAN**: PK86MEZN0015040115102971
- **Title**: PLAYBEAT DIGITAL PRIVATE LIMITED
- **Endpoint**: `/api/payments/meezan/initiate`
- **User displays**: Bank details for manual transfer

---

## Frontend Routes

### Public Routes
| Route | File | Purpose |
|-------|------|---------|
| `/` | `index.html` | Home page |
| `/admin` | `admin.html` | Admin panel with payment management |
| `/unified` | `playbeat_unified_fixed.html` | Unified interface |
| `/checkout` | `index.html` | Checkout with payment methods |

### Payment Routes
| Route | Purpose |
|-------|---------|
| `/payment/success` | Payment success confirmation |
| `/payment/failed` | Payment failure handling |
| `/payment/pending` | Payment pending status |

---

## Backend API Endpoints

### Payment Methods
```
GET /api/payments/methods
Response:
{
  "success": true,
  "methods": [
    { "id": "stripe", "label": "Debit/Credit Card", "icon": "stripe", "currencies": ["USD", "PKR"] },
    { "id": "jazzcash", "label": "JazzCash Wallet", "icon": "jazzcash", "currencies": ["PKR"] },
    { "id": "alfalah", "label": "Bank Alfalah", "icon": "alfalah", "currencies": ["PKR"] },
    { "id": "meezan", "label": "Meezan Bank", "icon": "meezan", "currencies": ["PKR"] }
  ]
}
```

### Stripe Checkout
```
POST /api/payments/stripe/create-checkout
Body:
{
  "items": [
    { "name": "Product", "price": 29.99, "quantity": 1, "images": [] }
  ],
  "orderId": "order-123",
  "customerEmail": "user@example.com"
}
Response:
{
  "success": true,
  "sessionId": "cs_test_...",
  "url": "https://checkout.stripe.com/..."
}
```

### JazzCash Payment
```
POST /api/payments/jazzcash/initiate
Body:
{
  "amount": 5000,
  "orderId": "order-123",
  "mobileNumber": "03001234567"
}
Response:
{
  "success": true,
  "txnRefNo": "JZ123456",
  "redirectUrl": "..."
}
```

### Bank Transfer Details
```
GET /api/payments/alfalah/bank-details
Response:
{
  "accountNumber": "00681011050474",
  "accountTitle": "PLAYBEAT DIGITAL (PRIVATE LIMITED)"
}

GET /api/payments/meezan/bank-details
Response:
{
  "iban": "PK86MEZN0015040115102971",
  "accountTitle": "PLAYBEAT DIGITAL PRIVATE LIMITED"
}
```

---

## Health Checks

### Backend Health
```bash
curl https://api.playbeat.digital/api/health
```

Expected Response:
```json
{
  "success": true,
  "status": "ok",
  "timestamp": "2024-01-01T00:00:00Z"
}
```

---

## Testing Checklist

### Frontend
- [ ] Home page loads at `playbeat.digital`
- [ ] Admin panel loads at `playbeat.digital/admin`
- [ ] Unified interface loads at `playbeat.digital/unified`
- [ ] Navigation between pages works
- [ ] API calls reach backend successfully

### Backend
- [ ] Health endpoint responds
- [ ] `/api/payments/methods` returns all payment methods
- [ ] Stripe checkout session creation works
- [ ] Bank details endpoints return correct data
- [ ] CORS allows frontend domain

### Payments
- [ ] Stripe test payment can be initiated
- [ ] JazzCash payment flow works
- [ ] Bank transfer details display correctly
- [ ] Payment success/failure redirects work

---

## Troubleshooting

### Frontend won't load
1. Check Vercel build logs
2. Verify environment variables
3. Check CORS policy in backend

### Backend errors
1. Check Render logs: `render.com/dashboard`
2. Verify environment variables are set
3. Test MongoDB connection
4. Check payment gateway credentials

### Payment gateway issues
1. **Stripe**: Verify API keys in Stripe Dashboard
2. **JazzCash**: Check merchant credentials
3. **Banks**: Verify bank details in config
4. Enable webhook logging for debugging

---

## Security Notes

⚠️ **IMPORTANT**: Never commit sensitive credentials to Git!
- Keep `.env` files in `.gitignore`
- Use Vercel/Render's environment variable management
- Rotate API keys regularly
- Enable webhook signature verification

---

## Monitoring & Logs

### Vercel Logs
- Dashboard → Deployments → Logs

### Render Logs
- Dashboard → playbeat-backend → Logs

### Payment Logs
- Check backend logs for payment transactions
- Monitor Stripe Dashboard for payment events

---

## Support

For issues:
1. Check backend logs on Render
2. Check frontend build logs on Vercel
3. Verify environment variables
4. Test API endpoints with curl/Postman
5. Contact payment gateway support

