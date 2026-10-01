# DNA → RNA → Asam Amino

Satu notebook dengan satu tujuan: **mengubah urutan DNA menjadi urutan asam amino (protein) melalui mRNA terlebih dahulu**.

## Cara pakai

Cara termudah adalah **Google Colab**, tanpa instalasi apa pun.

1. Buka <https://colab.research.google.com>, pilih **File → Open notebook → tab GitHub**, tempel URL repositori ini, lalu pilih `DNA_ke_RNA_ke_Asam_Amino.ipynb`. Alternatif: unduh file `.ipynb`, lalu **File → Upload notebook**.
2. Isi kotak **"1. Masukkan DNA di sini"**. Isian awal adalah contoh dari slide kuliah.
3. Klik **Runtime → Run all** (atau `Ctrl + F9`).
4. Hasil tampil di bawah kotak **"3. Hasil"**.

Untuk DNA lain, ubah isiannya lalu ulangi langkah 3.

Di Colab, kode pada sel 1 sampai 3 disembunyikan dan hanya kotak isian serta hasilnya yang tampak. Untuk melihat kodenya, klik dua kali pada sel tersebut.

### Isian

| Isian | Arti | Pilihan |
|---|---|---|
| `dna` | Urutan DNA yang ingin diubah, dibaca sebagai untai cetakan | Teks bebas (lihat format di bawah) |
| `berhenti_di_stop` | Berhenti di kodon stop pertama | Dicentang: protein dipotong sebelum stop. Tidak: seluruh urutan diterjemahkan, stop ditandai `*` |
| `reading_frame` | Basa awal pembacaan kodon pada mRNA | 1, 2, atau 3 |
| `tampilkan_tabel_kodon` | Tampilkan tabel kode genetik (64 kodon) | Dicentang atau tidak |

### Format DNA yang diterima

- Huruf besar atau kecil.
- Spasi, angka, dan baris baru boleh ada; semuanya dibuang otomatis.
- Salinan dari FASTA boleh; baris judul yang diawali `>` diabaikan.
- Hanya huruf **A, C, G, T**. Jika ada karakter lain, atau huruf `U` (tampaknya RNA), notebook menampilkan pesan yang menjelaskan masalahnya, bukan error teknis.

### Contoh

Nilai awal pada notebook (default):

```
DNA     : TCGGTGAATCTGTTTGAT
Komplemen: AGCCACTTAGACAAACTA
mRNA    : AGCCACUUAGACAAACUA
Kodon   : AGC CAC UUA GAC AAA CUA
Protein : S H L D K L          (Ser-His-Leu-Asp-Lys-Leu)
```

Contoh dengan kodon stop: isi `dna` dengan `TACCGGTAACATTACCCGGCGACTTTCCCACGGGCTATC` dan centang `berhenti_di_stop`. Hasilnya `MAIVMGR`, berhenti di kodon stop `UGA` (kodon ke-8).

## Cara kerja

1. **Transkripsi (DNA → mRNA).** DNA yang dimasukkan dibaca sebagai untai cetakan. Tiap basa diganti pasangannya (A↔T, C↔G), lalu T diganti U. Sesuai slide, urutan tidak dibalik. Contoh slide: `GGATGCCAATG` → `CCTACGGTTAC` → `CCUACGGUUAC`.
2. **Translasi (mRNA → asam amino).** mRNA dibaca per tiga basa (kodon) memakai tabel kode genetik: 64 kodon untuk 20 asam amino dan sinyal stop (`UAA`, `UAG`, `UGA`).
3. **Reading frame.** Titik awal pembacaan menentukan pengelompokan kodon; menggesernya satu basa menghasilkan protein yang berbeda.

Bila jumlah basa tidak habis dibagi tiga, sisa di ujung tidak diterjemahkan dan hal ini dicatat pada hasil.

## Menjalankan di local komputer

Butuh Python 3.9 atau lebih baru dan Jupyter. Notebook hanya memakai pustaka standar Python, jadi tidak ada library lain yang perlu dipasang.

```bash
git clone <URL-repositori-ini>
cd <nama-folder-repositori>
pip install jupyter
jupyter notebook DNA_ke_RNA_ke_Asam_Amino.ipynb
```

Pilih **Kernel → Restart & Run All**. Di Jupyter atau VS Code, kotak isian dan penyembunyian kode khas Colab tidak muncul. Baris `dna = "..."` pada sel 1 tampil sebagai kode biasa; ubah nilainya langsung di sana, lalu jalankan ulang sel 1 sampai 3.

## Pesan galat yang mungkin muncul

| Pesan | Penyebab dan solusi |
|---|---|
| "Kotak DNA masih kosong" | Isian `dna` kosong. Isi dengan urutan DNA. |
| "…tampaknya sudah RNA" | Urutan mengandung `U`. Tulis `T` sebagai ganti `U`. |
| "Ditemukan karakter selain A, C, G, T" | Ada huruf lain pada urutan; pesan menyebut hurufnya dan posisinya. |
| "DNA terlalu pendek" | Minimal 3 basa (setelah reading frame diterapkan) untuk satu kodon. |
| "sel 1 dan 2 belum dijalankan" | Sel 3 dijalankan sebelum sel lain. Klik **Runtime → Run all**. |

## Batasan

- Translasi diterapkan pada seluruh urutan sesuai reading frame yang dipilih, tidak mencari kodon start `AUG`.
- Notebook menerima DNA saja (A, C, G, T); kode ambigu seperti N tidak didukung.
- DNA selalu dianggap untai cetakan, mengikuti contoh slide. Urutan gen dari database seperti NCBI umumnya berupa untai kode (sense); jika dimasukkan apa adanya, hasilnya berbeda dari protein aslinya. Untuk kasus itu, masukkan komplemennya.
- Transkripsi komplemen per basa tanpa membalik arah untai 5'→3', yang disederhanakan dibanding proses biologis sebenarnya.

## Pengujian

Hasil translasi dibandingkan dengan `Bio.Seq.translate` dari Biopython (setelah komplemen dibuat) pada 300 urutan acak untuk reading frame 1, 2, dan 3: tidak ada perbedaan. Dua contoh slide (`GGATGCCAATG` dan `TCGGTGAATCTGTTTGAT`) juga menghasilkan keluaran yang sama dengan slide. Biopython hanya dipakai untuk pengujian, bukan oleh notebook.