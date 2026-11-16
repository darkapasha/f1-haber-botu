# 🏎️ F1 Haber Botu

Telegram'da **@F1_haberbot**. Formula 1 takvimi, seans saatleri, yarış sonuçları, şampiyona puanları,
önemli haberlerin Türkçe özetleri ve her sabah sesli bir **F1 Radyosu** bülteni.

> Kardeş bot: [🥊 MMA Haber Botu](https://github.com/darkapasha/mma-haber-botu) (aynı motor, ayrı bot)

---

## Ne yapıyor?

### 📰 Sadece önemli haberler, Türkçe
Motorsport.com, The Race ve Autosport'un RSS'leri 10 dakikada bir okunur, her haberin tam metni çekilir.
Sadece önemli olanlar gönderilir:

| Etiket | Örnek |
|---|---|
| 🚩 Ceza | grid cezası, diskalifiye, FIA kararı |
| 💥 Kaza | ciddi kaza, hasar, yarış dışı kalma |
| 🤕 Sakatlık | pilotun yarışamaması |
| 🔁 Transfer | koltuk değişikliği, yeni sözleşme, takım patronu değişimi |
| 🔥 Popüler | Herkesin konuşacağı büyük haber (yapay zekâ 1-10 arası puanlar, 8 ve üstü gider) |

Her haber Gemini ile Türkçe başlık + tam özet olarak gelir. Aynı olayın farklı sitelerdeki tekrarları elenir.

### 📻 Sabah F1 Radyosu
Her sabah 08:10'da son yarışın ilk 5'i, günün haberleri ve yaklaşan Grand Prix'nin seans saatlerinden
**1-2 dakikalık Türkçe bir radyo bülteni** yazılır ve sesli mesaj olarak gönderilir (`/radyo` ile istendiğinde de).

- Metin: Gemini (olmazsa yerel gemma3:4b). Yalnızca verilen bilgiden yazar, uydurmaz.
- Ses: Gemini TTS, olmazsa Microsoft Edge sesi, o da olmazsa tamamen yerel Piper.
- Pilot ve takım isimleri İngilizce telaffuzla okunur, Türkçe ekleri Türkçe kalır ("Verstappen'in").

### 🗓️ Takvim ve puanlar
- `/gp` — sıradaki Grand Prix, pist ve tüm seansların **Türkiye saati**
- `/sonuclar` — son yarışın sonuçları
- `/pilotlar` — pilotlar şampiyonası
- `/takimlar` — takımlar şampiyonası
- `/haberler` — son 24 saatin önemli haberleri
- Sprint sıralama, sprint, sıralama ve yarıştan **30 dakika önce** hatırlatma
- `/abone` · `/ayril` — bildirimleri aç / kapat

Takvim ve sonuçlar [Jolpica](https://github.com/jolpica/jolpica-f1) (Ergast'ın devamı) API'sinden gelir.

## Yapı

Bu bot [MMA Haber Botu](https://github.com/darkapasha/mma-haber-botu) ile aynı kod tabanını kullanır;
`.env` içinde `SPORTS=f1` olunca F1 botu olarak açılır ve sadece F1 komutlarını gösterir.

```
services/f1.py      Jolpica: takvim, seanslar, sonuçlar, puan durumları
services/news.py    RSS + tam metin
services/llm.py     Gemini / Ollama: çeviri, özet, popülerlik puanı
services/radio.py   sabah bülteni ve ses motorları
jobs.py             haber taraması, seans hatırlatmaları, radyo
```

## Kurulum

```bash
python3 -m venv venv && venv/bin/pip install -r requirements.txt
cp .env.example .env          # TELEGRAM_BOT_TOKEN, GEMINI_API_KEY, SPORTS=f1, RADIO_TIME=08:10
sudo cp deploy/mma-bot.service /etc/systemd/system/f1-bot.service   # WorkingDirectory'yi düzenle
sudo systemctl enable --now f1-bot
```

Anahtarlar yalnızca sunucudaki `.env` dosyasında; depoda yok. Ayrıntılı teknik notlar: [TEKNIK.md](TEKNIK.md)

---

Oracle Cloud'daki küçük bir ARM sunucuda 7/24 çalışıyor. Claude ile birlikte, vibe coding ile yapıldı 🎧
