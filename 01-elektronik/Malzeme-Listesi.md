# Malzeme Listesi (BOM)

#bom #malzeme #tedarik #maliyet #stok

[[AKS-Board]] | [[PCB-Uretim]] | [[Sensor-Baglantilari]] | [[CAN-Bus]]

## Genel Bakış

AVT Racing elektrikli araç projesi için tam malzeme listesi. AKS PCB, güç katı, sensörler ve kablolaj için tüm bileşenler fiyat ve tedarik bilgileriyle.

> [!important] Bütçe Hedefi
> Toplam elektronik malzeme bütçesi: ₺15,000 (KOSGEB hibesi dahilinde)

## Ana Kontrol Ünitesi (MCU & Support)

### Microcontroller & Crystal
| Designator | Part Number | Value | Package | Qty | Unit Price (₺) | Supplier | Ordered | Received |
|------------|-------------|-------|---------|-----|---------------|----------|---------|-----------|
| U1 | STM32G491RET6 | 32-bit MCU | LQFP-64 | 1 | ₺180.00 | Direkt Chip | ☐ | ☐ |
| Y1 | ABS07-120-32.768KHz | 32.768kHz | 3.2x1.5mm | 1 | ₺15.00 | Direkt Chip | ☐ | ☐ |
| Y2 | ABM8G-25.000MHZ | 25MHz | 3.2x2.5mm | 1 | ₺18.00 | Direkt Chip | ☐ | ☐ |
| C20, C21 | 0603 | 18pF | 0603 | 2 | ₺2.50 | Direkt Chip | ☐ | ☐ |
| C22, C23 | 0603 | 10pF | 0603 | 2 | ₺2.50 | Direkt Chip | ☐ | ☐ |

### Decoupling & Power Supply
| Designator | Part Number | Value | Package | Qty | Unit Price (₺) | Supplier | Ordered | Received |
|------------|-------------|-------|---------|-----|---------------|----------|---------|-----------|
| C1-C8 | 0603 | 100nF | 0603 | 8 | ₺1.50 | Direkt Chip | ☐ | ☐ |
| C9-C12 | 1206 | 10μF/25V | 1206 | 4 | ₺8.00 | Direkt Chip | ☐ | ☐ |
| C13 | 1210 | 47μF/16V | 1210 | 1 | ₺12.00 | Direkt Chip | ☐ | ☐ |
| C14 | 1210 | 220μF/16V | 1210 | 1 | ₺25.00 | Direkt Chip | ☐ | ☐ |
| L1 | 1206 | 10μH | 1206 | 1 | ₺15.00 | Direkt Chip | ☐ | ☐ |

## Analog Frontend

### Sensor Interface ICs
| Designator | Part Number | Value | Package | Qty | Unit Price (₺) | Supplier | Ordered | Received |
|------------|-------------|-------|---------|-----|---------------|----------|---------|-----------|
| U2 | TLV9064IDR | Quad OpAmp | SOIC-14 | 1 | ₺25.00 | Direkt Chip | ☐ | ☐ |
| U3 | TMP102AIDRLR | Temp Sensor | SOT-563 | 1 | ₺18.00 | Direkt Chip | ☐ | ☐ |
| U4 | SN65HVD230DR | CAN Transceiver | SOIC-8 | 1 | ₺22.00 | Direkt Chip | ☐ | ☐ |

### Precision Resistors
| Designator | Part Number | Value | Package | Qty | Unit Price (₺) | Supplier | Ordered | Received |
|------------|-------------|-------|---------|-----|---------------|----------|---------|-----------|
| R1, R2 | 0603 | 3.3kΩ ±1% | 0603 | 2 | ₺3.00 | Direkt Chip | ☐ | ☐ |
| R3, R4, R5 | 0603 | 100Ω ±1% | 0603 | 3 | ₺3.00 | Direkt Chip | ☐ | ☐ |
| R6, R7 | 0603 | 1kΩ ±1% | 0603 | 2 | ₺3.00 | Direkt Chip | ☐ | ☐ |
| R8, R9 | 0603 | 10kΩ ±1% | 0603 | 2 | ₺3.00 | Direkt Chip | ☐ | ☐ |
| R10-R15 | 0603 | 4.7kΩ ±1% | 0603 | 6 | ₺3.00 | Direkt Chip | ☐ | ☐ |
| R16 | 0805 | 4.7kΩ ±1% | 0805 | 1 | ₺3.00 | Direkt Chip | ☐ | ☐ |
| R17 | 0603 | 220Ω ±1% | 0603 | 1 | ₺3.00 | Direkt Chip | ☐ | ☐ |
| R18, R19 | 0603 | 120Ω ±1% | 0603 | 2 | ₺3.00 | Direkt Chip | ☐ | ☐ |

### Current Shunt
| Designator | Part Number | Value | Package | Qty | Unit Price (₺) | Supplier | Ordered | Received |
|------------|-------------|-------|---------|-----|---------------|----------|---------|-----------|
| R_SHUNT | WSL25181L000FEA | 1mΩ ±1% 1W | 2512 | 1 | ₺45.00 | Vishay/Mouser | ☐ | ☐ |

## Protection & Filtering

### Diodes & Protection
| Designator | Part Number | Value | Package | Qty | Unit Price (₺) | Supplier | Ordered | Received |
|------------|-------------|-------|---------|-----|---------------|----------|---------|-----------|
| D1, D2 | BAT54S | Schottky | SOT-23 | 2 | ₺5.00 | Direkt Chip | ☐ | ☐ |
| D3 | 1N4148W | Fast Switch | SOD-123 | 1 | ₺2.00 | Direkt Chip | ☐ | ☐ |
| D4 | LED_0603 | Status LED | 0603 | 1 | ₺3.00 | Direkt Chip | ☐ | ☐ |
| F1 | 0ZCJ0050AF2A | PTC Fuse 500mA | 0603 | 1 | ₺8.00 | Mouser | ☐ | ☐ |

### ESD Protection
| Designator | Part Number | Value | Package | Qty | Unit Price (₺) | Supplier | Ordered | Received |
|------------|-------------|-------|---------|-----|---------------|----------|---------|-----------|
| TVS1, TVS2 | PESD1CAN | CAN ESD | SOT-23 | 2 | ₺12.00 | NXP/Mouser | ☐ | ☐ |
| TVS3-TVS6 | PESD5V0S1BA | 5V ESD | SOD-323 | 4 | ₺8.00 | NXP/Mouser | ☐ | ☐ |

## Power Management

### Voltage Regulators
| Designator | Part Number | Value | Package | Qty | Unit Price (₺) | Supplier | Ordered | Received |
|------------|-------------|-------|---------|-----|---------------|----------|---------|-----------|
| U5 | AP2112K-3.3TRG1 | 3.3V LDO | SOT-23-5 | 1 | ₺8.00 | Direkt Chip | ☐ | ☐ |
| U6 | TPS54331DR | 5V SMPS | SOIC-8 | 1 | ₺35.00 | TI/Mouser | ☐ | ☐ |

### Power MOSFETs & Gate Drivers
| Designator | Part Number | Value | Package | Qty | Unit Price (₺) | Supplier | Ordered | Received |
|------------|-------------|-------|---------|-----|---------------|----------|---------|-----------|
| Q1, Q2, Q3 | IRFR3004PBF | 40V 200A | TO-252 | 3 | ₺65.00 | Infineon | ☐ | ☐ |
| Q4, Q5, Q6 | IRFR3004PBF | 40V 200A | TO-252 | 3 | ₺65.00 | Infineon | ☐ | ☐ |
| U7, U8 | IR2104 | Half-Bridge Driver | SOIC-8 | 2 | ₺28.00 | Infineon | ☐ | ☐ |
| U9 | IR2101 | High-Side Driver | SOIC-8 | 1 | ₺25.00 | Infineon | ☐ | ☐ |

### Gate Drive Components
| Designator | Part Number | Value | Package | Qty | Unit Price (₺) | Supplier | Ordered | Received |
|------------|-------------|-------|---------|-----|---------------|----------|---------|-----------|
| R20-R25 | 0805 | 22Ω ±1% | 0805 | 6 | ₺3.00 | Direkt Chip | ☐ | ☐ |
| C30-C35 | 0805 | 1nF | 0805 | 6 | ₺2.50 | Direkt Chip | ☐ | ☐ |
| C40-C42 | 1206 | 22μF/50V | 1206 | 3 | ₺12.00 | Direkt Chip | ☐ | ☐ |

## Connectors & Mechanical

### Board-to-Board Connectors
| Designator | Part Number | Value | Package | Qty | Unit Price (₺) | Supplier | Ordered | Received |
|------------|-------------|-------|---------|-----|---------------|----------|---------|-----------|
| J1 | XHP-3 | Throttle | 3-pin JST-XH | 1 | ₺5.00 | JST | ☐ | ☐ |
| J2 | XHP-4 | I2C/Temp | 4-pin JST-XH | 1 | ₺6.00 | JST | ☐ | ☐ |
| J3 | XHP-5 | Hall Sensors | 5-pin JST-XH | 1 | ₺7.00 | JST | ☐ | ☐ |
| J4 | XHP-4 | CAN Bus | 4-pin JST-XH | 1 | ₺6.00 | JST | ☐ | ☐ |
| J5 | XHP-2 | Power Input | 2-pin JST-XH | 1 | ₺4.00 | JST | ☐ | ☐ |

### Programming & Debug
| Designator | Part Number | Value | Package | Qty | Unit Price (₺) | Supplier | Ordered | Received |
|------------|-------------|-------|---------|-----|---------------|----------|---------|-----------|
| J6 | TSW-105-07-G-S | SWD Header | 5-pin 1.27mm | 1 | ₺8.00 | Samtec | ☐ | ☐ |
| J7 | USB-C | USB-C Conn | SMD | 1 | ₺15.00 | Direkt Chip | ☐ | ☐ |

### Motor Power Connectors
| Designator | Part Number | Value | Package | Qty | Unit Price (₺) | Supplier | Ordered | Received |
|------------|-------------|-------|---------|-----|---------------|----------|---------|-----------|
| J8 | XT60H-F | Motor Phases | 6-pin XT60 | 1 | ₺25.00 | AMASS | ☐ | ☐ |
| J9 | XT30U-F | Battery | 2-pin XT30 | 1 | ₺12.00 | AMASS | ☐ | ☐ |

## Passive Components Summary

### Capacitor Summary
| Value | Package | Voltage | Qty | Total Cost |
|-------|---------|---------|-----|------------|
| 100nF | 0603 | 50V | 8 | ₺12.00 |
| 10μF | 1206 | 25V | 4 | ₺32.00 |
| 47μF | 1210 | 16V | 1 | ₺12.00 |
| 220μF | 1210 | 16V | 1 | ₺25.00 |
| 1nF | 0805 | 50V | 6 | ₺15.00 |
| 22μF | 1206 | 50V | 3 | ₺36.00 |

### Resistor Summary  
| Value | Package | Tolerance | Qty | Total Cost |
|-------|---------|-----------|-----|------------|
| 100Ω | 0603 | 1% | 3 | ₺9.00 |
| 220Ω | 0603 | 1% | 1 | ₺3.00 |
| 1kΩ | 0603 | 1% | 2 | ₺6.00 |
| 3.3kΩ | 0603 | 1% | 2 | ₺6.00 |
| 4.7kΩ | 0603 | 1% | 6 | ₺18.00 |
| 4.7kΩ | 0805 | 1% | 1 | ₺3.00 |
| 10kΩ | 0603 | 1% | 2 | ₺6.00 |
| 120Ω | 0603 | 1% | 2 | ₺6.00 |
| 22Ω | 0805 | 1% | 6 | ₺18.00 |

## Sensörler & Actuators

### External Sensors
| Component | Part Number | Specification | Qty | Unit Price (₺) | Supplier | Ordered | Received |
|-----------|-------------|---------------|-----|---------------|----------|---------|-----------|
| Throttle Pot | B5K-Linear | 5kΩ Linear | 1 | ₺45.00 | Alpha/Mouser | ☐ | ☐ |
| Brake Switch | SS-5GL | SPST 5A | 1 | ₺25.00 | Omron | ☐ | ☐ |
| Hall Sensor A | A3144EUA-T | Digital Hall | 1 | ₺8.00 | Allegro | ☐ | ☐ |
| Hall Sensor B | A3144EUA-T | Digital Hall | 1 | ₺8.00 | Allegro | ☐ | ☐ |
| Hall Sensor C | A3144EUA-T | Digital Hall | 1 | ₺8.00 | Allegro | ☐ | ☐ |

### Motor & Drive
| Component | Part Number | Specification | Qty | Unit Price (₺) | Supplier | Ordered | Received |
|-----------|-------------|---------------|-----|---------------|----------|---------|-----------|
| BLDC Motor | 3kW Hub Motor | 48V 3kW | 1 | ₺2500.00 | Yerli Üretici | ☐ | ☐ |
| Motor Encoder | Incremental | 1000 PPR | 1 | ₺180.00 | Kübler | ☐ | ☐ |

## Kablolaj & Mekanik

### Wiring Harness
| Component | Specification | Length | Qty | Unit Price (₺) | Supplier | Ordered | Received |
|-----------|---------------|--------|-----|---------------|----------|---------|-----------|
| Power Cable | 16AWG Copper | 2m | 1 | ₺25.00 | Nexans | ☐ | ☐ |
| Signal Cable | 24AWG Twisted | 3m | 1 | ₺18.00 | Nexans | ☐ | ☐ |
| CAN Cable | CAN Twisted Pair | 2m | 1 | ₺35.00 | Belden | ☐ | ☐ |
| Heat Shrink | 3:1 Ratio | Asst. | 1 | ₺15.00 | 3M | ☐ | ☐ |

### Mechanical Mounting
| Component | Part Number | Specification | Qty | Unit Price (₺) | Supplier | Ordered | Received |
|-----------|-------------|---------------|-----|---------------|----------|---------|-----------|
| PCB Standoffs | M3x10 | Nylon | 4 | ₺5.00 | Local | ☐ | ☐ |
| Enclosure | ABS 120x80x40 | IP54 | 1 | ₺85.00 | OKW | ☐ | ☐ |
| Gasket | Silicone | Waterproof | 1 | ₺12.00 | Local | ☐ | ☐ |

## Test Equipment & Tools

### Required Tools
| Tool | Purpose | Qty | Unit Price (₺) | Supplier | Ordered | Received |
|------|---------|-----|---------------|----------|---------|-----------|
| ST-Link V3 | Programming | 1 | ₺350.00 | STMicroelectronics | ☐ | ☐ |
| DMM Fluke 87V | Measurements | 1 | ₺1200.00 | Fluke | ☐ | ☐ |
| Oscilloscope | Signal Analysis | 1 | ₺2500.00 | Rigol DS1054Z | ☐ | ☐ |
| Power Supply | Bench PSU | 1 | ₺800.00 | Wanptek | ☐ | ☐ |

## Cost Analysis

### Cost Breakdown by Category

| Category | Item Count | Total Cost (₺) | Percentage |
|----------|------------|---------------|------------|
| **MCU & Support** | 6 | ₺233.50 | 10.8% |
| **Analog Frontend** | 3 | ₺65.00 | 3.0% |
| **Resistors** | 21 | ₺75.00 | 3.5% |
| **Capacitors** | 22 | ₺132.00 | 6.1% |
| **Power Components** | 9 | ₺471.00 | 21.8% |
| **Protection & ESD** | 11 | ₺67.00 | 3.1% |
| **Connectors** | 9 | ₺88.00 | 4.1% |
| **Sensors & Motor** | 6 | ₺2774.00 | 40.4% |
| **Mechanical** | 3 | ₺102.00 | 4.7% |
| **Tools & Test** | 4 | ₺4850.00 | - |

### Budget Summary
```
Electronics BOM Total: ₺4,107.50
Mechanical Parts: ₺102.00
PCB Manufacturing: ₺400.00
Assembly Labor: ₺300.00

Project Subtotal: ₺4,909.50
Contingency (10%): ₺490.95

TOTAL PROJECT COST: ₺5,400.45
```

> [!tip] Budget Status
> Current estimate ₺5,400 is well within ₺15,000 budget, leaving ₺9,600 for battery system and mechanical components.

## Supplier Information

### Primary Suppliers

#### Direkt Chip (Istanbul)
- **Contact:** +90 212 465 8888  
- **Website:** direktchip.com
- **Payment:** EFT, Kredi kartı
- **Delivery:** 1-3 days (Istanbul)
- **Minimum order:** None

#### Mouser Electronics  
- **Contact:** +90 212 283 5000
- **Website:** mouser.com.tr
- **Payment:** EFT, Kredi kartı
- **Delivery:** 2-5 days (airmail)
- **Minimum order:** $50 (free shipping >$500)

#### Local Suppliers
- **Nexans Kablo:** Power/signal cables
- **Hasco:** Mechanical components  
- **Teknorama:** Tools and test equipment

### Lead Times & Ordering

#### Critical Path Items
| Component | Lead Time | Stock Risk | Mitigation |
|-----------|-----------|------------|------------|
| STM32G491RET6 | 2-4 weeks | Medium | Order early, alternatives identified |
| Power MOSFETs | 1-2 weeks | Low | Multiple suppliers |
| Hub Motor | 4-6 weeks | High | Local manufacturer, deposit required |
| Custom PCB | 1-2 weeks | Medium | Backup manufacturer ready |

### Alternative Parts

#### MCU Alternatives
- STM32G474RET6 (more ADC channels)
- STM32F446RET6 (lower cost, less advanced)

#### Power MOSFET Alternatives  
- IRFR3707Z (higher current rating)
- SiR158DP (better thermal characteristics)

#### Connector Alternatives
- Molex PicoBlade (smaller than JST-XH)
- TE Connectivity MicroFit (higher current)

## Procurement Schedule

### Week 1-2: Critical Components
- [ ] MCU, power components ordered
- [ ] PCB fabrication started
- [ ] Motor order placed (long lead time)

### Week 3-4: Support Components  
- [ ] Passives and connectors ordered
- [ ] Sensors and test equipment
- [ ] Mechanical parts

### Week 5-6: Final Assembly
- [ ] All components received and verified
- [ ] PCB assembly completed
- [ ] Initial testing started

## İlgili Dokümanlar
- [[AKS-Board]] - Detaylı schematic ve part references
- [[PCB-Uretim]] - Manufacturing requirements
- [[Sensor-Baglantilari]] - Component specifications  
- [[Test-Proseduru]] - Component testing procedures
- [[Budget-Tracking]] - Financial monitoring

## Referanslar
- Digi-Key Component Search Engine
- Mouser Electronics Catalog  
- STMicroelectronics Official Distributor Network
- IPC Standards for Electronic Assembly
- TEKNOFEST Budget Guidelines