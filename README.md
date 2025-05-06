# Implementasi eksperimen XSS dan mitigasinya ke dalam VS Code
- Buka Visual Studio Code buat folder baru, misalnya xss-demo
- Di dalam folder xss-demo, Buat file baru beri nama: xss-test.html
  lalu masukkan kode:

```
  <!-- xss-test.html --><!DOCTYPE html>
<html lang="id">
  <head>
    <meta charset="UTF-8" />
    <title>Reflected XSS Demo</title>
  </head>
  <body>
    <form action="" method="GET">
      <input type="text" name="name" />
      <button type="submit">Kirim</button>
    </form>
    <p>Halo, <span id="output"></span></p>

    <script>
      const params = new URLSearchParams(window.location.search);
      // Baris ini rawan XSS
      document.getElementById("output").innerHTML =
        params.get("name") || "pengunjung";
    </script>

    <script>
      const params = new URLSearchParams(window.location.search);
      const clean = DOMPurify.sanitize(params.get("name"));
      document.getElementById("output").innerHTML = clean || "pengunjung";
    </script>
  </body>
</html>
```
  ![Screenshot (29)](https://github.com/user-attachments/assets/a78e5895-d1ac-4c7a-b096-4fc2d4c7c32d)

- Klik kanan pada file xss-test.html dan pilih "Open with Live Server"
  Maka akan muncul dan berpindah ke browser
  ![Screenshot 2025-05-06 150209](https://github.com/user-attachments/assets/394e0d85-8379-434e-9fc5-d73445beee29)

- Browser yang sudah terbuka tambahkan input seperti ini di URL:
  ![Screenshot 2025-05-06 150909](https://github.com/user-attachments/assets/0b36574b-3b41-4d8c-bfee-94ae4ea7d186)

- Tekan Enter maka akan muncul pop-up alert "XSS!" sebagai simulasi serangan.
  ![Screenshot 2025-05-06 151620](https://github.com/user-attachments/assets/6c631f72-4dc2-45bb-9560-f443f7ea1b19)

- Mitigasi Serangan XSS
  Ubah baris JavaScript pada bagian:
  ```
  <script>
    const params = new URLSearchParams(window.location.search);
    // Baris ini rawan XSS
    document.getElementById("output").innerHTML = params.get("name") || "pengunjung";
  </script>
  ```
  Menjadi
  ```
  <script>
  const params = new URLSearchParams(window.location.search);
  // Mitigasi XSS dengan textContent
  document.getElementById("output").textContent = params.get("name") || "pengunjung";
  </script>
  ```
  
- Tambahkan DOMPurify
  Jika ingin membolehkan beberapa HTML tapi tetap aman, maka tambahkan library DOMPurify
  Tambahkan CDN DOMPurify ke HTML:
  ```
  <script src="https://cdn.jsdelivr.net/npm/dompurify@3.0.3/dist/purify.min.js"></script>
  ```
  Lalu ubah code sebelumnya menjadi:
  ```
  <script>
  const params = new URLSearchParams(window.location.search);
  const clean = DOMPurify.sanitize(params.get("name"));
  document.getElementById("output").innerHTML = clean || "pengunjung";
  </script>

  ```  
