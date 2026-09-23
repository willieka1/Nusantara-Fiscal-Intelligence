# NFI Component Library

Panduan komponen UI dan design tokens yang digunakan di platform NFI.

---

## Design Tokens

```css
:root {
  /* Colors */
  --navy:      #0B1F3A;  /* Primary — sidebar, headings */
  --navy-mid:  #122845;
  --navy-lite: #1A3A5C;
  --gold:      #C9A227;  /* Accent — VFM, highlights */
  --gold-lite: #E2BD4E;
  --gold-dim:  #9A7C1E;

  /* Backgrounds */
  --bg:        #F8FAFC;  /* Page background */
  --surface:   #FFFFFF;  /* Card surface */
  --surface2:  #F1F5F9;  /* Secondary surface */

  /* Borders */
  --border:    #E2E8F0;
  --border2:   #CBD5E1;

  /* Text */
  --text:      #0F172A;
  --text-mid:  #334155;
  --text-low:  #64748B;
  --text-xlow: #94A3B8;

  /* Status */
  --green:     #16A34A;  /* Baik, positif */
  --amber:     #D97706;  /* Warning */
  --red:       #DC2626;  /* Alert, kritis */
  --blue:      #2563EB;  /* Info */
}
```

---

## Components

### StatCard
Kartu KPI dengan accent line bawah dan delta indicator.

```html
<div class="stat-card gold">
  <div class="stat-label"><div class="dot"></div> Label KPI</div>
  <div class="stat-value">Rp 3.325 <span class="stat-unit">T</span></div>
  <div class="stat-delta up">▲ +8,4%</div>
</div>
```
Modifier: `.gold` `.green` `.blue` `.amber` `.navy`

---

### Badge
Status pill untuk tabel dan kartu.

```html
<span class="badge badge-green">Baik</span>
<span class="badge badge-amber">Sedang</span>
<span class="badge badge-red">Kritis</span>
<span class="badge badge-blue">Info</span>
<span class="badge badge-navy">Internal</span>
```

---

### ProgressBar
Bar visualisasi persentase.

```html
<div class="progress-wrap">
  <div class="progress-bar">
    <div class="progress-fill" style="width:75%;background:#0B1F3A"></div>
  </div>
  <span class="progress-label">75%</span>
</div>
```

---

### IntelCard
Kartu anomali Fiscal Intelligence Engine.

```html
<div class="intel-card">
  <div class="intel-card-header">
    <div class="intel-severity high"></div>  <!-- high / medium / low -->
    <div class="intel-card-title">Judul Temuan</div>
    <div class="intel-card-meta">Sektor · Tanggal</div>
  </div>
  <div class="intel-card-body">
    <div class="intel-card-text">Deskripsi anomali...</div>
    <div class="intel-actions">
      <button class="intel-action-btn primary">Aksi Utama</button>
      <button class="intel-action-btn">Aksi Sekunder</button>
    </div>
  </div>
</div>
```

---

### InsightBanner
Banner notifikasi di atas dashboard.

```html
<div class="insight-banner">
  <div class="insight-icon"><!-- SVG icon --></div>
  <div>
    <div class="insight-title">Judul insight</div>
    <div class="insight-text">Teks penjelasan singkat...</div>
    <div class="insight-chips">
      <span class="insight-chip warn">Tag Warning</span>
      <span class="insight-chip">Tag Biasa</span>
    </div>
  </div>
</div>
```

---

### KpiRow
Row horizontal KPI cells.

```html
<div class="kpi-row">
  <div class="kpi-cell">
    <div class="kpi-cell-label">Label</div>
    <div class="kpi-cell-value">Nilai</div>
    <div class="kpi-cell-delta"><span class="up">+X%</span></div>
  </div>
  <!-- ...lebih banyak cell -->
</div>
```

---

### DataTable
Tabel data dengan hover state.

```html
<table class="data-table">
  <thead>
    <tr>
      <th>Kolom 1</th>
      <th>Kolom 2</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Data 1</td>
      <td>Data 2</td>
    </tr>
  </tbody>
</table>
```

---

### Navigation
Sidebar nav item.

```html
<div class="nav-item active" onclick="showPage('id')">
  <svg class="nav-icon"><!-- icon --></svg>
  Label Menu
  <span class="nav-badge">BADGE</span>  <!-- opsional -->
</div>
```
