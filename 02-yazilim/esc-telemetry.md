# 📡 Telemetri — esc_telemetry.c/h
#yazilim #esc #telemetri

> [!info] UART CSV + SD Kart (stub)
> Tüm araç verilerini 5 Hz'de loglar. Yarış sırası ve sonrası analiz için.

## UART CSV Format (18 Alan)

| # | Sütun | Birim | Açıklama |
|---|-------|-------|----------|
| 1 | tick | ms | Zaman damgası |
| 2 | state | enum | 0=OFF,1=READY,2=DRIVE,3=FAULT |
| 3 | pedal | % | Gaz pozisyonu |
| 4 | brake | 0/1 | Fren durumu |
| 5 | duty | 0-9999 | Motor PWM |
| 6 | mA | A | Motor akımı (filtrelenmiş) |
| 7 | mT | °C | Motor/ESC sıcaklığı |
| 8 | bV | V | Batarya gerilimi |
| 9 | bA | A | Batarya akımı |
| 10 | soc | % | Şarj durumu |
| 11 | W | W | Anlık güç |
| 12 | Wh | Wh | Toplam tüketim |
| 13 | rWh | Wh | Rejen enerji |
| 14 | kmh | km/h | Hız |
| 15 | km | km | Mesafe |
| 16 | whkm | Wh/km | Verimlilik |
| 17 | hall | 1-6 | Hall sensör kodu |
| 18 | fault | hex | Hata bayrakları |

**UART config:** USART2, 115200 baud, 8N1, ST-LINK VCP veya RF modülü

## Güncelleme Hızı
- **TELEMETRY_INTERVAL_MS = 200** → 5 Hz
- Non-blocking TX (busy ise frame drop — kabul edilebilir)

## SD Kart (Planlanıyor)
- [ ] CubeMX'te SPI2 veya SDMMC1 ekle
- [ ] FatFS middleware ekle
- [ ] `WriteSD()` implement et
- [ ] Circular buffer: 18000 frame = 60 dakika

## Dashboard Bağlantısı
- **Kablolu:** USB-UART → laptop → Python serial parse
- **Kablosuz:** HC-12 veya LoRa → alıcı → dashboard GUI
- **CAN:** 0x302 frame da enerji verisi gönderir

## İlişkili Dosyalar
- [[esc-energy]] — enerji verileri kaynağı
- [[esc-sensors]] — motor akım/sıcaklık
- [[vcu-can]] — CAN telemetri
- [[Test-Proseduru]] — UART test adımları
