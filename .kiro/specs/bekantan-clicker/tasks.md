# Tasks

## Task List

- [-] 1. Setup struktur proyek dan konfigurasi dasar
  - [ ] 1.1 Buat file `index.html` dengan struktur HTML5 dasar
  - [ ] 1.2 Tambahkan Tailwind CSS via CDN di `<head>`
  - [ ] 1.3 Tambahkan inline `<style>` untuk animasi custom (bounce, fade, scale, milestone pulse)
  - [ ] 1.4 Buat placeholder untuk semua elemen DOM utama (`#score-value`, `#score-label`, `#bekantan`, `#cat-container`, `#cat-reaction`, `#reset-btn`)

- [~] 2. Implementasi state dan logika inti (pure functions)
  - [ ] 2.1 Definisikan objek `state` dengan properti `score` (integer, default 0) dan `lastReaction` (string | null, default null)
  - [ ] 2.2 Implementasikan `reactionPool` array dengan minimal 8 reaksi unik (teks + emoji)
  - [ ] 2.3 Implementasikan fungsi `incrementScore(state)` yang menambah `state.score` sebesar 1
  - [ ] 2.4 Implementasikan fungsi murni `pickRandomReaction(pool, lastReaction)` yang mengembalikan reaksi acak berbeda dari `lastReaction` (kecuali pool hanya 1 entri)
  - [ ] 2.5 Implementasikan fungsi `isMilestone(score)` yang mengembalikan `true` jika `score > 0 && score % 10 === 0`

- [~] 3. Implementasi rendering DOM
  - [ ] 3.1 Implementasikan `renderScore(score)` yang memperbarui teks `#score-value` dengan integer tanpa desimal
  - [ ] 3.2 Implementasikan `renderCatReaction(reaction)` yang memperbarui teks `#cat-reaction` dan memicu animasi transisi
  - [ ] 3.3 Implementasikan logika milestone di `renderScore` — tambahkan class animasi khusus pada area score saat `isMilestone(score)` true

- [~] 4. Implementasi interaksi klik bekantan
  - [ ] 4.1 Implementasikan `showClickFeedback(event)` yang membuat elemen `.plus-one` di posisi klik dan menghapusnya setelah animasi selesai
  - [ ] 4.2 Implementasikan animasi bounce/scale pada `#bekantan` saat diklik (tambah class, hapus setelah animasi)
  - [ ] 4.3 Implementasikan `handleBekantanClick(event)` sebagai orkestrator: panggil `incrementScore`, `renderScore`, `showClickFeedback`, `pickRandomReaction`, `renderCatReaction`
  - [ ] 4.4 Pasang event listener `click` pada elemen `#bekantan`

- [~] 5. Implementasi tombol reset
  - [ ] 5.1 Implementasikan `handleReset()` yang mengatur `state.score = 0` dan `state.lastReaction = null`
  - [ ] 5.2 Panggil `renderScore(0)` dan `renderCatReaction(null)` (tampilkan reaksi netral) di dalam `handleReset()`
  - [ ] 5.3 Pasang event listener `click` pada elemen `#reset-btn`

- [~] 6. Styling dan responsivitas dengan Tailwind CSS
  - [ ] 6.1 Terapkan layout responsif menggunakan Tailwind (flexbox/grid) yang berfungsi dari 320px hingga 1920px
  - [ ] 6.2 Styling `#bekantan` dengan ukuran yang cukup besar dan `cursor-pointer`
  - [ ] 6.3 Styling area score (`#score-value`, `#score-label`) agar terlihat jelas
  - [ ] 6.4 Styling `#cat-container` dan `#cat-reaction` agar terlihat dan menarik
  - [ ] 6.5 Styling `#reset-btn` sebagai tombol yang jelas dan mudah diklik
  - [ ] 6.6 Tambahkan fallback `onerror` pada `<img>` bekantan untuk menampilkan emoji `🐒` jika gambar gagal dimuat

- [~] 7. Setup testing dengan fast-check
  - [ ] 7.1 Buat file `test/bekantan-clicker.test.js` (atau `.test.ts`)
  - [ ] 7.2 Install/setup fast-check dan test runner (misalnya Vitest atau Jest) via CDN atau npm
  - [ ] 7.3 Ekstrak fungsi-fungsi murni (`incrementScore`, `pickRandomReaction`, `isMilestone`, `renderScore`) ke modul terpisah `src/logic.js` agar dapat diimpor di test

- [~] 8. Implementasi property-based tests
  - [ ] 8.1 Tulis property test untuk Property 1: klik selalu menambah skor tepat 1
    - Generator: `fc.integer({ min: 0, max: 1_000_000 })` sebagai skor awal
    - Verifikasi: `incrementScore(state).score === initialScore + 1`
    - Tag: `Feature: bekantan-clicker, Property 1: klik menambah skor tepat 1`
  - [ ] 8.2 Tulis property test untuk Property 2: reaksi selalu berbeda dari sebelumnya
    - Generator: `fc.array(fc.string(), { minLength: 2 })` sebagai pool, pilih lastReaction dari pool
    - Verifikasi: `pickRandomReaction(pool, lastReaction) !== lastReaction`
    - Tag: `Feature: bekantan-clicker, Property 2: reaksi berbeda dari sebelumnya`
  - [ ] 8.3 Tulis property test untuk Property 3: reaksi selalu berasal dari pool
    - Generator: `fc.array(fc.string(), { minLength: 1 })` sebagai pool, `fc.option(fc.string())` sebagai lastReaction
    - Verifikasi: `pool.includes(pickRandomReaction(pool, lastReaction))`
    - Tag: `Feature: bekantan-clicker, Property 3: reaksi berasal dari pool`
  - [ ] 8.4 Tulis property test untuk Property 4: reset selalu mengembalikan skor ke 0
    - Generator: `fc.integer({ min: 0, max: 1_000_000 })` sebagai skor sebelum reset
    - Verifikasi: setelah reset, `state.score === 0`
    - Tag: `Feature: bekantan-clicker, Property 4: reset mengembalikan skor ke 0`
  - [ ] 8.5 Tulis property test untuk Property 5: skor ditampilkan tanpa desimal
    - Generator: `fc.integer({ min: 0, max: 1_000_000 })`
    - Verifikasi: `String(score)` tidak mengandung `'.'` atau `','`
    - Tag: `Feature: bekantan-clicker, Property 5: skor tanpa desimal`
  - [ ] 8.6 Tulis property test untuk Property 6: milestone terdeteksi pada kelipatan 10
    - Generator: `fc.integer({ min: 1, max: 100_000 }).map(n => n * 10)`
    - Verifikasi: `isMilestone(score) === true`
    - Tag: `Feature: bekantan-clicker, Property 6: milestone pada kelipatan 10`

- [~] 9. Implementasi unit tests (example-based)
  - [ ] 9.1 Tulis unit test: `reactionPool` memiliki minimal 8 entri unik
  - [ ] 9.2 Tulis unit test: `pickRandomReaction` dengan pool 1 entri mengembalikan entri tersebut
  - [ ] 9.3 Tulis unit test: `pickRandomReaction` dengan pool kosong mengembalikan reaksi default
  - [ ] 9.4 Tulis unit test: `renderScore(42)` menampilkan `"42"` di DOM
  - [ ] 9.5 Tulis unit test: `renderScore(0)` menampilkan `"0"` di DOM
  - [ ] 9.6 Tulis unit test: `isMilestone(10)` → true, `isMilestone(0)` → false, `isMilestone(15)` → false
  - [ ] 9.7 Tulis unit test: setelah reset, `state.lastReaction === null`

- [~] 10. Verifikasi akhir dan polish
  - [ ] 10.1 Jalankan semua property tests dan unit tests, pastikan semua lulus
  - [ ] 10.2 Lakukan manual smoke test: buka `index.html` di browser, verifikasi semua elemen tampil
  - [ ] 10.3 Lakukan manual smoke test: klik bekantan beberapa kali, verifikasi skor bertambah, reaksi kucing berubah, animasi muncul
  - [ ] 10.4 Lakukan manual smoke test: klik reset, verifikasi skor kembali ke 0 dan reaksi kucing netral
  - [ ] 10.5 Lakukan manual smoke test: resize browser ke 320px dan 1920px, verifikasi layout tidak rusak
  - [ ] 10.6 Verifikasi milestone: klik hingga skor 10, 20, 30 — pastikan efek visual khusus muncul
