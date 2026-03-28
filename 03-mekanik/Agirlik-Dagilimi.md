# ⚖️ Ağırlık Dağılımı & Bütçe
#mekanik #ağırlık #critical

> [!warning] Hedef
> Toplam araç ağırlığı (sürücüsüz) **< 180 kg**. Her kg = daha fazla Wh/km.

## Ağırlık Bütçesi

| Alt Sistem | Hedef (kg) | Gerçek (kg) | Durum |
|------------|-----------|-------------|-------|
| Şasi (çelik boru) | 40 | | ⬜ |
| Gövde panelleri | 15 | | ⬜ |
| Motor + ESC | 15 | | ⬜ |
| Batarya paketi | 30 | | ⬜ |
| Direksiyon sistemi | 5 | | ⬜ |
| Fren sistemi | 8 | | ⬜ |
| Süspansiyon | 12 | | ⬜ |
| Tekerlekler (4×) | 20 | | ⬜ |
| Koltuk + emniyet | 10 | | ⬜ |
| Kablolama + elektronik | 8 | | ⬜ |
| Diğer (bağlantı, cıvata) | 7 | | ⬜ |
| **TOPLAM (sürücüsüz)** | **170** | | |
| Sürücü | ~70 | | — |
| **TOPLAM (sürücülü)** | **~240** | | |

## Ağırlık Merkezi (CG)
- Hedef: akslar arası %45 ön / %55 arka
- Yükseklik: mümkün olduğunca düşük (devrilme riski)
- [ ] CG hesaplaması (her parçanın konumu × ağırlığı)
- [ ] CG ölçümü (tartı + tekerlek yükü yöntemi)

## Azaltma Fırsatları
- [ ] Alüminyum jant vs çelik jant (jant başına ~2kg tasarruf)
- [ ] Karbon fiber gövde vs fiberglas (~%30 daha hafif)
- [ ] Kablo optimizasyonu (kesit küçültme, rota kısaltma)
- [ ] Gereksiz destek elemanlarını FEA ile tespit et

## İlişkili Dosyalar
- [[Sasi]] — ana yapısal ağırlık
- [[Batarya]] — en ağır alt sistem
- [[Enerji-Simulasyon]] — ağırlık → enerji etkisi
