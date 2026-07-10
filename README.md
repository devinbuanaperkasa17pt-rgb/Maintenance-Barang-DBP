# Maintenance Barang - PT Devin Buana Perkasa

Sistem manajemen maintenance & perbaikan barang (AC, Printer, Lampu, dll) untuk kantor dan proyek.

## Alur Kerja

```
Staff ajukan servis
        |
        v
   Status: Pending
        |
        v
Admin crosscheck --> Disetujui / Ditolak
        |
        v (jika disetujui)
Status: Menunggu Teknisi
        |
        v
Status: Sedang Diservis
        |
        v
Admin isi tindakan, hasil akhir, biaya
        |
        v
   Status: Selesai
```

Selain alur perbaikan (reaktif), ada juga **Jadwal Maintenance Rutin** (preventif) yang berjalan terpisah — untuk servis berkala seperti cuci AC tiap 3 bulan, tanpa menunggu barang rusak.

## Arsitektur

```
Google Sheet (database)
      |
      v
Google Apps Script (Code.gs) -- Web App API (doGet / doPost)
      |
      v
index.html (frontend, di-hosting di GitHub Pages)
```

## Struktur Google Sheet

File: `DB_Maintenance_Barang`

| Sheet | Isi |
|---|---|
| `Data_Barang` | Master aset: nama, kategori, proyek & lokasi saat ini, kondisi |
| `Riwayat_Perpindahan` | Log setiap kali barang pindah antar proyek |
| `Permintaan_Perbaikan` | Data inti: pengajuan staff, approval admin, progres servis, hasil akhir |
| `Jadwal_Maintenance_Rutin` | Jadwal servis berkala, otomatis hitung tanggal berikutnya (rumus EDATE) |
| `Master_Data` | Sumber dropdown: kategori, divisi, staff, teknisi/vendor, proyek |
| `Log_Aktivitas` | Jejak audit semua perubahan |

## Setup

### 1. Google Sheet
- Buka `DB_Maintenance_Barang_TEMPLATE.xlsx` → File → Save as Google Sheets
- Pastikan 6 sheet di atas ada dengan nama persis sama

### 2. Google Apps Script (Backend)
- Di Google Sheet: **Extensions → Apps Script**
- Paste isi `Code.gs`
- Ganti `SPREADSHEET_ID` di baris atas dengan ID spreadsheet kamu (dari URL sheet)
- **Deploy → New deployment → Web app**
  - Execute as: **Me**
  - Who has access: **Anyone**
- Copy URL yang berakhiran `/exec`

### 3. Frontend (GitHub Pages)
- Upload `index.html` ke repo ini
- Aktifkan di **Settings → Pages** (branch `main`, folder `/root`)
- Buka URL GitHub Pages-nya
- Tempel URL Apps Script `/exec` di kotak konfigurasi (muncul di atas saat pertama buka) → **Simpan & Muat Data**

## Cara Pakai

- **Sebagai Staff**: pilih role "Staff" di sidebar, isi nama, buka menu **Ajukan Servis** untuk lapor kerusakan, pantau progres di **Permintaan Saya**
- **Sebagai Admin**: pilih role "Admin", buka **Kelola Permintaan** untuk approve/tolak, panggil teknisi, mulai servis, dan menyelesaikan dengan catatan tindakan + biaya
- **Jadwal Rutin**: admin tandai selesai setiap servis berkala; tanggal berikutnya terhitung otomatis

## Troubleshooting

| Masalah | Solusi |
|---|---|
| Menu Extensions tidak muncul di Sheet | File masih format `.xlsx`, convert dulu: File → Save as Google Sheets |
| `{"success":false,"message":"Action tidak dikenali: undefined"}` | Kurang parameter `?action=...` di belakang URL |
| Data tidak muncul di web | Pastikan pakai URL `/exec` (bukan `/dev`), dan deployment access-nya "Anyone" |
| Setelah edit `Code.gs`, perubahan tidak berlaku | Harus buat deployment baru atau **Manage deployments → Edit → New version** |

## Catatan

- Belum ada sistem login sungguhan — role & nama dipilih manual di sidebar (tahap awal)
- Upload foto bukti kerusakan pakai link eksternal (Google Drive/WA), belum upload langsung
