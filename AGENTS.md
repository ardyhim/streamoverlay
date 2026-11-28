# AGENTS.md — Panduan Pengembangan Overlay Streaming (OBS, Tako & YouTube Live Chat)


Aturan teknis wajib untuk pembuatan/modifikasi overlay di repositori ini. Bebas 
menggunakan tema dan gaya visual apa pun selama memenuhi ketentuan di bawah.


---


## 1. OBS Browser Source


- **Kanvas Transparan**: `body` wajib `background: transparent;` dan `overflow: 
hidden;`.

- **Responsif**: Hindari `max-width` tetap pada kontainer utama; gunakan `width: 100%; 
box-sizing: border-box;`.

- **Anti-Kedip (Anti-FOUT/FOIT)**:

  - Overlay yang me-*reload* iframe per detik (seperti Song Share) **dilarang** 
memakai webfont eksternal (`<link>`/`@import`); gunakan *system font* OS agar render 
0ms.

  - Kunci tinggi kontainer baris dinamis (`height`, `flex-wrap: nowrap`) dan beri 
ukuran eksplisit (`width`, `height`) pada `<img>` dinamis agar layout tidak bergeser.


---


## 2. Tako Custom Overlay


- **Referensi Utama**: Ikuti seluruh aturan struktur HTML, CSS (`body`, `rem`, tanpa 
`vh`/`vw`), JS (tanpa komentar `//`), dan daftar variabel resmi di:

  https://help.tako.id/id/article/cara-membuat-overlay-kustom-lsezav/

- **Catatan Produksi Tambahan**:

  - **Badges (`{{gifterBadges}}`, `{{requesterBadges}}`)**: Di produksi bisa mengirim 
**URL gambar dipisah koma** (bukan hanya nama teks). Selalu cek prefix `https://` / 
`http://` / `/` terlebih dahulu sebelum mencocokkan ke `BADGE_ICON_URLS`.

  - **Gambar**: Sembunyikan `<img>` kosong/belum terganti via CSS (`img[src=""], 
img[src*="{"] { display: none !important; }`) dan pasang fallback `onerror`.

  - **Song Share (`progress-tick`)**: Gunakan `.bar-fill` dan `#time-el`. Hindari 
`transition: width`, animasi loop CSS yang ter-reset tiap tick, serta render elemen 
statis (avatar, cover, status verified) langsung di HTML+CSS agar tidak berkedip 
*show/hide*.


---


## 3. YouTube Live Chat Custom CSS


- **Override**: Gunakan `!important` untuk menimpa style Shadow/Polymer YouTube.

- **Bersihkan UI**: Sembunyikan `yt-live-chat-header-renderer`, 
`yt-live-chat-message-input-renderer`, dan `#ticker`.

- **Pembedaan Role** (sertakan varian `[is-highlighted]`):

  - **Regular**: `yt-live-chat-text-message-renderer` (default).

  - **Membership**: `yt-live-chat-text-message-renderer[author-type="member"]` + 
fallback `:has(yt-live-chat-author-chip[is-member])`.

  - **Moderator**: `yt-live-chat-text-message-renderer[author-type="moderator"]`.

  - **Owner**: `yt-live-chat-text-message-renderer[author-type="owner"]`.

  - Reset `yt-live-chat-author-chip #author-name { background: transparent !important; 
}` dan atur warna `#author-name` serta `#message` per role.

- **Notifikasi Member**: Style `yt-live-chat-membership-item-renderer` (dengan `#card, 
#header { background: transparent !important; }`).


---


## 4. Standardisasi URL Parameters


Gunakan nama parameter baku berikut untuk semua overlay OBS Browser Source. Nama
yang sama **wajib** memiliki fungsi yang sama di semua scene, terutama pasangan
scene seperti `starting` dan `ending`.


### 4.1 Nama Parameter Baku

> **Catatan Kreativitas**: Seluruh parameter di bawah **bersifat opsional** (*non-strict*). 
Jangan terlalu terpaku untuk selalu memakai atau menampilkan semuanya apabila hal 
tersebut justru membatasi kreativitas desain, komposisi visual, atau konsep artistik 
overlay yang sedang dibuat. Gunakan hanya parameter yang relevan dengan kebutuhan 
scene, dengan nilai *default* yang tetap tampil estetis saat tidak ada parameter yang 
dikirim.

| Parameter | Nilai | Default | Fungsi |
| --- | --- | --- | --- |
| `streamer` | teks | identitas scene | Nama streamer / *main content*. |
| `ig` | handle | kosong | Username Instagram (awalan `@` opsional). |
| `tt` | handle | kosong | Username TikTok (awalan `@` opsional). |
| `yt` | handle | kosong | Username/channel YouTube (awalan `@` opsional). |
| `title` | teks | khusus scene | Judul/status scene. |
| `theme` | `light`, `dark` | `light` | Tema warna utama scene. |
| `bg` | `transparent` | kosong | Membuat seluruh background scene transparan. |
| `chat` | `box`, `off`, `none`, `hide` | `box` | Mode panel chat. Mode iframe aktif otomatis jika `chatId` diisi. |
| `chatId` | video ID YouTube | kosong | Mengaktifkan embed YouTube Live Chat. |
| `chatBg` | `light`, `dark`, `transparent` | ikut `theme` | Warna background chat, atau *cutout* jika `transparent`. |
| `chatCrop` | `1` | kosong | Memotong header/komposer pada iframe chat YouTube. |
| `media` | URL gambar/video/embed | aset bawaan | Media kustom untuk scene yang mendukung potret/konten dinamis: gambar, video, atau embed YouTube. |
| `minus` | angka (menit) | `5` untuk countdown utama; opsional untuk scene outtro | Durasi hitung mundur. |


### 4.2 Aturan Nilai dan Perilaku


- **Nama peka huruf besar/kecil**: gunakan camelCase persis seperti tabel
  (`chatId`, `chatBg`, `chatCrop`), bukan `chatid` atau `chat_bg`.

- **Urutan resolusi `chat`**: `off`/`none`/`hide` menyembunyikan panel; `box`
  memaksa mode OBS box; jika tidak ada nilai dan `chatId` diisi, mode iframe
  aktif; tanpa keduanya, default `box`.

- **Transparansi**: `bg=transparent` membuat seluruh scene transparan.
  `chatBg=transparent` hanya membuat area chat menjadi *cutout* dan hanya berlaku
  pada mode OBS box. Jangan menukar fungsi keduanya.

- **`chatId`**: isi dengan video ID saja, bukan URL lengkap. URL iframe baku:
  `https://www.youtube.com/live_chat?is_popout=1&v=<VIDEO_ID>&embed_domain=<HOST>`,
  tambahkan `&dark_theme=1` saat tema chat gelap. Terapkan tone/filter iframe yang
  sama di semua scene.

- **`chatCrop=1`**: hanya untuk iframe YouTube; potong bagian atas iframe agar
  header dan input chat tidak terlihat.

- **`minus`**: satuan menit, boleh desimal (`2.5`), diparse dengan `parseFloat`.
  Countdown dimulai ulang saat halaman di-refresh/dimuat.

- **`media`**: satu parameter untuk konten visual dinamis, bukan hanya gambar.
  Scene mendeteksi jenis media dari nilai/URL-nya:

  - **Gambar** (`.jpg`, `.jpeg`, `.png`, `.webp`, `.gif`, `.avif`, `.svg`): render
    dengan `<img>` biasa atau diolah ke canvas (mis. dithering).
  - **Video** (`.mp4`, `.webm`, `.mov`, atau URL video langsung): pakai `<video
    autoplay muted loop playsinline>` tanpa kontrol, dengan `object-fit: cover`.
  - **Embed YouTube** (`youtube.com/watch?v=`, `youtu.be/`, `youtube.com/shorts/`):
    normalisasi ke `https://www.youtube.com/embed/<VIDEO_ID>` dan tambahkan
    `autoplay=1&mute=1&loop=1&playlist=<VIDEO_ID>&controls=0&modestbranding=1&rel=0`.
    Jangan pernah memperlakukan URL embed sebagai sumber gambar.
    iframe tidak bisa dibaca pikselnya (lintas origin), jadi efek 1-bit dibuat
    dengan filter CSS monokrom (grayscale + contrast) plus overlay halftone di
    atas iframe. iframe wajib mengisi penuh slot media (skala cover, crop tengah),
    bukan kotak 16:9 berletterbox.

  Nilai yang tidak dikenali diperlakukan sebagai gambar. Media wajib mengisi frame
  secara `object-fit: cover` (crop terpusat) agar layout scene tidak bergeser saat
  rasio sumber berbeda.

- **CORS dan fallback `media`**: sumber gambar/video yang diproses canvas wajib
  mengizinkan CORS. Scene wajib menyediakan fallback ke aset bawaan jika media gagal
  dimuat, termasuk embed YouTube yang tidak mengizinkan pemutaran (fallback ke
  potret/aset statis, bukan kotak kosong).

- **`img` bukan nama baku**: `img` hanya alias lama dari `media`. Kode baru wajib
  membaca `media` lebih dulu, misalnya `params.get("media") || params.get("img")`,
  dan dokumentasi baru tidak boleh memperkenalkan `img` lagi.

- **`theme`**: scene wajib menurunkan warna background, ink, chat, dan tone iframe
  dari tema yang dipilih. `chatBg` boleh menimpa warna chat saja.


### 4.3 Atribut Internal Baku


Selain URL parameter, gunakan atribut internal yang sama agar CSS/JS konsisten:

- `data-theme="dark"` untuk tema gelap.
- `data-bg="transparent"` untuk scene transparan.
- `data-chat-bg="light|dark"` untuk warna chat.
- `data-chat-cutout="1"` untuk mode cutout chat.
- `data-chat-crop="1"` untuk crop iframe.
- `data-iframe-tone="light|dark"` untuk tone iframe YouTube.

Jangan membuat atribut baru untuk fungsi yang sudah diwakili atribut di atas.


### 4.4 Aturan Konsistensi Antar Scene


- **Satu fungsi, satu nama.** Jangan membuat nama baru untuk fungsi yang sudah ada.
  Alias lama seperti `chatMode`, `min`, `minutes`, `showChat`, atau `img` hanya boleh
  dipertahankan untuk kompatibilitas; kode dan dokumentasi baru wajib memakai nama
  baku (`chat`, `minus`, `chatId`, `media`, dan seterusnya).

