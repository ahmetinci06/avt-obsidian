# 📊 Enerji İzleme — esc_energy.c/h
#yazilim #esc #enerji #yarış

> [!tip] Yarış Metriki
> TEKNOFEST Efficiency Challenge sıralaması **Wh/km** bazında. Hedef: **< 110 Wh/km**

## Ölçülen Değerler

| Metrik | Kaynak | Formül |
|--------|--------|--------|
| Gerilim (V) | BMS CAN 0x101 | `pack_voltage` |
| Akım (A) | BMS CAN 0x102 | `pack_current` |
| Güç (W) | Hesaplama | `V × I` |
| Enerji (Wh) | Trapezoidal ∫ | `Σ (P_prev+P_now)/2 × Δt` |
| Rejen (Wh) | P<0 durumu | Ayrı sayaç |
| Hız (km/h) | Hall RPM | `RPM × wheel_circ / gear × 3.6/60` |
| Mesafe (km) | ∫ v dt | Trapezoidal integrasyon |
| Verimlilik | Hesaplama | `E_net / distance_km` |

## Araç Parametreleri (Kalibre Edilecek)

```c
#define ENERGY_WHEEL_CIRCUMFERENCE_M   1.60f   // π × tekerlek çapı
#define ENERGY_MOTOR_POLE_PAIRS        7U      // Motor kutup çifti
#define ENERGY_GEAR_RATIO              1.0f    // Dişli oranı (direct=1.0)
#define ENERGY_UPDATE_INTERVAL_MS      100U    // 10 Hz güncelleme
```

> [!warning] Kalibrasyon
> Tekerlek çevresi ve dişli oranı araç ölçümüne göre ayarlanmalı!

## RPM Hesaplama
```
RPM = 60000 / (hall_period_ms × 6 × pole_pairs)
```
- 6 hall edge per elektriksel devir
- Hall timeout: 500ms hareketsizlik → hız = 0

## CAN Telemetri Frame (0x302)

| Byte | Alan | Birim | Ölçek |
|------|------|-------|-------|
| 0-1 | Verimlilik | Wh/km | ×10 uint16 BE |
| 2-3 | Hız | km/h | ×10 uint16 BE |
| 4-5 | Net enerji | Wh | ×10 uint16 BE |
| 6-7 | Mesafe | km | ×10 uint16 BE |

## Fonksiyonlar

| Fonksiyon | Dönüş | Açıklama |
|-----------|-------|----------|
| `ESC_Energy_Init()` | void | Sayaçları sıfırla |
| `ESC_Energy_Update(tick)` | void | Hesapla (100ms rate limit) |
| `ESC_Energy_GetEfficiency()` | float | Wh/km (min 100m sonra) |
| `ESC_Energy_GetSpeed()` | float | km/h |
| `ESC_Energy_GetDistanceKm()` | float | Toplam km |
| `ESC_Energy_GetNetEnergyWh()` | float | Net Wh |
| `ESC_Energy_Reset(tick)` | void | Yarış başında sıfırla |
| `ESC_Energy_PackCANFrame(data)` | void | 8 byte CAN paketi |

## İlişkili Dosyalar
- [[vcu-can]] — BMS voltage/current kaynağı, 0x302 frame
- [[esc-commutation]] — Hall RPM kaynağı
- [[Enerji-Simulasyon]] — teorik vs gerçek karşılaştırma
- [[Yaris-Strateji]] — P&G stratejisi
