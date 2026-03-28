# 🏁 Yarış Stratejisi
#simulasyon #strateji #yarış

## Pulse & Glide vs Sabit Hız

| Strateji | Açıklama | Avantaj | Dezavantaj |
|----------|----------|---------|------------|
| Sabit hız | 46.5 km/h sürekli | Basit, tahmin edilebilir | Aero kayıp sabit |
| P&G | Hızlan→kosta→hızlan | Daha verimli (~%10-15) | Karmaşık, sürücü becerisi |

## P&G Parametreleri (Optimize Edilecek)
- **Pulse hız:** __ km/h (üst limit)
- **Glide hız:** __ km/h (alt limit)  
- **Pulse süresi:** __ saniye
- **Glide süresi:** __ saniye
- **Ortalama hız:** 46.5 km/h (22.5km/29dk)

## Tur Enerji Bütçesi
- Toplam enerji: < 3000 Wh (batarya limiti)
- Hedef Wh/km: < 110
- Toplam tüketim hedefi: < 2475 Wh (22.5 km × 110 Wh/km)
- Güvenlik marjı: %10 → < 2250 Wh kullan

## Sürücü Talimatları
- [ ] Hız göstergesi/dashboard hazır
- [ ] P&G hız bantları sürücüye öğretildi
- [ ] Pratik turları yapıldı
- [ ] Zamanlama testi (29dk içinde bitiyor mu?)

## Hava Durumu Etkileri
- Rüzgar: karşı rüzgar → enerji ↑, arka rüzgar → enerji ↓
- Sıcaklık: düşük sıcaklık → batarya kapasitesi ↓ (%5-10)
- Yağmur: yuvarlanma direnci ↑, görüş ↓

## İlişkili: [[Enerji-Simulasyon]], [[esc-energy]], [[Kurallar]]
