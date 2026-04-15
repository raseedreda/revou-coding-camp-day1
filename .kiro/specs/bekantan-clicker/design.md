# Design Document: Bekantan Clicker

## Overview

Bekantan Clicker adalah single-page clicker web app yang dibangun sepenuhnya di sisi klien menggunakan HTML5, Tailwind CSS, dan Vanilla JavaScript. Pengguna mengklik gambar bekantan (proboscis monkey) untuk mengumpulkan poin, sementara karakter kucing menampilkan reaksi acak sebagai elemen hiburan. Tidak ada backend, tidak ada state persistence ke server — semua state hidup di memori browser selama sesi berlangsung.

Tujuan desain utama:
- Sederhana dan responsif di semua ukuran layar (320px–1920px)
- Umpan balik visual yang jelas dan menyenangkan pada setiap klik
- Kode yang mudah dipahami dan dimodifikasi tanpa framework

---

## Architecture

Aplikasi terdiri dari satu file HTML tunggal yang memuat semua aset inline atau via CDN. Tidak ada build step, tidak ada bundler.

```
index.html
├── <head>
│   ├── Tailwind CSS (CDN)
│   └── Inline <style> untuk animasi custom
└── <body>
    ├── ScoreDisplay (#score-display)
    ├── BekantanClickTarget (#bekantan)
    ├── CatReactionDisplay (#cat-reaction)
    └── ResetButton (#reset-btn)
    └── <script> (inline JS, module pattern)
```

### Alur Data

```
Click_Event (user)
      │
      ▼
handleBekantanClick()
      ├── incrementScore()        → update state.score
      ├── renderScore()           → update DOM #score-display
      ├── showClickFeedback()     → animasi +1 & bounce pada bekantan
      ├── pickRandomReaction()    → pilih dari reactionPool (≠ previous)
      └── renderCatReaction()     → update DOM #cat-reaction + animasi
```

### Prinsip Arsitektur

- **State terpusat**: Satu objek `state` menyimpan `score` dan `lastReaction`.
- **Fungsi murni untuk logika**: `pickRandomReaction(pool, lastReaction)` adalah fungsi murni yang tidak menyentuh DOM.
- **Pemisahan render dari logika**: Fungsi `render*` hanya membaca state dan memperbarui DOM.
- **Tidak ada dependensi eksternal** selain Tailwind CSS via CDN.

---

## Components and Interfaces

### 1. State Object

```js
const state = {
  score: 0,           // integer, nilai skor saat ini
  lastReaction: null  // string | null, reaksi terakhir yang ditampilkan
};
```

### 2. `reactionPool` (Array\<string\>)

Array berisi minimal 8 string reaksi kucing. Contoh:

```js
const reactionPool = [
  "😺 Wah, keren!",
  "😹 Hahaha!",
  "😾 Hmph...",
  "🙀 Astaga!",
  "😸 Bagus sekali!",
  "😼 Lumayan...",
  "😻 Aku suka ini!",
  "🐱 Terus klik!"
];
```

### 3. Fungsi Inti

#### `incrementScore(state) → state`
Menambah `state.score` sebesar 1. Memodifikasi state secara langsung (in-place mutation) karena ini adalah aplikasi sederhana tanpa immutability requirement.

#### `pickRandomReaction(pool, lastReaction) → string`
Fungsi murni. Memilih satu reaksi acak dari `pool` yang berbeda dari `lastReaction`. Jika `pool` hanya memiliki 1 entri, kembalikan entri tersebut.

```
Input:  pool: string[], lastReaction: string | null
Output: string (reaksi yang dipilih)
Constraint: result !== lastReaction (kecuali pool.length === 1)
```

#### `renderScore(score) → void`
Memperbarui teks elemen `#score-display` dengan nilai `score` sebagai integer.

#### `showClickFeedback(event) → void`
- Menambahkan class animasi bounce/scale pada `#bekantan` sementara.
- Membuat elemen `+1` di posisi klik, lalu menghapusnya setelah animasi selesai.

#### `renderCatReaction(reaction) → void`
Memperbarui teks elemen `#cat-reaction` dan memicu animasi transisi (fade/bounce).

#### `handleBekantanClick(event) → void`
Orkestrator utama. Memanggil semua fungsi di atas secara berurutan.

#### `handleReset() → void`
Mengatur `state.score = 0` dan `state.lastReaction = null`, lalu merender ulang score dan menampilkan reaksi netral pada kucing.

### 4. DOM Elements

| ID / Selector       | Peran                                              |
|---------------------|----------------------------------------------------|
| `#score-value`      | Menampilkan angka skor                             |
| `#score-label`      | Label teks "Klik" atau "Poin"                      |
| `#bekantan`         | Gambar/ilustrasi bekantan, target klik utama       |
| `#cat-container`    | Wrapper karakter kucing                            |
| `#cat-reaction`     | Teks reaksi kucing                                 |
| `#reset-btn`        | Tombol reset skor                                  |
| `.plus-one`         | Elemen dinamis "+1" yang muncul saat klik          |

---

## Data Models

### Score

```
score: integer
  - Nilai awal: 0
  - Bertambah 1 setiap Click_Event
  - Direset ke 0 saat reset
  - Ditampilkan sebagai angka bulat tanpa desimal
```

### Cat Reaction

```
reaction: string
  - Dipilih dari reactionPool (array of string)
  - Minimal 8 entri unik
  - Tidak boleh sama dengan reaksi sebelumnya (jika pool > 1)
  - Direset ke null / reaksi netral saat reset
```

### Milestone

```
milestone: boolean (derived)
  - true jika score > 0 && score % 10 === 0
  - Digunakan untuk memicu efek visual khusus pada score display
```

---

## Correctness Properties

*A property is a characteristic or behavior that should hold true across all valid executions of a system — essentially, a formal statement about what the system should do. Properties serve as the bridge between human-readable specifications and machine-verifiable correctness guarantees.*

### Property 1: Klik selalu menambah skor sebesar 1

*For any* nilai skor awal yang valid (integer ≥ 0), setelah satu Click_Event, nilai skor harus bertambah tepat 1.

**Validates: Requirements 2.1**

### Property 2: Reaksi kucing selalu berbeda dari reaksi sebelumnya

*For any* Reaction_Pool dengan lebih dari 1 entri dan reaksi sebelumnya yang valid, `pickRandomReaction` harus mengembalikan reaksi yang berbeda dari reaksi sebelumnya.

**Validates: Requirements 3.5**

### Property 3: Reaksi kucing selalu berasal dari Reaction_Pool

*For any* pemanggilan `pickRandomReaction`, hasil yang dikembalikan harus selalu merupakan salah satu elemen dari `reactionPool`.

**Validates: Requirements 3.2, 3.3**

### Property 4: Reset selalu mengembalikan skor ke 0

*For any* nilai skor (termasuk 0, nilai besar, atau nilai setelah banyak klik), setelah operasi reset, nilai skor harus tepat 0.

**Validates: Requirements 6.2**

### Property 5: Skor ditampilkan sebagai integer tanpa desimal

*For any* nilai skor integer, representasi string yang dirender ke DOM harus tidak mengandung karakter titik (`.`) atau koma (`,`) sebagai pemisah desimal.

**Validates: Requirements 4.1**

### Property 6: Milestone terdeteksi pada setiap kelipatan 10

*For any* nilai skor yang merupakan kelipatan 10 dan lebih besar dari 0, fungsi deteksi milestone harus mengembalikan `true`.

**Validates: Requirements 4.3**

---

## Error Handling

Karena aplikasi ini berjalan sepenuhnya di sisi klien tanpa backend, skenario error yang perlu ditangani terbatas:

| Skenario | Penanganan |
|---|---|
| `reactionPool` kosong | Guard check: jika pool kosong, tampilkan reaksi default `"😺"` |
| `reactionPool` hanya 1 entri | `pickRandomReaction` mengembalikan entri tersebut meskipun sama dengan `lastReaction` |
| Klik sangat cepat (spam klik) | Tidak perlu throttle — setiap klik valid dan ditangani secara sinkron |
| Animasi belum selesai saat klik berikutnya | Animasi di-reset (class dihapus lalu ditambahkan kembali) agar selalu responsif |
| Gambar bekantan gagal dimuat | Fallback teks emoji `🐒` ditampilkan via `onerror` handler pada `<img>` |

---

## Testing Strategy

### Unit Tests (Example-Based)

Fokus pada fungsi murni dan logika inti:

- `pickRandomReaction(pool, lastReaction)`:
  - Contoh: pool dengan 8 entri, lastReaction = entri pertama → hasil bukan entri pertama
  - Edge case: pool dengan 1 entri → kembalikan entri tersebut
  - Edge case: pool kosong → kembalikan reaksi default
- `renderScore(score)`:
  - Contoh: score = 42 → DOM menampilkan `"42"` tanpa desimal
  - Contoh: score = 0 → DOM menampilkan `"0"`
- Deteksi milestone:
  - Contoh: score = 10 → milestone true
  - Contoh: score = 0 → milestone false
  - Contoh: score = 15 → milestone false

### Property-Based Tests

Menggunakan library **fast-check** (JavaScript). Setiap property test dijalankan minimal **100 iterasi**.

**Tag format**: `Feature: bekantan-clicker, Property {N}: {deskripsi}`

#### Property 1 — Klik selalu menambah skor sebesar 1
```
Feature: bekantan-clicker, Property 1: klik menambah skor tepat 1
Generator: fc.integer({ min: 0, max: 1_000_000 }) sebagai skor awal
Verifikasi: incrementScore(state) → state.score === initialScore + 1
```

#### Property 2 — Reaksi selalu berbeda dari sebelumnya
```
Feature: bekantan-clicker, Property 2: reaksi berbeda dari sebelumnya
Generator: fc.array(fc.string(), { minLength: 2 }) sebagai pool,
           fc.integer() untuk memilih lastReaction dari pool
Verifikasi: pickRandomReaction(pool, lastReaction) !== lastReaction
```

#### Property 3 — Reaksi selalu dari pool
```
Feature: bekantan-clicker, Property 3: reaksi berasal dari pool
Generator: fc.array(fc.string(), { minLength: 1 }) sebagai pool,
           fc.option(fc.string()) sebagai lastReaction
Verifikasi: pool.includes(pickRandomReaction(pool, lastReaction))
```

#### Property 4 — Reset mengembalikan skor ke 0
```
Feature: bekantan-clicker, Property 4: reset mengembalikan skor ke 0
Generator: fc.integer({ min: 0, max: 1_000_000 }) sebagai skor sebelum reset
Verifikasi: setelah reset, state.score === 0
```

#### Property 5 — Skor ditampilkan tanpa desimal
```
Feature: bekantan-clicker, Property 5: skor tanpa desimal
Generator: fc.integer({ min: 0, max: 1_000_000 })
Verifikasi: String(score) tidak mengandung '.' atau ','
```

#### Property 6 — Milestone pada kelipatan 10
```
Feature: bekantan-clicker, Property 6: milestone pada kelipatan 10
Generator: fc.integer({ min: 1, max: 100_000 }).map(n => n * 10)
Verifikasi: isMilestone(score) === true
```

### Integration / Smoke Tests

- Halaman dimuat tanpa error di browser (smoke test manual)
- Bekantan tampil di tengah halaman
- Klik bekantan → skor bertambah, reaksi kucing berubah, animasi muncul
- Tombol reset → skor kembali ke 0, reaksi kucing kembali ke netral
- Layout tidak rusak pada viewport 320px dan 1920px
