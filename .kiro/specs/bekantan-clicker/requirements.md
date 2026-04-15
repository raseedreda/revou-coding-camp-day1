# Requirements Document

## Introduction

Bekantan Clicker adalah sebuah single-page web app berbasis klik (clicker game) yang menampilkan bekantan (proboscis monkey) sebagai objek utama yang dapat diklik. Setiap klik menghasilkan poin, dan terdapat kucing dengan reaksi acak yang muncul sebagai elemen pendukung hiburan. Aplikasi dibangun menggunakan HTML5, CSS Tailwind, dan Vanilla JavaScript tanpa dependensi backend.

## Glossary

- **Bekantan**: Objek utama yang dapat diklik oleh pengguna; merupakan gambar/ilustrasi monyet bekantan (proboscis monkey).
- **Clicker_App**: Aplikasi web single-page yang menjadi sistem utama dalam dokumen ini.
- **Score**: Nilai numerik yang bertambah setiap kali pengguna mengklik Bekantan.
- **Cat**: Elemen karakter kucing yang menampilkan reaksi acak sebagai hiburan tambahan.
- **Cat_Reaction**: Ekspresi atau animasi teks/emoji yang ditampilkan oleh Cat secara acak saat dipicu.
- **Click_Event**: Aksi pengguna menekan/mengklik elemen Bekantan.
- **Reaction_Pool**: Kumpulan reaksi yang tersedia untuk ditampilkan oleh Cat.

---

## Requirements

### Requirement 1: Tampilan Bekantan

**User Story:** Sebagai pengguna, saya ingin melihat gambar bekantan di layar, sehingga saya tahu objek apa yang harus saya klik.

#### Acceptance Criteria

1. THE Clicker_App SHALL menampilkan ilustrasi atau gambar Bekantan di area tengah halaman saat halaman pertama kali dimuat.
2. THE Clicker_App SHALL menampilkan Bekantan dengan ukuran yang cukup besar sehingga mudah diklik pada perangkat desktop maupun mobile.
3. WHEN pengguna mengarahkan kursor ke Bekantan, THE Clicker_App SHALL mengubah tampilan kursor menjadi pointer untuk menandakan elemen tersebut dapat diklik.

---

### Requirement 2: Mekanisme Klik dan Penambahan Skor

**User Story:** Sebagai pengguna, saya ingin mendapatkan poin setiap kali saya mengklik bekantan, sehingga saya merasakan progres dalam permainan.

#### Acceptance Criteria

1. WHEN pengguna melakukan Click_Event pada Bekantan, THE Clicker_App SHALL menambahkan 1 poin ke Score.
2. THE Clicker_App SHALL menampilkan nilai Score terkini secara real-time di area yang terlihat jelas oleh pengguna.
3. WHEN pengguna melakukan Click_Event pada Bekantan, THE Clicker_App SHALL menampilkan animasi visual pada Bekantan (misalnya efek scale atau bounce) untuk memberikan umpan balik klik.
4. WHEN pengguna melakukan Click_Event pada Bekantan, THE Clicker_App SHALL menampilkan indikator "+1" yang muncul dan menghilang di dekat posisi klik sebagai umpan balik visual.

---

### Requirement 3: Tampilan dan Reaksi Kucing

**User Story:** Sebagai pengguna, saya ingin melihat kucing dengan reaksi acak, sehingga pengalaman bermain menjadi lebih menghibur.

#### Acceptance Criteria

1. THE Clicker_App SHALL menampilkan karakter Cat di area halaman yang terlihat oleh pengguna.
2. WHEN pengguna melakukan Click_Event pada Bekantan, THE Clicker_App SHALL memilih satu Cat_Reaction secara acak dari Reaction_Pool dan menampilkannya pada karakter Cat.
3. THE Reaction_Pool SHALL mengandung minimal 8 Cat_Reaction yang berbeda, mencakup ekspresi teks dan/atau emoji.
4. WHEN Cat_Reaction baru ditampilkan, THE Clicker_App SHALL menampilkan animasi transisi pada Cat untuk menarik perhatian pengguna.
5. THE Clicker_App SHALL memastikan Cat_Reaction yang ditampilkan berbeda dari Cat_Reaction sebelumnya apabila Reaction_Pool memiliki lebih dari 1 entri.

---

### Requirement 4: Tampilan Skor

**User Story:** Sebagai pengguna, saya ingin melihat skor saya dengan jelas, sehingga saya dapat memantau progres klik saya.

#### Acceptance Criteria

1. THE Clicker_App SHALL menampilkan Score dengan format angka bulat tanpa desimal.
2. THE Clicker_App SHALL menampilkan label yang menjelaskan bahwa angka yang ditampilkan adalah jumlah klik atau poin.
3. WHEN Score mencapai kelipatan 10, THE Clicker_App SHALL menampilkan efek visual khusus pada area Score sebagai penanda pencapaian.

---

### Requirement 5: Responsivitas dan Kompatibilitas

**User Story:** Sebagai pengguna, saya ingin menggunakan aplikasi di berbagai ukuran layar, sehingga saya dapat bermain di perangkat apa pun.

#### Acceptance Criteria

1. THE Clicker_App SHALL menampilkan layout yang dapat digunakan pada lebar layar minimal 320px hingga 1920px.
2. THE Clicker_App SHALL menggunakan Tailwind CSS untuk seluruh styling sehingga konsistensi desain terjaga.
3. THE Clicker_App SHALL berjalan sepenuhnya di sisi klien (client-side) tanpa memerlukan koneksi ke server backend.

---

### Requirement 6: Reset Skor

**User Story:** Sebagai pengguna, saya ingin dapat mereset skor saya, sehingga saya dapat memulai permainan dari awal.

#### Acceptance Criteria

1. THE Clicker_App SHALL menyediakan tombol reset yang dapat diklik oleh pengguna.
2. WHEN pengguna mengklik tombol reset, THE Clicker_App SHALL mengatur Score kembali ke nilai 0.
3. WHEN pengguna mengklik tombol reset, THE Clicker_App SHALL menampilkan Cat_Reaction awal atau netral pada karakter Cat.
