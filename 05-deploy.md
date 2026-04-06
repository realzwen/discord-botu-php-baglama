# Deploy — İnternete Açmak

## Senaryo 1: Aynı Sunucuda (En Yaygın)

PHP ve Node.js aynı VPS'te çalışır.
PHP, `http://localhost:3000` adresine istek atar.
Dışarıya sadece PHP (Apache/Nginx) açık olur, Node.js port'u kapalı kalır.

```
İnternet → Nginx (80/443) → PHP
                          → Node.js (sadece localhost:3000, dışarıya kapalı)
```

Nginx config örneği — Node.js'i dışarıya kapatmak için:

```nginx
# /etc/nginx/sites-available/site.conf

server {
    listen 80;
    server_name siteadresi.com;
    root /var/www/html;
    index index.php;

    location ~ \.php$ {
        include fastcgi_params;
        fastcgi_pass unix:/run/php/php8.2-fpm.sock;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
    }
}
```

Node.js'i arka planda çalıştırmak için `pm2` kullan:

```bash
npm install -g pm2
pm2 start server.js --name discord-bot
pm2 save
pm2 startup
```

## Senaryo 2: Ayrı Sunucular

Node.js farklı bir sunucuda veya Railway/Render'da çalışıyorsa,
PHP'deki `baseUrl`'i o sunucunun adresiyle değiştir:

```php
$discord = new DiscordAPI(
    baseUrl: 'https://bot.siteadresi.com',
    apiKey:  $_ENV['BOT_API_KEY']
);
```

Bu durumda Node.js tarafında CORS ve rate limiting kritik hale gelir.

## Senaryo 3: Localhost Test (ngrok)

```bash
npx ngrok http 3000
```

Sana `https://abc123.ngrok.io` gibi bir URL verir.
PHP'deki `baseUrl`'i bununla değiştir, test edebilirsin.

## Özet Kontrol Listesi

- [ ] `.env` dosyası sunucuda mevcut
- [ ] `API_SECRET` PHP ve Node.js tarafında aynı
- [ ] Node.js portu (3000) dışarıya kapalı
- [ ] pm2 ile bot arka planda çalışıyor
- [ ] PHP curl eklentisi aktif (`php -m | grep curl`)
