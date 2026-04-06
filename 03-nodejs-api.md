# Node.js API Sunucusu

Bu dosya hem Discord botunu çalıştırır hem de PHP'nin bağlanacağı
HTTP endpoint'lerini sunar.

## server.js

```js
require('dotenv').config();
const express   = require('express');
const cors      = require('cors');
const { Client, GatewayIntentBits } = require('discord.js');

const app    = express();
const client = new Client({
  intents: [
    GatewayIntentBits.Guilds,
    GatewayIntentBits.GuildMessages,
    GatewayIntentBits.MessageContent,
  ]
});

app.use(express.json());
app.use(cors());

// Her istekte API anahtarı kontrolü
app.use((req, res, next) => {
  if (req.headers['x-api-key'] !== process.env.API_SECRET) {
    return res.status(401).json({ hata: 'Yetkisiz' });
  }
  next();
});

// Bot durumu
app.get('/durum', (req, res) => {
  res.json({
    aktif: client.isReady(),
    ping: client.ws.ping,
    sunucuSayisi: client.guilds.cache.size
  });
});

// Kanala mesaj gönder
app.post('/mesaj-gonder', async (req, res) => {
  const { kanalId, mesaj } = req.body;
  try {
    const kanal = await client.channels.fetch(kanalId);
    await kanal.send(mesaj);
    res.json({ basarili: true });
  } catch (e) {
    res.status(500).json({ hata: e.message });
  }
});

// Sunucu üye listesi
app.get('/uyeler/:sunucuId', async (req, res) => {
  try {
    const sunucu = await client.guilds.fetch(req.params.sunucuId);
    const uyeler = await sunucu.members.fetch();
    const liste  = uyeler.map(u => ({
      id: u.id,
      isim: u.user.username,
      rol: u.roles.highest.name
    }));
    res.json(liste);
  } catch (e) {
    res.status(500).json({ hata: e.message });
  }
});

client.once('ready', () => {
  console.log(`Bot hazir: ${client.user.tag}`);
});

client.login(process.env.DISCORD_TOKEN);
app.listen(process.env.PORT, () => {
  console.log(`API calisiyor: http://localhost:${process.env.PORT}`);
});
```

## Çalıştır

```bash
node server.js
```

Artık `http://localhost:3000` adresinde bir API var.
PHP bu adrese istek atacak.
