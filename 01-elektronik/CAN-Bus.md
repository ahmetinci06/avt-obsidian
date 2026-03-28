# CAN Bus Haberleşme Sistemi

#can-bus #haberleşme #fdcan #stm32

[[AKS-Board]] | [[vcu-can]] | [[Sensor-Baglantilari]]

## Genel Bakış

FDCAN1 kullanarak 500 kbit/s CAN 2.0B protokolü ile araç içi haberleşme sağlanır. VCU (Vehicle Control Unit) ve BMS (Battery Management System) arasında kritik veri transferi gerçekleşir.

> [!important] Kritik Haberleşme
> CAN bus arızası durumunda araç güvenlik moduna geçer ve motor durdurulur.

## Pin Konfigürasyonu

| Pin | Fonksiyon | AF | Özellik |
|-----|-----------|-----|---------|
| PA11 | FDCAN1_RX | AF9 | CAN alıcı |
| PA12 | FDCAN1_TX | AF9 | CAN verici |

```c
// CubeMX GPIO Config
PA11 - FDCAN1_RX
PA12 - FDCAN1_TX
```

## CAN Mesaj Haritası

### BMS Mesajları (0x100-0x104)

#### 0x100 - BMS Durum
| Byte | Bit 7-0 | Açıklama |
|------|---------|----------|
| 0 | State | 0=OFF, 1=STANDBY, 2=READY, 3=CHARGE, 4=FAULT |
| 1 | Fault_Code_Low | Hata kodu düşük byte |
| 2 | Fault_Code_High | Hata kodu yüksek byte |
| 3 | Temp_High | En yüksek hücre sıcaklığı (°C + 40) |
| 4 | Balancing_Status | Dengeleme durumu bitmap |
| 5 | Reserved | - |
| 6 | Reserved | - |
| 7 | Checksum | XOR checksum |

#### 0x101 - BMS Gerilim
| Byte | Bit 7-0 | Açıklama |
|------|---------|----------|
| 0-1 | Pack_Voltage | Toplam gerilim (0.1V çözünürlük) |
| 2-3 | Cell_Max | En yüksek hücre gerilimi (mV) |
| 4-5 | Cell_Min | En düşük hücre gerilimi (mV) |
| 6-7 | Cell_Diff | Hücre farkı (mV) |

#### 0x102 - BMS Akım
| Byte | Bit 7-0 | Açıklama |
|------|---------|----------|
| 0-3 | Current | Akım (mA, signed) |
| 4-7 | Energy_Used | Kullanılan enerji (Wh) |

#### 0x103 - BMS Sıcaklık
| Byte | Bit 7-0 | Açıklama |
|------|---------|----------|
| 0 | Temp_1 | Sensör 1 (°C + 40) |
| 1 | Temp_2 | Sensör 2 (°C + 40) |
| 2 | Temp_3 | Sensör 3 (°C + 40) |
| 3 | Temp_4 | Sensör 4 (°C + 40) |
| 4 | Temp_Avg | Ortalama sıcaklık (°C + 40) |
| 5-7 | Reserved | - |

#### 0x104 - BMS SOC
| Byte | Bit 7-0 | Açıklama |
|------|---------|----------|
| 0 | SOC | Şarj durumu (%) |
| 1 | SOH | Batarya sağlığı (%) |
| 2-3 | Remaining_Capacity | Kalan kapasite (Wh) |
| 4-5 | Full_Capacity | Tam kapasite (Wh) |
| 6-7 | Time_To_Empty | Boşalma süresi (dakika) |

### VCU Mesajları (0x300-0x302)

#### 0x300 - VCU Durum
| Byte | Bit 7-0 | Açıklama |
|------|---------|----------|
| 0 | State | 0=OFF, 1=READY, 2=DRIVE, 3=FAULT |
| 1 | Error_Code | Hata kodu |
| 2 | Drive_Mode | Sürüş modu |
| 3 | Throttle_Raw | Ham gaz pedalı (0-255) |
| 4 | Brake_Status | Fren durumu (0/1) |
| 5 | Temperature | VCU sıcaklığı (°C + 40) |
| 6-7 | Reserved | - |

#### 0x301 - VCU Pedal
| Byte | Bit 7-0 | Açıklama |
|------|---------|----------|
| 0-1 | Throttle_ADC | Ham ADC değeri |
| 2-3 | Throttle_Filtered | Filtreli değer |
| 4-5 | Throttle_Percent | Yüzde (x100) |
| 6 | Pedal_Fault | Hata durumu |
| 7 | Reserved | - |

#### 0x302 - VCU Enerji
| Byte | Bit 7-0 | Açıklama |
|------|---------|----------|
| 0-3 | Distance_m | Mesafe (metre) |
| 4-5 | Speed_kmh | Hız (km/h x10) |
| 6-7 | Efficiency_WhKm | Verimlilik (Wh/km) |

## CAN Transceiver

### SN65HVD230
- 3.3V supply
- 1 Mbps max
- Standby mod desteği
- ESD koruması

```
Pin Bağlantısı:
1 (D)    -> PA12 (FDCAN1_TX)
2 (GND)  -> GND
3 (VCC)  -> 3V3
4 (R)    -> PA11 (FDCAN1_RX)
5 (VREF) -> NC
6 (CANL) -> CAN_L
7 (CANH) -> CAN_H
8 (RS)   -> GND (Normal mod)
```

## Bus Terminasyon

> [!warning] Terminasyon Zorunlu
> Her CAN segmentinin her iki ucunda 120Ω terminasyon direnci bulunmalı.

### Terminasyon Şeması
```
VCU Side:           BMS Side:
CANH ----[120Ω]---- CANL
  |                   |
[CAN_H] ----------- [CAN_H]
[CAN_L] ----------- [CAN_L]
  |                   |
CANH ----[120Ω]---- CANL
```

## Kablolaj

| Sinyal | Renk | Kesit | Uzunluk |
|--------|------|-------|---------|
| CAN_H | Turuncu/Beyaz | 0.5mm² | Max 2m |
| CAN_L | Turuncu | 0.5mm² | Max 2m |
| GND | Siyah | 1.0mm² | - |

### Connector: JST-XH 4-pin
```
Pin 1: CAN_H
Pin 2: CAN_L  
Pin 3: GND
Pin 4: NC
```

## Yazılım Konfigürasyonu

### FDCAN Init
```c
hfdcan1.Instance = FDCAN1;
hfdcan1.Init.FrameFormat = FDCAN_FRAME_CLASSIC;
hfdcan1.Init.Mode = FDCAN_MODE_NORMAL;
hfdcan1.Init.AutoRetransmission = ENABLE;
hfdcan1.Init.TransmitPause = DISABLE;
hfdcan1.Init.ProtocolException = DISABLE;
hfdcan1.Init.NominalPrescaler = 8;
hfdcan1.Init.NominalSyncJumpWidth = 1;
hfdcan1.Init.NominalTimeSeg1 = 13;
hfdcan1.Init.NominalTimeSeg2 = 2;
```

### Message Filter Setup
```c
// BMS mesajları için filter
FDCAN_FilterTypeDef filter1;
filter1.IdType = FDCAN_STANDARD_ID;
filter1.FilterIndex = 0;
filter1.FilterType = FDCAN_FILTER_RANGE;
filter1.FilterConfig = FDCAN_FILTER_TO_RXFIFO0;
filter1.FilterID1 = 0x100;  // BMS_STATUS
filter1.FilterID2 = 0x104;  // BMS_SOC
HAL_FDCAN_ConfigFilter(&hfdcan1, &filter1);
```

## Test ve Doğrulama

### CAN Bus Test Checklist

- [ ] Hardware bağlantıları kontrol edildi
- [ ] Terminasyon dirençleri (120Ω) monte edildi
- [ ] CAN_H ve CAN_L sinyalleri ölçüldü (diferansiyel ~2V)
- [ ] VCU-BMS haberleşmesi test edildi
- [ ] Mesaj periyotları doğrulandı (10Hz)
- [ ] Error frame yönetimi test edildi
- [ ] Bus-off recovery test edildi
- [ ] EMI/EMC ön test yapıldı

### Debug Araçları
- CAN analyzer (CANoe/CANalyzer)
- Oscilloscope (CAN sinyalleri)
- Multimeter (terminasyon kontrolü)

### Tipik Sorunlar ve Çözümler

| Sorun | Sebep | Çözüm |
|-------|-------|-------|
| Bus-off | Terminasyon eksik | 120Ω direnç ekle |
| Corrupt frames | EMI | Twisted-pair kablo kullan |
| No communication | TX/RX ters | Pin bağlantılarını kontrol et |
| Random errors | Ground loop | Ortak ground sağla |

## Performans Parametreleri

| Parametre | Değer | Not |
|-----------|-------|-----|
| Bit rate | 500 kbit/s | FDCAN1 nominal |
| Bus utilization | <30% | Güvenli margin |
| Message latency | <2ms | Kritik mesajlar |
| Error rate | <0.1% | Kabul edilebilir |

> [!tip] Optimizasyon
> Yüksek öncelikli mesajlar için düşük ID değerleri kullanın (arbitration advantage).

## İlgili Dokümanlar
- [[vcu-can]] - VCU CAN yazılım implementasyonu
- [[AKS-Board]] - PCB şematik detayları  
- [[Test-Proseduru]] - CAN test prosedürleri
- [[Troubleshooting]] - Sorun giderme rehberi

## Referanslar
- STM32G4 FDCAN Reference Manual
- CAN 2.0B Specification
- SN65HVD230 Datasheet
- TEKNOFEST Efficiency Challenge Rules v2.1