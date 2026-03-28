# 🌊 CFD — Aerodinamik Analiz
#simulasyon #CFD

## Hedefler
- **Cd (sürükleme katsayısı):** < 0.25
- **Frontal alan:** minimize (hedef < 1.2 m²)
- **Cd × A < 0.30 m²**

## Sınır Koşulları
| Parametre | Değer |
|-----------|-------|
| Hız aralığı | 30-60 km/h (8.3-16.7 m/s) |
| Türbülans modeli | k-ε veya k-ω SST |
| Yol yüzeyi | Moving wall (araç hızında) |
| Domain boyutu | 5L ön, 10L arka, 3W yan |

## Checklist
- [ ] Gövde 3D modeli CFD'ye aktarıldı
- [ ] Mesh oluşturuldu (yüzey: 5mm, hacim: 50mm, inflation 10 katman)
- [ ] 40 km/h analizi tamamlandı — Cd: __, Cl: __
- [ ] 50 km/h analizi tamamlandı
- [ ] Basınç dağılımı görselleştirildi
- [ ] Ayrılma bölgeleri tespit edildi
- [ ] Tasarım iyileştirme iterasyonu yapıldı
- [ ] Rapor hazırlandı

## İlişkili: [[Govde]], [[Enerji-Simulasyon]]
