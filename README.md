# Dokumentasi Template Skripsi — SaDhar

Dokumen ini menjelaskan cara menyiapkan, menjalankan, dan menulis skripsi dengan template ini:
mulai dari instalasi MiKTeX dan paketnya, konfigurasi VS Code + LaTeX Workshop, struktur file,
cara kompilasi, *troubleshooting*, sampai cara menulis persamaan, algoritma, singkatan, daftar
pustaka, tabel, gambar, kode, dan lampiran.

## Daftar Isi

1. [Gambaran Singkat](#1-gambaran-singkat)
2. [Instalasi MiKTeX dan Paket LaTeX](#2-instalasi-miktex-dan-paket-latex)
3. [Instalasi Font](#3-instalasi-font)
4. [Instalasi Perl (khusus Windows)](#4-instalasi-perl-khusus-windows)
5. [Instalasi VS Code dan LaTeX Workshop](#5-instalasi-vs-code-dan-latex-workshop)
6. [Pengaturan User `settings.json` di VS Code](#6-pengaturan-user-settingsjson-di-vs-code)
7. [Struktur Direktori dan Penjelasan Tiap File](#7-struktur-direktori-dan-penjelasan-tiap-file)
8. [Cara Menjalankan (Kompilasi)](#8-cara-menjalankan-kompilasi)
9. [Mengisi Data Diri dan Mengatur Halaman Awal](#9-mengisi-data-diri-dan-mengatur-halaman-awal)
10. [Menambah Bab Baru](#10-menambah-bab-baru)
11. [Daftar Pustaka (Sitasi)](#11-daftar-pustaka-sitasi)
12. [Daftar Singkatan](#12-daftar-singkatan)
13. [Persamaan (Equation)](#13-persamaan-equation)
14. [Algoritma (Pseudocode)](#14-algoritma-pseudocode)
15. [Tabel](#15-tabel)
16. [Gambar](#16-gambar)
17. [Kode Program (Listing)](#17-kode-program-listing)
18. [Flowchart dengan TikZ](#18-flowchart-dengan-tikz)
19. [Lampiran](#19-lampiran)
20. [Label dan Referensi Silang](#20-label-dan-referensi-silang)
21. [Troubleshooting](#21-troubleshooting)
22. [Ringkasan Cepat (Cheat Sheet)](#22-ringkasan-cepat-cheat-sheet)

---

## 1. Gambaran Singkat

| Komponen             | Yang dipakai template ini                                      |
|----------------------|----------------------------------------------------------------|
| Distribusi LaTeX     | MiKTeX (Windows/macOS/Linux). TeX Live juga bisa.              |
| *Engine*             | **LuaLaTeX** (wajib, karena memakai `fontspec` + Times New Roman) |
| Daftar pustaka       | `biblatex` gaya **APA** + **biber**                            |
| Daftar singkatan     | `glossaries` + **makeglossaries**                              |
| Editor               | VS Code + ekstensi **LaTeX Workshop**                          |
| File utama           | `main.tex`                                                     |

Urutan kompilasi lengkap yang dibutuhkan:

```
lualatex  →  biber  →  makeglossaries  →  lualatex  →  lualatex
```

> Kenapa berkali-kali? Kompilasi pertama membuat file bantu (`.bcf`, `.glo`, `.aux`),
> biber dan makeglossaries mengolahnya, lalu dua kompilasi terakhir memasukkan hasilnya
> (daftar pustaka, singkatan, nomor halaman di daftar isi) ke PDF.

---

## 2. Instalasi MiKTeX dan Paket LaTeX

### 2.1 Instal MiKTeX

1. Unduh dari <https://miktex.org/download> lalu instal.
2. Saat instalasi, pada opsi **"Install missing packages on-the-fly"** pilih **Yes** (atau
   *Always*). Dengan ini paket yang belum ada akan diunduh otomatis saat kompilasi.
3. Setelah selesai, buka **MiKTeX Console** → menu **Updates** → **Check for updates** →
   **Update now**. Lakukan juga di tab **Updates** untuk mode *admin* bila MiKTeX diinstal untuk
   semua user.
4. Pastikan perintah berikut dikenali di terminal (Command Prompt / PowerShell / terminal):

   ```bash
   lualatex --version
   biber --version
   makeglossaries --version
   latexmk --version
   ```

   Jika "command not found", restart komputer atau tambahkan folder `bin` MiKTeX ke `PATH`
   (Windows biasanya `C:\Users\<nama>\AppData\Local\Programs\MiKTeX\miktex\bin\x64\`).

### 2.2 Daftar paket yang harus terinstal

Meskipun *on-the-fly install* sudah aktif, sangat disarankan menginstal paket di bawah secara
manual dulu. Instalasi otomatis kadang gagal di tengah kompilasi (terutama lewat VS Code yang
tidak bisa menampilkan dialog konfirmasi MiKTeX).

Cara instal manual lewat GUI: **MiKTeX Console → Packages** → cari nama paket → klik **+**.

**Engine & alat kompilasi**

| Paket MiKTeX     | Kegunaan                                                    |
|------------------|-------------------------------------------------------------|
| `luatex`         | Engine LuaLaTeX                                             |
| `luaotfload`     | Pemuat font OpenType/TrueType untuk LuaLaTeX                |
| `latexmk`        | Otomatisasi kompilasi (dipakai LaTeX Workshop)              |
| `biber`          | Pemroses daftar pustaka untuk biblatex                      |
| `glossaries`     | Daftar singkatan + skrip `makeglossaries`                   |

**Bahasa, font, dan tata letak**

| Paket MiKTeX         | Paket `\usepackage` yang disediakan                  |
|----------------------|------------------------------------------------------|
| `babel`              | `babel`                                              |
| `babel-indonesian`   | bahasa Indonesia untuk babel                         |
| `babel-english`      | bahasa Inggris untuk babel                           |
| `fontspec`           | `fontspec`                                           |
| `addfont`            | `addfont`                                            |
| `psnfss`             | `courier`                                            |
| `geometry`           | `geometry` (margin 4-3-3-3 cm)                       |
| `setspace`           | `setspace` (spasi ganda)                             |
| `titlesec`           | `titlesec` (format judul bab/subbab)                 |
| `fancyhdr`           | `fancyhdr` (nomor halaman)                           |
| `tocloft`            | `tocloft` (daftar isi, daftar persamaan, dll.)       |
| `tocbibind`          | `tocbibind`                                          |
| `ragged2e`           | `ragged2e` (`\justifying`)                           |
| `enumitem`           | `enumitem`                                           |
| `tools`              | `indentfirst`, `longtable`, `tabularx`, `calc`       |
| `koma-script`        | `scrwfile`, `scrhack`                                |
| `etoolbox`           | `etoolbox`                                           |
| `chngcntr`           | `chngcntr`                                           |
| `comment`            | `comment`                                            |
| `lipsum`             | `lipsum` (teks contoh)                               |
| `background`         | `background` (watermark)                             |
| `everypage`          | dependensi `background`                              |
| `appendix`           | `appendix`                                           |
| `import`             | `import` (`\subimport`)                              |
| `hyperref`           | `hyperref`                                           |

**Matematika, algoritma, kode**

| Paket MiKTeX     | Paket `\usepackage` yang disediakan                  |
|------------------|------------------------------------------------------|
| `amsmath`        | `amsmath` (persamaan)                                |
| `icomma`         | `icomma` (koma desimal)                              |
| `algorithms`     | `algorithm` (lingkungan *float* algoritma)           |
| `algorithmicx`   | `algorithmicx`, `algpseudocode`                      |
| `listings`       | `listings` (kode program)                            |
| `pythontex`      | `pythontex`                                          |

**Tabel, gambar, dan grafik**

| Paket MiKTeX     | Paket `\usepackage` yang disediakan                  |
|------------------|------------------------------------------------------|
| `graphics`       | `graphicx`                                           |
| `float`          | `float` (posisi `[H]`)                               |
| `caption`        | `caption`, `subcaption`                              |
| `rotating`       | `rotating`                                           |
| `pdflscape`      | `pdflscape` (halaman *landscape*)                    |
| `pdfpages`       | `pdfpages` (sisipkan PDF)                            |
| `overpic`        | `overpic`                                            |
| `tabularray`     | `tabularray` (`longtblr`)                            |
| `ninecolors`     | dependensi `tabularray`                              |
| `xcolor`         | `xcolor`                                             |
| `csvsimple`      | `csvsimple-l3` (impor CSV ke tabel)                  |
| `pgf`            | `tikz`, `pgffor`                                     |
| `pgfplots`       | `pgfplots` (grafik)                                  |
| `tikz-network`   | `tikz-network`                                       |
| `quiver`         | `quiver`                                             |
| `forest`         | `forest` (pohon direktori)                           |
| `neuralnetwork`  | `neuralnetwork`                                      |

**Daftar pustaka & singkatan**

| Paket MiKTeX     | Kegunaan                                             |
|------------------|------------------------------------------------------|
| `biblatex`       | `biblatex`                                           |
| `biblatex-apa`   | gaya sitasi APA (`style=apa`)                        |
| `glossaries`     | `glossaries`                                         |
| `mfirstuc`, `tracklang`, `xfor`, `datatool` | dependensi `glossaries`   |

### 2.3 Instal semua paket sekaligus lewat terminal

**Windows (PowerShell):**

```powershell
$pkgs = @(
  "luatex","luaotfload","latexmk","biber","glossaries","mfirstuc","tracklang","xfor","datatool",
  "babel","babel-indonesian","babel-english","fontspec","addfont","psnfss","geometry","setspace",
  "titlesec","fancyhdr","tocloft","tocbibind","ragged2e","enumitem","tools","koma-script",
  "etoolbox","chngcntr","comment","lipsum","background","everypage","appendix","import","hyperref",
  "amsmath","icomma","algorithms","algorithmicx","listings","pythontex",
  "graphics","float","caption","rotating","pdflscape","pdfpages","overpic","tabularray",
  "ninecolors","xcolor","csvsimple","pgf","pgfplots","tikz-network","quiver","forest",
  "neuralnetwork","biblatex","biblatex-apa"
)
foreach ($p in $pkgs) { miktex packages install $p }
```

**Linux / macOS (bash/zsh):**

```bash
for p in luatex luaotfload latexmk biber glossaries mfirstuc tracklang xfor datatool \
  babel babel-indonesian babel-english fontspec addfont psnfss geometry setspace \
  titlesec fancyhdr tocloft tocbibind ragged2e enumitem tools koma-script \
  etoolbox chngcntr comment lipsum background everypage appendix import hyperref \
  amsmath icomma algorithms algorithmicx listings pythontex \
  graphics float caption rotating pdflscape pdfpages overpic tabularray \
  ninecolors xcolor csvsimple pgf pgfplots tikz-network quiver forest \
  neuralnetwork biblatex biblatex-apa; do
  miktex packages install "$p"
done
```

> Versi MiKTeX lama memakai `mpm --install=<paket>` sebagai ganti `miktex packages install <paket>`.
> Pesan "package is already installed" tidak masalah, abaikan saja.

Setelah instalasi, perbarui database nama file:

```bash
miktex fndb refresh
miktex fontmaps configure
```

(Atau di MiKTeX Console: **Tasks → Refresh file name database** dan **Tasks → Refresh font map files**.)

---

## 3. Instalasi Font

Template memakai **Times New Roman** (teks utama) dan **Courier New** (algoritma). Font ini
diambil dari sistem operasi, bukan dari MiKTeX.

- **Windows / macOS**: sudah tersedia bawaan.
- **Linux (Ubuntu/Debian)**:

  ```bash
  sudo apt install ttf-mscorefonts-installer
  fc-cache -fv
  ```

  Distro lain: cari paket `msttcorefonts` / `ttf-ms-fonts`.

Cek font terdeteksi: `fc-list | grep -i "times new roman"`.

---

## 4. Instalasi Perl (khusus Windows)

`latexmk` dan `makeglossaries` adalah skrip **Perl**. Di Windows, Perl tidak terpasang bawaan.

1. Unduh **Strawberry Perl** dari <https://strawberryperl.com/> dan instal.
2. Restart VS Code (atau komputer) agar `PATH` terbarui.
3. Cek: `perl --version`.

Kalau tidak mau memasang Perl, ganti tool `makeglossaries` dengan `makeglossaries-lite`
(versi Lua, sudah ikut paket `glossaries`) — lihat bagian [6.3](#63-alternatif-tanpa-perl-windows).
Namun `latexmk` tetap membutuhkan Perl.

Linux/macOS umumnya sudah memiliki Perl.

---

## 5. Instalasi VS Code dan LaTeX Workshop

1. Instal VS Code dari <https://code.visualstudio.com/>.
2. Buka VS Code → tab **Extensions** (`Ctrl+Shift+X`).
3. Cari **LaTeX Workshop** (penerbit: *James Yu*) → **Install**.
4. (Opsional, disarankan) ekstensi tambahan:
   - **LTeX+** / **Code Spell Checker** — cek ejaan.
   - **Rainbow CSV** — mempermudah edit file CSV di `Back_matters/attachments`.
5. Buka **folder** template ini (bukan hanya file `main.tex`): **File → Open Folder…**.

Setelah terpasang, muncul ikon **TeX** di *Activity Bar* kiri. Dari situ tersedia menu
*Build LaTeX project*, *View LaTeX PDF*, *Clean up auxiliary files*, dan daftar *recipe*.

---

## 6. Pengaturan User `settings.json` di VS Code

Bawaan LaTeX Workshop memakai `pdflatex` dan tidak menjalankan `makeglossaries`, sehingga
template ini **akan gagal** jika tidak diatur ulang.

### 6.1 Membuka `settings.json`

`Ctrl+Shift+P` → ketik **Preferences: Open User Settings (JSON)** → Enter.

Lokasi file:
- Windows: `%APPDATA%\Code\User\settings.json`
- Linux: `~/.config/Code/User/settings.json`
- macOS: `~/Library/Application Support/Code/User/settings.json`

### 6.2 Isi yang ditambahkan

Tambahkan blok berikut **di dalam** kurung kurawal `{ ... }` paling luar (jika sudah ada
`latex-workshop.latex.tools` atau `latex-workshop.latex.recipes`, **ganti**, jangan duplikat):

```jsonc
{
  // ... pengaturan lain yang sudah ada ...

  "latex-workshop.latex.tools": [
    {
      "name": "lualatexmk",
      "command": "latexmk",
      "args": [
        "-synctex=1",
        "-interaction=nonstopmode",
        "-file-line-error",
        "-pdf",
        "-pdflatex=lualatex %O %S",
        "%DOC%"
      ]
    },
    {
      "name": "biber",
      "command": "biber",
      "args": ["%DOCFILE%"]
    },
    {
      "name": "makeglossaries",
      "command": "makeglossaries",
      "args": ["%DOCFILE%"]
    }
  ],

  "latex-workshop.latex.recipes": [
    {
      "name": "lualatex + biber + glossaries",
      "tools": [
        "lualatexmk",
        "biber",
        "makeglossaries",
        "lualatexmk",
        "lualatexmk"
      ]
    }
  ],

  "latex-workshop.latex.recipe.default": "first",
  "latex-workshop.latex.autoBuild.run": "never",
  "latex-workshop.view.pdf.viewer": "tab",
  "latex-workshop.latex.autoClean.run": "onFailed"
}
```

Penjelasan tiap pengaturan:

| Kunci | Fungsi |
|-------|--------|
| `latex.tools` | Mendefinisikan perintah yang bisa dipanggil. `name` adalah nama yang dirujuk oleh *recipe*. |
| `latex.recipes` | Urutan tool yang dijalankan saat *build*. **Nama di `tools` dalam recipe harus sama persis dengan `name` di `latex.tools`.** |
| `latex.recipe.default` | `"first"` = pakai recipe pertama. Bisa juga diisi nama recipe persis, mis. `"lualatex + biber + glossaries"`. |
| `latex.autoBuild.run` | `"never"` = tidak kompilasi otomatis tiap simpan. Template ini lambat dikompilasi (±30–60 dtk), jadi lebih nyaman kompilasi manual. Ganti `"onSave"` jika ingin otomatis. |
| `view.pdf.viewer` | `"tab"` = PDF terbuka di tab VS Code dan otomatis *refresh*. |
| `latex.autoClean.run` | `"onFailed"` = file bantu dihapus otomatis bila kompilasi gagal, mengurangi error "nyangkut". |

> **Placeholder `%DOC%` vs `%DOCFILE%`**: `%DOC%` = path lengkap tanpa ekstensi
> (untuk latexmk), `%DOCFILE%` = nama file tanpa ekstensi (`main`) — dipakai biber dan
> makeglossaries karena keduanya dijalankan di folder yang sama dengan `main.tex`.

Linux dengan VS Code versi Snap/Flatpak kadang butuh tambahan `env` agar latexmk tidak error
modul GTK:

```jsonc
{
  "name": "lualatexmk",
  "command": "latexmk",
  "args": [ "...sama seperti di atas..." ],
  "env": { "GTK_PATH": null, "GIO_MODULE_DIR": null }
}
```

### 6.3 Alternatif tanpa Perl (Windows)

Ganti tool `makeglossaries` menjadi:

```jsonc
{
  "name": "makeglossaries",
  "command": "makeglossaries-lite",
  "args": ["%DOCFILE%"]
}
```

### 6.4 Pengaturan workspace (opsional)

Jika tidak ingin mengubah pengaturan global (misalnya karena ada proyek LaTeX lain yang memakai
pdflatex), taruh blok yang sama di file `.vscode/settings.json` di dalam folder template ini.
Pengaturan workspace akan menimpa pengaturan user hanya untuk folder ini.

---

## 7. Struktur Direktori dan Penjelasan Tiap File

```
skripsi-template/
├── main.tex                      ← FILE UTAMA (kompilasi file ini)
├── template.tex                  ← versi lama/acuan template, tidak dikompilasi
├── main.pdf                      ← hasil kompilasi
├── indonesian.lbx                ← terjemahan istilah daftar pustaka ke bahasa Indonesia
├── SANATA_DHARMA.png             ← logo untuk halaman judul
├── SANATA_DHARMA_yellow.png      ← logo untuk watermark (opsional)
├── docs.md                       ← dokumentasi ini
├── .gitignore                    ← mengabaikan file bantu hasil kompilasi
│
├── Front_matters/                ← bagian awal skripsi
│   ├── abstract.tex              ← abstrak bahasa Indonesia + kata kunci
│   ├── abstract_english.tex      ← abstract bahasa Inggris + keywords
│   ├── kata_pengantar.tex        ← kata pengantar
│   └── glossary_entries.tex      ← DAFTAR SINGKATAN (definisi akronim)
│
├── Skripsi/                      ← isi bab-bab
│   ├── Pendahuluan/
│   │   ├── pendahuluan.tex       ← Bab I (berisi contoh tabel, gambar, label)
│   │   └── SANATA_DHARMA.jpg     ← contoh gambar untuk Bab I
│   ├── Landasan_Teori/landasan_teori.tex         ← Bab II
│   ├── Metode_Penelitian/metode_penelitian.tex   ← Bab III
│   ├── Hasil_Penelitian/hasil_penelitian.tex     ← Bab IV
│   └── Kesimpulan/kesimpulan.tex                 ← Bab V
│
└── Back_matters/                 ← bagian akhir skripsi
    ├── daftar_pustaka.bib        ← DATABASE REFERENSI (BibTeX)
    ├── lampiran.tex              ← isi lampiran
    └── attachments/              ← data untuk lampiran
        ├── apx-sample-1.csv
        └── apx-sample-2.csv
```

### 7.1 `main.tex`

File inti yang mengatur seluruh dokumen. Urutan isinya:

| Bagian (cari komentar `%====`) | Isi |
|---|---|
| Komentar paling atas | Catatan *troubleshooting* glossary & perintah kompilasi manual. `% !TEX program = lualatex` memberi tahu editor agar memakai LuaLaTeX. |
| `\documentclass` + `\usepackage` | Kelas `book`, A4, 12pt, satu sisi, dan semua paket. |
| `\makeglossaries` + `\subimport{Front_matters}{glossary_entries}` | Mengaktifkan daftar singkatan dan memuat definisinya. |
| `\begin{comment}...\end{comment}` | Contoh pengaturan VS Code (tidak ikut dicetak). |
| `Title details` | **Data diri yang wajib diisi**: judul, judul Inggris, nama, NIM, prodi, pembimbing, penguji, dekan. |
| `Watermark` | Watermark logo (dinonaktifkan; hapus `%` untuk mengaktifkan). |
| `Citations` | `biblatex` gaya APA, pemetaan bahasa Indonesia, dan lokasi `.bib`. Ada contoh pemakaian `\parencite`, `\textcite`, dll. |
| `Fonts` | `\setmainfont{Times New Roman}`. |
| `Tikz` | Gaya flowchart: `startstop`, `io`, `process`, `decision`, `subprocess`, `arrow`. |
| `Page Numbering` | Nomor halaman kanan atas; halaman awal bab kanan bawah. |
| `Table modifications` | Teks "Tabel x.y", "Lanjutan", "Dilanjutkan di halaman selanjutnya" untuk `longtblr`. |
| `Chapter numbering` | Format "BAB I", penomoran subbab, spasi judul. |
| `Equations Modifications` | Spasi di sekitar persamaan. |
| `Caption label modifications` | "Gambar", "Tabel", penomoran per bab (2.1, 2.2, …) untuk tabel, gambar, persamaan, algoritma. |
| `Code block modifications` | Gaya `lstlisting`. |
| Daftar Lampiran / Persamaan / Algoritma | Membuat daftar kustom + perintah `\mylampiran{}`, `\myequation{}`, `\myalgorithm{}`. |
| `Hyphenation` | Mematikan pemenggalan kata. |
| `\begin{document}` | Halaman judul (Indonesia & Inggris), persetujuan, (proposal—dikomentari), pengesahan, pernyataan keaslian, persetujuan publikasi, kata pengantar, abstrak, daftar isi, daftar tabel, gambar, algoritma, persamaan, singkatan, lampiran. |
| `\mainmatter` | Memuat bab I–V via `\subimport`. Semua bab berada di dalam `doublespace`. |
| `\backmatter` | Daftar pustaka (`\printbibliography`), pernyataan penggunaan LLM (opsional), lampiran. |

> Banyak bagian ditandai `%TODO:`. Gunakan **Ctrl+F → `TODO`** untuk menemukan bagian yang
> perlu disesuaikan.

### 7.2 `template.tex`

Salinan template versi awal/asli sebagai acuan. **Tidak dikompilasi** dan tidak perlu diubah.
Berguna untuk membandingkan jika `main.tex` rusak.

### 7.3 `indonesian.lbx`

File bahasa untuk biblatex: mengubah istilah di daftar pustaka menjadi bahasa Indonesia
(`and` → `dan`, `et al.` → `dkk.`, `edition` → `edisi`, dst.). **Harus berada di folder yang
sama dengan `main.tex`.** Jangan dihapus.

### 7.4 `Front_matters/`

| File | Isi | Dimuat di |
|---|---|---|
| `abstract.tex` | Teks abstrak (ganti `\lipsum[1]`) + baris "Kata kunci:". | Halaman ABSTRAK |
| `abstract_english.tex` | Teks *abstract* + "Keywords:". | Halaman ABSTRACT |
| `kata_pengantar.tex` | Kata pengantar + tempat/tanggal + nama penulis (`\author`). | Halaman KATA PENGANTAR |
| `glossary_entries.tex` | Bagian atas: format tampilan daftar singkatan (**jangan diubah** kecuali paham). Bagian bawah: daftar `\newacronym`. | Preamble `main.tex` |

### 7.5 `Skripsi/`

Satu folder per bab. Setiap file diawali `\chapter{...}`. File `pendahuluan.tex` berisi
**contoh** cara membuat section, tabel, gambar, subgambar, dan label — hapus isinya setelah
dipelajari. Gambar untuk suatu bab sebaiknya diletakkan di folder bab itu sendiri (karena
`\subimport` membuat path gambar relatif terhadap folder bab).

### 7.6 `Back_matters/`

| File | Isi |
|---|---|
| `daftar_pustaka.bib` | Semua entri referensi. Bagian atas berisi contoh format `article`, `book`, `online`. |
| `lampiran.tex` | Bagian atas: pengaturan format lampiran (**jangan diubah**). Bagian bawah: isi lampiran (contoh tabel dari CSV). |
| `attachments/*.csv` | Data CSV yang ditampilkan sebagai tabel di lampiran. |

### 7.7 File hasil kompilasi (diabaikan Git)

`main.aux`, `main.log`, `main.toc`, `main.bcf`, `main.bbl`, `main.blg`, `main.run.xml`,
`main.glo`, `main.gls`, `main.glg`, `main.ist`, `main.acn`, `main.acr`, `main.alg`, `main.equ`,
`main.lamp`, `main.lof`, `main.lot`, `main.out`, `main.fls`, `main.fdb_latexmk`,
`main.synctex.gz`. Semuanya aman dihapus kapan saja (akan dibuat ulang saat kompilasi).
`.gitignore` mengabaikan semua `main.*` kecuali `main.tex` dan `main.pdf`.

---

## 8. Cara Menjalankan (Kompilasi)

### 8.1 Lewat VS Code (disarankan)

1. Buka folder template di VS Code.
2. Buka `main.tex` (kompilasi selalu dari `main.tex`; jika sedang membuka file bab, LaTeX
   Workshop biasanya otomatis mendeteksi `main.tex` sebagai *root*).
3. Jalankan salah satu:
   - Klik ikon **TeX** di sidebar → **Build LaTeX project** → pilih recipe
     **lualatex + biber + glossaries**, atau
   - Tekan **`Ctrl+Alt+B`** (memakai recipe default).
4. Tunggu hingga ikon centang ✓ muncul di *status bar* bawah.
5. Lihat PDF: **`Ctrl+Alt+V`** atau ikon kaca pembesar di kanan atas editor.

Fitur berguna:

| Aksi | Shortcut |
|---|---|
| Build | `Ctrl+Alt+B` |
| Lihat PDF | `Ctrl+Alt+V` |
| Loncat dari kode ke PDF (SyncTeX) | `Ctrl+Alt+J` |
| Loncat dari PDF ke kode | `Ctrl+Klik` di PDF |
| Hapus file bantu | `Ctrl+Alt+C` |
| Lihat log error | Panel **Problems** (`Ctrl+Shift+M`) atau ikon TeX → *View Log messages* |
| Hentikan kompilasi | ikon TeX → *Terminate current compilation* |

### 8.2 Lewat terminal (manual)

Jalankan di folder yang berisi `main.tex`:

```bash
lualatex main.tex        # membuat main.aux, main.bcf, main.glo
biber main               # membuat main.bbl (daftar pustaka)
makeglossaries main      # membuat main.gls (daftar singkatan)
lualatex main.tex        # memasukkan daftar pustaka & singkatan
lualatex main.tex        # memperbaiki nomor halaman di daftar isi
```

Atau dengan latexmk (otomatis mengulang lualatex sesuai kebutuhan, tapi tetap jalankan
makeglossaries sendiri):

```bash
latexmk -pdf -pdflatex="lualatex %O %S" main.tex
makeglossaries main
latexmk -pdf -pdflatex="lualatex %O %S" main.tex
```

### 8.3 Kapan harus *full build*?

| Perubahan | Yang perlu dijalankan |
|---|---|
| Edit teks biasa | Cukup lualatex (atau recipe lengkap, sama saja) |
| Tambah/ubah sitasi atau isi `.bib` | lualatex → **biber** → lualatex ×2 |
| Tambah/ubah singkatan atau pemakaian `\gls` pertama kali | lualatex → **makeglossaries** → lualatex ×2 |
| Tambah bab, persamaan, algoritma, lampiran (daftar isi berubah) | lualatex ×2 |

Paling aman: selalu pakai recipe lengkap.

---

## 9. Mengisi Data Diri dan Mengatur Halaman Awal

Di `main.tex` bagian `Title details`:

```latex
\def\title{Judul Skripsi dalam Bahasa Indonesia}
\def\englishtitle{Thesis Title in English}
\def\author{NAMA LENGKAP}
\def\nim{123456789}
\def\prodi{Informatika}
\def\program{Informatics}
\def\fakultas{Sains dan Teknologi}
\def\faculty{Science and Technology}
\def\sarjana{Sarjana Komputer}
\def\pembimbing{Nama Pembimbing, Gelar}
\def\ketuapenguji{Nama Ketua Penguji, Gelar}
\def\sekretarispenguji{Nama Sekretaris Penguji, Gelar}
\def\dekan{Nama Dekan, Gelar}
```

Nilai ini otomatis dipakai di halaman judul, persetujuan, pengesahan, pernyataan, dan kata
pengantar.

**Menyembunyikan halaman yang belum dibutuhkan** (misalnya halaman pengesahan saat masih
proposal): beri `%` di depan setiap baris pada bagian yang bersangkutan
(`%=== Pengesahan ===%`, dll.). Di VS Code, blok baris → **`Ctrl+/`** untuk
komentar/uncomment sekaligus.

**Mode proposal (Metode Penelitian)**: bagian `%=== PROPOSAL ===%` sudah dikomentari. Hapus
`%` di bagian itu dan komentari halaman skripsi yang tidak dipakai.

---

## 10. Menambah Bab Baru

1. Buat folder dan file, misalnya `Skripsi/Implementasi/implementasi.tex`:

   ```latex
   \chapter{Implementasi}
   \section{Lingkungan Pengembangan}
   Isi teks...
   ```

2. Di `main.tex`, di dalam `\begin{doublespace} ... \end{doublespace}`, tambahkan pada urutan
   yang diinginkan:

   ```latex
   \clearpage
   \subimport{Skripsi/Implementasi}{implementasi}
   ```

   Catatan: argumen kedua **tanpa** `.tex`, dan nama folder ditulis **tanpa** `/` di akhir
   (contoh di template memakai `Skripsi/Pendahuluan` tanpa garis miring penutup).

3. Struktur heading yang tersedia:

   ```latex
   \chapter{...}        % BAB I
   \section{...}        % 1.1
   \subsection{...}     % 1.1.1
   \subsubsection{...}  % 1.1.1.1
   ```

   Daftar isi menampilkan hingga level `\subsection` (`tocdepth = 2`).

---

## 11. Daftar Pustaka (Sitasi)

Template memakai **biblatex gaya APA** dengan istilah bahasa Indonesia (`dkk.`, `dan`, `hlm.`).
Referensi disimpan di `Back_matters/daftar_pustaka.bib`.

> Komentar di bagian atas `daftar_pustaka.bib` menyebut "format IEEE" dan `\cite{}` —
> itu sisa versi lama. Yang aktif di `main.tex` adalah `style=apa`, jadi gunakan
> `\parencite` / `\textcite` seperti di bawah.

### 11.1 Menambahkan referensi baru

1. **Dapatkan BibTeX-nya**. Cara termudah:
   - **Google Scholar** → klik ikon kutip (") di bawah artikel → **BibTeX** → salin.
   - Halaman penerbit (IEEE Xplore, ScienceDirect, ACM, Springer) → *Cite / Export citation* → BibTeX.
   - Dari DOI: buka <https://doi.org/> lalu gunakan layanan seperti
     <https://www.doi2bib.org/>.
   - Zotero/Mendeley → *Export* → BibTeX.
2. **Tempel** ke `Back_matters/daftar_pustaka.bib` di bawah baris
   `%===================== Citation Entries ===================%`.
3. **Periksa / rapikan kunci** (teks setelah `@article{`). Kunci harus unik, tanpa spasi.
   Disarankan pola `NamaBelakangTahunKata`, mis. `Mariani2016Identify`.
4. **Sitasi** di naskah memakai kunci tersebut (lihat 11.3).
5. **Kompilasi ulang dengan recipe lengkap** (biber harus dijalankan).

Contoh entri:

```bibtex
@article{Mariani2016Identify,
  author  = {Mariani, Manuel Sebastian and Medo, Mat{\'u}{\v{s}} and Zhang, Yi-Cheng},
  title   = {Identification of milestone papers through time-balanced network centrality},
  journal = {Journal of Informetrics},
  volume  = {10},
  number  = {4},
  pages   = {1207--1223},
  year    = {2016},
  doi     = {10.1016/j.joi.2016.10.005}
}

@book{Newman2018,
  author    = {Newman, Mark},
  title     = {Networks},
  edition   = {2},
  publisher = {Oxford University Press},
  year      = {2018}
}

@inproceedings{Kreutz2020,
  author    = {Kreutz, Christin Katharina and others},
  title     = {Judul Makalah Konferensi},
  booktitle = {Proceedings of Nama Konferensi},
  pages     = {1--10},
  year      = {2020}
}

@online{NetworkX2024,
  author  = {{NetworkX Developers}},
  title   = {NetworkX Documentation},
  year    = {2024},
  url     = {https://networkx.org/documentation/stable/},
  urldate = {2026-10-01}
}
```

Aturan penulisan entri:
- **Beberapa penulis** dipisah dengan `and` (bukan koma): `author = {A, B and C, D}`.
- **Organisasi sebagai penulis** dibungkus kurung kurawal ganda: `{{Badan Pusat Statistik}}`
  agar tidak dianggap "Statistik, Badan Pusat".
- **Pertahankan huruf kapital** di judul dengan kurung kurawal: `title = {Analisis {COVID-19} di {Indonesia}}`.
- **Rentang halaman** pakai dua tanda hubung: `pages = {12--20}`.
- **Karakter khusus** `&`, `%`, `_`, `#` ditulis `\&`, `\%`, `\_`, `\#` (kecuali di `url` dan `doi`).
- Untuk sumber online, `urldate` (format `YYYY-MM-DD`) menghasilkan teks "Diakses pada …".

### 11.2 Jenis entri yang umum

| Jenis | Untuk | Field wajib |
|---|---|---|
| `@article` | Artikel jurnal | `author`, `title`, `journal`, `year` |
| `@book` | Buku | `author`, `title`, `publisher`, `year` |
| `@inproceedings` | Makalah konferensi | `author`, `title`, `booktitle`, `year` |
| `@incollection` | Bab dalam buku suntingan | `author`, `title`, `booktitle`, `editor`, `publisher`, `year` |
| `@thesis` | Skripsi/tesis/disertasi | `author`, `title`, `institution`, `type`, `year` |
| `@online` | Website, dokumentasi | `author`/`title`, `url`, `year`, `urldate` |
| `@misc` / `@report` | Preprint (arXiv), laporan teknis | `author`, `title`, `year` |

### 11.3 Cara menyitasi di naskah

| Kebutuhan | Perintah | Hasil |
|---|---|---|
| Dalam kurung | `\parencite{Mariani2016Identify}` | (Mariani dkk., 2016) |
| Penulis sebagai bagian kalimat | `\textcite{Kreutz2020}` | Kreutz dkk. (2020) |
| Beberapa sumber | `\parencite{Mariani2016Identify, Mariani_2019}` | (Mariani dkk., 2016, 2019) |
| Dengan halaman | `\parencite[hlm.~12]{Wang_2013}` | (Wang dkk., 2013, hlm. 12) |
| Tahun saja | `\parencite*{Ke_2015}` | (2015) |
| Nama penulis saja | `\citeauthor{Ke_2015}` | Ke dkk. |

Contoh:

```latex
Penelitian sebelumnya menunjukkan bahwa PageRank bias terhadap makalah lama
\parencite{Mariani2016Identify}. \textcite{Kreutz2020} kemudian mengusulkan ...
```

### 11.4 Hal yang perlu diketahui

- Entri **hanya muncul** di Daftar Pustaka jika disitasi. Untuk menampilkan semua entri
  (mis. saat cek), hapus `%` pada `% \nocite{*}` sebelum `\printbibliography` di `main.tex`.
- Daftar pustaka otomatis diurutkan menurut APA (nama belakang penulis, lalu tahun).
- Jika sitasi tampil sebagai **kunci tebal** (mis. **Mariani2016Identify**) berarti biber
  belum dijalankan atau kunci salah ketik — lihat [Troubleshooting](#21-troubleshooting).

---

## 12. Daftar Singkatan

Singkatan didefinisikan di `Front_matters/glossary_entries.tex` (bagian bawah, setelah
`% TODO: Add your acronyms here`) dan ditampilkan di halaman **DAFTAR SINGKATAN**.

### 12.1 Menambahkan singkatan baru

Format:

```latex
\newacronym[sort=SINGKATAN]{label}{SINGKATAN}{\textit{Kepanjangan}}
```

| Bagian | Arti |
|---|---|
| `sort=...` | Kunci pengurutan alfabet di daftar. Isi sama dengan singkatannya. |
| `label` | Nama internal untuk dipanggil di naskah. Huruf kecil, tanpa spasi, unik. |
| `SINGKATAN` | Teks singkatan yang dicetak. |
| `Kepanjangan` | Kepanjangan. Pakai `\textit{}` bila istilah asing (bahasa Inggris); tanpa `\textit` bila bahasa Indonesia. |

Contoh:

```latex
\newacronym[sort=GNN]{gnn}{GNN}{\textit{Graph Neural Network}}
\newacronym[sort=PR]{pr}{PR}{\textit{PageRank}}
\newacronym[sort=KBBI]{kbbi}{KBBI}{Kamus Besar Bahasa Indonesia}
```

Setelah menambah, **jalankan recipe lengkap** (makeglossaries harus jalan).

### 12.2 Memakai singkatan di naskah

| Perintah | Hasil | Keterangan |
|---|---|---|
| `\gls{gnn}` | pertama kali: *Graph Neural Network* (GNN); selanjutnya: GNN | Paling disarankan |
| `\acrshort{gnn}` | GNN | Selalu singkatan |
| `\acrlong{gnn}` | *Graph Neural Network* | Selalu kepanjangan |
| `\acrfull{gnn}` | *Graph Neural Network* (GNN) | Selalu lengkap |
| `\Gls{gnn}` | Huruf pertama kapital (awal kalimat) | |
| `\glspl{gnn}` | Bentuk jamak (GNNs) | |
| `\glsresetall` | Reset sehingga `\gls` berikutnya kembali menampilkan bentuk lengkap | Misal di awal bab baru |

Contoh:

```latex
Penelitian ini menggunakan \acrlong{rl} (\acrshort{rl}). Metode \acrshort{rl} ...
```

### 12.3 Penting

- **Singkatan yang didefinisikan tetapi tidak pernah dipakai** (`\gls`, `\acrshort`, dll.)
  **tidak akan muncul** di Daftar Singkatan. Daftar akan kosong bila belum ada satu pun yang dipakai.
- Label yang sama tidak boleh didefinisikan dua kali (error *Glossary entry already defined*).
- Jangan mengubah bagian `START ... END` di atas file kecuali ingin mengubah tampilan tabel singkatan.
- Template juga menyediakan contoh *glossary* istilah (`\newglossaryentry`) yang dikomentari
  di akhir file bila nanti butuh **Daftar Istilah**.

---

## 13. Persamaan (Equation)

Persamaan dinomori per bab: (2.1), (2.2), …, dan bisa didaftarkan ke **DAFTAR PERSAMAAN**
dengan perintah `\myequation{Nama}`.

### 13.1 Persamaan bernomor

```latex
\begin{equation}
    C_B(v) = \sum_{s \neq v \neq t} \frac{\sigma_{st}(v)}{\sigma_{st}}
    \label{eq:betweenness}
\end{equation}
\myequation{Betweenness Centrality}

\noindent di mana $\sigma_{st}$ adalah jumlah lintasan terpendek dari $s$ ke $t$, dan
$\sigma_{st}(v)$ adalah jumlah lintasan yang melewati $v$.
```

- `\label{eq:...}` diletakkan **di dalam** `equation`.
- `\myequation{...}` diletakkan **tepat setelah** `\end{equation}` agar nomor yang tercatat
  benar. Judulnya otomatis ditulis kapital di Daftar Persamaan.
- `\noindent` dipakai bila kalimat penjelasan menyambung (bukan paragraf baru).
- Jangan menambah `\vspace` di sekitar persamaan — spasi sudah diatur di `main.tex`.

Merujuk: `Persamaan \eqref{eq:betweenness}` → "Persamaan (2.1)", atau `\ref{eq:betweenness}` → "2.1".

### 13.2 Variasi

**Tanpa nomor**: `\begin{equation*} ... \end{equation*}` atau `\[ ... \]`.

**Matematika dalam kalimat**: `$x^2 + y^2 = r^2$`.

**Beberapa baris, rata di tanda `=`**:

```latex
\begin{align}
    Q(s,a) &\leftarrow Q(s,a) + \alpha \left[ r + \gamma \max_{a'} Q(s',a') - Q(s,a) \right] \label{eq:qlearning} \\
    V(s)   &= \max_{a} Q(s,a) \label{eq:value}
\end{align}
\myequation{Pembaruan Q-Learning}
```

Catatan: `\myequation` setelah `align` hanya mencatat nomor terakhir. Jika setiap baris perlu
masuk daftar, pisahkan menjadi beberapa `equation`.

**Satu persamaan panjang dipecah beberapa baris, satu nomor**:

```latex
\begin{equation}
\begin{split}
    f(x) &= a_0 + a_1 x + a_2 x^2 \\
         &\quad + a_3 x^3 + a_4 x^4
\end{split}
\label{eq:polinom}
\end{equation}
\myequation{Polinomial Orde Empat}
```

**Fungsi bercabang**:

```latex
\begin{equation}
    f(x) =
    \begin{cases}
        1 & \text{jika } x \geq 0 \\
        0 & \text{lainnya}
    \end{cases}
    \label{eq:step}
\end{equation}
\myequation{Fungsi Tangga}
```

Simbol yang sering dipakai: `\frac{a}{b}`, `\sqrt{x}`, `\sum_{i=1}^{n}`, `\prod`, `\int_a^b`,
`\alpha \beta \gamma \lambda \sigma`, `\leq \geq \neq \approx`, `\in \subseteq`,
`\mathbb{R}` (butuh `amssymb`, tambahkan `\usepackage{amssymb}` jika perlu),
`\mathbf{x}` (vektor), `\text{teks}` (teks di dalam rumus), `\left( ... \right)` (kurung menyesuaikan tinggi).

Koma desimal: berkat paket `icomma`, tulis `$3,14$` dan tidak akan ada spasi setelah koma.

---

## 14. Algoritma (Pseudocode)

Template memakai `algorithm` (float) + `algpseudocode` dengan opsi `noend`
(baris `end for`, `end if` tidak dicetak). Font algoritma otomatis Courier New 10pt.
Dinomori per bab dan bisa didaftarkan ke **DAFTAR ALGORITMA** dengan `\myalgorithm{Nama}`.

### 14.1 Contoh

```latex
\begin{algorithm}[H]
    \caption{Algoritma \textit{Sliding Window} PageRank}
    \label{alg:sliding-pagerank}
    \myalgorithm{Algoritma Sliding Window PageRank}
    \begin{algorithmic}[1]
        \Require Graf sitasi $G$, ukuran jendela $w$, faktor redam $d$
        \Ensure Skor centrality $S$ untuk setiap makalah
        \State $S \gets \emptyset$
        \For{$t \gets t_{awal}$ \textbf{to} $t_{akhir}$}
            \State $G_t \gets$ \Call{Subgraf}{$G, t-w, t$}
            \State $PR_t \gets$ \Call{PageRank}{$G_t, d$}
            \If{$PR_t(v) > \theta$}
                \State $S \gets S \cup \{v\}$
            \Else
                \State \textbf{continue}
            \EndIf
        \EndFor
        \State \Return $S$
    \end{algorithmic}
\end{algorithm}
```

- Urutan: `\caption` → `\label` → `\myalgorithm` (setelah `\caption` agar nomornya benar).
- `[H]` = algoritma tetap di posisi tersebut (tidak "melayang").
- `[1]` pada `algorithmic` = nomor baris setiap 1 baris. Hapus `[1]` untuk tanpa nomor baris.
- **Meskipun `noend`, `\EndFor`, `\EndIf`, `\EndWhile`, `\EndFunction` tetap wajib ditulis.**

### 14.2 Perintah algpseudocode

| Perintah | Tampilan |
|---|---|
| `\Require`, `\Ensure` | Require: / Ensure: (input/output) |
| `\State` | Satu baris pernyataan |
| `\Statex` | Baris tanpa nomor |
| `\If{c} ... \ElsIf{c} ... \Else ... \EndIf` | if-then-else |
| `\For{c} ... \EndFor` | for |
| `\ForAll{c} ... \EndFor` | for all |
| `\While{c} ... \EndWhile` | while |
| `\Repeat ... \Until{c}` | repeat-until |
| `\Function{Nama}{arg} ... \EndFunction` | Fungsi |
| `\Procedure{Nama}{arg} ... \EndProcedure` | Prosedur |
| `\Call{Nama}{arg}` | Pemanggilan fungsi |
| `\Return` | return |
| `\Comment{teks}` | ▷ komentar di kanan |

Mengganti teks menjadi bahasa Indonesia (opsional, taruh di preamble `main.tex`):

```latex
\algrenewcommand\algorithmicrequire{\textbf{Masukan:}}
\algrenewcommand\algorithmicensure{\textbf{Keluaran:}}
```

Merujuk: `Algoritma \ref{alg:sliding-pagerank}` → "Algoritma 3.1".

---

## 15. Tabel

### 15.1 Tabel utama (disarankan): `longtblr`

Bisa berlanjut ke halaman berikutnya dan otomatis memberi teks "Lanjutan".

```latex
\begin{longtblr}[
    caption={Perbandingan Metode Centrality},
    label={tab:perbandingan}
]{
    colspec={ c l X[l] },          % c=tengah, l=kiri, r=kanan, X=lebar fleksibel
    rowhead = 1,                    % baris header diulang di halaman berikutnya
    row{1} = {font=\bfseries},      % header tebal
    hlines, vlines                  % garis horizontal & vertikal
}
    No & Metode & Keterangan \\
    1  & PageRank & Berbasis random walk \\
    2  & Betweenness & Berbasis lintasan terpendek \\
\end{longtblr}
```

Merujuk: `Tabel \ref{tab:perbandingan}`. Daftar Tabel terisi otomatis dari `caption`.

Template juga menyediakan lingkungan `newtblr` (header tebal sans-serif + garis vertikal) dengan
sintaks yang sama.

### 15.2 Tabel dari CSV

```latex
\begin{longtblr}[caption={Data Hasil}, label={tab:hasil}]{colspec={c c c}, hlines, vlines}
    \csvreader[no head, late after line=\\]{Back_matters/attachments/hasil.csv}{}%
    {\csvcoli & \csvcolii & \csvcoliii}
\end{longtblr}
```

Path CSV ditulis relatif terhadap folder `main.tex`. Di dalam CSV, karakter `_`, `%`, `&`
harus di-escape: `\_`, `\%`, `\&` (lihat contoh di `attachments/`).

Cara cepat menampilkan seluruh CSV: `\csvautotabular{Back_matters/attachments/hasil.csv}`.

### 15.3 Merge sel & warna (tabularray)

```latex
\SetCell[c=2]{c} Gabung 2 kolom & &  \\
\SetCell[r=2]{m} Gabung 2 baris & a \\
                                & b \\
```

Warna baris/sel: di bagian spesifikasi tambahkan `row{1} = {bg=gray!20}` atau `cell{2}{3} = {bg=yellow!30}`.

---

## 16. Gambar

```latex
\begin{figure}[H]
    \centering
    \includegraphics[width=0.8\textwidth]{\detokenize{diagram_alur.png}}
    \caption{Diagram Alur Penelitian}
    \label{fig:diagram-alur}
\end{figure}
```

- Letakkan file gambar di folder bab yang sama (mis. `Skripsi/Metode_Penelitian/diagram_alur.png`).
- `\detokenize{...}` dibutuhkan bila nama file mengandung `_`.
- `width=0.8\textwidth` = 80% lebar teks. Alternatif: `height=5cm`, `scale=0.5`.
- Format yang didukung: `.png`, `.jpg`, `.pdf` (PDF vektor paling tajam untuk grafik).
- Sumber gambar dari referensi: `\caption{Arsitektur GNN \parencite{Kreutz2020}}`.

**Subgambar** berdampingan:

```latex
\begin{figure}[H]
    \centering
    \begin{subfigure}{0.45\textwidth}
        \centering
        \includegraphics[width=\linewidth]{a.png}
        \caption{Sebelum}
        \label{fig:sebelum}
    \end{subfigure}
    \hfill
    \begin{subfigure}{0.45\textwidth}
        \centering
        \includegraphics[width=\linewidth]{b.png}
        \caption{Sesudah}
        \label{fig:sesudah}
    \end{subfigure}
    \caption{Perbandingan Hasil}
    \label{fig:perbandingan}
\end{figure}
```

**Halaman landscape** untuk gambar/tabel lebar: bungkus dengan
`\begin{landscape} ... \end{landscape}`.

---

## 17. Kode Program (Listing)

```latex
\begin{lstlisting}[language=Python, caption={Menghitung PageRank}, label={lst:pagerank}]
import networkx as nx

G = nx.DiGraph()
G.add_edges_from([(1, 2), (2, 3), (3, 1)])
pr = nx.pagerank(G, alpha=0.85)
print(pr)
\end{lstlisting}
```

Kode dari file eksternal:

```latex
\lstinputlisting[language=Python, caption={Skrip Utama}]{Back_matters/attachments/main.py}
```

Bahasa yang didukung antara lain: `Python`, `Java`, `C`, `C++`, `SQL`, `HTML`, `R`, `Matlab`, `bash`.
Kode inline: `\texttt{nama\_fungsi()}` atau `\lstinline|nama_fungsi()|`.

---

## 18. Flowchart dengan TikZ

Gaya sudah didefinisikan di `main.tex`: `startstop`, `io`, `process`, `decision`,
`subprocess`, `arrow`.

```latex
\begin{figure}[H]
\centering
\begin{tikzpicture}[node distance=2cm]
    \node (start) [startstop] {Mulai};
    \node (in)    [io, below of=start] {Input data sitasi};
    \node (proc)  [process, below of=in] {Hitung centrality};
    \node (dec)   [decision, below of=proc, yshift=-0.5cm] {Skor $> \theta$?};
    \node (sub)   [subprocess, right of=dec, xshift=3cm] {Tandai seminal};
    \node (stop)  [startstop, below of=dec, yshift=-0.5cm] {Selesai};

    \draw [arrow] (start) -- (in);
    \draw [arrow] (in) -- (proc);
    \draw [arrow] (proc) -- (dec);
    \draw [arrow] (dec) -- node[anchor=south] {ya} (sub);
    \draw [arrow] (dec) -- node[anchor=east] {tidak} (stop);
    \draw [arrow] (sub) |- (stop);
\end{tikzpicture}
\caption{Flowchart Penelitian}
\label{fig:flowchart}
\end{figure}
```

Alternatif yang lebih mudah: buat diagram di draw.io / diagrams.net, ekspor sebagai PDF, lalu
sisipkan dengan `\includegraphics`.

---

## 19. Lampiran

Isi lampiran ditulis di `Back_matters/lampiran.tex`, **di bawah** baris
`% =========================== END ============================`.

Menambah lampiran baru:

```latex
\section{Kode Program Lengkap}\label{apx:kode}
\mylampiran{Kode Program Lengkap}

Isi lampiran di sini...
```

- Setiap `\section` = satu lampiran ("Lampiran 1: …", "Lampiran 2: …"), otomatis mulai di halaman baru.
- `\mylampiran{...}` **wajib** agar muncul di **DAFTAR LAMPIRAN**.
- Merujuk dari bab: `Lampiran \ref{apx:kode}`.
- Menyisipkan PDF (mis. surat izin, kuesioner): `\includepdf[pages=-]{Back_matters/attachments/surat.pdf}`.

---

## 20. Label dan Referensi Silang

Gunakan awalan agar mudah diingat:

| Objek | Awalan label | Rujukan |
|---|---|---|
| Bab/subbab | `ch:` / `sec:` | `Bab \ref{ch:metode}` |
| Gambar | `fig:` | `Gambar \ref{fig:flowchart}` |
| Tabel | `tab:` | `Tabel \ref{tab:perbandingan}` |
| Persamaan | `eq:` | `Persamaan \eqref{eq:betweenness}` |
| Algoritma | `alg:` | `Algoritma \ref{alg:sliding-pagerank}` |
| Kode | `lst:` | `Kode \ref{lst:pagerank}` |
| Lampiran | `apx:` | `Lampiran \ref{apx:kode}` |

`\ref` hanya mencetak nomor, jadi tulis nama objeknya sendiri ("Gambar", "Tabel"). Gunakan
spasi tak terputus agar tidak terpisah baris: `Gambar~\ref{fig:flowchart}`.
Rujukan muncul sebagai **??** sampai dokumen dikompilasi dua kali.

---

## 21. Troubleshooting

> Langkah pertama untuk hampir semua error aneh: **hapus file bantu** (`Ctrl+Alt+C` atau
> hapus semua `main.*` selain `main.tex` dan `main.pdf`), lalu build ulang dengan recipe lengkap.
> Baca error **pertama** di log, bukan yang terakhir — error berikutnya sering hanya akibat.

### 21.1 Masalah VS Code / LaTeX Workshop

| Gejala | Penyebab | Solusi |
|---|---|---|
| `Recipe terminated with fatal error: spawn latexmk ENOENT` | `latexmk` tidak ada di PATH, atau (Windows) Perl belum terpasang | Instal paket `latexmk` di MiKTeX + Strawberry Perl, restart VS Code. |
| `spawn biber ENOENT` / `spawn makeglossaries ENOENT` | Alat belum terinstal / tidak di PATH | Instal paket `biber` / `glossaries`; Windows: pasang Perl atau pakai `makeglossaries-lite`. |
| `Skipping undefined tool "lualatexmk"` atau recipe tidak jalan | Nama tool di `recipes` ≠ nama di `latex.tools` | Samakan nama persis (mis. keduanya `lualatexmk`). |
| `Failed to resolve build recipe` / recipe default salah | `latex.recipe.default` berisi nama recipe yang tidak ada | Isi `"first"` atau nama recipe persis. |
| `settings.json` bergaris merah / pengaturan diabaikan | JSON tidak valid (koma kurang/lebih, kurung tidak seimbang) | Perhatikan peringatan VS Code. Koma setelah elemen terakhir diperbolehkan VS Code, tapi koma *kurang* antar-elemen tidak. |
| Kompilasi memakai pdflatex dan error `fontspec requires XeTeX or LuaTeX` | Recipe default LaTeX Workshop masih dipakai | Pastikan recipe yang dipilih adalah recipe LuaLaTeX di atas. |
| PDF tidak ter-*refresh* / "permission denied" saat menulis `main.pdf` | PDF sedang dibuka di Adobe Reader (Windows mengunci file) | Tutup PDF eksternal, gunakan viewer tab VS Code. |
| Kompilasi seperti *hang* | MiKTeX menunggu konfirmasi instal paket, atau error di mode interaktif | Batalkan (*Terminate current compilation*), instal paket manual di MiKTeX Console, set *on-the-fly install* = Always. |
| Build dari file bab menghasilkan error "missing \begin{document}" | LaTeX Workshop menganggap file bab sebagai root | Buka `main.tex` lalu build, atau tambahkan `% !TEX root = ../../main.tex` di baris pertama file bab. |

### 21.2 Masalah paket & font

| Gejala | Solusi |
|---|---|
| `! LaTeX Error: File 'xxx.sty' not found.` | Instal paket yang memuat `xxx.sty` lewat MiKTeX Console (cari nama `xxx`), lalu **Tasks → Refresh file name database**. |
| `Package babel Error: Unknown option 'indonesian'` | Instal `babel-indonesian`. |
| `Package fontspec Error: The font "Times New Roman" cannot be found.` | Instal font (lihat [Bagian 3](#3-instalasi-font)), lalu hapus cache font LuaLaTeX: `luaotfload-tool --update --force`. |
| Kompilasi pertama sangat lama (beberapa menit) | Normal — LuaLaTeX sedang membangun cache font. Kompilasi berikutnya lebih cepat. |
| Error setelah update MiKTeX sebagian | MiKTeX Console → **Updates → Update now** sampai tidak ada update tersisa (update semua sekaligus). |

### 21.3 Masalah daftar pustaka

| Gejala | Penyebab | Solusi |
|---|---|---|
| Sitasi tampil sebagai **kunci tebal**, daftar pustaka kosong | biber belum dijalankan | Build dengan recipe lengkap. |
| `Citation 'xxx' undefined` | Kunci salah ketik / entri belum ada di `.bib` | Cocokkan kunci di `\parencite{}` dengan `@article{kunci,` (peka huruf besar/kecil). |
| `Found biblatex control file version 3.x, expected version 3.y` | Versi biber dan biblatex tidak cocok | Update MiKTeX lengkap (biber dan biblatex harus versi pasangan). |
| `ERROR - Cannot find 'main.bcf'!` | lualatex gagal sebelum sempat menulis `.bcf` | Perbaiki error lualatex terlebih dulu. |
| biber error `syntax error: found "...", expected "end of entry"` | Kesalahan sintaks `.bib` (koma kurang, kurung tidak seimbang, `@` di dalam teks) | Buka `main.blg` untuk melihat nomor baris entri bermasalah. |
| Istilah masih bahasa Inggris (`et al.`, `and`) | `indonesian.lbx` hilang/terpindah | Pastikan `indonesian.lbx` ada di folder yang sama dengan `main.tex`. |
| Daftar pustaka masih memuat entri lama yang sudah dihapus | File `main.bbl` lama | Hapus file bantu, build ulang. |
| Nama penulis organisasi terbalik ("Statistik, B. P.") | Tidak dibungkus kurung ganda | `author = {{Badan Pusat Statistik}}`. |

### 21.4 Masalah daftar singkatan

| Gejala | Penyebab | Solusi |
|---|---|---|
| Halaman DAFTAR SINGKATAN kosong | Belum ada singkatan yang dipakai di naskah | Pakai minimal satu `\gls{}` / `\acrshort{}`. |
| Kosong padahal sudah dipakai | makeglossaries belum jalan / `main.glo` tidak terbentuk | Jalankan manual: `lualatex main.tex` → `makeglossaries main` → `lualatex main.tex` ×2. Pastikan `main.glo` lalu `main.gls` muncul. |
| `makeglossaries: Can't locate ... perl` / tidak jalan di Windows | Perl tidak ada | Pasang Strawberry Perl atau gunakan `makeglossaries-lite`. |
| `Glossary entry 'xxx' has not been defined` | Label di `\gls{xxx}` tidak ada / salah ketik | Cek `glossary_entries.tex`. |
| `Glossary entry 'xxx' has already been defined` | Label ganda | Hapus/ubah salah satu `\newacronym`. |
| Urutan singkatan tidak alfabetis | `sort=` tidak diisi / salah | Isi `sort=` sesuai singkatan. |

### 21.5 Masalah isi dokumen

| Gejala | Solusi |
|---|---|
| Rujukan tampil `??` | Kompilasi sekali lagi. Jika tetap, label salah ketik. |
| Persamaan/algoritma tidak masuk daftar | Tambahkan `\myequation{}` / `\myalgorithm{}`, kompilasi 2×. |
| Nomor di Daftar Persamaan meleset satu | `\myequation` diletakkan *sebelum* `equation` — pindahkan ke setelah `\end{equation}`. |
| `Missing $ inserted` | Simbol matematika (`_`, `^`, `\alpha`) di luar mode matematika, atau `_` pada teks biasa. Bungkus dengan `$...$` atau tulis `\_`. |
| `Misplaced alignment tab character &` | `&` di teks biasa / CSV. Tulis `\&`. |
| Teks setelah `%` hilang | `%` adalah komentar di LaTeX. Tulis `\%` (mis. "95\%"). |
| `File 'xxx.png' not found` | Path gambar salah; ingat path relatif terhadap folder bab saat memakai `\subimport`. Nama file dengan `_` bungkus `\detokenize{}`. |
| `Undefined control sequence` | Salah ketik perintah, atau paket yang menyediakan perintah belum dimuat. Lihat nomor baris di log. |
| Gambar/tabel pindah ke halaman lain | Pakai `[H]` (sudah tersedia via paket `float`). |
| Overfull/Underfull hbox (peringatan kuning) | Hanya peringatan; boleh diabaikan kecuali teks keluar margin. Perbaiki dengan menyusun ulang kalimat atau `\linebreak`. |

### 21.6 Error di bagian glossary setelah menambah recipe (catatan di `main.tex`)

LuaLaTeX tidak membuat `.gls` sendiri. Jika glossary error, kompilasi manual dan pastikan file
`main.glo` (dari lualatex) dan `main.gls` (dari makeglossaries) terbentuk:

```bash
lualatex main.tex       # → main.glo
biber main
makeglossaries main     # → main.gls
lualatex main.tex
lualatex main.tex
```

---

## 22. Ringkasan Cepat (Cheat Sheet)

```latex
% ---------- Sitasi ----------
\parencite{kunci}            % (Penulis, 2020)
\textcite{kunci}             % Penulis (2020)
\parencite[hlm.~5]{kunci}    % (Penulis, 2020, hlm. 5)

% ---------- Singkatan ----------
% di Front_matters/glossary_entries.tex:
\newacronym[sort=ABC]{abc}{ABC}{\textit{A Big Concept}}
% di naskah:
\gls{abc}  \acrshort{abc}  \acrlong{abc}  \acrfull{abc}

% ---------- Persamaan ----------
\begin{equation}
    E = mc^2
    \label{eq:energi}
\end{equation}
\myequation{Energi Relativistik}
% rujuk: Persamaan \eqref{eq:energi}

% ---------- Algoritma ----------
\begin{algorithm}[H]
    \caption{Nama}\label{alg:nama}
    \myalgorithm{Nama}
    \begin{algorithmic}[1]
        \State ...
    \end{algorithmic}
\end{algorithm}

% ---------- Gambar ----------
\begin{figure}[H]
    \centering
    \includegraphics[width=0.8\textwidth]{file.png}
    \caption{Judul}\label{fig:nama}
\end{figure}

% ---------- Tabel ----------
\begin{longtblr}[caption={Judul}, label={tab:nama}]
    {colspec={c l}, rowhead=1, row{1}={font=\bfseries}, hlines, vlines}
    A & B \\
    1 & 2 \\
\end{longtblr}

% ---------- Lampiran (Back_matters/lampiran.tex) ----------
\section{Judul Lampiran}\label{apx:nama}
\mylampiran{Judul Lampiran}
```

**Alur kerja harian:**

1. Tulis di file bab (`Skripsi/.../*.tex`).
2. Tambah referensi → `daftar_pustaka.bib`; tambah singkatan → `glossary_entries.tex`.
3. Simpan (`Ctrl+S`), build (`Ctrl+Alt+B`), lihat PDF (`Ctrl+Alt+V`).
4. Error? Baca error pertama di panel *Problems* → jika aneh, `Ctrl+Alt+C` lalu build ulang.
