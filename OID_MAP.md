# Peta OID OLT HSGQ-G02ID

OLT HSGQ-G02ID, firmware `HSGQ-G02ID_IGC_V1.2.9ID_Rel`, dibaca lewat SNMP v2c read-only dan dicocokkan dengan web UI pada 11 Oktober 2026. **Terkonfirmasi** = ada bukti dari nilai di UI atau selisih dua pembacaan. **Dugaan** = belum ada bukti.

| Hal | Nilai |
| --- | --- |
| sysObjectID | `.1.3.6.1.4.1.50224.3.1.1` |
| Enterprise OID | `1.3.6.1.4.1.50224` (subtree kerja `1.3.6.1.4.1.50224.3`) |
| Model | HSGQ-G02ID (sama dengan Product Model di UI) |

## Aturan indeks dan skala

- Indeks port dan PON di tabel enterprise: `16777216 + (ifIndex - 256) x 256`. PON01 (ifIndex 257) = 16777472, PON02 = 16777728, GE01 (259) = 16777984, XGE01 (263) = 16779008.
- Indeks ONU: `16777472 + N`, dengan N = nomor ONU di UI (PON01/N). Nomor boleh bolong. Indeks mengikuti ONU, bukan nama: setelah nama diganti di UI, nama di SNMP berubah tetapi indeks dan serial tetap.
- Nilai optik SFP dan ONU dibagi 100 (suhu, tegangan, arus, dBm). Uptime dan lama online dalam centidetik.
- Counter64 octet dikali 8 dan dihitung per detik menjadi bit per detik.

Peta subtree di bawah `1.3.6.1.4.1.50224.3` (walk 2286 baris, 2284 masuk 10 subtree ini):

| Subtree | Entri | Isi |
| --- | --- | --- |
| 1.1 | 28 | Skalar sistem |
| 1.5 | 14 | Tabel akun (sensitif, jangan dipantau) |
| 2.1 | 98 | Tabel port |
| 2.3 | 10 | Jumlah ONU per PON |
| 2.4 | 33 | Optik SFP (PON01, PON02, XGE01) |
| 10.1 | 315 | Statistik port |
| 12.2 | 1300 | Tabel ONU (52 ONU x 25 kolom) |
| 12.3 | 265 | Optik ONU |
| 12.8 | 14 | Belum diperiksa isinya |
| 13.1 | 207 | Belum diperiksa isinya |

## OID terkonfirmasi

### Sistem

| OID | Arti | Skala / satuan | Bukti |
| --- | --- | --- | --- |
| 1.3.6.1.2.1.1.1.0 | Model (sysDescr) | Teks | Sama dengan Product Model di UI |
| 1.3.6.1.2.1.1.5.0 | Nama perangkat (sysName) | Teks | Sama dengan Device Name di UI |
| 1.3.6.1.2.1.1.3.0 | Uptime (sysUpTime) | Centidetik | Cocok dengan Running Status |
| 1.3.6.1.4.1.50224.3.1.1.1.0 | MAC OLT | Hex | Sama dengan MAC di UI |
| 1.3.6.1.4.1.50224.3.1.1.6.0 | Versi firmware | Hex ke teks | Hex `49 47 43 5F ...` menjadi `IGC_V1.2.9ID_Rel` |
| 1.3.6.1.4.1.50224.3.1.1.20.0 | Serial number OLT | Teks | Sama dengan SN di Running Status |
| 1.3.6.1.4.1.50224.3.1.1.18.0 | Memori terpakai | Persen | 39 sama dengan Memory Usage 39% di UI; 185444 dari 466988 = 39,7% |

### Port

| OID | Arti | Skala / satuan | Bukti |
| --- | --- | --- | --- |
| 1.3.6.1.2.1.2.2.1.2 | Nama port (ifDescr) | Teks | Tujuh port: PON01, PON02, GE01 sampai GE04, XGE01 |
| 1.3.6.1.2.1.2.2.1.8 | Status link (ifOperStatus) | 1 up, 2 down | 7 dari 7 port cocok dengan UI. Hanya bisa lewat walk, GET dijawab No Such Instance |
| 1.3.6.1.2.1.31.1.1.1.6 | Octet masuk (ifHCInOctets) | Counter64, kali 8 per detik = bps | Selisih sama dengan kolom enterprise 10.1.1.4; lajunya sebanding dengan Performance Monitor |
| 1.3.6.1.2.1.31.1.1.1.10 | Octet keluar (ifHCOutOctets) | Counter64, kali 8 per detik = bps | Selisih sama dengan kolom enterprise 10.1.1.26 |

### Optik PON (SFP modul OLT)

| OID | Arti | Skala / satuan | Bukti |
| --- | --- | --- | --- |
| 1.3.6.1.4.1.50224.3.2.4.1.8.indeks | Suhu SFP | Bagi 100 = derajat C | 4450 = 44,50 C; UI 44,70 C |
| 1.3.6.1.4.1.50224.3.2.4.1.9.indeks | Tegangan SFP | Bagi 100 = V | 330 = 3,30 V; UI 3,31 V |
| 1.3.6.1.4.1.50224.3.2.4.1.10.indeks | Arus bias SFP | Bagi 100 = mA | 2736 = 27,36 mA; UI 27,36 mA |
| 1.3.6.1.4.1.50224.3.2.4.1.11.indeks | Daya Tx SFP | Bagi 100 = dBm | 809 = 8,09 dBm; UI 8,1000 dBm |

Port PON tanpa modul bernilai 0 di semua kolom (di UI: "not detected").

### ONU

| OID | Arti | Skala / satuan | Bukti |
| --- | --- | --- | --- |
| 1.3.6.1.4.1.50224.3.12.2.1.2.indeks | Nama ONU | Teks | Sama dengan ONT Table; berubah setelah diganti di UI |
| 1.3.6.1.4.1.50224.3.12.2.1.15.indeks | Serial ONU | Teks | Sama dengan ONT Basic Info |
| 1.3.6.1.4.1.50224.3.12.2.1.19.indeks | Jarak | Meter | 13 dari 13 ONU pertama cocok dengan Distance(m) di UI |
| 1.3.6.1.4.1.50224.3.12.2.1.23.indeks | Lama online | Centidetik | Last up time ditambah nilai ini sama dengan waktu sekarang; sama dengan Uptime di ONT Basic Info |
| 1.3.6.1.4.1.50224.3.12.2.1.22.indeks | Penyebab down terakhir | Teks | Cocok dengan Last down cause; nilai yang terlihat: `Power down`, `Laser out` |
| 1.3.6.1.4.1.50224.3.12.3.1.4.indeks.0.0 | Daya Rx ONU | Bagi 100 = dBm, resolusi 1 dB, dibulatkan ke bawah | Skala dan urutan kolom cocok dengan ONT Optical Diagnose; contoh: -20 dBm di SNMP sama dengan -19,36 dBm di ONT Table |

## Dugaan dan belum terbukti

| OID | Dugaan arti | Kenapa belum terbukti | Cara membuktikan |
| --- | --- | --- | --- |
| 1.3.6.1.4.1.50224.3.1.1.17.0 | CPU terpakai (persen) | Nilai 0 cocok dengan UI 0-3%, tapi 12 sampel semuanya 0 | Baca saat System Monitor di UI menunjukkan CPU di atas 0 |
| 1.3.6.1.4.1.50224.3.1.1.26 / 27 / 28 | Memori total / terpakai / bebas | Terpakai + bebas = total, tapi satuannya tidak diketahui | Bandingkan dengan total memori di UI jika ada |
| 1.3.6.1.4.1.50224.3.1.1.8 / 9 / 10 / 11 | Jumlah PON, GE, XGE, total port (2, 4, 1, 7) | Cocok dengan perangkat keras, belum diuji | Tidak mendesak |
| 1.3.6.1.4.1.50224.3.2.3.1.3 / 4 / 5 | ONU registered / online / offline per PON | Saat ini nilainya sama semua (tidak ada ONU offline), urutan tak bisa dibedakan | Baca saat ada ONU offline secara alami |
| 1.3.6.1.4.1.50224.3.12.2.1.3 / 4 / 5 / 24 | Status ONU | Semua bernilai 1 untuk ONU yang online | Baca saat ada ONU offline |
| 1.3.6.1.2.1.2.2.1.13 / 14 / 19 / 20 | Discard dan error port | Selalu 0 selama uptime 122 hari | Tunggu ada error nyata |
| 1.3.6.1.2.1.31.1.1.1.15 | Kecepatan port (Mbps) | Satu port melapor 100, lainnya 0; belum dicocokkan dengan UI | Bandingkan dengan Status > Uplink |
| 1.3.6.1.4.1.50224.3.2.4.1.4 / 5 | Modul SFP terdeteksi | 2 untuk port bermodul, 1 untuk yang kosong | Pasang modul di slot kosong |
| 1.3.6.1.4.1.50224.3.2.4.1.6 / 7 | Vendor dan nomor part modul SFP | Belum dicocokkan dengan label modul | Cek label fisik modul |
| 1.3.6.1.4.1.50224.3.2.4.1.12 | Daya Rx modul SFP PON | Sekitar -33 dBm; tidak ada padanannya di UI dan janggal untuk banyak ONU aktif | Cari di UI atau tanya vendor |
| 1.3.6.1.4.1.50224.3.12.8 | Daftar ONU belum terdaftar | Isinya belum diperiksa | Baca dari file walk |
| 1.3.6.1.4.1.50224.3.13.1 | Tabel MAC | Isinya belum diperiksa | Cocokkan dengan MAC Management di UI |

**Daya Rx ONU yang meragukan.** Pada satu ONU pernah terbaca +1,49 dBm di ONT Optical Diagnose dan lewat SNMP, sementara ONT Table menunjukkan -19,36 dBm. Beberapa saat kemudian SNMP membaca -20 dBm, cocok dengan ONT Table. Penyebab lonjakan itu belum diketahui.

**Ambang Rx** -26 dBm (peringatan) dan -28 dBm (tinggi) adalah usulan awal, bukan spesifikasi model ONU.

## Yang tidak ada, yang berbahaya, dan keanehan perangkat

- **Tidak ada suhu board atau chassis** lewat SNMP enterprise. Satu-satunya suhu adalah SFP modul PON. Suhu SFP uplink juga tidak ada: port GE tidak muncul di tabel optik, dan slot XGE tanpa modul bernilai 0.
- **Ada OID yang menyimpan kredensial**, termasuk community tulis, dan terbaca lewat community baca. Ini ada di subtree sistem dan subtree akun. Jangan dimasukkan ke monitoring, ke chat, atau ke log, dan jangan bagikan hasil walk mentah. Batasi akses UDP/161 dan ganti community default.
- **Status port enterprise terbalik.** Kolom status di tabel port enterprise bernilai 2 untuk up dan 1 untuk down. Pakai `ifOperStatus` standar.
- **GET ke ifTable gagal.** `1.3.6.1.2.1.2.2.1.x` dijawab No Such Instance padahal walk berhasil. `ifXTable` (`1.3.6.1.2.1.31.1.1.1.x`) bisa di-GET. Karena itu template memakai satu item `walk[]` sebagai sumber, dan item port sebagai item turunan.
- **Target trap bawaan** menunjuk ke alamat lain, bukan ke server monitoring. Item trap di template tidak akan terisi sebelum target diubah.
- **Walk penuh** sebaiknya tidak diulang di jam sibuk. Polling dibuat lambat (1 sampai 5 menit).

## Pengujian yang masih terbuka

Dilakukan lewat perubahan alami, tanpa memutus layanan:

- [ ] Kolom status ONU 3 / 4 / 5 / 24: baca saat ada ONU offline
- [ ] Jumlah ONU per PON (`2.3.1.3 / 4 / 5`): baca saat ada ONU offline
- [ ] CPU (`1.1.17`): baca saat CPU di System Monitor UI di atas 0
- [ ] Kecepatan port uplink: cocokkan dengan Status > Uplink
- [ ] Rx ONU: pantau apakah lonjakan positif muncul lagi
