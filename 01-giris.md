# Discord Bot + PHP Web Sitesi Entegrasyonu

## Mimari

```
[Discord]  <-->  [Node.js Bot + Express API]  <-->  [PHP Web Sitesi]
```

PHP, Discord API ile doğrudan konuşamaz çünkü Discord bot bağlantısı
WebSocket gerektirir. PHP ise istek-cevap modeliyle çalışır.

Çözüm: Araya bir Node.js katmanı koyarsın.
Node.js hem Discord'a bağlı kalır hem de PHP'nin çağırabileceği
bir HTTP API sunar.

## Akış

```
Kullanıcı PHP sayfasında bir şey yapar
        ↓
PHP, Node.js API'ye HTTP isteği atar (curl)
        ↓
Node.js isteği alır, Discord'a iletir
        ↓
Discord'da işlem gerçekleşir
```

## Gereksinimler

- Node.js 18+
- PHP 8.0+
- Bir Discord uygulaması (bot token)
- Sunucu veya localhost
