# PlayBeat Digital — Quick Start Deployment

## ✅ What's Done

- ✅ Frontend organized in `frontend/front/`
- ✅ Backend organized in `backend/back/`
- ✅ Payment gateway integration ready (Stripe, JazzCash, Alfalah, Meezan)
- ✅ Environment configuration files (.env)
- ✅ Vercel configuration (vercel.json)
- ✅ Render configuration (render.yaml)
- ✅ API endpoints fully implemented
- ✅ Frontend/Backend linking via API calls
- ✅ Documentation (DEPLOYMENT_COMPLETE.md, API_DOCUMENTATION.md)

---

## 📋 Deployment Checklist

### Phase 1: Pre-Deployment ✅

- [ ] Push updated code to GitHub
  ```bash
  cd "d:\playbeat digital"
  git add .
  git commit -m "feat: complete deployment setup with payment gateways"
  git push origin main
  ```

### Phase 2: Backend Deployment (Render)

- [ ] Go to https://render.com/dashboard
- [ ] Connect GitHub repo if not already connected
- [ ] Create new Web Service:
  - Select repository: PLAYBEATFRONT-BK
  - Render detects render.yaml automatically
  - Click "Deploy"
  
- [ ] Set Environment Variables in Render Dashboard:
  ```
  MONGODB_URI=mongodb+srv://admin:***@playbeat.umqpdyx.mongodb.net/?appName=playbeat
  SECRET_KEY=***
  STRIPE_SECRET_KEY=***
  STRIPE_PUBLISHABLE_KEY=pk_test_***
  STRIPE_RESTRICTED_KEY=rk_test_***
  JAZZCASH_MERCHANTID=your_merchant_id
  JAZZCASH_PASSWORD=your_password
  JAZZCASH_HASHKEY=your_hash_key
  ALFAPAY_MERCHANT_USERNAME=your_username
  ALFAPAY_MERCHANT_PASSWORD=your_password
  MEZPAY_USERNAME=your_username
  MEZPAY_PASSWORD=your_password
  ```

- [ ] Get backend URL: `https://playbeat-backend.onrender.com` (or custom domain)
- [ ] Configure custom domain: `api.playbeat.digital`
  - Add CNAME record: `api.playbeat.digital → playbeat-backend.onrender.com`

- [ ] Test backend health:
  ```bash
  curl https://api.playbeat.digital/api/health
  ```

### Phase 3: Frontend Deployment (Vercel)

- [ ] Go to https://vercel.com/dashboard
- [ ] Import GitHub project
- [ ] Vercel auto-detects framework (React + Vite)
- [ ] Set build command: `npm run build`
- [ ] Set environment variables:
  ```
  VITE_API_URL=https://api.playbeat.digital
  VITE_STRIPE_PUBLISHABLE_KEY=pk_test_51TMvD1K7C5jIjluKwjpb2ZJzlZoH4I9hxOwbcZkXr15wNvaLLX7wHpfeTw6Eu09O0aZDCpWo6ptHvoQjhloFApJ200lkcdWYVw
  ```
- [ ] Deploy

- [ ] Add custom domain: `playbeat.digital`
  - Update nameservers to Vercel:
    - `ns1.vercel-dns.com`
    - `ns2.vercel-dns.com`

### Phase 4: Testing

- [ ] Test frontend:
  - [ ] Home page: `https://playbeat.digital/`
  - [ ] Admin: `https://playbeat.digital/admin`
  - [ ] Unified: `https://playbeat.digital/unified`

- [ ] Test backend:
  - [ ] Health: `curl https://api.playbeat.digital/api/health`
  - [ ] Payment methods: `curl https://api.playbeat.digital/api/payments/methods`

- [ ] Test payment methods:
  - [ ] Stripe with test card `4242 4242 4242 4242`
  - [ ] Bank details display for JazzCash
  - [ ] Bank details display for Alfalah
  - [ ] Bank details display for Meezan

### Phase 5: Stripe Webhook Setup

- [ ] Go to https://dashboard.stripe.com/webhooks
- [ ] Add endpoint: `https://api.playbeat.digital/api/payments/stripe/webhook`
- [ ] Select events:
  - [ ] payment_intent.succeeded
  - [ ] payment_intent.payment_failed
  - [ ] checkout.session.completed
- [ ] Copy signing secret to `STRIPE_WEBHOOK_SECRET` in Render

---

## 📁 File Structure After Setup

```
frontend/front/
├── dist/
│   └── public/
│       ├── index.html                  (Home - served at /)
│       ├── admin.html                  (Admin - served at /admin)
│       ├── playbeat_unified_fixed.html (Unified - served at /unified)
│       ├── shared.css
│       ├── shared.js
│       └── [other assets]
├── src/
│   ├── services/api.ts                (API client)
│   └── [other source files]
├── vercel.json                        (Vercel config)
└── .env.production                    (Frontend env vars)

backend/back/
├── config/
│   ├── stripe.js                      (Stripe config)
│   ├── jazzcash.js                    (JazzCash config)
│   ├── alfalah.js                     (Alfalah config)
│   └── meezan.js                      (Meezan config)
├── controllers/
│   ├── stripeController.js
│   ├── jazzcashController.js
│   ├── alfalahController.js
│   └── meezanController.js
├── routes/
│   ├── payments.js                    (All payment endpoints)
│   ├── orders.js
│   └── health.js
├── app.js                             (Express app)
├── server.js                          (Entry point)
├── render.yaml                        (Render deployment config)
└── .env                               (Backend env vars)
```

---

## 🔗 API Endpoints Summary

All endpoints use base URL: `https://api.playbeat.digital`

### Payment Methods
- `GET /api/payments/methods` — Get available payment methods

### Stripe
- `POST /api/payments/stripe/create-checkout` — Initiate Stripe payment
- `POST /api/payments/stripe/webhook` — Stripe webhooks
- `GET /api/payments/stripe/session/:id` — Get session details

### JazzCash (Pakistan)
- `POST /api/payments/jazzcash/initiate` — Initiate JazzCash payment
- `GET /api/payments/jazzcash/status/:ref` — Check payment status

### Bank Alfalah (Pakistan)
- `POST /api/payments/alfalah/initiate` — Initiate bank transfer
- `GET /api/payments/alfalah/bank-details` — Get account details

### Meezan Bank (Pakistan)
- `POST /api/payments/meezan/initiate` — Initiate bank transfer
- `GET /api/payments/meezan/bank-details` — Get account details

### Orders
- `POST /api/orders` — Create order
- `GET /api/orders/:id` — Get order details
- `PUT /api/orders/:id/status` — Update order status

### Health
- `GET /api/health` — Backend health check

---

## 🚀 One-Command Deployment

After setting up Vercel and Render:

```bash
cd "d:\playbeat digital"
git add .
git commit -m "Deploy: Complete PlayBeat Digital setup"
git push origin main
```

Both Vercel and Render will auto-deploy from GitHub!

---

## 📞 Support

### Issue: Frontend won't load
1. Check Vercel build logs
2. Verify `VITE_API_URL` environment variable
3. Check CORS settings in backend

### Issue: Payment methods not showing
1. Ensure backend is running (`curl https://api.playbeat.digital/api/health`)
2. Check browser console for API errors
3. Verify `VITE_API_URL` points to correct backend

### Issue: Stripe payment fails
1. Verify API keys in Render environment variables
2. Check Stripe dashboard for any issues
3. Test with Stripe test card: `4242 4242 4242 4242`

### Issue: Bank details not displaying
1. Check that config files have correct bank details
2. Verify backend is serving the endpoint
3. Test endpoint directly: `curl https://api.playbeat.digital/api/payments/alfalah/bank-details`

---

## 📚 Documentation

- [Complete Deployment Guide](./DEPLOYMENT_COMPLETE.md)
- [API Documentation](./API_DOCUMENTATION.md)
- [Repository Memory](./playbeat-payment-setup.md)

---

## ✨ Next Features (Optional)

- [ ] Add payment confirmation emails
- [ ] Implement admin dashboard for transaction monitoring
- [ ] Add refund processing
- [ ] Implement loyalty points system
- [ ] Add multi-currency support
- [ ] Implement payment analytics

---

**Created**: 2024-01-10
**Status**: ✅ Ready for Deployment
**Domains**: playbeat.digital (Frontend), api.playbeat.digital (Backend)
