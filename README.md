# AVT Racing Team - TEKNOFEST 2026 Elektromobil Projesi

> **"Her cıvata, her kablo, her kod satırı — hiçbir şey gözden kaçmayacak."**

## 🚗 Proje Özeti

Bu Obsidian vault'u, AVT Racing Team'in TEKNOFEST Efficiency Challenge 2026 Elektromobil kategorisi için geliştirdiği elektrikli aracın **tek kaynak gerçeği** (single source of truth) dokümantasyonudur.

**Hedef:** <110 Wh/km enerji verimliliği ile yarışmayı kazanmak.

## 📋 Nasıl Kullanılır

### 1. Başlangıç Noktası
- **[[HOME]]** - Ana dashboard, her şeyin linklerini içerir
- **[[TIMELINE]]** - Tüm deadlineları ve milestone'ları gösterir
- **[[00-dashboard/Proje-Durumu]]** - Anlık proje durumu ve progress barları

### 2. Navigasyon
- `Ctrl+O` (Quick Switcher) ile herhangi bir dosyayı hızlıca aç
- `[[wiki-link]]` formatındaki linkler ile dosyalar arası gezin
- Sol paneldeki klasör ağacını kullan

### 3. Tag Sistemi
- `#todo` - Yapılması gerekenler
- `#done` - Tamamlanan işler
- `#blocked` - Bloke olan işler
- `#critical` - Kritik/acil işler
- `#elektronik` / `#yazilim` / `#mekanik` - Takım bazlı filtreler

### 4. Checkbox Sistemi
- `- [ ]` Her aksiyon item için checkbox kullan
- `- [x]` Tamamlanan işleri işaretle
- Plugin: Checklist ile toplu checkbox yönetimi

## 🏗️ Vault Yapısı

```
├── 00-dashboard/         # Proje durumu ve toplantılar
├── 01-elektronik/        # PCB, güç katı, sensörler, BOM
├── 02-yazilim/          # STM32 kodu, CubeMX, test prosedürleri
├── 03-mekanik/          # Şasi, motor, frenler, aerodinamik
├── 04-simulasyon/       # FEA, CFD, enerji modelleri
├── 05-yaris-hazirlik/   # Kurallar, güvenlik, lojistik
├── 06-takim/            # Ekip üyeleri, görev matrisi
├── 07-sponsorluk/       # Sponsorluk materyalleri
└── _templates/          # Meeting ve task şablonları
```

## ⚡ Hızlı Başlangıç

1. **[[HOME]]** sayfasını aç
2. **[[00-dashboard/Proje-Durumu]]** ile mevcut durumu kontrol et
3. Kendi takımının klasörüne git:
   - **Elektronik:** [[01-elektronik/AKS-Board]]
   - **Yazılım:** [[02-yazilim/CubeMX-Config]]
   - **Mekanik:** [[03-mekanik/Sasi]]
4. Görevlerini bul ve checkbox'ları işaretle

## 🔗 Harici Linkler

- **GitHub Repo:** https://github.com/avt-racing/elektriskli-arac-2026
- **WhatsApp Grubu:** AVT Elektromobil 2026
- **Drive Klasörü:** AVT Racing - TEKNOFEST 2026 Shared

## 📞 İletişim

- **Takım Kaptanı:** [Kaptanın ismi]
- **Elektronik Lead:** Onur Musaoğlu
- **Yazılım Lead:** Aybüke Pamukçu
- **Mekanik Lead:** Mahir Cengiz

---

**⚠️ ÖNEMLİ:** Bu vault'ta her türlü teknik detay, BOM bilgisi, kod snippet'i ve prosedür yer alıyor. Güvenlik nedeniyle özel bilgileri (API key'ler, şifreler) burada saklamayın.

**💡 İPUCU:** Obsidian'ın Graph View'ını kullanarak dosyalar arası bağlantıları görselleştirin.