# 📊 AVT Racing Team - Genel Proje Durumu

> **Son Güncelleme:** 2026-03-28 20:40 UTC  
> **TEKNOFEST'e kalan gün:** 148 gün  
> **Teknik Rapor deadline'a kalan gün:** 100 gün

## 🎯 Genel Progress: 65% 

```
█████████████████████████████████████████████████████████████████░░░░░░░░░░░░░░ 65%
```

---

## 📈 Takım Bazlı Progress

### 🔌 Elektronik Takımı: 85%
```
███████████████████████████████████████████████████████████████████████████████░░░ 85%
```
**Lead:** Onur Musaoğlu | **Status:** 🟢 İyi durumda

#### Tamamlanan İşler ✅
- [x] STM32G491RET pin mapping finalize edildi
- [x] AKS PCB schematic %95 tamamlandı  
- [x] TLV9064 current sense amplifier seçimi yapıldı
- [x] TMP102 sıcaklık sensörü entegrasyonu planlandı
- [x] CAN bus transceiver konfigürasyonu belirlendi
- [x] Power stage MOSFET araştırması tamamlandı

#### Devam Eden İşler 🟡
- [ ] PCB layout finalizasyonu (Deadline: Apr 15) #critical
- [ ] Gate driver IC finalizasyonu (IR2110 vs UCC27211) 
- [ ] Current sense gain hesaplamaları (1mΩ shunt için)
- [ ] Dead time calculation (170ns doğrulama)

#### Bloke İşler 🔴
- [ ] PCB üretim maliyeti teklifi (3 üretici bekleniyor)

---

### 💻 Yazılım Takımı: 70%
```
██████████████████████████████████████████████████████████████████░░░░░░░░░░░░ 70%
```
**Lead:** Aybüke Pamukçu | **Status:** 🟡 Plan dahilinde

#### Tamamlanan İşler ✅  
- [x] STM32CubeMX project template oluşturuldu
- [x] TIM1 PWM konfigürasyonu (Period=9999, Prescaler=0)
- [x] ADC1 konfigürasyonu (PC0=throttle, PC1=current)
- [x] FDCAN1 konfigürasyonu (PA11/PA12)
- [x] I2C1 konfigürasyonu (TMP102 için)
- [x] vcu_pedal.c temel yapısı oluşturuldu

#### Devam Eden İşler 🟡
- [ ] 6-step commutation tablosu implementasyonu
- [ ] Hall sensor debouncing algoritması  
- [ ] Current limit protection (overcurrent detection)
- [ ] CAN message format standardizasyonu
- [ ] Energy calculation algoritması (Wh/km)

#### Öncelik Listesi
1. **Hall sensor commutation** (Bu hafta) #critical
2. **CAN protocol** (Gelecek hafta)  
3. **Safety systems** (2 hafta sonra)

---

### 🔩 Mekanik Takımı: 45%  
```
█████████████████████████████████████████████░░░░░░░░░░░░░░░░░░░░░░░░░░░░░ 45%
```
**Lead:** Mahir Cengiz | **Status:** 🔴 Geride kalıyor

#### Tamamlanan İşler ✅
- [x] Şasi konsept tasarımı tamamlandı
- [x] Motor mounting bracket ön tasarımı
- [x] Wheel hub tasarım alternatifleri araştırıldı
- [x] Brake disk seçimi yapıldı

#### Devam Eden İşler 🟡
- [ ] Şasi FEA analizi (safety factor >3.0 target)
- [ ] Motor cooling analizi
- [ ] Suspension system tasarımı
- [ ] Aerodynamik body panel tasarımı

#### Bloke İşler 🔴 #critical
- [ ] **Carbon fiber şasi malzeme tedariki** - Sponsor onayı bekleniyor
- [ ] Motor delivery - Gümrük süreçleri (Çin'den geliyor)

---

### 📊 Simulasyon Takımı: 60%
```
███████████████████████████████████████████████████████████████░░░░░░░░░░░ 60%
```
**Lead:** Ömer Arda Dündar | **Status:** 🟡 Plan dahilinde

#### Tamamlanan İşler ✅
- [x] ANSYS Mechanical workspace kuruldu  
- [x] SolidWorks CFD temel setup
- [x] Enerji tüketim modeli v1.0 (Excel-based)
- [x] TEKNOFEST pist profili analizi

#### Devam Eden İşler 🟡
- [ ] Şasi structural analysis (von Mises stress)
- [ ] Aerodynamik drag coefficient hesaplamaları
- [ ] Motor efficiency curve modeling
- [ ] Optimal yarış stratejisi (P&G vs constant speed)

---

## 🚨 Kritik Risk'ler & Blocker'lar

### 🔴 Yüksek Öncelik
1. **Şasi malzeme tedariki** - Carbon fiber sponsor onayı gerekli
2. **PCB üretim maliyeti** - Bütçe aşım riski var
3. **Motor delivery** - Gümrük süreçleri uzun sürüyor

### 🟡 Orta Öncelik  
1. **Takım koordinasyonu** - Bazı bağımlılıklar net değil
2. **Test ekipmanı** - Oscilloscope, güç kaynağı gerekli
3. **Dokümantasyon** - Teknik rapor için malzeme toplama

---

## 📊 Bütçe Durumu

| Kategori | Budget | Harcanan | Kalan | Status |
|----------|--------|----------|-------|--------|
| **Elektronik** | ₺15,000 | ₺8,500 | ₺6,500 | 🟡 |
| **Mekanik** | ₺25,000 | ₺5,000 | ₺20,000 | 🟢 |  
| **Yazılım** | ₺2,000 | ₺500 | ₺1,500 | 🟢 |
| **Test & Araçlar** | ₺8,000 | ₺1,200 | ₺6,800 | 🟢 |
| **Lojistik** | ₺5,000 | ₺0 | ₺5,000 | 🟢 |
| **TOPLAM** | **₺55,000** | **₺15,200** | **₺39,800** | **🟡** |

### Sponsor Durumu
- **Aktif Sponsorlar:** 2 firma (₺20,000 confirmed)
- **Görüşülen Firmalar:** 8 firma  
- **Bekleyen Onaylar:** 3 firma (₺35,000 potential)

---

## ⏰ Bu Hafta Kritik Görevler

### Pazartesi-Çarşamba (25-27 Mar)
- [ ] **PCB layout review meeting** - Tüm elektronik takımı #critical
- [ ] **Motor delivery takibi** - Gümrük durum kontrolü
- [ ] **Sponsor meetings** - 2 firma ile görüşme

### Perşembe-Cuma (28-29 Mar)  
- [ ] **CAN protocol specification** - Yazılım takımı
- [ ] **Şasi FEA başlangıç** - Simulasyon takımı
- [ ] **Test prosedürü review** - Cross-team meeting

---

## 📞 Acil İletişim

| Konu | Sorumlu | İletişim |
|------|---------|----------|
| Elektronik acil | Onur Musaoğlu | +90xxx |
| Yazılım acil | Aybüke Pamukçu | +90xxx |
| Mekanik acil | Mahir Cengiz | +90xxx |
| Koordinasyon | Takım Kaptanı | +90xxx |

---

## 🎯 Gelecek Hafta Hedefleri

1. **PCB Gerber dosyalarını finalize et** (Apr 15 deadline)
2. **Hall sensor test protokolünü yaz ve test et**  
3. **Şasi malzeme sponsor onayını al**
4. **Motor delivery date'ini kesinleştir**

---

> **💡 Not:** Bu sayfa her Çarşamba ve Pazar günleri güncellenir.  
> Acil durumlarda Slack/WhatsApp'ten güncel durum istenir.

**[[00-dashboard/Blocker-Takibi|Detaylı Blocker Analizi]] | [[TIMELINE|Proje Timeline'ı]] | [[HOME|Ana Dashboard]]**