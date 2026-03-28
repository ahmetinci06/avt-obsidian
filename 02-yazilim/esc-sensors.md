# ⚡ ESC Sensörler — esc_sensors.c/h
#yazilim #esc #adc #sensör

> [!info] Modül
> ADC DMA ile throttle + akım ölçümü, I2C ile TMP102 sıcaklık.

## ADC DMA Konfigürasyonu

| Rank | Kanal | Pin | Sinyal | Açıklama |
|------|-------|-----|--------|----------|
| 1 | IN6 | PC0 | Throttle | J1 konektör → R17(220Ω) → C4(100nF) → PC0 |
| 2 | IN7 | PC1 | Current | Rshunt(1mΩ) → TLV9064 ch2 → R5(100Ω) → C1(100nF) → PC1 |

- **Mod:** Scan, Continuous, DMA Circular, Software Trigger
- **Çözünürlük:** 12-bit (0-4095)
- **Referans:** 3.3V (VDDA)
- **Buffer:** `adc_buffer[2]` — DMA sürekli günceller

## Fonksiyonlar

| Fonksiyon | Dönüş | Açıklama |
|-----------|-------|----------|
| `ESC_Sensors_Init()` | void | ADC DMA başlat |
| `ESC_Sensors_Update()` | void | Buffer'dan oku + TMP102 oku |
| `ESC_Sensors_GetThrottleRaw()` | uint16_t | Gaz ADC (0-4095) |
| `ESC_Sensors_GetCurrentRaw()` | uint16_t | Akım ADC (0-4095) |
| `ESC_Sensors_GetTemperatureC()` | float | TMP102 sıcaklık (°C) |

## TMP102 Sıcaklık Sensörü

- **I2C adresi:** 0x48 (ADD0=GND), HAL 8-bit: 0x90
- **Register:** 0x00 (temperature)
- **Format:** 12-bit, MSB first, 0.0625°C/LSB
- **Pull-up:** R14=4.7kΩ (SDA), R15=4.7kΩ (SCL)
- **Bypass:** C14=100nF

## Filtre Kesim Frekansları

| Sinyal | R | C | f_cutoff |
|--------|---|---|----------|
| Throttle | R17=220Ω | C4=100nF | **7.2 kHz** |
| Current | R5=100Ω | C1=100nF | **15.9 kHz** |

## Kalibrasyon Prosedürü
- [ ] Motor kapalı → current_raw offset not et (sıfır akım değeri)
- [ ] Bilinen yük ile current_raw → amper dönüşüm katsayısı hesapla
- [ ] Throttle idle (serbest) → raw değer not et (~250)
- [ ] Throttle full (tam basılı) → raw değer not et (~3600)
- [ ] TMP102 oda sıcaklığı ile karşılaştır (±2°C tolerans)

## İlişkili Dosyalar
- [[CubeMX-Config]] — ADC1 + I2C1 peripheral ayarları
- [[esc-motor-control]] — throttle raw → duty mapping
- [[esc-safety]] — current threshold kontrolü
- [[AKS-Board]] — donanım şeması
