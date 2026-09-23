# NFI — Nusantara Fiscal Intelligence

**Sistem Digital Nasional untuk Menghubungkan Anggaran, Biaya Pelayanan, Aset, dan Outcome Publik.**

> "Platform Fiscal Intelligence untuk mendukung pengambilan keputusan berbasis data dan meningkatkan value for money belanja publik."

---

## Cara Menjalankan

### Opsi 1 — Langsung di Browser (Zero Config)
Buka `public/index.html` langsung di browser modern. Tidak memerlukan server atau instalasi apapun.

### Opsi 2 — Local Dev Server
```bash
cd nfi-project
npx serve public
# buka http://localhost:3000
```

### Opsi 3 — Python HTTP Server
```bash
cd nfi-project
python3 -m http.server 8080 --directory public
# buka http://localhost:8080
```

### Opsi 4 — Deploy ke Vercel
```bash
npm i -g vercel
cd nfi-project
vercel --public
```

---

## Struktur Proyek

```
nfi-project/
├── public/
│   └── index.html              # Aplikasi utama (self-contained, ~100KB)
├── src/
│   ├── data/
│   │   └── dummy-dataset.json  # Data dummy nasional (regions, budgets, assets, outcomes)
│   ├── components/
│   │   └── COMPONENTS.md       # Panduan komponen UI & design system
│   └── api/
│       └── API_SCHEMA.md       # Skema REST API & database
├── docs/
│   └── ARCHITECTURE.md         # Arsitektur sistem full-stack
└── README.md
```

---

## Halaman & Fitur

| Halaman             | Deskripsi                                                      |
|---------------------|----------------------------------------------------------------|
| Dashboard Nasional  | KPI fiskal (Rp 3.325T APBN), tren anggaran, ranking VFM       |
| Fiscal Map          | Peta Indonesia interaktif, klik pulau untuk detail wilayah     |
| Anggaran            | Serapan per kementerian, progress realisasi, status tracking   |
| Pendidikan          | 218K sekolah, biaya/siswa, inventaris, modal detail aset       |
| Kesehatan           | Faskes, outcome JKN, rasio dokter, kepuasan layanan            |
| Infrastruktur Jalan | Kondisi jalan, cost/km, deteksi anomali belanja                |
| Irigasi             | Jaringan irigasi, produktivitas lahan                          |
| Air Bersih          | Cakupan akses, jaringan PDAM, kesenjangan wilayah              |
| Asset Management    | Inventaris aset pemerintah, filter kondisi/jenis/wilayah       |
| Outcome Analysis    | IPM, kemiskinan, Gini ratio, pertumbuhan PDB                   |
| Fiscal Intelligence | Deteksi anomali AI + estimasi potensi efisiensi belanja        |
| Administration      | Manajemen pengguna & level akses (Nasional/Provinsi/Kab-Kota)  |

---

## Design System

| Token          | Nilai          | Penggunaan                   |
|----------------|----------------|------------------------------|
| Navy           | `#0B1F3A`      | Sidebar, heading, chart      |
| Gold           | `#C9A227`      | Accent, VFM score, highlight |
| Background     | `#F8FAFC`      | Page background              |
| Surface        | `#FFFFFF`      | Card background              |
| Green          | `#16A34A`      | Status baik, tren positif    |
| Amber          | `#D97706`      | Warning, perlu perhatian     |
| Red            | `#DC2626`      | Alert, kondisi buruk         |
| Font           | Inter          | Google Fonts                 |

---

## Upgrade ke Production Stack

Untuk deployment produksi tingkat Kementerian, upgrade ke:

```
Frontend:   Next.js 15 + TypeScript + Tailwind CSS + shadcn/ui
Backend:    Next.js API Routes atau Laravel 11
Database:   PostgreSQL 16 + PostGIS (untuk data spasial)
ORM:        Prisma
Map:        Mapbox GL JS (Indonesia detail shapefile)
Charts:     Recharts atau Observable Plot
Auth:       NextAuth.js + SSO Kemenkeu
Deploy:     Vercel + Supabase (atau on-premise)
```

Database schema tersedia di `src/api/API_SCHEMA.md`.

---

## Roadmap

### Phase 1 — MVP (Selesai)
- [x] Dashboard nasional
- [x] Fiscal Map interaktif
- [x] 8 modul sektor
- [x] Asset Management
- [x] Fiscal Intelligence Engine

### Phase 2
- [ ] Integrasi Mapbox GL JS
- [ ] Import data APBD via Excel
- [ ] Koneksi PostgreSQL + PostGIS
- [ ] SSO Kemenkeu / BPKP

### Phase 3
- [ ] AI Fiscal Analyst (LLM)
- [ ] Prediksi kebutuhan maintenance
- [ ] Deteksi potensi fraud belanja
- [ ] Export PDF/Excel laporan

---

## Lisensi
Internal Government Use — Kementerian Keuangan Republik Indonesia
