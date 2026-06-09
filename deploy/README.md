# Deploying PlayBeat

This file contains quick deployment instructions for deploying the frontend to Vercel and the backend to Render (with MongoDB).

Prereqs
- A Git hosting service (GitHub/GitLab/Bitbucket) and a remote repository
- Vercel account (https://vercel.com)
- Render account (https://render.com)
- MongoDB Atlas account (https://www.mongodb.com/cloud/atlas) or a running `mongod` instance

Steps (summary)

1) Prepare repository

```bash
# from repository root
git init          # if not already a repo
git add .
git commit -m "Add project and deployment config"
# create remote on GitHub and then:
git remote add origin <YOUR_REPO_URL>
git push -u origin main
```

2) Deploy frontend to Vercel

- Using the UI: Import project and set the Project Root to `1`. Build command: `npm run build:prod`. Output directory: `dist`.
- Or using Vercel CLI:

```bash
npm i -g vercel
cd 1
vercel --prod --scope team_OHrUKs5OeS1mGpkw4FekDHSp
```

> If you want to bind this project to your Vercel team, you can also add `"scope": "team_OHrUKs5OeS1mGpkw4FekDHSp"` to `vercel.json`.

3) Deploy backend to Render

- In Render, create a new Web Service and connect your Git repo.
- Use branch `main` (or your branch).
- Set Build Command to: `cd 2 && npm install --legacy-peer-deps`
- Set Start Command to: `cd 2 && npm start`
- Add environment variables in Render:
  - `MONGODB_URI` — MongoDB connection string (Atlas or your host)
  - `STRIPE_SECRET_KEY` — your Stripe secret key (optional)
  - `FRONTEND_URL` — Vercel URL for the frontend

4) MongoDB (Atlas)

- Create a free cluster in MongoDB Atlas, create a database user, and whitelist Render's outbound IPs (or use 0.0.0.0/0 for testing).
- Copy the connection string and set it as `MONGODB_URI` in Render.

Notes
- `vercel.json` at the repo root is configured to build the frontend in folder `1` and use the `dist` output.
- `render.yaml` is a template you can use with Render's Infrastructure as Code (replace `<REPO_URL>` and env values).
- Do not commit secrets to the repo — use Render/Vercel environment variables.
