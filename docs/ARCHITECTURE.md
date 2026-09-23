# NFI — Arsitektur Sistem

## Overview

```
┌─────────────────────────────────────────────────────────┐
│                    PRESENTATION LAYER                   │
│                                                         │
│   ┌──────────────────────────────────────────────────┐  │
│   │           NFI Web Application                    │  │
│   │      (Next.js 15 + TypeScript + Tailwind)        │  │
│   │                                                  │  │
│   │  Dashboard │ Fiscal Map │ Modul Sektor │ Intel   │  │
│   └──────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────┘
                           │ REST API / tRPC
┌─────────────────────────────────────────────────────────┐
│                   APPLICATION LAYER                     │
│                                                         │
│  ┌───────────────┐  ┌───────────────┐  ┌────────────┐  │
│  │   API Routes  │  │  Intelligence │  │   Auth     │  │
│  │  (Next.js)    │  │   Engine      │  │  (SSO)     │  │
│  └───────────────┘  └───────────────┘  └────────────┘  │
└─────────────────────────────────────────────────────────┘
                           │
┌─────────────────────────────────────────────────────────┐
│                     DATA LAYER                          │
│                                                         │
│  ┌─────────────────────┐   ┌─────────────────────────┐  │
│  │  PostgreSQL 16      │   │    Redis Cache           │  │
│  │  + PostGIS          │   │    (dashboard KPI)       │  │
│  │  (Prisma ORM)       │   └─────────────────────────┘  │
│  └─────────────────────┘                               │
└─────────────────────────────────────────────────────────┘
```

## Environment Variables

```env
# .env.local
DATABASE_URL="postgresql://user:pass@host:5432/nfi_db"
NEXTAUTH_SECRET="..."
NEXTAUTH_URL="https://nfi.kemenkeu.go.id"
MAPBOX_TOKEN="pk.eyJ1..."
REDIS_URL="redis://..."
```

## Deployment

### Vercel (Recommended)
```bash
vercel env pull .env.local
vercel deploy --prod
```

### Docker
```dockerfile
FROM node:20-alpine
WORKDIR /app
COPY package*.json .
RUN npm ci
COPY . .
RUN npm run build
CMD ["npm", "start"]
```
