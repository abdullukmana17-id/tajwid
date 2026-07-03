# Panduan Membuat Project React Menggunakan Vite

## Persiapan

1. Install **Node.js** versi LTS melalui **https://nodejs.org/**.
2. Install **Windows Terminal** melalui **Microsoft Store** (disarankan agar pengalaman menggunakan terminal lebih nyaman).

## Membuat Project

1. Buat sebuah folder baru sebagai lokasi penyimpanan project.
2. Buka folder tersebut.
3. Klik kanan pada area kosong di dalam folder, kemudian pilih **Open in Terminal**.

## Membuat Project React

Jalankan perintah berikut:

```bash
npm create vite@latest
```

Jika muncul konfirmasi seperti berikut:

```text
Need to install the following packages:
create-vite@9.1.1
Ok to proceed? (y)
```

Ketik **`y`**, kemudian tekan **Enter**.

## Konfigurasi Project

Saat proses pembuatan project berlangsung, pilih opsi berikut:

* **Project name**: Masukkan nama project yang diinginkan.

  ```text
  Project name: nama_project
  ```
* **Framework**: Pilih **React**.
* **Variant**: Pilih **JavaScript**.
* **Linter**: Pilih **ESLint**.
* **Install with npm and start now?**: Pilih **Yes** agar seluruh dependensi langsung diinstal dan development server dijalankan secara otomatis.

## Menjalankan Project

Setelah proses instalasi selesai, Vite akan menjalankan development server secara otomatis dan menampilkan alamat lokal seperti berikut:

```text
Local: http://localhost:5173/
```

Buka browser, kemudian akses alamat berikut untuk melihat aplikasi React yang telah dibuat:

```text
http://localhost:5173/
```

Apabila halaman React berhasil ditampilkan, berarti project telah berhasil dibuat dan siap untuk dikembangkan.
