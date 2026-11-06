# PRD: Field-to-GIS Toolkit untuk Survei Bidang Tanah

Nov 6, 2026 · @Albar

## 1. Ringkasan produk

Field-to-GIS Toolkit adalah kumpulan alat web yang berjalan sepenuhnya di browser, untuk membantu tenaga teknis survei dan pemetaan mengubah data lapangan (GPS handheld, Avenza Maps, Locus, foto geotag) menjadi data spasial yang rapi dan siap diolah di software GIS.

**Latar belakang.** Tugas tenaga teknis mencakup pengumpulan data spasial bidang tanah (pengukuran terestris atau ploting GPS handheld/aplikasi mobile), deliniasi batas dan geotagging, inventarisasi bidang tanah kategori KW 4, 5, 6 berdasarkan data KKP, pengolahan data spasial (overlay, editing, topologi), dan penyusunan geodatabase serta laporan pemutakhiran data.

**Masalah yang diselesaikan.**

- Data dari berbagai alat lapangan datang dalam format berbeda-beda, lalu dikonversi dan dihitung manual di Excel atau salin-tempel koordinat.
- Foto geotag tersebar dan sulit dikaitkan ke bidang tanah yang benar.
- Kesalahan topologi (poligon tidak tertutup, tumpang tindih, titik ganda) baru ketahuan setelah masuk ke software GIS.
- Progres inventarisasi dan laporan harian disusun ulang secara manual.

**Posisi produk.** Alat bantu mandiri dan tidak resmi. Produk tidak terhubung ke GeoKKP atau KKP dan tidak menggantikan sistem ATR/BPN. Seluruh data diproses di perangkat pengguna dan tidak dikirim ke server.

## 2. Tujuan, non-tujuan, dan metrik keberhasilan

Produk berhasil jika tenaga teknis bisa membawa data dari lapangan ke format siap-GIS tanpa Excel manual, dan menemukan kesalahan data sebelum masuk ke pengolahan.

**Tujuan**

- Mengubah data lapangan dari berbagai format menjadi data spasial standar dalam beberapa langkah.
- Menghitung koordinat, luas, jarak, dan azimut dengan benar di sistem koordinat yang dipakai di Indonesia.
- Menghubungkan foto geotag dengan titik atau bidang tanah.
- Mendeteksi kesalahan topologi lebih awal.
- Menyederhanakan pelacakan progres dan pelaporan.

**Non-tujuan**

- Tidak terhubung ke GeoKKP, KKP, atau sistem internal ATR/BPN apa pun.
- Tidak menggantikan software GIS (QGIS, ArcGIS) atau proses resmi pengukuran dan pendaftaran tanah.
- Tidak memiliki akun pengguna, basis data server, atau penyimpanan cloud.
- Tidak menyimpan data pribadi pemilik tanah (nama, NIK, alamat).

**Metrik keberhasilan (target usulan, dikonfirmasi saat uji coba)**

| Metrik | Target |
| --- | --- |
| Akurasi transformasi koordinat vs QGIS/pyproj | Selisih di bawah 1 cm pada semua titik uji |
| Akurasi luas vs QGIS | Selisih di bawah 0,1% |
| Waktu dari file lapangan ke file ekspor siap-GIS | Di bawah 2 menit untuk 100 titik |
| Foto geotag terbaca | Lebih dari 95% foto yang memiliki EXIF GPS |
| Pengguna uji coba yang mau memakai lagi | Minimal 3 dari 5 |

## 3. Pengguna dan skenario penggunaan

Pengguna utama adalah tenaga teknis lapangan yang bekerja dengan ponsel atau laptop, sering dengan sinyal terbatas dan di bawah terik matahari.

| Persona | Kebutuhan utama | Perangkat |
| --- | --- | --- |
| Tenaga teknis lapangan | Mengonversi hasil ploting GPS/Avenza/Locus, mengelola foto geotag, menandai progres bidang | Ponsel Android, sering offline |
| Operator GIS / pengolah data | Memeriksa topologi dan merapikan data sebelum overlay di QGIS | Laptop |
| Koordinator / penyelia | Melihat rekap progres dan laporan per desa | Laptop atau ponsel |

**Skenario utama**

1. **Selesai ploting di lapangan.** Teknisi mengekspor KML dari Avenza, membukanya di toolkit, memilih zona TM-3, dan mengunduh GeoJSON dan CSV koordinat beserta luas tiap bidang.
2. **Foto geotag.** Teknisi mengunggah 80 foto bidang tanah, melihat posisinya di peta, mengelompokkannya per bidang, dan mengekspor tabel foto.
3. **Kontrol kualitas.** Operator GIS mengunggah poligon hasil deliniasi dan menerima daftar bidang yang tumpang tindih atau tidak tertutup.
4. **Pelaporan.** Koordinator mengimpor daftar bidang KW 4/5/6, menandai status survei, dan mencetak rekap mingguan.

## 4. Ruang lingkup dan prioritas

MVP hanya berisi dua modul, yaitu konverter koordinat dan pembaca foto geotag, karena keduanya paling cepat dibuat dan langsung terpakai.

| Modul | Isi | Prioritas | Fase |
| --- | --- | --- | --- |
| M1 Konverter dan transformasi koordinat | Impor, transformasi CRS, luas/jarak/azimut, ekspor | Wajib | 1 |
| M2 Pembaca foto geotag | Baca EXIF, peta, pengelompokan, ekspor | Wajib | 1 |
| M3 Pemeriksa topologi | Deteksi error poligon, laporan error | Tinggi | 2 |
| M4 Tracker inventaris bidang (KW 4/5/6) | Impor daftar CSV, status survei, progres | Sedang | 3 |
| M5 Generator laporan | Rekap harian/mingguan ke PDF | Sedang | 3 |

**Di luar lingkup** (jangan dikerjakan tanpa persetujuan): login dan akun, penyimpanan di server, integrasi GeoKKP/KKP/Bhumi, penggambaran dan editing poligon penuh di peta, pengolahan citra drone, dan aplikasi native Android/iOS.

## 5. Kebutuhan fungsional per modul

Setiap kebutuhan punya ID agar AI agent dan pengujinya bisa merujuk satu per satu. Kriteria penerimaan (KP) menentukan kapan modul dianggap selesai.

### M1. Konverter dan transformasi koordinat (Fase 1)

- **FR-1.1** Impor GeoJSON, KML, KMZ, GPX, dan CSV (kolom lat/lon atau X/Y, dengan pilihan CRS saat impor).
- **FR-1.2** Transformasi antara WGS84 (lintang/bujur), UTM, dan TM-3 untuk zona di Indonesia. Pengguna memilih CRS sumber dan tujuan, dan zona dapat ditebak otomatis dari bujur titik.
- **FR-1.3** Hitung luas (m² dan ha), keliling, jarak antar titik berurutan, dan azimut. Luas dihitung di bidang proyeksi TM-3 dan sebagai pembanding secara geodesik.
- **FR-1.4** Tabel titik yang bisa diedit (tambah, hapus, ubah urutan) dengan penutupan poligon otomatis.
- **FR-1.5** Peta pratinjau (Leaflet) dengan basemap OpenStreetMap.
- **FR-1.6** Ekspor GeoJSON, CSV, dan KML pada MVP. Ekspor Shapefile (zip) dan DXF pada Fase 2.
- **KP-1** Untuk 10 titik uji, hasil transformasi sama dengan QGIS/pyproj dengan selisih di bawah 1 cm. Luas bidang uji berbeda kurang dari 0,1% dari QGIS. File KML dari Avenza dan GPX dari Locus terbaca tanpa error.

### M2. Pembaca foto geotag (Fase 1)

- **FR-2.1** Unggah banyak foto sekaligus (JPG dan HEIC jika didukung browser, hingga 200 foto). Baca EXIF: lintang, bujur, ketinggian, arah foto, dan waktu pemotretan.
- **FR-2.2** Foto tanpa GPS ditandai dan dikecualikan dari peta, tetapi tetap tampil di daftar.
- **FR-2.3** Peta dengan marker foto dan galeri thumbnail. Klik marker menampilkan foto, klik foto memusatkan peta.
- **FR-2.4** Pengelompokan foto per bidang: otomatis berdasarkan jarak (default 20 m, bisa diubah) atau manual dengan label bidang.
- **FR-2.5** Peringatan untuk koordinat mencurigakan (0,0), waktu foto yang tidak wajar, atau foto di luar area kerja.
- **FR-2.6** Ekspor GeoJSON titik foto, CSV (nama file, lat, lon, X/Y TM-3, waktu, label bidang), dan KML.
- **KP-2** 50 foto uji dengan EXIF GPS muncul di posisi benar. Tidak ada foto yang dikirim ke jaringan (diverifikasi lewat tab Network di browser).

### M3. Pemeriksa topologi (Fase 2)

- **FR-3.1** Impor poligon dari GeoJSON, KML, atau Shapefile (zip).
- **FR-3.2** Deteksi: ring tidak tertutup, self-intersection, titik duplikat, vertex berjarak di bawah ambang (default 1 cm), bidang sangat kecil (sliver), tumpang tindih antar bidang, dan celah antar bidang bersebelahan.
- **FR-3.3** Daftar error yang bisa difilter dan diklik untuk memusatkan peta, dengan marker lokasi error.
- **FR-3.4** Perbaikan otomatis hanya untuk titik duplikat dan penutupan ring. Error lain diperbaiki manual di software GIS.
- **FR-3.5** Ekspor GeoJSON titik error dan laporan CSV (ID bidang, jenis error, lokasi, ukuran).
- **KP-3** Pada dataset uji sintetis dengan error yang sudah diketahui, semua error terdeteksi tanpa false positive. 500 poligon diperiksa di bawah 10 detik.

### M4. Tracker inventaris bidang (Fase 3)

- **FR-4.1** Impor daftar bidang dari CSV. Pengguna memetakan sendiri kolom ke field (identifier bidang, desa, kategori KW, catatan). Nama kolom tidak boleh diasumsikan.
- **FR-4.2** Status survei per bidang lewat dropdown: belum, sudah diukur, perlu cek ulang, selesai.
- **FR-4.3** Filter dan pencarian, serta ringkasan progres per desa dan per kategori KW.
- **FR-4.4** Data tersimpan lokal di IndexedDB, dengan ekspor dan impor cadangan (JSON/CSV).
- **FR-4.5** Tautkan bidang ke foto (M2) dan poligon (M1/M3) lewat identifier yang sama.
- **KP-4** Impor 5.000 baris tetap responsif. Data tetap ada setelah browser ditutup dan dibuka lagi.

### M5. Generator laporan (Fase 3)

- **FR-5.1** Rekap harian dan mingguan: jumlah bidang diukur, luas total, jumlah foto, dan isu topologi yang belum selesai.
- **FR-5.2** Ekspor PDF berisi kop (nama kegiatan, desa, tanggal, nama petugas), tabel rekap, peta statis, dan thumbnail foto terpilih.
- **FR-5.3** Template laporan dapat diubah sederhana (kop dan teks pengantar).
- **KP-5** PDF terbuat tanpa koneksi internet selain tile peta, dan angka di laporan sama dengan data di M2 dan M4.

## 6. Kebutuhan non-fungsional

Privasi dan kemampuan offline adalah syarat utama, karena data pertanahan sensitif dan sinyal di lapangan tidak bisa diandalkan.

| Aspek | Persyaratan |
| --- | --- |
| Privasi | Semua pemrosesan di browser. Tidak ada upload file, tidak ada analytics yang membaca isi data. Satu-satunya permintaan jaringan yang boleh membawa lokasi adalah tile peta (hanya koordinat tile). |
| Offline | Dibangun sebagai PWA: aplikasi bisa dibuka dan dipakai tanpa internet setelah kunjungan pertama. Basemap tidak wajib offline. |
| Performa | 1.000 titik, 200 foto, atau 500 poligon diproses dalam waktu wajar di ponsel kelas menengah. Proses berat dijalankan di Web Worker agar antarmuka tidak macet. |
| Kompatibilitas | Chrome dan Edge terbaru (Android dan desktop) wajib. Safari iOS diusahakan. |
| Bahasa dan antarmuka | Bahasa Indonesia. Tombol besar (target sentuh minimal 44 px), kontras tinggi agar terbaca di bawah matahari, mode gelap opsional. |
| Keamanan | Content Security Policy ketat, tanpa eval, dependensi dikunci versinya, tanpa skrip pihak ketiga selain library yang dibundel. |
| Lisensi | Kode memakai lisensi permisif. Atribusi OpenStreetMap tampil di peta. |
| Kualitas data | Setiap hasil perhitungan menampilkan CRS yang dipakai. Tampilkan peringatan jelas bahwa hasil adalah alat bantu dan harus diverifikasi sebelum dipakai resmi. |

## 7. Arsitektur dan tech stack

Aplikasi berupa situs statis (single-page app) tanpa backend, sehingga bisa dihosting gratis dan tidak ada data yang melewati server.

| Kebutuhan | Pilihan yang disarankan |
| --- | --- |
| Build dan bahasa | Vite + TypeScript |
| UI | React (atau Svelte jika agent lebih nyaman) |
| Peta | Leaflet |
| Transformasi koordinat | proj4js (definisi CRS dari registri EPSG) |
| Analisis spasial | Turf.js, ditambah library topologi seperti JSTS bila Turf tidak cukup |
| Baca EXIF | exifr |
| Format file | @tmcw/togeojson (KML/GPX), shpjs dan shp-write (Shapefile), JSZip (KMZ/zip) |
| Penyimpanan lokal | Dexie (IndexedDB) |
| PDF | pdf-lib atau jsPDF |
| PWA | vite-plugin-pwa |
| Pengujian | Vitest (unit) dan Playwright (end-to-end) |
| Hosting | Situs statis (Cloudflare Pages, GitHub Pages, atau server kantor) |

**Aturan arsitektur**

- Satu modul = satu folder fitur yang mandiri. Logika murni (transformasi, topologi, parsing) dipisah dari komponen UI agar mudah diuji.
- Pekerjaan berat (topologi, parsing file besar) berjalan di Web Worker.
- Representasi internal selalu WGS84, urutan \[bujur, lintang\]. Konversi ke CRS tampilan atau ekspor dilakukan di tepi (saat impor dan ekspor).

**Struktur proyek yang disarankan**

```text
src/
  core/          # crs, geometry, units, errors (tanpa UI)
  io/            # parser dan writer: geojson, kml, gpx, csv, shp, dxf
  features/
    coords/      # M1
    photos/      # M2
    topology/    # M3
    tracker/     # M4
    report/      # M5
  workers/
  ui/            # komponen bersama, tema, tata letak
  pwa/
tests/
  fixtures/      # data uji sintetis
```

## 8. Model data, format file, dan sistem koordinat

Semua geometri disimpan dalam WGS84 dan baru diubah ke UTM atau TM-3 saat ditampilkan atau diekspor, supaya tidak ada pembulatan berlapis.

**Model data inti (TypeScript, acuan awal)**

```typescript
type SurveyPoint = { id: string; lon: number; lat: number; elevation?: number; label?: string };
type Parcel      = { id: string; ring: [number, number][]; label?: string; village?: string; kw?: 4 | 5 | 6 };
type Photo       = { id: string; fileName: string; lon?: number; lat?: number; takenAt?: string; bearing?: number; parcelId?: string };
type TrackerRow  = { parcelId: string; village?: string; kw?: string; status: 'belum' | 'diukur' | 'cek_ulang' | 'selesai'; note?: string };
```

**Sistem koordinat**

- Yang harus didukung: WGS84 (EPSG:4326), UTM zona Indonesia (belahan utara dan selatan), dan TM-3 untuk zona di Indonesia.
- Parameter CRS (kode EPSG, meridian tengah, faktor skala, false easting dan northing) harus diambil dari registri EPSG resmi saat implementasi, bukan ditulis dari ingatan. Agent wajib mencantumkan sumbernya di kode.
- Datum DGN95 dan SRGI2013 praktis sangat dekat dengan WGS84. Untuk MVP perlakukan sebagai setara, tampilkan catatan itu di antarmuka, dan konfirmasi dengan pengguna apakah transformasi datum perlu ditambah.

**Contoh CSV titik (impor dan ekspor)**

```csv
id,label,x,y,crs
P1,patok_1,438120.512,9124050.318,EPSG:32749
P2,patok_2,438145.870,9124061.002,EPSG:32749
```

**Aturan nama file ekspor**: `<modul>_<tanggal YYYYMMDD>_<crs>.<ekstensi>`, misalnya `titik_20261106_tm3.csv`. Tidak ada data pribadi di dalam file ekspor.

## 9. Rancangan halaman dan alur UX

Antarmuka dibuat mobile-first dan sesederhana mungkin: satu halaman per modul, satu tindakan utama per layar.

| Halaman | Isi |
| --- | --- |
| Beranda | Kartu besar untuk tiap modul, status offline, dan tautan ke panduan singkat |
| Konverter koordinat | Area unggah, tabel titik, panel CRS sumber dan tujuan, peta pratinjau, tombol ekspor |
| Foto geotag | Area unggah, peta dan galeri berdampingan (bertumpuk di ponsel), panel pengelompokan |
| Topologi | Unggah poligon, daftar error, peta dengan marker error |
| Tracker | Tabel bidang dengan filter, ringkasan progres |
| Laporan | Formulir kop laporan dan pratinjau PDF |
| Pengaturan | CRS default, ambang jarak, tema terang/gelap, hapus data lokal |

**Alur utama modul M1**

1. Pengguna menyeret atau memilih file dari Avenza/Locus/GPS.
2. Aplikasi mendeteksi format dan menampilkan titik di peta serta tabel.
3. Aplikasi menebak CRS dan zona, pengguna bisa mengoreksi.
4. Aplikasi menampilkan luas, keliling, dan azimut.
5. Pengguna memilih format ekspor lalu mengunduh.

**Prinsip antarmuka**

- Semua pesan error dalam bahasa Indonesia dan menyebut cara memperbaikinya.
- Tidak ada langkah yang butuh akun atau pendaftaran.
- Setiap hasil menampilkan CRS dan satuan yang dipakai.
- Tombol "Hapus semua data lokal" selalu mudah ditemukan.

## 10. Rencana fase dan urutan pengerjaan

Pekerjaan dibagi lima fase berurutan, dan fase berikutnya tidak dimulai sebelum syarat selesai fase sebelumnya terpenuhi. Durasi menyesuaikan waktu Anda dan tidak ditetapkan di sini.

1. **Fase 0: Fondasi.** Setup proyek Vite + TypeScript, CI sederhana, kerangka PWA, halaman Beranda, dan modul `core/crs`. *Selesai jika* build dan tes dasar lulus dan aplikasi bisa dibuka offline.
2. **Fase 1: MVP (M1 dan M2).** Konverter koordinat dan pembaca foto geotag dengan ekspor GeoJSON, CSV, dan KML. *Selesai jika* KP-1 dan KP-2 terpenuhi dan dua orang tenaga teknis mencobanya dengan data nyata mereka sendiri.
3. **Fase 2: Kontrol kualitas.** Pemeriksa topologi (M3), ekspor Shapefile dan DXF. *Selesai jika* KP-3 terpenuhi.
4. **Fase 3: Manajemen dan pelaporan.** Tracker (M4) dan generator laporan (M5). *Selesai jika* KP-4 dan KP-5 terpenuhi dan format data KKP yang dipakai sudah dikonfirmasi.
5. **Fase 4: Pemolesan dan uji coba.** Perbaikan dari umpan balik, panduan pengguna singkat, dan dokumentasi untuk pemeliharaan. *Selesai jika* minimal tiga dari lima pengguna uji bersedia memakainya lagi.

## 11. Strategi pengujian dan data uji

Kebenaran hitungan koordinat dan luas diuji terhadap nilai acuan dari QGIS atau pyproj, bukan terhadap hasil kode itu sendiri.

- **Unit test (Vitest).** Setiap fungsi di `core` dan `io`, termasuk transformasi CRS, luas, azimut, dan parser. Nilai acuan dihasilkan sekali dari QGIS/pyproj lalu disimpan di `tests/fixtures`.
- **Data uji sintetis.** Dibuat sendiri, tanpa data pertanahan nyata: poligon dengan error yang sudah diketahui (tidak tertutup, tumpang tindih, celah), file KML/GPX contoh dari Avenza dan Locus, dan foto contoh dengan EXIF GPS.
- **Uji end-to-end (Playwright).** Alur utama tiap modul: unggah, lihat hasil, ekspor, dan buka file ekspor.
- **Uji privasi.** Pantau tab Network saat memproses file dan pastikan tidak ada permintaan yang membawa isi data.
- **Uji manual di ponsel.** Checklist untuk Chrome Android: unggah dari galeri, tampilan di bawah cahaya terang, dan penggunaan offline.

**Definisi selesai untuk setiap fitur:** kode ditulis, tes lulus, kriteria penerimaan (KP) terpenuhi, dan perilaku yang berubah dicatat di CHANGELOG.

## 12. Risiko, asumsi, dan pertanyaan terbuka

Risiko terbesar adalah salah paham tentang sistem koordinat dan format data kantor, jadi keduanya harus dikonfirmasi sebelum Fase 1 dianggap final.

| Risiko | Dampak | Mitigasi |
| --- | --- | --- |
| Parameter TM-3 atau datum salah | Koordinat bergeser, hasil tidak bisa dipakai | Ambil parameter dari registri EPSG, uji dengan nilai acuan QGIS, tampilkan CRS di setiap hasil |
| Format ekspor KKP berbeda dari dugaan | Modul tracker tidak cocok | Pengguna memetakan kolom sendiri, minta contoh struktur data tanpa isi |
| Data sensitif bocor | Pelanggaran privasi | Proses di browser, tanpa upload, tanpa data pribadi, peringatan di antarmuka |
| Dipakai sebagai alat resmi tanpa verifikasi | Kesalahan data resmi | Peringatan jelas bahwa produk adalah alat bantu tidak resmi |
| Foto dari beberapa ponsel tidak punya EXIF GPS | Foto tidak muncul di peta | Tandai foto tanpa GPS dan izinkan penempatan manual |
| Kinerja buruk di ponsel lama | Aplikasi macet | Web Worker, batas ukuran data, pesan peringatan |

**Asumsi**

- Pengguna punya browser modern di ponsel atau laptop.
- Pengolahan akhir tetap dilakukan di software GIS yang dipakai kantor.
- Tidak ada data pertanahan nyata yang dipakai selama pengembangan.

**Pertanyaan terbuka (jawab sebelum agent mulai Fase 1)**

- [ ] Zona TM-3 dan datum apa yang dipakai di kantor Anda (DGN95 atau SRGI2013)?
- [ ] Apa definisi kategori KW 4, 5, 6 dan seperti apa format ekspor datanya dari KKP?
- [ ] Aplikasi lapangan apa yang dipakai dan format ekspor apa (KML, GPX, CSV)?
- [ ] Ponsel apa yang dipakai di lapangan (merek, versi Android)?
- [ ] Produk dipakai sendiri, dibagikan ke rekan, atau diajukan resmi ke kantor?
- [ ] Nama produk dan tempat hosting.

## 13. Instruksi kerja untuk AI agent

Bagian ini ditujukan langsung kepada AI agent yang mengerjakan proyek. Agent membaca seluruh PRD dulu, lalu bekerja satu fase pada satu waktu.

**Aturan kerja**

1. Kerjakan hanya fase yang diminta. Jangan menambah fitur di luar bagian 4 (ruang lingkup).
2. Sebelum menulis kode Fase 1, tanyakan jawaban untuk pertanyaan terbuka di bagian 12 yang belum terjawab. Jangan menebak jawabannya.
3. Jangan menulis parameter CRS dari ingatan. Ambil dari registri EPSG dan catat sumbernya di komentar kode.
4. Tidak ada kode yang mengirim data pengguna ke jaringan. Jangan menambah analytics, telemetri, atau layanan pihak ketiga.
5. Tulis tes bersama kode. Fitur dianggap selesai hanya jika kriteria penerimaan (KP) terpenuhi.
6. Commit kecil dan sering, dengan pesan yang jelas. Catat perubahan perilaku di CHANGELOG.
7. Di akhir tiap fase, laporkan: apa yang selesai, hasil tes, KP yang terpenuhi atau belum, dan hal yang butuh keputusan saya.
8. Jika spesifikasi ambigu, berhenti dan tanya. Jangan memilih sendiri untuk hal yang memengaruhi akurasi data.

**Prompt awal yang bisa ditempel ke agent**

```text
Baca PRD "Field-to-GIS Toolkit untuk Survei Bidang Tanah" sampai selesai.
Kerjakan Fase 0 lalu Fase 1 (M1 dan M2) sesuai bagian 5, 7, dan 8.
Patuhi aturan kerja di bagian 13. Sebelum mulai coding, ajukan pertanyaan
yang belum terjawab dari bagian 12. Setelah Fase 1 selesai, laporkan hasil
tes dan kriteria penerimaan KP-1 dan KP-2, lalu tunggu persetujuan saya
sebelum lanjut ke Fase 2.
```
