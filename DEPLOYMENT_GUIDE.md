# PlayBeat Digital - Separate Deployment Structure

## Repository Organization

Your project is now organized into two separate repositories for independent deployment:

### 1. **Frontend Repository** (Folder 1)
- **URL**: https://github.com/playbeatdigitaltd/12330.git
- **Deployment**: Vercel
- **Tech Stack**: React 19 + Vite + TypeScript + ShadcN UI
- **Branch**: `main`
- **Build Output**: `dist/`

**Files:**
- `package.json` - Frontend dependencies
- `vite.config.ts` - Vite build config
- `.env.production` - Production environment variables
- `src/` - React source code
- `public/` - Static assets

**Deployment Command (Vercel):**
```bash
cd 1 && pnpm install && pnpm build
```

### 2. **Backend Repository** (Folder 2)
- **URL**: https://github.com/playbeatdigital-tech/supreme12235.git
- **Deployment**: Render
- **Tech Stack**: Express.js + Node.js + MongoDB
- **Branch**: `main`
- **Port**: 3000

**Files:**
- `package.json` - Backend dependencies
- `server.js` - Express server entry point
- `app.js` - Express app configuration
- `config/` - Payment gateway configs
- `controllers/` - Payment & order handlers
- `routes/` - API endpoints
- `.env` - Environment variables (MongoDB URI, API keys)

**Deployment Command (Render):**
```bash
cd 2 && npm install --legacy-peer-deps && npm start
```

---

## Deployment Steps

### Frontend on Vercel

1. **In Vercel Dashboard:**
   - Import project: `https://github.com/playbeatdigitaltd/12330`
   - Framework: Other (Vite)
   - Root Directory: `.`
   - Build Command: `pnpm install && pnpm build`
   - Output Directory: `dist`
   - Install Command: `pnpm install`

2. **Environment Variables:**
   ```
   VITE_API_URL=https://playbeat-backend.onrender.com
   VITE_STRIPE_PUBLISHABLE_KEY=pk_test_51TMvD1K7C5jIjluKwjpb2ZJzlZoH4I9hxOwbcZkXr15wNvaLLX7wHpfeTw6Eu09O0aZDCpWo6ptHvoQjhloFApJ200lkcdWYVw
   ```

3. **Deploy:**
   - Vercel auto-deploys on push to `main`
   - URL: `https://playbeat.digital` (after DNS setup)

### Backend on Render

1. **In Render Dashboard:**
   - Create New Web Service
   - Connect: `https://github.com/playbeatdigital-tech/supreme12235`
   - Environment: Node
   - Build Command: `npm install --legacy-peer-deps`
   - Start Command: `npm start`

2. **Environment Variables:**
   ```
   MONGODB_URI=mongodb+srv://playbeatdigital:A3bE17J0zUPZQ5FE@playbeatdep.c1rxiyu.mongodb.net/?appName=playbeatdep
   JWT_SECRET=RLTJLDFGLDJFGVC
   PORT=3000
   NODE_ENV=production
   STRIPE_ACCOUNT_ID=your_stripe_account_id
   STRIPE_PUBLISHABLE_KEY=your_stripe_publishable_key
   STRIPE_SECRET_KEY=your_stripe_secret_key
   STRIPE_RESTRICTED_KEY=your_stripe_restricted_key
   FRONTEND_URL=https://playbeat.digital
   ```

3. **Deploy:**
   - Render auto-deploys on push to `main`
   - URL: `https://playbeat-backend.onrender.com`

---

## API Endpoints (Backend)

All endpoints accessible at: `https://playbeat-backend.onrender.com/api`

### Payments
- `GET /payments/methods` - List available payment gateways
- `POST /payments/stripe/create-checkout` - Create Stripe session
- `POST /payments/stripe/webhook` - Stripe webhook
- `POST /payments/jazzcash/initiate` - JazzCash payment
- `POST /payments/alfalah/initiate` - Bank Alfalah transfer
- `POST /payments/meezan/initiate` - Meezan Bank transfer

### Orders
- `GET /orders` - List orders
- `POST /orders` - Create order
- `GET /orders/:id` - Get order details

### Health
- `GET /health` - Health check

---

## Local Development

### Frontend
```bash
cd 1
pnpm install
pnpm dev  # runs on http://localhost:8080
```

### Backend
```bash
cd 2
npm install --legacy-peer-deps
npm run dev  # runs on http://localhost:3000
```

---

## Next Steps

1. ✅ Push frontend to 12330 repo
2. ✅ Push backend to supreme12235 repo
3. ⏳ Connect 12330 repo to Vercel
4. ⏳ Connect supreme12235 repo to Render
5. ⏳ Set environment variables on both platforms
6. ⏳ Deploy and test

---

**Last Updated**: 2026-06-09
