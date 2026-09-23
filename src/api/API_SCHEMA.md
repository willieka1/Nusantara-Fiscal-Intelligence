# NFI API Schema

Dokumentasi REST API dan skema database PostgreSQL untuk NFI Production.

---

## Database Schema (PostgreSQL + PostGIS)

```sql
-- Tabel regions
CREATE TABLE regions (
  id          SERIAL PRIMARY KEY,
  province    VARCHAR(100) NOT NULL,
  city        VARCHAR(100) NOT NULL,
  population  INTEGER,
  latitude    DECIMAL(10, 7),
  longitude   DECIMAL(10, 7),
  geom        GEOMETRY(Point, 4326),  -- PostGIS
  created_at  TIMESTAMP DEFAULT NOW(),
  updated_at  TIMESTAMP DEFAULT NOW()
);

-- Tabel budgets
CREATE TABLE budgets (
  id                SERIAL PRIMARY KEY,
  region_id         INTEGER REFERENCES regions(id),
  sector            VARCHAR(50) NOT NULL,
  year              INTEGER NOT NULL,
  allocated_budget  BIGINT NOT NULL,   -- dalam Rupiah
  realized_budget   BIGINT,
  created_at        TIMESTAMP DEFAULT NOW()
);

-- Tabel assets
CREATE TABLE assets (
  id               VARCHAR(20) PRIMARY KEY,  -- format: AST-XXXXXXX
  region_id        INTEGER REFERENCES regions(id),
  asset_type       VARCHAR(50),   -- gedung, jalan, jembatan, irigasi, dll
  asset_name       VARCHAR(200) NOT NULL,
  value            BIGINT,        -- nilai aset dalam Rupiah
  condition        VARCHAR(30),   -- baik, sedang, rusak_ringan, rusak_berat
  build_year       INTEGER,
  maintenance_cost BIGINT,        -- biaya maintenance per tahun
  latitude         DECIMAL(10, 7),
  longitude        DECIMAL(10, 7),
  geom             GEOMETRY(Point, 4326),
  last_inspection  DATE,
  created_at       TIMESTAMP DEFAULT NOW(),
  updated_at       TIMESTAMP DEFAULT NOW()
);

-- Tabel outcomes
CREATE TABLE outcomes (
  id         SERIAL PRIMARY KEY,
  region_id  INTEGER REFERENCES regions(id),
  indicator  VARCHAR(100) NOT NULL,  -- ipm, angka_kemiskinan, vfm_score, dll
  value      DECIMAL(10, 4) NOT NULL,
  year       INTEGER NOT NULL,
  created_at TIMESTAMP DEFAULT NOW()
);

-- Tabel users
CREATE TABLE users (
  id           SERIAL PRIMARY KEY,
  name         VARCHAR(100) NOT NULL,
  email        VARCHAR(200) UNIQUE NOT NULL,
  role         VARCHAR(100),
  access_level VARCHAR(20),  -- nasional, provinsi, kab_kota
  region_id    INTEGER REFERENCES regions(id),  -- null jika nasional
  is_active    BOOLEAN DEFAULT TRUE,
  created_at   TIMESTAMP DEFAULT NOW()
);

-- Tabel intelligence_alerts
CREATE TABLE intelligence_alerts (
  id               SERIAL PRIMARY KEY,
  severity         VARCHAR(10),   -- high, medium, low
  sector           VARCHAR(50),
  title            VARCHAR(200),
  description      TEXT,
  potential_saving BIGINT,
  status           VARCHAR(20) DEFAULT 'active',  -- active, resolved, dismissed
  resolved_at      TIMESTAMP,
  created_at       TIMESTAMP DEFAULT NOW()
);

-- Indeks untuk performa
CREATE INDEX idx_budgets_region_year  ON budgets(region_id, year);
CREATE INDEX idx_budgets_sector       ON budgets(sector);
CREATE INDEX idx_assets_region        ON assets(region_id);
CREATE INDEX idx_assets_condition     ON assets(condition);
CREATE INDEX idx_outcomes_region_year ON outcomes(region_id, year);
CREATE INDEX idx_outcomes_indicator   ON outcomes(indicator);
CREATE UNIQUE INDEX idx_outcomes_unique ON outcomes(region_id, indicator, year);

-- PostGIS spatial index
CREATE INDEX idx_assets_geom   ON assets USING GIST(geom);
CREATE INDEX idx_regions_geom  ON regions USING GIST(geom);
```

---

## REST API Endpoints

### Regions
```
GET  /api/regions                    # Daftar semua wilayah
GET  /api/regions/:id                # Detail satu wilayah
GET  /api/regions/:id/summary        # Ringkasan fiskal wilayah
GET  /api/regions/:id/budgets        # Anggaran per wilayah
GET  /api/regions/:id/assets         # Aset per wilayah
GET  /api/regions/:id/outcomes       # Outcome per wilayah
```

### Budgets
```
GET  /api/budgets?year=2025&sector=pendidikan   # Filter anggaran
GET  /api/budgets/national-summary              # Ringkasan nasional
GET  /api/budgets/sector-distribution           # Distribusi per sektor
GET  /api/budgets/trend?from=2020&to=2025       # Tren historis
POST /api/budgets                               # Tambah data (admin)
PUT  /api/budgets/:id                           # Update data (admin)
```

### Assets
```
GET  /api/assets?region_id=2&condition=baik     # Filter aset
GET  /api/assets/:id                            # Detail aset
GET  /api/assets/geojson                        # GeoJSON untuk peta
GET  /api/assets/summary                        # Ringkasan kondisi aset
POST /api/assets                                # Tambah aset
PUT  /api/assets/:id                            # Update aset
```

### Outcomes
```
GET  /api/outcomes?indicator=ipm&year=2025      # Filter outcome
GET  /api/outcomes/vfm-ranking                  # Ranking VFM provinsi
```

### Intelligence
```
GET  /api/intelligence/alerts                   # Daftar anomali aktif
GET  /api/intelligence/alerts/:id               # Detail anomali
POST /api/intelligence/run                      # Jalankan analisis baru
PUT  /api/intelligence/alerts/:id/resolve       # Tandai selesai
```

### Dashboard
```
GET  /api/dashboard/national                    # Semua KPI nasional
GET  /api/dashboard/fiscal-map                  # Data untuk peta heatmap
```

---

## Contoh Response

### GET /api/dashboard/national
```json
{
  "meta": { "year": 2025, "generated_at": "2025-09-23T07:00:00Z" },
  "kpi": {
    "total_apbn": 3325100000000000,
    "total_realisasi": 2917400000000000,
    "serapan_pct": 87.7,
    "total_assets": 11842000000000000,
    "wilayah_terintegrasi": 514,
    "vfm_index": 73.4
  },
  "sector_distribution": [
    { "sector": "infrastruktur", "amount": 856200000000000, "pct": 25.7 },
    { "sector": "pendidikan",    "amount": 665000000000000, "pct": 20.0 },
    { "sector": "kesehatan",     "amount": 531300000000000, "pct": 16.0 }
  ],
  "vfm_ranking": [
    { "province": "DI Yogyakarta", "vfm_score": 88.4, "trend": "+3.2" },
    { "province": "Bali",          "vfm_score": 85.1, "trend": "+2.8" }
  ]
}
```

### GET /api/intelligence/alerts
```json
{
  "total": 12,
  "active": 12,
  "resolved_ytd": 47,
  "total_potential_saving": 18400000000000,
  "alerts": [
    {
      "id": 1,
      "severity": "high",
      "sector": "infrastruktur",
      "title": "Kesenjangan Anggaran-Kualitas Jalan Nasional",
      "potential_saving": 7200000000000,
      "status": "active",
      "created_at": "2025-09-20"
    }
  ]
}
```

---

## Prisma Schema (ORM)

```prisma
// prisma/schema.prisma
generator client {
  provider = "prisma-client-js"
}

datasource db {
  provider = "postgresql"
  url      = env("DATABASE_URL")
}

model Region {
  id         Int       @id @default(autoincrement())
  province   String
  city       String
  population Int?
  latitude   Float?
  longitude  Float?
  budgets    Budget[]
  assets     Asset[]
  outcomes   Outcome[]
  users      User[]
}

model Budget {
  id               Int      @id @default(autoincrement())
  regionId         Int      @map("region_id")
  sector           String
  year             Int
  allocatedBudget  BigInt   @map("allocated_budget")
  realizedBudget   BigInt?  @map("realized_budget")
  region           Region   @relation(fields: [regionId], references: [id])
}

model Asset {
  id              String   @id
  regionId        Int      @map("region_id")
  assetType       String?  @map("asset_type")
  assetName       String   @map("asset_name")
  value           BigInt?
  condition       String?
  buildYear       Int?     @map("build_year")
  maintenanceCost BigInt?  @map("maintenance_cost")
  latitude        Float?
  longitude       Float?
  region          Region   @relation(fields: [regionId], references: [id])
}

model Outcome {
  id        Int    @id @default(autoincrement())
  regionId  Int    @map("region_id")
  indicator String
  value     Float
  year      Int
  region    Region @relation(fields: [regionId], references: [id])
}

model User {
  id          Int      @id @default(autoincrement())
  name        String
  email       String   @unique
  role        String?
  accessLevel String?  @map("access_level")
  regionId    Int?     @map("region_id")
  isActive    Boolean  @default(true) @map("is_active")
  region      Region?  @relation(fields: [regionId], references: [id])
}
```
