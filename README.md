# WhatsApp Broadcast Bot (whatsapp-web.js)

Script ini menggunakan [whatsapp-web.js](https://github.com/pedroslopez/whatsapp-web.js) untuk mengirim pesan broadcast otomatis ke banyak nomor WhatsApp menggunakan akun pribadi.

## 📦 Instalasi

### 1. Install Node.js

- Unduh dan install Node.js versi **18.x atau lebih baru** dari [Node.js Official](https://nodejs.org/). atau [Node.js Binary](https://nodejs.org/download/release/v22.17.1/) lalu extract di C:/
- Set path Nodejs
    ```powershell
    set PATH=%PATH%;C:\nodejs\
    ```
- Pastikan sudah terpasang dengan cek versi pada terminal atau CMD:
    ```powershell
    node -v
    npm -v
    ```

### 2. Buat Folder Project

- Jalankan command dibawah dengan terminal atau CMD
    ```powershell
    - mkdir whatsapp-broadcast
    ```
  ```powershell
    - cd whatsapp-broadcast
  ```

### 3. Inisialisasi Project

- Jalankan command dibawah dengan terminal atau CMD
    ```powershell
    npm init -y
    ```

### 4. Install Dependency

- Jalankan command dibawah dengan terminal atau CMD
    ```powershell
    npm install whatsapp-web.js qrcode-terminal
    ```

### 5. Buat File index.js
- ketik di cmd
    ```powershell
    notepad index.js
    ```
    
- Salin kode berikut ke file index.js:
    ```js
    const { Client, LocalAuth, MessageMedia } = require('whatsapp-web.js');
    const qrcode = require('qrcode-terminal');
    const fs = require('fs');
    const path = require('path');
    
    const loadRecipients = (filename) => {
        const ext = path.extname(filename).toLowerCase();
        const data = fs.readFileSync(filename, 'utf-8');
    
        return data
            .split('\n')
            .map(line => line.trim())
            .filter(line => line.length)
            .map(number => number + '@c.us');
    }
    
    const client = new Client({
        authStrategy: new LocalAuth(),
        puppeteer: { headless: true }
    });
    
    const recipients = loadRecipients('recipients.csv'); //sesuaikan nama file
    const sleep = (ms) => new Promise(resolve => setTimeout(resolve, ms));
    
    client.on('qr', qr => {
        qrcode.generate(qr, { small: true });
    });
    
    client.on('ready', async () => {
        console.log('Client is ready!');
    
        for (let number of recipients) {
            const media = MessageMedia.fromFilePath('./path/to/image.png');
            await client.sendMessage(number, media, { caption: 'test message from whatsapp-web-developer' }); // Ubah pesan dibagian ini
            console.log(`Message sent to ${number}`);
            await sleep(3000);
        }
    });
    
    client.on('authenticated', () => {
        console.log('Authenticated successfully!');
    });
    
    client.on('auth_failure', msg => {
        console.error('Authentication failed:', msg);
    });
    
    client.on('disconnected', (reason) => {
        console.log('Client disconnected:', reason);
        client.initialize();
    });
    
    client.initialize();
    ```

### 6. Jalankan Project
- ketik pada cmd
    ```powershell
    node index.js
    ```
