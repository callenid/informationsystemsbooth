# ESI Booth Showcase — Mode Kamera (Hand Tracking)

Kedua game sekarang bisa dimainkan **pakai tangan di depan kamera**, tanpa keyboard
dan tanpa mouse. Mode lama (keyboard/mouse/sentuh) tetap ada dan jadi cadangan.

---

## 1. Yang berubah

### 🎮 ESI Data Rush → kendali tangan bebas 2 arah

- Tombol baru **📷 Hand** di pojok kanan atas game.
- ESI tidak lagi terkunci di dasar layar. ESI **mengikuti tangan** ke mana pun
  (kiri–kanan **dan** atas–bawah), jadi pemain bisa "menjemput" disket sebelum jatuh.
- **🤏 Jepit** (ujung jempol + telunjuk bertemu) = **ambil** data. Kalau tangan
  terbuka, item baik akan lewat begitu saja.
- **🐛 Bug tetap kena walau tangan terbuka** — jadi pemain harus tetap menghindar.
  Ini yang bikin levelnya masih menantang.
- Ada **preview kamera kecil** (picture-in-picture) di pojok kanan atas kanvas,
  lengkap dengan rangka 21 titik tangan supaya penonton di booth ikut lihat
  teknologinya bekerja. Ini biasanya yang bikin orang berhenti dan ikut mencoba.

### ⌨️ Typing Challenge → 🖐️ **Air Spelling**

Mengetik di udara itu tidak akurat, jadi game ketiknya diubah jadi versi kamera
yang benar-benar bisa dimainkan:

- Huruf-huruf melayang sebagai gelembung di kanvas.
- Kata target tampil di atas. Pemain **menjepit huruf sesuai urutan** untuk
  mengeja kata tersebut.
- Huruf salah = **−1 detik** + efek getar. Huruf benar = huruf terkunci di kata.
- Level 1 menyorot huruf yang benar (warna teal) supaya pemain baru cepat paham.
  Level 2 dan 3 tidak ada bantuan itu.
- Judul section otomatis berubah jadi **ESI Air Spelling** saat mode kamera aktif.
- Jumlah gelembung naik per level: 8 → 10 → 12.
- **Skornya sama persis** dengan mode keyboard (poin per huruf + bonus sisa waktu),
  jadi satu leaderboard tetap adil untuk kedua mode.

---

## 2. Syarat menjalankan (PENTING)

Browser **hanya mengizinkan kamera di halaman yang aman**:

| Cara buka | Kamera jalan? |
| --- | --- |
| `https://domain-anda.com/` | ✅ Ya |
| `http://localhost/esi/` (XAMPP) | ✅ Ya |
| `http://127.0.0.1/esi/` | ✅ Ya |
| **Klik ganda file `index.html` (`file:///…`)** | ❌ **Tidak** |
| `http://192.168.x.x/` (IP LAN biasa) | ❌ Tidak |

Jadi untuk booth: taruh folder ini di `htdocs` XAMPP dan buka lewat
`http://localhost/esi/`. Kalau di-hosting, wajib HTTPS.

Browser yang disarankan: **Chrome / Edge terbaru** di laptop dengan webcam.

---

## 3. Cara main di booth

1. Buka halaman → klik **📷 Hand** di game yang mau dipakai.
2. Izinkan akses kamera saat browser bertanya.
3. Tunggu sampai label preview berubah jadi **"✋ Tracking — pinch to grab"**.
4. Klik **⛶** untuk fullscreen, lalu **▶ Start**.

Hanya satu game yang boleh pakai kamera dalam satu waktu — kalau kamera
dinyalakan di game kedua, game pertama otomatis kembali ke mode keyboard.

---

## 4. Kalau tracking penuh gagal → "Motion Mode" otomatis

Pelacakan jari memakai **MediaPipe Hand Landmarker** yang diambil dari CDN saat
pertama kali dinyalakan. Kalau gagal (tidak ada internet, browser lama, atau
WebGL/WebGPU bermasalah), sistem **tidak mati** — otomatis turun ke **Motion Mode**:

- Deteksi gerakan sederhana dari beda antar-frame, jalan 100% offline.
- Tidak ada deteksi jepit, jadi:
  - **Data Rush** — cukup sentuh datanya dengan ESI.
  - **Air Spelling** — arahkan kursor ke huruf dan **tahan diam ±0,5 detik**
    (ada cincin oranye sebagai indikator progres).

Label di preview kamera akan menulis **"🌀 Motion mode"** kalau ini yang aktif.

---

## 5. Hosting sendiri file MediaPipe (biar aman tanpa internet di booth)

Sangat disarankan kalau WiFi booth tidak bisa diandalkan. Sejak versi ini
**semua alamat MediaPipe dikumpulkan di satu tempat**: buka `index.html`, cari
blok `const CDN = {` di dalam modul **`ESIMP`**, lalu ganti isinya:

```js
const CDN = {
    lib:  "assets/mediapipe/vision_bundle.mjs",
    wasm: "assets/mediapipe/wasm",
    hand: "assets/mediapipe/hand_landmarker.task",
    pose: "assets/mediapipe/pose_landmarker_lite.task",
};
```

File yang perlu diunduh sekali (dari komputer yang ada internet):

1. **Library + WASM** — `npm pack @mediapipe/tasks-vision@0.10.14`, lalu ekstrak.
   Salin `vision_bundle.mjs` dan seluruh folder `wasm/` ke
   `assets/mediapipe/`.
2. **Model tangan** — unduh
   `https://storage.googleapis.com/mediapipe-models/hand_landmarker/hand_landmarker/float16/1/hand_landmarker.task`
   dan simpan sebagai `assets/mediapipe/hand_landmarker.task`.
3. **Model pose (untuk Pose Party)** — unduh
   `https://storage.googleapis.com/mediapipe-models/pose_landmarker/pose_landmarker_lite/float16/1/pose_landmarker_lite.task`
   dan simpan sebagai `assets/mediapipe/pose_landmarker_lite.task` (±5,8 MB).
   Perhatikan nama filenya **mengulang nama varian** — inilah yang sempat salah
   tulis dan bikin Pose Party tidak jalan.

Setelah itu mode kamera penuh jalan tanpa internet sama sekali.

Catatan: font Google Fonts juga masih dari internet. Kalau mau benar-benar
offline, unduh fontnya juga dan ganti `<link>` di `<head>`.

---

## 6. Tips penyetelan (kalau perlu diubah sendiri)

Semua ada di dalam blok `ESIHand` di `index.html`:

| Yang mau diatur | Variabel | Nilai sekarang |
| --- | --- | --- |
| Seberapa jauh tangan harus digerakkan | `GAIN_LO` / `GAIN_HI` | `0.16` / `0.84` — dilebarkan (mis. `0.05`/`0.95`) kalau ruang gerak sempit |
| Sensitivitas jepit | `PINCH_DOWN` / `PINCH_UP` | `0.45` / `0.62` — naikkan `PINCH_DOWN` kalau susah "menjepit" |
| Sensitivitas Motion Mode | ambang `d > 70` dan `sum > 9000` | turunkan kalau ruangan gelap |
| Lama tahan di Motion Mode | `0.55` (detik) di `airStep` | |
| Ukuran kotak tangkap ESI | `HW` / `HH` di game Data Rush | `40` / `56` |

Pencahayaan sangat berpengaruh. Untuk booth: hindari lampu/jendela terang tepat
**di belakang** pemain — cahaya dari depan atau samping jauh lebih stabil.

---

## 7. Game baru: 🎤 ESI Sound Rocket (kontrol suara)

Game ketiga, dikendalikan **mikrofon**. Suara = bahan bakar jetpack ESI.

- **Makin keras suara → ESI makin naik.** Diam → ESI jatuh. Ada garis penanda di
  meter: kira-kira 41% = cukup untuk melayang stabil.
- Terbang lewat celah **firewall** 🔥. Lewat satu dinding = **+15**.
- Ambil data 💾 **+10** dan ⭐ **+30** (dengan combo sampai ×3).
- Nabrak dinding = **−15**, combo hilang, kebal 1 detik supaya tidak beruntun.
- 3 level × 35 detik. Target kumulatif **130 → 360 → 660**. Dinding makin cepat
  dan celahnya makin sempit tiap level.
- Punya leaderboard sendiri (`esi_voice_board`) dan sudah muncul di section
  Leaderboard Booth.

### Kalibrasi otomatis untuk booth yang ramai

Saat mikrofon dinyalakan, sistem **mengukur kebisingan ruangan selama 1,5 detik**
(pemain diminta diam), lalu memakainya sebagai titik nol. Jadi di ruangan yang
berisik pun tetap terasa adil.

Kalau kondisi ruangan berubah (misalnya kerumunan makin ramai),
**klik dua kali panel mikrofon** untuk kalibrasi ulang.

### Kalau mikrofon tidak ada / tidak diizinkan

Game tetap bisa dimainkan: **tahan tombol Space** atau **klik-tahan kanvas**
untuk menyalakan jetpack. Berguna untuk demo cepat atau kalau booth terlalu bising.

Syarat `https://` atau `localhost` sama persis seperti kamera (lihat bagian 2).

**Catatan teknis:** input mikrofon sengaja **tidak** disambungkan ke speaker,
jadi tidak akan terjadi feedback/howling walaupun speaker booth kencang.

---

## 8. Tampilan kamera diperbesar

Sebelumnya preview kamera cuma 190px dan memang terlalu kecil. Sekarang:

1. **Kamera tampil besar sebagai latar kanvas game.** Saat mode 📷 Hand aktif,
   video kamera digambar memenuhi seluruh kanvas (dibuat samar supaya elemen
   game tetap terbaca), lengkap dengan rangka tangan berukuran besar. Jadi
   pemain melihat dirinya sendiri seukuran layar penuh, bukan cuma kotak kecil.
2. **Panel preview jauh lebih besar dan bisa diatur.** Data Rush sekarang
   default ke ukuran besar, dan **panel bisa diklik untuk ganti ukuran**:
   sedang → besar → sangat besar → sedang. Resolusi kanvas preview juga
   dinaikkan (400×300) supaya tidak pecah saat diperbesar.
3. Di **fullscreen** panelnya ikut membesar (sampai 42% lebar layar).

Kalau latar kamera terasa terlalu ramai, kurangi nilai alpha-nya di `draw()`
game Data Rush: `ESIHand.paintBackdrop(ctx, cv.width, cv.height, 0.85, 0.52)` —
angka terakhir adalah kepekatan lapisan putih peredam (makin besar makin pudar).

---

## 9. Perbaikan bug

Ditemukan saat pengujian: **loop animasi level sebelumnya tidak pernah
dibatalkan**, sehingga game berjalan **dobel kecepatan di Level 2 dan tiga kali
lipat di Level 3**. Ini bug lama yang sudah ada di Data Rush sejak awal — waktu
30 detik sebenarnya habis dalam 15 detik di level 2. Sudah diperbaiki di Data
Rush dan Sound Rocket dengan `cancelAnimationFrame` sebelum setiap level dimulai.
Kalau target skor level 2/3 terasa jadi lebih mudah sekarang, itu sebabnya —
levelnya memang baru sekarang berjalan penuh 30 detik.

---

## 10. Game baru: 🕺 ESI Pose Party (seluruh badan)

Game keempat, memakai **pelacakan pose seluruh badan** (MediaPipe Pose
Landmarker, 33 titik). ESI memperagakan gaya konyol, pemain menirukannya.

### Cara main

1. Nyalakan **🕺 Body**, mundur ±2 meter supaya kedua lengan masuk frame.
2. Kartu di kanan layar menampilkan **boneka ESI** yang sedang memperagakan gaya,
   lengkap dengan nama dan petunjuk singkat.
3. Tirukan gayanya. Cincin di kiri bawah menunjukkan **persentase kemiripan**;
   begitu cukup mirip, cincin oranye di luarnya mulai terisi — **tahan** sampai penuh.
4. Skor per gaya = 40 + (kemiripan × 70). Makin persis, makin besar poinnya.
   Sisa waktu di akhir level jadi bonus.

Level: **5 gaya / 45 detik** → **6 gaya** → **7 gaya**, dengan toleransi
mengecil tiap level (62° → 50° → 42°).

### Bagian yang lucunya

- **Badan pemain berubah jadi ESI.** Kamera digambar penuh sebagai latar, lalu di
  atas badan pemain digambar badan navy ESI, perut putih, sirip tebal, syal
  oranye di leher, dan **kepala ESI menempel di kepala pemain** — ikut miring
  kalau kepala dimiringkan. Ini yang bikin penonton booth ikut tertawa.
- 10 gaya bertema penguin: Touchdown 🙌, Robo-Penguin 🤖, Strong Penguin 💪,
  Penguin Flap 🐧, Big Y 🙆, Disco Fever 🕺, Wing Salute 🫡, Droopy Wings 🥲,
  Goal Post 🥅, Fly Away 🦅.
- Sorakan acak saat berhasil: "PERFECT! ESI is speechless 😲",
  "Certified penguin 🏅", dan seterusnya, plus ledakan konfeti.

### Keputusan desain yang perlu diketahui

- **Hanya badan bagian atas yang dinilai** (sudut lengan atas dan lengan bawah,
  kiri dan kanan). Kaki sengaja tidak dipakai karena webcam booth biasanya
  dipasang setinggi meja dan kaki tidak kelihatan — gaya yang butuh kaki akan
  bikin game terasa rusak.
- Penilaian memakai **sudut**, bukan posisi titik, jadi **tidak terpengaruh
  tinggi badan, jarak ke kamera, atau posisi berdiri**. Anak kecil dan orang
  dewasa dinilai sama adilnya.
- **Gaya cermin ikut diterima.** Untuk gaya asimetris seperti Disco Fever, mau
  tangan kanan atau kiri yang di atas, dua-duanya dihitung benar. Ini penting
  supaya orang tidak bingung soal kiri/kanan di layar.
- Kalau lengan tidak terlihat, muncul pesan "Step back so ESI can see your arms".

### Kalau mau diatur sendiri

Di dalam IIFE game pose:

| Yang mau diubah | Tempatnya |
| --- | --- |
| Tambah/ubah gaya | array `POSES` — cukup isi sudut `lu`/`lf`/`ru`/`rf` (derajat, 0 = kanan, 90 = atas, sisi "l" = kiri layar) |
| Tingkat kesulitan | `tol` (toleransi derajat) dan `thresh` (ambang) di `LEVELS` |
| Lama menahan gaya | `hold` di `LEVELS` |
| Ukuran kepala ESI | `headR = shW * 0.62` di `ESIPose.drawCostume` |
| Seberapa pekat latar kamera | `ESIHand.paintBackdrop(ctx, W, Hh, 0.9, 0.42)` di `drawScene` |

Karena gaya didefinisikan sebagai sudut, boneka contoh **digambar dari angka yang
sama** dengan yang dipakai untuk menilai — jadi gaya yang ditampilkan dijamin
selalu bisa dicapai, tidak mungkin ada gaya contoh yang mustahil ditiru.

---

## 11. Catatan: kamera dipakai bergantian

Browser tidak suka dua permintaan kamera sekaligus, jadi sekarang ada modul
`ESICamera` yang memegang **satu** aliran kamera. Data Rush, Air Spelling, dan
Pose Party meminjam bergantian — begitu satu dinyalakan, yang lain otomatis mati
dan tombolnya kembali abu-abu. Mikrofon (Sound Rocket) memakai jalur terpisah,
jadi boleh menyala bersamaan dengan kamera.

Efek sampingnya: berpindah antar-game kamera perlu 1–3 detik untuk memuat ulang
model. Ini wajar, jangan panik kalau di booth terasa jeda sebentar.

---

## 12. Perubahan terbaru

### 🎤 Sound Rocket dirombak — langit tanpa batas

Sesuai masukan, aturan mainnya diganti total:

- **Tidak ada batas atas lagi.** Dulu ESI mentok di tepi atas kanvas. Sekarang
  layar **ikut naik mengikuti ESI**, jadi bisa terbang setinggi apa pun. Ada
  penggaris ketinggian di kiri layar, awan yang lewat, langit yang makin gelap
  makin tinggi, dan penunjuk `▼ ground …m below` kalau tanah sudah tidak terlihat.
- **Dinding firewall dihapus.** Satu-satunya bahaya sekarang adalah **tanah**.
  Menyentuh es = kehilangan 1 nyawa (dari 3) dan −10 poin, lalu ESI dipantulkan
  ke atas supaya sempat pulih. Habis nyawa = level berakhir.
- **Fisikanya dibuat lebih mengambang** supaya "kalau teriak dia terbang, bukan
  jatuh terus": gravitasi diturunkan (700), dorongan dinaikkan (1900), dan
  kecepatan jatuh dibatasi. Melayang stabil cukup di **±37% kekerasan suara** —
  garis penanda di meter sudah disesuaikan.
- Muncul peringatan merah **"🔊 LOUDER! PULL UP!"** kalau ESI mendekati tanah.
- HUD **Combo** diganti **Lives ❤❤❤**; combo pindah ke pojok kanan bawah kanvas
  bersama pembacaan ketinggian.
- Ada **masa aman 2,5 detik** di awal tiap level, dan pemain **tidak dihukum
  selama mikrofon masih mengukur kebisingan ruangan** — supaya tidak langsung
  kalah sebelum sempat paham.

**Soal keseimbangan:** data biasa sengaja muncul sedikit **di bawah** posisi ESI
dan bintang ⭐ jauh **di atas**, sehingga rata-ratanya seimbang. Ini penting:
kalau semua data muncul relatif terhadap ketinggian pemain, pemain akan terseret
naik terus tanpa ujung dan tanah jadi tidak relevan. Hasil pengujian: pemain yang
teriak nonstop memang bisa naik sampai puluhan ribu meter — tapi **skornya 0**,
karena dia terus melewati datanya. Jadi langit tanpa batas tetap ada, tanpa jadi
celah untuk curang.

Target skor sekarang **150 → 370 → 640** dalam 40 detik per level.

### 🕺 Pose Party — skeleton sekarang kelihatan & error-nya jelas

- **Rangka badan sekarang digambar di atas kostum ESI**, bukan cuma di panel
  preview kecil. Garisnya tebal dengan outline gelap + isi terang supaya terlihat
  di latar apa pun, dengan titik sendi putih (oranye di pergelangan tangan). Jadi
  pemain langsung tahu pelacakannya bekerja.
- **Kalau gagal dimuat, pesan aslinya sekarang ditampilkan di kanvas** — bukan
  lagi diam saja. Ada juga `console.error` di DevTools. Kalau masih belum jalan,
  tolong kirimkan tulisan yang muncul di kanvas itu; dari situ bisa dipastikan
  apakah masalahnya jaringan, model tidak terunduh, atau GPU.
- Saat sedang mengunduh, muncul **"⏳ Downloading the body model…"** dengan
  catatan ukurannya ±6 MB. Batas waktunya dinaikkan jadi 60 detik.

### Dua perbaikan di balik layar yang kemungkinan besar jadi penyebabnya

1. **Library MediaPipe dulu dimuat dua kali.** Pelacak tangan dan pelacak pose
   masing-masing membuat `FilesetResolver` sendiri, jadi mesin WASM diinisialisasi
   dua kali. Ini boros memori dan sering gagal di laptop tanpa GPU kuat. Sekarang
   ada modul **`ESIMP`** yang memuat library dan mesin WASM **sekali saja** lalu
   dipakai bersama.
2. **Pelacak lama tidak pernah ditutup.** Setiap berpindah antar-game kamera,
   objek landmarker lama ditinggalkan begitu saja sehingga konteks WebGL menumpuk.
   Browser membatasi jumlah konteks WebGL (sekitar 16), jadi setelah beberapa kali
   berpindah, pelacak berikutnya **pasti gagal dibuat** — dan pose adalah yang
   terakhir ditambahkan, jadi paling sering kena. Sekarang `landmarker.close()`
   dipanggil setiap kali kamera dilepas.

Kalau setelah ini pose masih belum jalan, kemungkinan besar tinggal soal jaringan
(model 6 MB tidak terunduh) — dan solusi paling aman untuk booth tetap
**hosting mandiri** seperti di bagian 5.

---

## 13. Perbaikan Pose Party (penyebab sebenarnya)

Pose Party tidak pernah jalan karena **alamat file modelnya salah tulis**.

| | |
| --- | --- |
| Yang dipakai (salah) | `.../pose_landmarker_lite/float16/1/pose_landmarker.task` |
| Yang benar | `.../pose_landmarker_lite/float16/1/pose_landmarker_lite.task` |

Nama file MediaPipe **mengulang nama folder variannya**. Untuk tangan kebetulan
nama varian dan nama file sama (`hand_landmarker`), jadi tebakan pola yang sama
untuk pose menghasilkan URL 404 — pelacak tangan jalan, pelacak pose tidak. Itu
sebabnya hanya game pose yang mati.

Selain memperbaiki URL-nya, sekarang `ESIMP.make` bisa menerima **daftar URL
cadangan**: kalau model *lite* gagal diunduh, otomatis dicoba model *full*. Setiap
percobaan yang gagal juga dicatat ke console (`[ESI] PoseLandmarker GPU failed: …`)
supaya masalah serupa langsung kelihatan, tidak diam-diam saja.

---

## 14. Tata letak Pose Party

Kartu contoh gaya tadinya di kanan atas — persis di tempat panel kamera (PIP)
menempel, jadi menutupi tampilan pelacakan. Sekarang semua panel dikumpulkan di
**sisi kiri**:

| Elemen | Posisi |
| --- | --- |
| Kartu contoh gaya (boneka ESI) | kiri atas, x 18–250 |
| Cincin kemiripan + "HOLD IT!" | kiri bawah, tepat di bawah kartu |
| Nama level | tengah atas |
| Panel kamera (PIP) | kanan atas (posisi CSS, bisa diklik untuk ganti ukuran) |

Dengan begitu **bagian tengah dan kanan-tengah kanvas bebas** — itu area tempat
badan pemain dan rangkanya digambar. Kalau mau menggeser sendiri, ubah `cx`/`cy`
pada blok "Kartu contoh gaya" dan `rx`/`ry` pada blok "cincin kemiripan" di dalam
fungsi `drawScene`. Posisi PIP diatur lewat CSS kelas `.pip` (`right`/`top`).

---

## 15. Fitur baru: 💬 Ngobrol dengan ESI

Ada section baru **`#tanya`** tepat di bawah hero — pengunjung bisa mengobrol
dengan ESI sebelum main game. Tombol hero sekarang mengarah ke sana, dan gelembung
ucapan ESI di hero jadi bisa diklik.

### Yang bisa ditanyakan

23 topik, semuanya bersumber dari isi halaman ini sendiri supaya tidak ada
informasi yang bertabrakan:

- Apa itu Sistem Informasi, dan bedanya dengan Informatika/Ilmu Komputer
- **Tiga peminatan** — masing-masing lengkap dengan mata kuliah unggulan dan
  prospek karier, persis seperti di section Specializations
- "Aku cocok yang mana?" — ESI membantu mempersempit pilihan
- Prospek kerja, perlu bisa ngoding dulu atau tidak, seberapa banyak matematikanya
- FAIDAS, UPH, game di halaman ini, tentang maskot ESI, Instagram, booth
- Plus lelucon garing dan jawaban jujur saat ditanya "kamu AI beneran?"

### Dwibahasa

Ada tombol **🇮🇩 Bahasa / 🇬🇧 English** di kanan atas panel. Semua 23 jawaban,
label tombol saran, sapaan, dan pesan "tidak tahu" tersedia dalam dua bahasa.
Mesin pencocokannya mengenali kata kunci Indonesia **dan** Inggris sekaligus,
jadi pengunjung boleh mengetik campur.

Ada juga tombol **🔈 Voice** (default mati) yang membuat ESI membacakan jawabannya
lewat `speechSynthesis` bawaan browser. Sengaja dimatikan default supaya tidak
bertabrakan dengan suara game.

### Kenapa TIDAK memakai layanan AI daring

Ini keputusan sadar, bukan karena keterbatasan:

1. **Booth harus jalan tanpa internet.** Semua fitur lain sudah punya jalur
   cadangan offline; chat yang mati saat WiFi putus akan jadi titik lemah.
2. **Kunci API tidak boleh ada di file HTML publik.** File ini dibuka langsung
   di browser, jadi kunci apa pun di dalamnya bisa dibaca siapa saja.
3. **Yang terpenting: akurasi.** LLM umum tidak tahu detail Sistem Informasi UPH
   dan akan mengarang nama mata kuliah, biaya, atau syarat pendaftaran dengan
   sangat meyakinkan. Di booth penerimaan mahasiswa, itu bukan gangguan kecil —
   itu informasi salah kepada calon mahasiswa dan orang tuanya.

Karena itu ESI **menolak menebak**. Untuk biaya kuliah, pendaftaran, beasiswa,
dan lokasi kampus, jawabannya selalu mengarahkan ke petugas booth. Pertanyaan di
luar topik dijawab dengan daftar hal yang memang bisa dibahas, bukan tebakan.
Panel juga menulis terus terang di bawahnya bahwa ESI bukan AI sungguhan.

### Hasil pengujian pencocokan

Diuji dengan 54 pertanyaan yang realistis dalam dua bahasa (termasuk gaya santai
seperti "peminatannya apa aja?", "harus bisa ngoding dulu ga?") — **54/54 benar**.
Enam pertanyaan di luar topik ("berapa harga bakso", "siapa presiden indonesia")
semuanya jatuh ke jawaban "tidak tahu", tidak ada yang salah tebak.

Satu temuan penting saat pengujian: kata berimbuhan Indonesia seperti
**"peminatan<u>nya</u>"**, **"daftar<u>nya</u>"**, **"matematika<u>nya</u>"** awalnya
tidak pernah cocok, sehingga ESI sering menjawab "tidak tahu" untuk pertanyaan
yang jelas-jelas ada jawabannya. Sekarang teks dibandingkan dua kali: apa adanya,
dan versi yang imbuhannya (`-nya`, `-ku`, `-mu`, `-lah`, `-kah`, `-pun` dan `-s`
jamak Inggris) sudah dipangkas.

### Menambah atau mengubah jawaban

Semua isi ada di objek **`KB`** di dalam blok "5. NGOBROL DENGAN ESI":

```js
nama_topik: {
    kw: ["kata", "kunci", "dua bahasa"],   // pemicu
    en: "Jawaban bahasa Inggris (boleh HTML)",
    id: "Jawaban bahasa Indonesia",
    next: ["topik_lanjutan", "topik_lain"], // tombol saran sesudahnya
},
```

Kalau topik itu juga mau muncul sebagai tombol saran, tambahkan labelnya di objek
**`LABEL`**. Hindari kata kunci yang terlalu umum (seperti `berapa` atau `harga`
sendirian) — itu menyeret pertanyaan yang tidak berkaitan.

### Kalau nanti mau memakai LLM sungguhan

Jangan panggil API langsung dari halaman ini. Buat endpoint kecil di server
sendiri (PHP di XAMPP sudah cukup) yang menyimpan kunci API di sisi server, lalu
ganti isi fungsi `ask()` untuk memanggil endpoint itu. Sertakan isi objek `KB`
sebagai konteks dalam prompt, dan beri instruksi tegas agar model **hanya**
menjawab dari konteks itu dan mengarahkan ke petugas booth untuk hal lain —
kalau tidak, masalah nomor 3 di atas akan muncul kembali. Sebaiknya tetap
sediakan `KB` sebagai jalur cadangan saat jaringan mati.

---

## 16. Maskot hero bisa diklik & suara ESI diperbaiki

### 🐧 Klik ESI di hero banner

Gambar ESI di hero sekarang bisa diklik (ada tulisan **"👆 Tap me to talk!"** di
bawahnya yang hilang setelah diketuk). Sekali klik, ESI langsung:

1. **menyapa dengan suara** — klik adalah gestur pemakai, jadi browser
   mengizinkan suara diputar saat itu juga;
2. **menggulir halaman ke panel chat**;
3. **menulis sapaan di panel chat** dan menampilkan tombol-tombol saran.

Sapaannya **bergantian dari 4 versi** tiap kali diklik, jadi tidak terdengar
seperti rekaman yang sama berulang. Bisa juga dijalankan lewat keyboard (Tab lalu
Enter/Spasi) dan sudah diberi `role="button"` untuk pembaca layar.

Satu hal yang sengaja dijaga: klik maskot **menyalakan suara otomatis**, tapi
**hanya kalau pemakai belum pernah mengatur tombol Voice sendiri**. Kalau tadi
sudah dimatikan dengan sengaja, ESI tetap diam — pilihan pemakai lebih penting
daripada efek kejut.

### 🔊 Suaranya tidak lagi terdengar seperti robot

Ada tiga hal yang membuat suara sintetis terdengar kaku, dan ketiganya sudah
ditangani:

**1. Emoji tidak lagi dibaca.** Sebelumnya "🐧" dibaca "penguin", "📊" dibaca
"bar chart", "🥉" dibaca "third place medal" — itu penyebab terbesar kesan robot.
Sekarang semua emoji, simbol, panah, dan penanda ragam disaring sebelum diucapkan.
Diuji ke seluruh 23 jawaban × 2 bahasa: tidak ada emoji yang lolos.

**2. Tanda baca daftar dirapikan.** Pemisah `·` pada daftar mata kuliah jadi jeda
koma, `&` jadi "dan"/"and", garis miring tidak lagi dibaca "slash", dan titik dua
yang disusul titik (bekas potongan `<ul>`) dibersihkan supaya tidak tersendat.
Singkatan juga diperbaiki: UI/UX, UPH, IoT, GenAI, AR, VR dieja per huruf, bukan
dibaca sebagai kata.

**3. Diucapkan per kalimat, bukan satu tarikan napas.** Tiap kalimat jadi satu
ucapan terpisah sehingga muncul jeda alami di antaranya. Ini sekaligus mengatasi
bug Chrome yang memotong ucapan panjang setelah sekitar 15 detik.

Ditambah lagi:

- **Pemilihan suara terbaik yang tersedia** di komputer itu, berurut sesuai
  daftar prioritas (mis. *Google Bahasa Indonesia* untuk ID, *Google UK English
  Female* atau *Samantha* untuk EN), bukan lagi suara bawaan pertama yang
  kebetulan dipakai — yang di Windows biasanya suara robotik "Microsoft David".
- **Nada dan kecepatan diperhalus**: pitch turun dari 1,25 (terdengar melengking)
  ke 1,06, kecepatan 0,97–1,0.
- **Jawaban panjang dipotong di batas kalimat** (±430 karakter), jadi daftar
  mata kuliah yang panjang tidak dibacakan sampai habis dan tidak pernah terputus
  di tengah kata.

Catatan jujur: kualitas akhirnya tetap bergantung pada suara yang terpasang di
komputer booth. Kalau di laptop booth suaranya masih kurang enak, coba pasang
paket suara tambahan lewat Windows Settings → Time & Language → Speech, atau
pakai Chrome (suara Google-nya jauh lebih baik daripada bawaan Windows).

---

## 17. Suara ESI dibuat lebih manusiawi (tahap 2)

Tiga perubahan lagi setelah masukan "masih kurang seperti manusia":

**1. Suara dipilih berdasarkan penilaian, bukan urutan.** Dulu dicari nama yang
cocok dari daftar prioritas. Sekarang tiap suara di komputer diberi skor: suara
ber-label **Natural / Neural / Online / Premium** dapat nilai besar (inilah suara
generasi baru yang benar-benar terdengar manusiawi), sedangkan suara lama seperti
*Microsoft David* dan *Zira* diberi nilai minus. Di Windows 11 hasilnya biasanya
*Microsoft Aria Online (Natural)* untuk Inggris dan *Microsoft Gadis Online
(Natural)* untuk Indonesia — bedanya sangat terasa.

**2. Ada napas di antara kalimat.** Sebelumnya semua kalimat dilempar ke browser
sekaligus dan diucapkan beruntun tanpa jeda. Sekarang ESI punya antrean sendiri:
kalimat berikutnya baru diucapkan **setelah kalimat sebelumnya selesai plus jeda
pendek**. Jedanya menyesuaikan — lebih panjang setelah tanda tanya atau seru,
lebih pendek setelah kalimat pendek.

**3. Nada dan kecepatan tidak lagi seragam.** Bicara yang benar-benar rata justru
terdengar seperti mesin. Tiap kalimat kini diberi variasi kecil (kecepatan ±0,035,
nada ±0,05); kalimat pembuka sedikit lebih pelan, kalimat tanya naik nadanya,
kalimat seru sedikit lebih cepat dan tinggi. Diuji: lima kalimat berturut-turut
menghasilkan lima kombinasi nada/kecepatan yang berbeda.

Batasnya tetap sama seperti sebelumnya: kualitas akhir bergantung pada suara yang
terpasang di komputer booth. Kalau di Windows belum ada suara "Natural", pasang
lewat **Settings → Time & Language → Speech → Manage voices**, atau buka halaman
ini di Chrome.

---

## 18. Fitur baru: 📸 ESI Photo Booth

Section baru **`#photo`** — pengunjung berfoto, hasilnya jadi **satu strip foto**
yang layak di-posting.

### Alurnya

1. Nyalakan **📷 Camera**, pilih tema bingkai (🎞️ Classic, 🌇 Sunset, ❄️ Ice,
   🌌 Neon).
2. Tekan **Start Photo Session**. ESI menyebutkan **gaya konyol** yang harus
   ditiru — diambil acak dari 10 gaya (🙌 Hands up, ✌️ Peace, 🐧 Penguin flap,
   😎 Too cool, 🫶 Heart hands, 🤯 Mind blown, dan lainnya).
3. Ada waktu ±1,5 detik untuk membaca gayanya, lalu **hitungan mundur 3-2-1
   dengan nada naik**, kilat blitz, dan bunyi rana. Diulang **tiga kali**.
4. Selesai — **jingle kemenangan** berbunyi dan strip fotonya muncul.

### Hasilnya

Satu gambar PNG **760 × 1464 piksel** berisi:

- Judul **ESI PHOTO BOOTH** + "Information Systems · FAIDAS · Universitas Pelita
  Harapan", dengan pita gradien khas prodi
- Tiga foto bersudut membulat, masing-masing dengan **label gaya** di pojoknya
- Kaki strip: **@sisteminformasi.uph**, tanggal, tagar **#ESIPhotoBooth**, dan
  logo Sistem Informasi

Tombolnya: **💾 Save Photo** (unduh PNG dengan nama bertanggal), **📤 Share**
(memakai Web Share API di ponsel; kalau tidak didukung, diarahkan ke Save), dan
**🔁 Take Again**.

### Properti ESI di badan

Kalau pelacakan badan tersedia, ESI memakaikan **kacamata VR khasnya di mata**
(mengikuti kemiringan kepala), **syal oranye di leher**, dan **sirip oranye di
pergelangan tangan**. Sengaja hanya properti — wajah pengunjung tidak ditutupi,
karena ini foto mereka, bukan foto ESI. Bisa dimatikan lewat tombol **🥽 Props**.

### Dua keputusan penting dari hasil pengujian

**Kamera dinyalakan lebih dulu, properti menyusul.** Awalnya kamera baru hidup
setelah model pelacakan badan (±6 MB) selesai diunduh — di booth, pengunjung akan
menatap layar kosong beberapa detik dan mengira rusak. Sekarang pratinjau muncul
dalam sekejap, properti menempel belakangan saat modelnya siap.

**Kegagalan properti tidak boleh mematikan foto.** Modul pelacak pose menutup
kamera ketika gagal dimuat — kalau dibiarkan, kegagalan properti ikut mematikan
photo booth. Sekarang kamera dinyalakan ulang otomatis, tombol Props mati sendiri,
dan muncul pesan "goggles are off — but the photos still work". Diuji dengan
kondisi pelacakan pose sengaja digagalkan: ketiga foto tetap terambil dan strip
tetap jadi.

### Catatan

Ekspor foto **membutuhkan halaman dibuka lewat `http://localhost`** (XAMPP), bukan
klik ganda file. Kalau dibuka lewat `file://`, browser menganggap kanvas
"ternoda" dan menolak mengekspor gambar — ESI akan menampilkan pesan yang
menjelaskan ini, bukan gagal diam-diam.

Untuk mengubah gaya atau tema, sunting array **`POSES`** dan **`THEMES`** di dalam
blok "3b4. ESI PHOTO BOOTH".

---

## 19. Navigasi, tautan program, Sound Rocket & Photo Booth (revisi)

### 🧭 Menu navigasi dirapikan

Setelah jadi sembilan section, sepuluh tautan berjejer dalam satu baris memang
jadi berantakan dan membungkus ke baris kedua. Sekarang:

- **Kelima game dikelompokkan ke satu menu "🎮 Play ▾"** — dari 10 tautan tinggal
  **6 item** di baris atas: Ask ESI · Play ▾ · Specializations · Quiz ·
  Leaderboard · Booth.
- Di layar sempit (<980px) seluruh navigasi dilipat ke tombol **☰ Menu**, dengan
  submenu Play yang bisa dibuka-tutup.
- **Penanda posisi otomatis**: tautan section yang sedang dilihat menyala dengan
  warna gradien prodi; kalau yang dilihat salah satu game, tombol "Play" ikut
  menyala. Dipakai `IntersectionObserver`, jadi ringan.
- Menu tertutup sendiri saat tautan diklik, saat menekan **Esc**, atau saat
  mengklik di luar menu. Sudah pakai `aria-expanded`/`aria-controls`.

### 🔗 Tombol ke halaman program UPH

Setiap kartu peminatan sekarang punya tombol **"🔗 See this program on uph.edu ↗"**
yang membuka tab baru:

| Peminatan | Tautan |
| --- | --- |
| Data Science & Analytics | `uph.edu/program/data-science-and-data-analytics` |
| Interactive Intelligence Systems | `uph.edu/program/interactive-intelligence-systems/` |
| Digital Technology and Innovation | `uph.edu/program/digital-technology-and-innovation/` |

Tautan yang sama juga ditambahkan di **jawaban ESI di chat** — dalam dua bahasa,
jadi pengunjung yang bertanya lewat chat tetap bisa langsung ke halaman resminya.

### 🎤 Sound Rocket: sekarang bisa maju ke samping

Dulu ESI terkunci di satu garis tegak; suara hanya menaikkan. Sekarang **suara
juga memajukan ESI ke kanan**, jadi pemain bisa menjemput data, bukan menunggu
data datang. Ada garis putus-putus yang menandai sejauh mana ESI bisa maju kalau
berteriak penuh. Tombol **← →** juga bisa dipakai untuk menggeser sedikit.

**Catatan penting dari pengujian.** Versi pertama memakai kekerasan suara
*sesaat* untuk posisi mendatar — dan hasilnya justru **memperburuk** permainan:
skor pemain uji turun dari 540/860/1120 jadi 250/480/730. Sebabnya, tiap kali
pemain berhenti bersuara untuk turun mengambil data, ESI ikut tertarik mundur ke
kiri, jadi datanya lewat begitu saja. Sekarang yang dipakai **rata-rata suara**
(tetapan waktu ±1,2 detik): naik-turun cepat tidak menggeser posisi mendatar,
tapi teriakan panjang tetap membawa ESI maju. Skor naik lagi jadi
**620/820/1120** — di atas versi sebelum ada gerak menyamping. Jangkauan tangkap
data juga sedikit diperlonggar.

### 📸 Photo Booth: efek, foto grup, dan simpan ke Desktop

**Gaya diganti yang ramah rame-rame.** Dua belas gaya yang gampang ditiru
bersama: ✌️ Peace, 🙌 Hands up, 🫶 Heart hands, 👍 Thumbs up, 🤗 Squeeze in,
😜 Silly face, 👉 Point at us, 💪 Strong squad, 🐧 Penguin flap, 🦘 Jump!,
😎 Too cool, 🤳 Say ESI. Gaya rumit sengaja dibuang — kalau susah, satu orang
bengong dan fotonya berantakan.

**Tiga efek baru**, semuanya digambar penuh satu layar (bukan menempel di badan),
jadi tetap bekerja berapa pun jumlah orangnya:

- **🌀 Motion trail** — efek "velocity" yang diminta. Beberapa frame terakhir
  dirata-ratakan, jadi bagian yang diam tetap tajam sementara tangan yang
  bergerak meninggalkan bayangan. Paling seru dipakai untuk gaya 🦘 Jump.
- **✨ Sparkles** — kerlip bintang yang berkedip.
- **🎉 Confetti** — kertas warna-warni berjatuhan (default mati).

**Dua pilihan tata letak:**

- **🎞️ Strip (3 photos)** — strip tegak 760×1464 seperti sebelumnya.
- **🖼️ Group (1 big photo)** — satu foto besar mendatar **1268×907**, satu
  jepretan saja. Ini untuk foto rame-rame: mukanya jadi jauh lebih besar
  daripada dipaksakan ke strip bertiga.

**Simpan ke Desktop.** Tombol Save sekarang memakai *File System Access API*
kalau tersedia (Chrome/Edge di desktop): muncul dialog "Save As" sehingga bisa
langsung memilih **Desktop**. Kalau browsernya tidak mendukung, jatuh ke unduhan
biasa ke folder Downloads dengan pesan yang menjelaskan.

---

## 20. Photo Booth: bingkai polaroid & hasilnya kelihatan utuh

### Hasil foto tadinya terpotong

Penyebabnya ada dua, dan dua-duanya bug:

1. Panel hasil menumpuk **judul + gambar + tombol secara vertikal** di dalam area
   kanvas yang cuma setinggi ±540px, tanpa bisa digulir. Strip foto itu tinggi,
   jadi bagian bawahnya terpotong begitu saja.
2. Aturan lama `.game-overlay img { width: 110px; animation: bob }` — yang
   dibuat untuk gambar maskot di layar mulai — **ikut mengenai gambar strip**,
   sehingga hasil fotonya malah bergoyang naik-turun.

Sekarang gambar hasil dan tombolnya **berdampingan** (bertumpuk lagi di layar
sempit), panelnya bisa digulir, animasinya dimatikan, dan strip bisa **diketuk
untuk membuka ukuran penuh di tab baru**.

### Bentuknya sekarang polaroid sungguhan

Tiap foto dibungkus bingkai putih dengan **dagu bawah yang lebih lebar** —
ciri khas polaroid — berisi nama gaya (mis. "✌️ Peace!") dan nomor foto (1/3).
Tiap kartu **dimiringkan sedikit bergantian** (−1,5° / +1,1° / −0,7°) dan diberi
bayangan lembut, jadi terlihat seperti tiga foto yang ditempel, bukan gambar yang
ditumpuk lurus.

- **Strip**: 740 × 1638 piksel, tiga polaroid tegak
- **Group**: 1248 × 971 piksel, satu polaroid besar mendatar
- Kepala strip: ESI PHOTO BOOTH + nama prodi · Kaki strip: @sisteminformasi.uph,
  tanggal, #ESIPhotoBooth, dan logo Sistem Informasi
- Warna dasar strip mengikuti tema yang dipilih (Classic / Sunset / Ice / Neon),
  dengan gradien halus ke bawah

### Dua tombol untuk lanjut

Setelah selesai, panel hasil punya:

- **📷 Take New Photos** — langsung mulai sesi baru dengan pengaturan yang sama.
  Strip lama dibersihkan supaya tidak tercampur.
- **🎨 Change Style** — kembali ke layar awal untuk mengganti tata letak, warna
  bingkai, atau efek dulu sebelum foto lagi.

Ditambah **💾 Save Photo** dan **📤 Share** yang sudah ada.
