# Stream Overlay

Kumpulan overlay HTML untuk OBS / browser source, termasuk overlay kustom untuk [Tako](https://tako.id).

## Struktur

```
Memphis/
  index.html
  starting.html
  waiting.html

Postmodern/
  starting.html
  ending.html
  tako/
    alert.html
    milestone.html
    songshare.html
```

## Catatan

- Semua overlay berupa HTML mandiri (CSS + JS inline), tanpa build step.
- Ukuran memakai `rem` supaya mudah di-scale lewat pengaturan ukuran font.
- Overlay Tako memakai placeholder `{{...}}` yang diisi otomatis oleh Tako saat render.
- Hindari komentar `//` di dalam `<script>` pada overlay Tako, karena Tako menghapus newline sebelum merender.
