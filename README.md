# Stream Overlay

Kumpulan overlay HTML untuk OBS / browser source, termasuk overlay kustom untuk [Tako](https://tako.id).

## Struktur

```
index.html          galeri semua overlay
Memphis/
  index.html
  starting.html
  waiting.html

Postmodern/
  starting.html
  ending.html
  livechat.css      stylesheet YouTube live chat (OBS)
  tako/
    alert.html
    milestone.html
    songshare.html
```

## Preview

https://ardyhim.github.io/streamoverlay/

Overlay Tako tidak di-preview di galeri karena file-nya masih berisi placeholder `{{...}}`
yang hanya diisi oleh Tako. Isi file `Postmodern/tako/*.html` di-copy langsung ke dashboard Tako.

## Catatan

- Semua overlay berupa HTML mandiri (CSS + JS inline), tanpa build step.
- Ukuran memakai `rem` supaya mudah di-scale lewat pengaturan ukuran font.
- `Postmodern/livechat.css` adalah stylesheet terpisah untuk YouTube live chat di OBS:
  tempel isinya ke tab CSS browser source, bukan dibuka sebagai halaman.
- Overlay Tako memakai placeholder `{{...}}` yang diisi otomatis oleh Tako saat render.
- Hindari komentar `//` di dalam `<script>` pada overlay Tako, karena Tako menghapus newline sebelum merender.
