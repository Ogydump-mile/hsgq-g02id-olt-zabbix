# Zabbix template untuk OLT GPON HSGQ-G02ID (SNMP)

**Status: v1.0.0-beta.** Dipetakan dari perangkat nyata (firmware `HSGQ-G02ID_IGC_V1.2.9ID_Rel`) dan diuji di Zabbix 7.4.6. Setiap OID diberi label **terkonfirmasi** (cocok dengan web UI OLT) atau **dugaan** (nilai terbaca tapi artinya belum terbukti).

**English summary:** a community-made Zabbix SNMP template and OID map for the HSGQ-G02ID GPON OLT (enterprise OID `1.3.6.1.4.1.50224`). It covers ports and uplink traffic, PON optics, per-ONU Rx power, distance and uptime. Vendor documentation was not available, so every OID was verified against the OLT web UI; unverified ones are marked as such. Documentation is in Indonesian.

## Isi repo

| File | Isi |
| --- | --- |
| `HSGQ_GPON_OLT_by_SNMP.yaml` | Template Zabbix (format ekspor 7.0) |
| `OID_MAP.md` | Peta OID: arti, skala, bukti, dan daftar dugaan |

## Yang dipantau

- Sistem: model, uptime, versi firmware, serial, memori
- Port (PON, GE, XGE): status link, traffic masuk dan keluar, error dan discard
- PON: suhu, tegangan, arus bias, dan daya Tx modul SFP; jumlah ONU
- Per ONU (ditemukan otomatis lewat LLD): daya Rx, jarak, lama online, penyebab down terakhir
- 17 trigger siap pakai (suhu SFP, Rx rendah, memori, uptime, dan lainnya)

## Cara pakai

1. Di Zabbix: **Data collection > Templates > Import**, pilih `HSGQ_GPON_OLT_by_SNMP.yaml`.
2. Centang **Create new** dan **Update existing**. Jangan centang **Delete missing**.
3. Pasang template ke host OLT. Host harus punya interface SNMP v2c dengan community **read-only**.
4. Di host, buka **Discovery** dan klik **Execute now** pada tiga rule: port, PON, ONU.
5. Atur makro `{$OLT.*}` sesuai kebutuhan, misalnya `{$OLT.IF.ALERT:"GE01"}=1` untuk menyalakan alarm link-down di port uplink.

Butuh Zabbix 7.0 atau lebih baru.

## Peringatan keselamatan

- Template hanya **membaca** (SNMP GET). Jangan pernah SNMP SET ke OLT produksi.
- Beberapa OID pada perangkat ini menyimpan **kredensial** (community dan akun) dan terbaca lewat community baca. Template ini sengaja tidak memakainya. Jangan menambahkannya, dan jangan membagikan hasil walk mentah.
- Pakai community baca saja, batasi akses UDP/161, dan polling pelan (1 sampai 5 menit).
- Walk penuh ke OLT produksi sebaiknya tidak diulang-ulang di jam sibuk.

## Keterbatasan yang diketahui

- Tidak ada suhu board/chassis lewat SNMP. Satu-satunya suhu adalah suhu SFP modul PON.
- Daya Rx ONU hanya beresolusi 1 dB dan dibulatkan ke bawah.
- Status per ONU (online/offline), jumlah ONU per PON, dan CPU masih **dugaan**: nilainya belum terlihat berubah. Semuanya sengaja tanpa trigger.
- GET langsung ke `ifTable` dijawab "No Such Instance" oleh firmware ini walau walk berhasil, jadi template memakai item `walk[]` sebagai sumber data.
- Kolom status port di tabel enterprise terbalik (2 = up). Template memakai `ifOperStatus` standar.
- Ambang Rx (-26 dan -28 dBm) adalah usulan, bukan spesifikasi model ONU.

## Kontribusi

Ada OLT HSGQ lain atau firmware berbeda? Silakan buka Issue dengan hasil yang kamu temukan, terutama untuk OID yang masih berstatus dugaan. Sertakan hanya nilai yang sudah dibersihkan dari nama pelanggan, serial, dan kredensial.

## Lisensi

MIT. Tidak ada jaminan apa pun; gunakan dengan risiko sendiri, dan uji dulu di lingkungan yang tidak kritis.
