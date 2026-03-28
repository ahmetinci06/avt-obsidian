# 🔬 FEA — Yapısal Analiz
#simulasyon #FEA

## Yük Durumları
| Durum | Açıklama | Güvenlik Katsayısı Hedef |
|-------|----------|-------------------------|
| Statik | Araç kendi ağırlığı | > 3.0 |
| Çukur/engel | 3g dikey yük | > 2.0 |
| Viraj | 1.5g yanal yük | > 2.0 |
| Acil frenleme | 1.5g boyuna yük | > 2.0 |
| Çarpışma (rollbar) | Üstten 2× araç ağırlığı | > 2.5 |

## Checklist
- [ ] Şasi 3D modeli simülasyona aktarıldı
- [ ] Malzeme özellikleri tanımlandı (E, σ_yield, ρ)
- [ ] Mesh oluşturuldu (eleman boyutu < 5mm kritik bölgelerde)
- [ ] Statik analiz tamamlandı — SF: __
- [ ] Çukur/engel analizi — SF: __
- [ ] Viraj analizi — SF: __
- [ ] Frenleme analizi — SF: __
- [ ] Rollbar analizi — SF: __
- [ ] Rapor hazırlandı (Teknik Tasarım Raporu için)

## Yazılım
- SolidWorks Simulation veya ANSYS Student
- [ ] Lisans/erişim sağlandı

## İlişkili: [[Sasi]], [[Suspansiyon]], [[Guvenlik]]
