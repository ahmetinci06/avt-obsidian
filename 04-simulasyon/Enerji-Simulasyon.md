# ⚡ Enerji Tüketim Simülasyonu
#simulasyon #enerji #critical

> [!tip] Yarış Skoru = Wh/km → bu simülasyon stratejinin temelidir

## Güç Denklemi
```
P_total = P_aero + P_rolling + P_grade + P_accel + P_losses

P_aero    = 0.5 × ρ × Cd × A × v³
P_rolling = Crr × m × g × v
P_grade   = m × g × sin(θ) × v
P_accel   = m × a × v
P_losses  = P_total × (1/η - 1)   // η = drivetrain efficiency ~0.85
```

## Parametreler

| Parametre | Değer | Kaynak |
|-----------|-------|--------|
| Araç kütlesi (m) | ~240 kg | [[Agirlik-Dagilimi]] |
| Cd | 0.25 (hedef) | [[CFD-Analiz]] |
| Frontal alan (A) | 1.2 m² | [[Govde]] |
| Crr | 0.008 | [[Tekerlekler]] |
| Drivetrain η | 0.85 | tahmin |
| Batarya kapasitesi | 3 kWh max | yarış kuralı |
| Mesafe | 22.5 km | yarış kuralı |
| Süre | 29 dakika | yarış kuralı |

## Optimal Hız Hesabı
- Min P_total noktası: ~30-35 km/h (aero dominant değil)
- Ama 22.5km/29dk = ortalama 46.5 km/h gerekli
- → Sabit 46.5 km/h veya P&G stratejisi

## Simülasyon Sonuçları Template

| Strateji | Ortalama Hız | Toplam Enerji | Wh/km | Uygun mu? |
|----------|-------------|---------------|-------|-----------|
| Sabit 46.5 km/h | 46.5 | __ Wh | __ | |
| P&G (60/30) | 46.5 | __ Wh | __ | |
| P&G (55/35) | 46.5 | __ Wh | __ | |

## Checklist
- [ ] Python/MATLAB modeli oluşturuldu
- [ ] Pist profili (eğim) girildi
- [ ] Sabit hız simülasyonu
- [ ] P&G simülasyonu
- [ ] Batarya yeterliliği doğrulandı (< 3 kWh)
- [ ] Optimal strateji seçildi
- [ ] Rapor hazırlandı

## İlişkili: [[Yaris-Strateji]], [[esc-energy]], [[Batarya]]
