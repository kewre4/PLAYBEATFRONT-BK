# PlayBeat Digital - Vercel Deployment Guide

## Frontend Deployment (Vercel)

### Prerequisites
- GitHub repository connected to Vercel
- Node.js 18+ (Vercel uses this)
- pnpm package manager

### Environment Variables (Set in Vercel Dashboard)
```
VITE_API_URL=https://playbeat-backend.onrender.com
VITE_STRIPE_PUBLISHABLE_KEY=pk_test_51TMvD1K7C5jIjluKwjpb2ZJzlZoH4I9hxOwbcZkXr15wNvaLLX7wHpfeTw6Eu09O0aZDCpWo6ptHvoQjhloFApJ200lkcdWYVw
```

### Build Configuration
- **Root Directory**: `/` (monorepo root)
- **Build Command**: `cd 1 && pnpm install && pnpm build`
- **Output Directory**: `1/dist`
- **Node Version**: 18.x or later

### Deploy Steps

1. **Push to GitHub**
   ```bash
   git add .
   git commit -m "Prepare Vercel deployment"
   git push origin main
   ```

2. **In Vercel Dashboard**
   - Connect your GitHub repo `playbeatdigitaltd/12330`
   - Set Environment Variables (see above)
   - Override Build Settings:
     - Framework: Other (Vite)
     - Build Command: `cd 1 && pnpm install && pnpm build`
     - Output Directory: `1/dist`
     - Node.js: 18.x

3. **Deploy**
   - Vercel will auto-build on push to `main` branch
   - Frontend serves at: https://playbeat.digital (after DNS setup)

### Verify Deployment

After deployment, test:
- Frontend loads: https://playbeat.digital
- API calls work: `https://playbeat-backend.onrender.com/api/payments/methods`
- Payment methods load in checkout
- Stripe keys are accessible

### Troubleshooting

**Build fails with pnpm errors:**
- Ensure `pnpm-lock.yaml` is committed
- Enable pnpm in Vercel settings (Project Settings → Build & Development)

**API not found errors:**
- Check `VITE_API_URL` env var points to correct Render backend
- Verify Render backend is running

**Static file issues:**
- Verify `base: "/"` in `vite.config.ts`
- Check `dist/` folder has all files

### Files Modified
- ✅ `1/package.json` - Fixed build script
- ✅ `1/.env.production` - Production API URL
- ✅ `vercel.json` - Build configuration
- ✅ `.vercelignore` - Ignore unnecessary files
- ✅ `1/.vercelignore` - Frontend ignore rules

### Commands Reference

```bash
# Local development
cd 1 && pnpm dev

# Build for production
cd 1 && pnpm build

# Preview production build
cd 1 && pnpm preview

# Lint check
cd 1 && pnpm lint
```

---
**Last Updated**: 2026-06-09
