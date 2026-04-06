# PHP'den API'ye Bağlanmak

PHP, Node.js API'sine `curl` ile istek atar.

## Temel Yardımcı Sınıf

```php
<?php
// DiscordAPI.php

class DiscordAPI {
    private string $baseUrl;
    private string $apiKey;

    public function __construct(string $baseUrl, string $apiKey) {
        $this->baseUrl = rtrim($baseUrl, '/');
        $this->apiKey  = $apiKey;
    }

    private function istek(string $yol, string $metod = 'GET', array $veri = []): array {
        $ch = curl_init($this->baseUrl . $yol);
        curl_setopt_array($ch, [
            CURLOPT_RETURNTRANSFER => true,
            CURLOPT_TIMEOUT        => 10,
            CURLOPT_HTTPHEADER     => [
                'Content-Type: application/json',
                'x-api-key: ' . $this->apiKey
            ],
        ]);

        if ($metod === 'POST') {
            curl_setopt($ch, CURLOPT_POST, true);
            curl_setopt($ch, CURLOPT_POSTFIELDS, json_encode($veri));
        }

        $yanit  = curl_exec($ch);
        $hata   = curl_error($ch);
        curl_close($ch);

        if ($hata) return ['hata' => $hata];
        return json_decode($yanit, true) ?? [];
    }

    public function durum(): array {
        return $this->istek('/durum');
    }

    public function mesajGonder(string $kanalId, string $mesaj): array {
        return $this->istek('/mesaj-gonder', 'POST', [
            'kanalId' => $kanalId,
            'mesaj'   => $mesaj
        ]);
    }

    public function uyeler(string $sunucuId): array {
        return $this->istek('/uyeler/' . $sunucuId);
    }
}
```

## Kullanım

```php
<?php
require 'DiscordAPI.php';

$discord = new DiscordAPI(
    baseUrl: 'http://localhost:3000',
    apiKey:  'gizli_bir_anahtar_uret'   // .env ile aynı olmalı
);

// Bot durumunu göster
$durum = $discord->durum();
echo "Bot aktif mi: " . ($durum['aktif'] ? 'Evet' : 'Hayır') . "\n";
echo "Ping: " . $durum['ping'] . "ms\n";

// Kanala mesaj gönder
$sonuc = $discord->mesajGonder('KANAL_ID_BURAYA', 'Web sitesinden merhaba!');
if (isset($sonuc['basarili'])) {
    echo "Mesaj gönderildi.\n";
}

// Üye listesi
$uyeler = $discord->uyeler('SUNUCU_ID_BURAYA');
foreach ($uyeler as $uye) {
    echo $uye['isim'] . ' — ' . $uye['rol'] . "\n";
}
```

## Kanal ve Sunucu ID Nasıl Bulunur

Discord'da geliştirici modunu aç:
Ayarlar → Gelişmiş → Geliştirici Modu → Aç

Sonra kanala veya sunucuya sağ tıkla → "ID'yi Kopyala"

## Güvenlik Notu

`apiKey` değerini PHP tarafında da `.env` veya config dosyasında tut,
doğrudan koda yazma. Örnek:

```php
$discord = new DiscordAPI(
    baseUrl: $_ENV['BOT_API_URL'],
    apiKey:  $_ENV['BOT_API_KEY']
);
```
