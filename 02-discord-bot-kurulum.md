# Discord Bot Kurulumu

## 1. Uygulama Oluştur

1. https://discord.com/developers/applications adresine git
2. "New Application" → isim ver
3. Sol menüden "Bot" → "Add Bot"
4. "Reset Token" ile token'ı kopyala, bir yere kaydet

## 2. Bot İzinleri

Bot sayfasında "Privileged Gateway Intents" altında şunları aç:
- Server Members Intent
- Message Content Intent

## 3. Sunucuya Ekle

Sol menüden "OAuth2" → "URL Generator"
- Scope: `bot`
- Bot Permissions: `Send Messages`, `Read Messages/View Channels`

Oluşan URL'yi tarayıcıda aç, sunucuna ekle.

## 4. Node.js Projesi

```bash
mkdir bot && cd bot
npm init -y
npm install discord.js express cors dotenv
```

`.env` dosyası oluştur:

```
DISCORD_TOKEN=buraya_bot_tokenini_yaz
API_SECRET=gizli_bir_anahtar_uret
PORT=3000
```

API_SECRET için rastgele bir şey yaz, PHP bu anahtarla doğrulama yapacak.
