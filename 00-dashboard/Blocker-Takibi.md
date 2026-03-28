# 🚧 AVT Racing Team - Aktif Blocker Takibi

> **Son Güncelleme:** 2026-03-28 20:40 UTC  
> **Total Blocker'lar:** 6 aktif, 2 çözülmüş

---

## 🔴 Kritik Blocker'lar (Proje durdurucu)

### #BLOCK-001: Şasi Malzeme Tedariki 
**🏷️ Tags:** #critical #mekanik #sponsor  
**📅 Başlangıç:** 2026-03-20  
**👤 Owner:** Mahir Cengiz  
**📍 Takım:** Mekanik

**❌ Problem:**
Carbon fiber şasi malzemesi için sponsor onayı alınamadı. 3 farklı firmadan teklif aldık ama bütçe aşımı var:
- **Firma A:** ₺18,000 (carbon fiber prepreg)
- **Firma B:** ₺22,000 (carbon fiber tube)  
- **Firma C:** ₺15,000 (aluminum alternatifi)

**🎯 Impact:**
- Şasi üretimi 2 hafta gecikebilir
- Timeline'da 15 Nisan milestone'ı risk altında
- Genel proje progressi %15 düşebilir

**🔧 Actions Taken:**
- [x] 3 firma ile müzakere edildi
- [x] Aluminum alternatifi araştırıldı  
- [x] Sponsor meeting schedule edildi
- [ ] Hibrit çözüm analizi (partial carbon)

**⏭️ Next Steps:**
1. **29 Mar:** Sponsor meeting (Ceren koordine ediyor)
2. **1 Apr:** Final karar verilmeli
3. **5 Apr:** Sipariş verilmeli

**🆘 Escalation:** Takım kaptanı devrede

---

### #BLOCK-002: PCB Üretim Maliyeti 
**🏷️ Tags:** #critical #elektronik #budget  
**📅 Başlangıç:** 2026-03-25  
**👤 Owner:** Onur Musaoğlu  
**📍 Takım:** Elektronik

**❌ Problem:**
AKS PCB üretim maliyeti beklenenden yüksek çıktı:
- **Hedef bütçe:** ₺2,500 (10 adet)
- **Gelen teklifler:** ₺4,200-₺5,800
- **Layer count:** 4-layer (complexity arttırıyor)

**🎯 Impact:**
- Elektronik bütçesinde ₺2,000-₺3,000 aşım
- Prototype sayısı düşmek zorunda (10→6 adet)
- Test kapasitesi kısıtlanır

**🔧 Actions Taken:**
- [x] 3 üreticiden teklif alındı
- [x] 2-layer alternatifi analiz edildi
- [ ] Via optimization için PCB review
- [ ] Local üretici araştırması

**⏭️ Next Steps:**
1. **30 Mar:** PCB redesign feasibility check
2. **3 Apr:** Final üretici seçimi
3. **15 Apr:** Gerber teslim deadline

---

## 🟡 Orta Öncelik Blocker'lar

### #BLOCK-003: Motor Import/Gümrük
**🏷️ Tags:** #medium #mekanik #logistics  
**📅 Başlangıç:** 2026-03-22  
**👤 Owner:** Mahir Cengiz  

**❌ Problem:**
Çin'den sipariş edilen BLDC motor gümrük süreçlerinde bekliyor. HS kodu belirsizliği ve ek belgeler isteniyor.

**🔧 Current Status:**
- [x] Gümrük müşaviri ile görüşüldü
- [ ] Technical specification translation
- [ ] CE certificate request to supplier

**⏭️ Estimated Resolution:** 10 gün

---

### #BLOCK-004: CAN Protocol Standardizasyonu
**🏷️ Tags:** #medium #yazilim #elektronik  
**📅 Başlangıç:** 2026-03-27  
**👤 Owner:** Aybüke Pamukçu

**❌ Problem:**
BMS ve motor controller'lar farklı CAN message formatları kullanıyor. Standardizasyon gerekli.

**🔧 Current Status:**
- [x] BMS protokolü dokümantasyonu alındı
- [x] VESC protokolü araştırıldı
- [ ] Custom message mapping table oluşturulacak

**⏭️ Estimated Resolution:** 5 gün

---

### #BLOCK-005: Test Ekipmanı Eksikliği
**🏷️ Tags:** #medium #testing #all-teams  
**📅 Başlangıç:** 2026-03-26  
**👤 Owner:** Ali Efe Kaya

**❌ Problem:**
Hall sensor ve current sense test için oscilloscope ve güç kaynağı gerekli. Lab'da yeterli ekipman yok.

**🔧 Actions:**
- [ ] Üniversite lab rezervasyonu
- [ ] Ekipman kiralama fiyat araştırması  
- [ ] Alumni network'ten borç alma

---

## 🟢 Düşük Öncelik / İzlenen

### #BLOCK-006: GitHub Access Rights
**🏷️ Tags:** #low #admin #yazilim  
**📅 Başlangıç:** 2026-03-28  
**👤 Owner:** Ata Doğan

**❌ Problem:**
Yeni ekip üyelerinin GitHub repo'ya push access'i yok.

**🔧 Status:** Admin ile iletişim kuruldu, 2 gün içinde çözülecek.

---

## ✅ Çözülen Blocker'lar (Son 1 hafta)

### #BLOCK-SOLVED-001: STM32 License
**📅 Çözüm Tarihi:** 2026-03-26  
**Problem:** STM32CubeIDE license sorunu  
**Çözüm:** Educational license alındı, tüm takım erişim sahibi

### #BLOCK-SOLVED-002: SolidWorks Installation  
**📅 Çözüm Tarihi:** 2026-03-25  
**Problem:** CAD workstation setup  
**Çözüm:** IT support ile çözüldü, tüm mekanik takımı kullanabilir

---

## 📊 Blocker Analytics

### Takım Bazlı Dağılım
- **Elektronik:** 2 aktif blocker
- **Yazılım:** 1 aktif blocker  
- **Mekanik:** 2 aktif blocker
- **Cross-team:** 1 aktif blocker

### Kategori Bazlı
- **Budget/Sponsor:** 2 blocker 🔴
- **Technical:** 2 blocker 🟡  
- **Process/Admin:** 2 blocker 🟢

### Çözüm Süresi (Ortalama)
- **Kritik blocker'lar:** 8.5 gün
- **Orta öncelik:** 4.2 gün
- **Düşük öncelik:** 1.8 gün

---

## 🔔 Blocker Escalation Matrisi

| Süre | Aksiyon |
|------|---------|
| **3 gün** | Owner daily update vermeli |
| **1 hafta** | Lead'e escalate et |  
| **2 hafta** | Takım kaptanına escalate et |
| **3 hafta** | Alternative solution implement et |

---

## 📝 Yeni Blocker Rapor Etme

**Template:**
```markdown
### #BLOCK-XXX: [Başlık]
**🏷️ Tags:** #priority #team #category
**📅 Başlangıç:** YYYY-MM-DD
**👤 Owner:** [İsim]
**📍 Takım:** [Takım]

**❌ Problem:** [Detaylı açıklama]

**🎯 Impact:** [Proje üzerindeki etkisi]

**🔧 Actions Taken:**
- [ ] [Action 1]
- [ ] [Action 2]

**⏭️ Next Steps:**
1. [Step 1] - [Date]
2. [Step 2] - [Date]
```

---

**🔗 İlgili Sayfalar:**  
[[00-dashboard/Proje-Durumu|Genel Proje Durumu]] | [[TIMELINE|Project Timeline]] | [[HOME|Ana Dashboard]]

> **💡 Not:** Bu sayfa her 2 günde bir güncellenir. Yeni blocker tespit ettiğinizde hemen ekleyin.  
> **📱 Acil durumlarda:** WhatsApp grubu "AVT Blocker Alert" kullanın.