# Jaden Bahtera Overlay

Overlay streaming bertema **1-bit bitmap / dithering** untuk OBS, plus dua overlay kustom untuk **Tako** (Alert dan Song Share).

Semua scene memakai latar transparan, font sistem, dan animasi canvas agar aman dipakai di OBS Browser Source.

## Isi Folder

| File | Fungsi |
| --- | --- |
| `starting.html` | Scene pembuka / *starting soon* dengan countdown, judul, sosial media, dan slot live chat. |
| `ending.html` | Scene penutup / *thanks for watching* dengan countdown offline opsional dan slot live chat. |
| `livechat.css` | Style panel chat (cutout, crop iframe, dan tone iframe). File HTML sudah membawa style-nya sendiri; file ini bisa dipakai untuk eksperimen atau Custom CSS OBS. |
| `bg.jpg` | Aset background / preview. |
| `tako/alert.html` | Custom HTML untuk overlay **Alert** Tako. |
| `tako/songshare.html` | Custom HTML untuk overlay **Song Share** Tako. |

---

## 1. Menggunakan Overlay di OBS

### Tambah Browser Source

1. Di OBS, buka panel **Sources** lalu klik **+** → **Browser**.
2. Beri nama, misalnya `STARTING` atau `ENDING`.
3. Isi **URL** dengan salah satu opsi berikut:
   - **File lokal:** `file:///D:/CODING/streamoverlay/jadenbahtera/starting.html`
   - **Hosting / GitHub Pages:** `https://<domain>/jadenbahtera/starting.html`
4. Atur **Width** dan **Height** sesuai kanvas OBS. Untuk kanvas 1920×1080 gunakan `1920` dan `1080`.
5. Set **FPS** ke `60` agar animasi dithering dan countdown halus.
6. Biarkan **Shutdown source when not visible** tidak dicentang jika overlay perlu tetap berjalan saat pindah scene.
7. Opsional: centang **Refresh browser when scene becomes active** agar countdown mulai ulang setiap scene diaktifkan.

> Jika memakai file lokal dan parameter URL tidak terbaca, jalankan folder ini lewat local server atau host ke GitHub Pages, lalu gunakan URL `http(s)`.

### Transparansi

`body` sudah transparan secara default. Ada dua cara membuat area transparan:

| Parameter | Efek |
| --- | --- |
| `bg=transparent` | Seluruh background scene transparan. Cocok jika chat diletakkan sebagai source terpisah di bawah overlay. |
| `chatBg=transparent` | Hanya area chat yang dibuat *cutout* (berlubang). Bagian lain scene tetap punya background. Cocok untuk menaruh chat OBS/Tako di belakang panel chat. |

### Mode Chat

Panel chat di `starting.html` dan `ending.html` punya tiga kondisi:

| Mode | Cara | Keterangan |
| --- | --- | --- |
| **OBS Box** (default) | Tanpa `chatId`, atau `chat=box` | Panel chat tampil sebagai kotak. Chat dari source lain bisa diletakkan di atas panel, atau di belakang panel dengan `chatBg=transparent`. |
| **YouTube Iframe** | Isi `chatId=VIDEO_ID` | Chat YouTube Live di-embed langsung ke dalam overlay. Gunakan video ID, bukan URL lengkap. |
| **Hidden** | `chat=off`, `chat=none`, atau `chat=hide` | Panel chat disembunyikan dan area utama melebar. |

`chatMode` masih diterima sebagai alias lama dari `chat`.

#### Contoh layering OBS Box Chat

1. Tambahkan source chat (YouTube Live Chat / Tako chat) sebagai Browser Source.
2. Posisikan source chat tepat di area panel chat overlay.
3. Tambahkan `starting.html` atau `ending.html` sebagai Browser Source **di atas** source chat.
4. Buka URL overlay dengan `?chatBg=transparent` agar area chat menjadi lubang dan chat di bawahnya terlihat.

Kalau ingin panel chat punya background sendiri, cukup gunakan `chat=box` tanpa `chatBg=transparent`, lalu taruh source chat di atas panel.

---

## 2. Parameter `starting.html` dan `ending.html`

Kedua scene menerima parameter yang sama.

| Parameter | Nilai | Default | Fungsi |
| --- | --- | --- | --- |
| `streamer` | teks | `@BITMAP_STUDIO` | Nama streamer di chip atas dan rail samping. |
| `ig` | handle | kosong | Handle Instagram. Awalan `@` opsional. |
| `tt` | handle | kosong | Handle TikTok. Awalan `@` opsional. |
| `yt` | handle | kosong | Handle YouTube. Awalan `@` opsional. |
| `title` | teks | `STARTING` / `THANKS FOR WATCHING` | Judul utama scene. |
| `theme` | `light` atau `dark` | `light` | Palet warna scene. |
| `bg` | `transparent` | kosong | Membuat seluruh background transparan. |
| `chatBg` | `light`, `dark`, `transparent` | ikut tema | Warna panel chat, atau cutout jika `transparent`. |
| `chat` | `box`, `off`, `none`, `hide` | `box` | Mode panel chat. |
| `chatId` | video ID YouTube | kosong | Mengaktifkan mode iframe YouTube Live Chat. |
| `chatCrop` | `1` | kosong | Memotong bagian atas iframe chat YouTube. |
| `img` | URL gambar | potret bawaan | Gambar kustom yang akan di-dither menjadi potret 1-bit. |
| `minus` | angka (menit) | `5` di starting; opsional di ending | Durasi countdown. |

### Catatan countdown

- `starting.html` selalu menampilkan countdown utama. Jika `minus` tidak diisi, durasinya 5 menit.
- `ending.html` menampilkan countdown kecil berbentuk pill hanya jika `minus` diisi. Contoh: `minus=3` → `OFFLINE: 03:00`.
- Hitung mundur dimulai saat halaman di-refresh/dimuat.

### Catatan iframe YouTube

- `chatId` harus berupa **video ID**, misalnya `dQw4w9WgXcQ`, bukan `https://youtube.com/watch?v=...`.
- Jika `chatId` diisi, mode iframe otomatis aktif kecuali `chat=off` atau `chat=box`.
- `chatCrop=1` berguna untuk menyembunyikan header dan kolom input chat YouTube.
- `chatBg=dark` atau `theme=dark` akan memakai tone gelap untuk iframe.

### Contoh URL

```text
# Starting: countdown 10 menit + sosial media
starting.html?streamer=JadenBahtera&ig=jadenbahtera&tt=jadenbahtera&yt=jadenbahtera&minus=10

# Starting: panel chat transparan untuk chat OBS di belakangnya
starting.html?streamer=JadenBahtera&chatBg=transparent

# Ending: embed YouTube Live Chat + crop header
ending.html?streamer=JadenBahtera&chatId=VIDEO_ID&chatCrop=1&minus=3

# Ending: tema gelap, tanpa panel chat
ending.html?theme=dark&chat=off

# Potret kustom
starting.html?img=https://example.com/foto.jpg
```

> `img` diambil dengan `crossOrigin="anonymous"`. Jika server gambar tidak mengizinkan CORS, overlay otomatis kembali ke potret bawaan.

---

## 3. Custom HTML di Tako

File `tako/alert.html` dan `tako/songshare.html` adalah overlay HTML yang dijalankan oleh Tako. Jangan membukanya sebagai halaman biasa di OBS; pasang lewat dashboard Tako agar variabel seperti `{{gifterName}}` terisi.

### Cara memasang

1. Buka dashboard **Tako**.
2. Pilih overlay yang ingin dikustom, misalnya **Alert** atau **Song Share**.
3. Buka pengaturan overlay, lalu pilih opsi **Custom HTML** / **Custom Overlay**.
4. Copy seluruh isi file:
   - `tako/alert.html` untuk Alert
   - `tako/songshare.html` untuk Song Share
5. Tempel ke editor HTML Tako, lalu simpan.
6. Klik **Test** atau picu alert/request lagu untuk memastikan tampilan dan variabel berjalan.
7. Copy URL overlay yang diberikan Tako.
8. Di OBS, tambahkan **Browser Source** baru dan tempel URL tersebut.
9. Atur ukuran Browser Source sesuai kebutuhan. Contoh:
   - Alert: sekitar `480 × 220`
   - Song Share: sekitar `480 × 280` (bisa bertambah jika antrean tampil)

Ukuran akhir tetap bisa disesuaikan. Tako memakai ukuran font overlay untuk scaling, jadi gunakan satuan `rem` saat mengedit.

### Aturan penting saat mengedit HTML Tako

Aturan ini penting agar overlay tidak blank atau berkedip:

- Gunakan struktur HTML standar (`<html>`, `<head>`, `<body>`).
- Jangan memakai `position: absolute` pada `body`; beri tinggi body yang pasti.
- Gunakan `px`, `rem`, atau `em`. **Jangan gunakan `vh` atau `vw`.**
- Jangan memakai komentar `//` di dalam `<script>`. Tako menghapus newline sebelum render, sehingga komentar `//` bisa “menelan” kode berikutnya. Gunakan `/* ... */` atau hindari komentar.
- Variabel JSON ditulis literal tanpa tanda kutip, contoh: `const queueData = {{queue}};`
- Jangan memberi `max-width` tetap pada container utama. Gunakan `width: 100%` agar mengikuti ukuran box OBS/Tako.
- Jangan mengganti nama variabel Tako. Contoh yang benar: `{{gifterName}}`, bukan `{{gifter_name}}`.
- Untuk Song Share, jangan memakai webfont eksternal. Overlay Song Share di-render ulang secara berkala; font eksternal bisa menyebabkan kedip. Gunakan font sistem seperti pada file contoh.
- Tetap gunakan `.bar-fill` dan `#time-el` untuk progress bar Song Share. Hindari `transition: width` dan animasi CSS yang di-reset setiap update.


### Kustomisasi cepat

- **Warna:** ubah `--bg` dan `--ink` di blok `:root`.
- **Teks label:** cari teks seperti `TAKO_SUPPORT // 1-BIT`, `SONG_SHARE // 1-BIT`, atau `SIGNAL_RECEIVED`.
- **Ukuran:** ubah nilai `rem` pada font, padding, dan border.
- **Badge:** edit `BADGE_ICON_URLS`.
- **Fallback gambar:** biarkan `img[src=""], img[src*="{"] { display: none !important; }` dan handler `onerror` agar gambar kosong tidak merusak layout.

---

## 4. Troubleshooting

| Masalah | Penyebab umum | Solusi |
| --- | --- | --- |
| Background OBS hitam | OBS menampilkan transparansi sebagai hitam di preview | Pastikan tidak ada Color Source di belakang overlay; cek source lain di scene. |
| Cutout chat tidak terlihat | `chatBg=transparent` dipakai saat mode iframe | Cutout hanya berlaku untuk mode OBS box. Gunakan `chat=box` dan taruh chat source di bawah overlay. |
| Iframe YouTube kosong | `chatId` diisi URL lengkap atau video tidak live | Gunakan video ID saja dan pastikan live chat tersedia. |
| Countdown tidak mulai | Halaman tidak di-refresh | Refresh Browser Source. Countdown dimulai saat halaman dimuat. |
| Potret kustom tidak muncul | URL gambar tidak bisa diakses atau CORS diblokir | Gunakan URL `https` yang mengizinkan CORS; overlay akan kembali ke potret bawaan jika gagal. |
| Overlay Tako blank | Ada komentar `//`, `vh`/`vw`, atau placeholder berubah | Ikuti aturan edit di atas dan kembalikan nama variabel persis seperti contoh. |
| Song Share berkedip | Memakai webfont eksternal atau animasi yang di-reset | Gunakan font sistem dan hindari animasi loop pada elemen yang di-render ulang. |
| Progress bar Song Share tidak bergerak | `.bar-fill` atau `#time-el` dihapus/diubah | Pertahankan struktur dan ID tersebut. |

---

## Lisensi dan Kredit

Overlay ini dibuat untuk kebutuhan channel **Jaden Bahtera**. Silakan modifikasi warna, teks, dan komposisi sesuai branding kamu.
