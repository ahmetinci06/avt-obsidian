# Sensör Bağlantıları

#sensörler #adc #i2c #gpio #pinout

[[AKS-Board]] | [[CAN-Bus]] | [[esc-sensors]] | [[Malzeme-Listesi]]

## Genel Bakış

Tüm sensör bağlantılarının detaylı pinout, kablolaj ve test prosedürleri. STM32G491RET MCU üzerinden 5 ana sensör grubu kontrol edilir.

> [!important] Sensör Güvenliği
> Tüm analog sensörlerde filtering ve protection circuitry bulunur.

## 1. Throttle (Gaz Pedalı) Sensörü

### Potansiyometre Tipi
- **Model:** 5kΩ Linear Potentiometer
- **Supply:** 3.3V
- **Range:** 0-3.3V
- **Resolution:** 12-bit (4096 steps)

### Bağlantı Şeması
```
Throttle Pedal Assembly:
┌─────────────┐    ┌──────────────┐    ┌─────────────┐
│   POT       │    │   Filtering  │    │ STM32G491   │
│             │    │   Circuit    │    │             │
│         ──┤├──J1─│─R17(220Ω)────│────│─PC0/ADC1_IN6│
│       ╱      │   │              │    │             │
│   ┌──╱       │   │─C4(100nF)────│────│─GND         │
│   │ VCC(3V3) │   │   ║          │    │             │
│   └──────────│───│───╫──────────│────│─GND         │
└─────────────┘    └──────────────┘    └─────────────┘
```

### Pin Detayları
| Pin | Sinyal | MCU Pin | Renk | Kesit | Fonksiyon |
|-----|--------|---------|------|-------|-----------|
| 1 | POT_VCC | 3V3 | Kırmızı | 0.5mm² | Güç besleme |
| 2 | POT_WIPER | PC0 | Yeşil | 0.5mm² | ADC girişi |
| 3 | POT_GND | GND | Siyah | 0.5mm² | Ground |

### Filtering Circuit
- **R17:** 220Ω (EMI protection)
- **C4:** 100nF (HF noise filtering)
- **Cutoff frequency:** fc = 1/(2πRC) ≈ 7.2kHz

### Konnektör
- **Tip:** JST-XH 3-pin
- **Housing:** XHP-3
- **Pins:** SXH-001T-P0.6

## 2. Akım Sensörü (Current Sensing)

### Shunt Resistor Yöntemi
- **Shunt:** 1mΩ ±1% precision
- **Max Current:** ±100A
- **Max Voltage:** 100mV
- **OpAmp:** TLV9064 (Rail-to-Rail)

### Detay Circuit
```
Current Path:
[BATTERY+] ──┬── [Rshunt=1mΩ] ──┬── [MOTOR+]
             │                  │
           GND_ISO          ┌─────┴───── Current Sense
                           │
                           │  ┌─R1─┐ 3.3kΩ
                     ┌─────┤  │    │
                     │     │  └─R2─┘ 3.3kΩ  
                     │     │    │
                ┌────▼────┤     └── GND
                │ TLV9064 │
                │ Ch2 (+) │───────R5(100Ω)───C1(100nF)───PC1/ADC1_IN7
                │     (-) │────── Mid-Point Reference
                └─────────┘
```

### Bağlantı Detayları
| Nokta | MCU Pin | Değer | Açıklama |
|-------|---------|-------|----------|
| Shunt High | - | 1mΩ | Yüksek taraf |
| Shunt Low | - | 1mΩ | Düşük taraf |
| Diff Amp Out | PC1 | 0-3.3V | Kalibre edilmiş |
| OpAmp Supply | 3V3 | - | Clean power |

### Kalibrasyon
```
I_actual(A) = (ADC_raw - 2048) * (100A / 2048)
Offset = 2048 (0A current)
Scale = 0.0488A/bit
```

## 3. Fren Sensörü (Brake Switch)

### Mikroswitch Tipi
- **Model:** OMRON SS-5GL
- **Rating:** 5A @ 125VAC
- **Contact:** SPST-NO
- **Activation:** 1.5N force

### Bağlantı
```
Brake Pedal:
┌─────────────┐    ┌──────────────┐    ┌─────────────┐
│  Brake      │    │  Debounce    │    │ STM32G491   │
│  Switch     │    │  Circuit     │    │             │
│             │    │              │    │             │
│    ┌─────SW1┼────│──3V3         │    │             │
│    │        │    │              │    │             │
│    └────────│────│──┐           │    │             │
└─────────────┘    │  │R16(4.7kΩ) │    │             │
                   │  └───────────│────│─PC5/GPIO    │
                   │              │    │             │
                   │──C15(100nF)──│────│─GND         │
                   └──────────────┘    └─────────────┘
```

### Component Values
- **R16:** 4.7kΩ pull-down
- **C15:** 100nF debounce capacitor
- **Debounce time:** τ = RC ≈ 470μs

### Pin Configuration
```c
// GPIO Config
PC5 - GPIO_Input
GPIO_PullDown = DISABLE  // External pull-down
GPIO_Speed = GPIO_SPEED_FREQ_LOW
```

## 4. Hall Sensörler (Motor Position)

### 3-Phase Hall Array
- **Sensors:** 3x Digital Hall (A3144)
- **Supply:** 5V (tolerant to 3.3V)
- **Output:** Open-collector
- **Pull-up:** 4.7kΩ each

### Mounting Position
- 120° electrical spacing
- Aligned with stator poles
- Secure mounting (vibration resistant)

### Bağlantı Matrisi
| Hall Sensor | MCU Pin | Pull-up | Renk | Konnektör Pin |
|-------------|---------|---------|------|---------------|
| Hall A | PB0/TIM3_CH3 | R10: 4.7kΩ | Mavi | 1 |
| Hall B | PB1/TIM3_CH4 | R11: 4.7kΩ | Beyaz | 2 |
| Hall C | PB2/GPIO | R12: 4.7kΩ | Gri | 3 |
| VCC | 5V | - | Kırmızı | 4 |
| GND | GND | - | Siyah | 5 |

### Hall State Decoding
| A | B | C | Sector | Next Commutation |
|---|---|---|--------|------------------|
| 0 | 0 | 1 | 1 | B+, C- |
| 0 | 1 | 1 | 2 | B+, A- |
| 0 | 1 | 0 | 3 | C+, A- |
| 1 | 1 | 0 | 4 | C+, B- |
| 1 | 0 | 0 | 5 | A+, B- |
| 1 | 0 | 1 | 6 | A+, C- |

### Konnektör: JST-XH 5-pin
```
Pin 1: Hall_A (Mavi)
Pin 2: Hall_B (Beyaz)  
Pin 3: Hall_C (Gri)
Pin 4: VCC_5V (Kırmızı)
Pin 5: GND (Siyah)
```

## 5. Sıcaklık Sensörü (TMP102)

### I2C Temperature Sensor
- **IC:** TMP102AIDRLR
- **Interface:** I2C (100kHz/400kHz)
- **Resolution:** 12-bit (0.0625°C)
- **Range:** -55°C to +150°C
- **Address:** 0x48 (A0=GND)

### I2C Bağlantı
```
TMP102 Sensor:
┌─────────────┐    ┌──────────────┐    ┌─────────────┐
│   TMP102    │    │   Pull-up    │    │ STM32G491   │
│             │    │   Resistors  │    │             │
│         SDA │────│──R14(4.7kΩ)──│────│─PB7/I2C1_SDA│
│         SCL │────│──R15(4.7kΩ)──│────│─PB6/I2C1_SCL│
│         A0  │────│──GND         │    │             │
│         VCC │────│──3V3         │    │─3V3         │
│         GND │────│──GND─────────│────│─GND         │
└─────────────┘    └──────────────┘    └─────────────┘
```

### Pin Mapping
| TMP102 Pin | MCU Pin | Değer | Açıklama |
|------------|---------|-------|----------|
| SDA | PB7 | - | I2C Data |
| SCL | PB6 | - | I2C Clock |
| VCC | 3V3 | - | Power |
| GND | GND | - | Ground |
| A0 | GND | - | Address select |
| ALERT | NC | - | Not connected |

### I2C Configuration
```c
hi2c1.Instance = I2C1;
hi2c1.Init.Timing = 0x20303E5D;        // 100kHz
hi2c1.Init.OwnAddress1 = 0;
hi2c1.Init.AddressingMode = I2C_ADDRESSINGMODE_7BIT;
hi2c1.Init.DualAddressMode = I2C_DUALADDRESS_DISABLE;
hi2c1.Init.GeneralCallMode = I2C_GENERALCALL_DISABLE;
hi2c1.Init.NoStretchMode = I2C_NOSTRETCH_DISABLE;
```

### Konnektör: JST-XH 4-pin
```
Pin 1: VCC_3V3 (Kırmızı)
Pin 2: GND (Siyah)
Pin 3: SDA (Yeşil)
Pin 4: SCL (Sarı)
```

## Test ve Doğrulama

### Pre-Assembly Test Checklist

#### Throttle Test
- [ ] Potansiometre direnci ölçüldü (5kΩ ±5%)
- [ ] Wiper hareketi smooth (no dead zones)
- [ ] Konnektör pin continuity test OK
- [ ] Filtering circuit values doğrulandı

#### Current Sensor Test  
- [ ] Shunt direnci ölçüldü (1.00mΩ ±1%)
- [ ] OpAmp supply voltage (3.3V)
- [ ] Offset voltage ölçüldü (1.65V ±50mV)
- [ ] Gain calculation verified

#### Brake Switch Test
- [ ] Switch activation force test (1.5N)
- [ ] Contact resistance (<100mΩ)
- [ ] Debounce circuit test
- [ ] Pull-down resistor value confirmed

#### Hall Sensors Test
- [ ] Supply voltage 5V confirmed
- [ ] Pull-up resistors installed (4.7kΩ)
- [ ] Magnet trigger test (each sensor)
- [ ] Output logic levels verified

#### Temperature Sensor Test
- [ ] I2C address response (0x48)
- [ ] Room temperature reading (20-25°C)
- [ ] Pull-up resistor values confirmed
- [ ] Communication integrity test

### Assembly Test Checklist

#### Power-On Test
- [ ] All supply voltages measured
- [ ] No short circuits detected
- [ ] Power consumption within limits
- [ ] LED indicators working

#### Functional Test
- [ ] ADC readings stable
- [ ] I2C communication working
- [ ] GPIO states correct
- [ ] CAN bus communication OK

#### Calibration Test
- [ ] Throttle full range mapped (0-100%)
- [ ] Current sensor zero-point set
- [ ] Temperature reading calibrated
- [ ] Hall sequence verified

## Troubleshooting

### Common Issues

| Sorun | Olası Sebep | Çözüm |
|-------|-------------|-------|
| ADC noisy | Poor filtering | Check C4, C1 values |
| I2C not responding | Pull-up missing | Verify R14, R15 |
| Hall unstable | Weak magnet | Check sensor alignment |
| Current reading drift | Temperature effect | Add compensation |
| Brake bouncing | No debounce | Verify C15 value |

### Test Equipment Required
- Digital multimeter (4.5-digit precision)
- Oscilloscope (≥100MHz, 4-channel)
- Function generator (current injection)
- I2C analyzer/logic analyzer
- Magnet field tester

### Expected Values

| Parametre | Minimum | Typical | Maximum | Unit |
|-----------|---------|---------|---------|------|
| Throttle voltage | 0.1 | 1.65 | 3.2 | V |
| Current offset | 1.6 | 1.65 | 1.7 | V |
| Temperature accuracy | -2 | 0 | +2 | °C |
| Hall trigger level | 2.0 | - | 3.3 | V |
| I2C clock rate | 90 | 100 | 110 | kHz |

## İlgili Dokümanlar
- [[AKS-Board]] - Schematic ve PCB layout
- [[esc-sensors]] - Yazılım ADC/I2C implementasyonu  
- [[Test-Proseduru]] - Sistemlik test prosedürleri
- [[Malzeme-Listesi]] - Component specifications
- [[Troubleshooting]] - Advanced debugging guide

## Referanslar
- STM32G491RET6 Datasheet
- TLV9064 OpAmp Datasheet  
- TMP102 Digital Temperature Sensor
- A3144 Hall Effect Sensor
- JST-XH Connector Series