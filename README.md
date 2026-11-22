# Aplikasi PPDB - SMK Pelayaran Akpelni Semarang

Calon taruna membuka **link formulir** (dibagikan lewat WhatsApp), mengisi data dan mengunggah foto,
lalu data masuk otomatis ke **aplikasi panitia** milik sekolah.

| File | Fungsi |
|---|---|
| `index.html` | Formulir pendaftaran untuk calon taruna (halaman publik) |
| `admin.html` | Aplikasi panitia (wajib login): daftar pendaftar, verifikasi berkas, statistik, ekspor CSV, WA, cetak |
| `config.js` | Pengaturan sekolah dan sambungan ke Supabase. **Satu-satunya file yang perlu Anda ubah** |
| `supabase/schema.sql` | Perintah untuk menyiapkan database, aturan keamanan, dan penyimpanan foto |

Tampilan ditayangkan gratis oleh **GitHub Pages**. Data dan foto disimpan di **Supabase**.

---

## Langkah 1. Buat proyek Supabase

1. Daftar di https://supabase.com (boleh masuk dengan akun GitHub).
2. Klik **New project**. Beri nama (misalnya `ppdb-akpelni`), buat **Database Password** dan simpan baik-baik,
   pilih region **Southeast Asia (Singapore)**.
3. Tunggu 1-2 menit sampai proyek siap.

## Langkah 2. Siapkan database

1. Di Supabase buka **SQL Editor** > **New query**.
2. Buka file `supabase/schema.sql`, salin **seluruh isinya**, tempel, lalu klik **Run**.
3. Harus muncul "Success". File ini aman dijalankan ulang.

## Langkah 3. Buat akun panitia

1. Buka **Authentication > Users > Add user > Create new user**.
2. Isi email dan kata sandi panitia, **centang "Auto Confirm User"**, lalu simpan.
3. Kembali ke **SQL Editor**, jalankan (ganti email dan nama):

```sql
insert into public.panitia (user_id, nama)
select id, 'Nama Panitia' from auth.users where email = 'panitia@sekolah.sch.id'
on conflict do nothing;
```

4. Ulangi langkah 1-3 untuk setiap panitia lain. Hanya akun yang ada di tabel `panitia` yang bisa membuka data.
5. **Penting:** buka **Authentication > Sign In / Providers** (atau **Settings**) dan **matikan "Allow new users to sign up"**,
   supaya orang luar tidak bisa membuat akun sendiri.

## Langkah 4. Isi config.js

1. Di Supabase buka **Project Settings > API**.
2. Salin **Project URL** ke `supabaseUrl`, dan kunci **anon / public** ke `supabaseKey` di file `config.js`.
3. Isi juga `whatsappPanitia` (format `628xxxxxxxxxx`) bila ingin ada tombol "Hubungi panitia".
4. **Jangan pernah memasukkan kunci `service_role`** ke file mana pun. Kunci anon memang aman dipublikasikan
   karena hak aksesnya dibatasi oleh aturan di database.

## Langkah 5. Tayangkan di GitHub Pages

1. Buat akun di https://github.com, lalu **New repository** (misalnya `ppdb`). Pilih **Public** (Pages gratis memerlukan repositori publik).
   Di repositori hanya ada kode; data pendaftar **tidak** pernah disimpan di GitHub.
2. Klik **uploading an existing file**, unggah semua file proyek ini (`index.html`, `admin.html`, `config.js`, `README.md`, `.nojekyll`
   dan folder `supabase`), lalu **Commit changes**.
3. Buka **Settings > Pages**. Pada *Build and deployment*, pilih **Deploy from a branch**, branch `main`, folder `/ (root)`, klik **Save**.
4. Tunggu 1-2 menit. Alamatnya:
   - Formulir calon taruna: `https://NAMA-GITHUB.github.io/ppdb/`
   - Aplikasi panitia: `https://NAMA-GITHUB.github.io/ppdb/admin.html`
5. Bagikan **alamat formulir** lewat WhatsApp, brosur, atau QR code. Alamat panitia jangan dibagikan ke umum (tetap wajib login).

## Langkah 6. Uji sebelum dibuka untuk umum

1. Buka alamat formulir, isi data uji dan unggah 4 foto, lalu kirim.
2. Masuk ke `admin.html` dengan akun panitia. Data uji harus tampil lengkap dengan fotonya.
3. Coba ubah status, tandai berkas, simpan catatan, lalu hapus data uji (foto ikut terhapus).

---

## Fitur aplikasi panitia

- Daftar pendaftar dengan pencarian, filter status, kompetensi, dan status berkas
- Melihat foto pas foto, akta lahir, KK, dan KTP orang tua/wali (tautan foto sementara, berlaku 15 menit)
- Status pendaftaran: baru, diverifikasi, diterima, ditolak. Pemeriksaan berkas: belum, lengkap, kurang. Catatan panitia
- Penanda **Gratis** untuk pendaftar pertama (jumlahnya diatur `kuotaGratis` di `config.js`) dan penanda **Data ganda**
- Tombol WA ke calon taruna dan orang tua dengan pesan otomatis sesuai status
- Cetak formulir, ekspor CSV (bisa dibuka di Excel), statistik (per kompetensi, jenis kelamin, sumber informasi, asal sekolah, per hari)
- Hapus data beserta fotonya

## Keamanan dan data pribadi

- Pengunjung umum hanya bisa **mengirim** pendaftaran. Mereka tidak bisa membaca, mengubah, atau menghapus data siapa pun.
- Foto disimpan di penyimpanan **privat**. Hanya panitia yang login yang bisa melihatnya.
- Pendaftaran ganda (nama + tanggal lahir + No. WA sama) ditolak otomatis. Ada batas 40 pendaftaran per menit untuk mencegah serangan spam.
- KK, KTP, dan akta termasuk **data pribadi sensitif**, apalagi milik anak. Batasi akun panitia seperlunya, gunakan kata sandi kuat,
  dan hapus data setelah tidak diperlukan sesuai kebijakan sekolah dan UU Pelindungan Data Pribadi.
- Lakukan **ekspor CSV berkala** sebagai cadangan.

## Batas paket gratis Supabase (perkiraan)

- Penyimpanan 1 GB. Foto dikecilkan otomatis; perkiraan 1-1,5 MB per pendaftar, jadi kira-kira 700 sampai 1.000 pendaftar. Ini perkiraan, bukan hasil uji.
- Proyek gratis **dijeda otomatis jika tidak ada aktivitas selama sekitar 1 minggu**. Cukup buka dashboard Supabase untuk mengaktifkannya lagi.
  Ini penting di luar masa pendaftaran.
- Jika pendaftar melebihi batas, pertimbangkan paket berbayar Supabase.

## Mengubah formulir

- Nama sekolah, tahun ajaran, dan kuota gratis: ubah di `config.js`.
- Menambah atau mengubah pertanyaan: harus diubah di tiga tempat (`index.html`, `admin.html`, dan `supabase/schema.sql`). Minta bantuan agar tidak ada yang terlewat.

## Pemecahan masalah

| Masalah | Penyebab / solusi |
|---|---|
| Muncul tulisan "belum disambungkan ke Supabase" | `supabaseUrl` atau `supabaseKey` di `config.js` belum diisi |
| Panitia login tetapi muncul "belum terdaftar sebagai panitia" | Jalankan SQL penambahan panitia (Langkah 3) untuk email tersebut |
| Data terkirim tetapi foto gagal | Tekan "Coba unggah ulang". Foto hanya bisa diunggah dalam 2 jam setelah pendaftaran |
| Foto tidak tampil di aplikasi panitia | Tautan foto berlaku 15 menit. Tutup dan buka lagi kartunya, atau tekan Muat ulang |
| Halaman tidak berubah setelah memperbarui file | Tunggu 1-2 menit lalu muat ulang paksa (Ctrl+F5) |
