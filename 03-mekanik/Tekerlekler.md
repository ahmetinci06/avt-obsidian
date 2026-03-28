# 🛞 Tekerlekler & Lastikler
#mekanik #tekerlek

## Tekerlek Specleri
- [ ] Jant çapı: __" (hedef: 14-16")
- [ ] Jant genişliği: __"
- [ ] Bolt pattern: __ × __mm
- [ ] Jant malzemesi: çelik / alüminyum (ağırlık farkı)

## Lastik Seçimi
> [!tip] Düşük yuvarlanma direnci en önemli kriter — Wh/km'yi doğrudan etkiler

- [ ] Lastik boyutu: ___/___R__
- [ ] Yuvarlanma direnci katsayısı (Crr): hedef < 0.008
- [ ] Lastik basıncı: __ bar (yüksek basınç → düşük Crr ama düşük tutunma)
- [ ] Marka/model seçimi

## Tekerlek Çevresi (Firmware İçin)
```
circumference = π × (jant_çapı + 2 × lastik_yüksekliği)
```
**Mevcut firmware değeri:** `ENERGY_WHEEL_CIRCUMFERENCE_M = 1.60f`
- [ ] Gerçek ölçümle güncelle!

## Rulman Seçimi
- [ ] Sealed/shielded mühürlü rulman (toz/su koruması)
- [ ] Düşük sürtünme — enerji kaybını minimize et
- [ ] Boyut: tekerlek hub'a uygun

## İlişkili Dosyalar
- [[esc-energy]] — tekerlek çevresi parametresi
- [[Enerji-Simulasyon]] — yuvarlanma direnci etkisi
- [[Agirlik-Dagilimi]] — tekerlek ağırlığı bütçesi
