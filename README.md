# Stream Overlay

Kumpulan overlay HTML untuk OBS / browser source, termasuk overlay kustom untuk [Tako](https://tako.id).

## Preview

https://ardyhim.github.io/streamoverlay/

Sebagian overlay Tako tidak di-preview di galeri karena file-nya masih berisi placeholder
`{{...}}` yang hanya diisi oleh Tako. Isi file-nya di-copy langsung ke dashboard Tako.

## Parameter URL

Beberapa overlay bisa dikustomisasi lewat query string di URL browser source, misalnya:

```
starting.html?name=Jaden&minutes=10&transparent=1
```

Nama panjang adalah yang disarankan. Alias pendek tetap didukung agar URL lama tidak rusak.
Tidak semua overlay memakai semua parameter — parameter yang tidak relevan akan diabaikan.

| Parameter | Alias | Fungsi |
| --- | --- | --- |
| `name` | `streamer`, `user` | Nama streamer/host |
| `instagram` | `ig` | Handle Instagram |
| `tiktok` | `tt` | Handle TikTok |
| `youtube` | `yt` | Handle YouTube |
| `title` | | Judul utama scene |
| `subtitle` | `sub` | Subjudul |
| `status` | `tag` | Teks status / tag sinyal |
| `message` | `msg` | Pesan tambahan di scene |
| `chat` | | Video ID YouTube untuk iframe live chat, `0` untuk mematikan |
| `domain` | | Embed domain iframe chat (default: hostname) |
| `minutes` | `min` | Durasi hitung mundur dalam menit |
| `seconds` | `sec` | Durasi hitung mundur dalam detik (menang atas `minutes`) |
| `transparent` | | `1` untuk latar transparan, tanpa panel/backdrop |
| `demo` | | `1` untuk mengisi data contoh (Tako alert, scene tertentu) |

Parameter di luar tabel bersifat tambahan dan hanya berlaku di scene terkait, misalnya `theme`,
`mode`, `fps`, `badge`, `channel`/`ch`, `cta`/`done`, `label`/`tape`, `opacity`/`alpha`, `slot`,
`cutout`, atau `start=HH:MM` untuk jam mulai.

Nilai teks yang mengandung spasi atau tanda baca sebaiknya di-encode (`%20`, `%2F`, dan
seterusnya). Parameter yang tidak diisi akan memakai nilai default di dalam file.

## Catatan

- Semua overlay berupa HTML mandiri (CSS + JS inline), tanpa build step — cukup dibuka
  di browser atau ditempel ke browser source.
- Stylesheet tambahan (misalnya untuk YouTube live chat) ditempel di tab CSS browser source,
  bukan dibuka sebagai halaman.

### Setup Custom HTML Alert di Tako

1. Buka dashboard Tako → menu **Overlay** → pilih **Alert**.
2. Aktifkan opsi **Custom HTML** pada pengaturan alert (bukan tema bawaan).
3. Copy seluruh isi file alert, dari `<html>` sampai `</html>`, lalu tempel ke kolom HTML
   dan simpan.
4. Atur lebar/tinggi box overlay dan ukuran font di pengaturan overlay. Karena layout memakai
   `rem`, semua elemen ikut menyesuaikan tanpa perlu edit kode.
5. Simpan, lalu copy **URL overlay** yang diberikan Tako.
6. Di OBS: tambah **Browser Source**, tempel URL tadi, dan samakan Width/Height dengan box
   overlay di dashboard.
7. Tes lewat tombol tes di dashboard atau donasi kecil, lalu cek tampilannya di OBS.

Tips saat setup: jangan menambahkan `?demo=1` ke URL yang dipakai OBS (parameter itu hanya
untuk preview contoh), dan setelah mengubah kode di dashboard, simpan ulang lalu refresh
browser source (klik kanan → Refresh) supaya perubahan terbaca.
