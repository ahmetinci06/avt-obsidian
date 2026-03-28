# PCB Üretim Süreci

#pcb #manufacturing #gerber #assembly #test

[[AKS-Board]] | [[Malzeme-Listesi]] | [[Test-Proseduru]]

## Genel Bakış

AKS (Araç Kontrol Sistemi) PCB'nin üretim, montaj ve test süreçlerini kapsayan detaylı rehber. 4-layer PCB, SMD/THT mix montaj, kalite kontrol prosedürleri.

> [!warning] Kritik Sistem
> Bu PCB araç güvenlik sistemlerini kontrol eder. Üretim hatası yaşam güvenliğini tehlikeye atar.

## PCB Özellikleri

### Teknik Parametreler
| Parametre | Değer | Not |
|-----------|-------|-----|
| Katman sayısı | 4-layer | Signal/Ground/Power/Signal |
| Boyut | 80mm x 60mm | Compact form factor |
| Kalınlık | 1.6mm | Standard thickness |
| Via boyutu | 0.2mm (drill) | Micro vias |
| Min trace width | 0.1mm | High density routing |
| Min spacing | 0.1mm | IPC-2221 Class 2 |
| Copper weight | 35μm (1oz) | Standard thickness |
| Surface finish | HASL/ENIG | RoHS compliant |

### Layer Stack-up
```
Layer 1 (Top):    Component + Signal
Layer 2 (GND):    Ground plane
Layer 3 (PWR):    Power planes (3.3V, 5V)
Layer 4 (Bottom): Signal + Component
```

## Gerber Export Süreci

### KiCad Export Settings

#### Gerber Files Checklist
- [ ] F.Cu (Top copper)
- [ ] B.Cu (Bottom copper) 
- [ ] In1.Cu (Ground plane)
- [ ] In2.Cu (Power plane)
- [ ] F.Paste (Top solder paste)
- [ ] B.Paste (Bottom solder paste)
- [ ] F.Silkscreen (Top silkscreen)
- [ ] B.Silkscreen (Bottom silkscreen)
- [ ] F.Mask (Top solder mask)
- [ ] B.Mask (Bottom solder mask)
- [ ] Edge.Cuts (Board outline)

#### Drill Files
- [ ] PTH (Plated through holes)
- [ ] NPTH (Non-plated holes)
- [ ] Excellon format
- [ ] Coordinate format: 3.3

#### Pick & Place Files
- [ ] Component positions (.csv)
- [ ] Rotation values corrected
- [ ] Reference designators included
- [ ] Component values listed

### Export Parameters
```
Gerber Format: RS-274X
Coordinate Format: 4.6 (inch) / 4.3 (mm)
Units: Millimeters
Zero Format: Suppress leading
Drill Origin: Absolute
Merge PTH/NPTH: No
```

## Manufacturer Selection

### Recommended Suppliers

#### Domestic Options
| Firma | Lokasyon | Lead Time | Quality | Cost |
|-------|----------|-----------|---------|------|
| **Arista PCB** | Istanbul | 5-7 days | ★★★★★ | $$$ |
| **Değirmenci PCB** | Ankara | 7-10 days | ★★★★☆ | $$ |
| **Karpat PCB** | Bursa | 10-14 days | ★★★☆☆ | $ |

#### International Options  
| Firma | Lokasyon | Lead Time | Quality | Cost |
|-------|----------|-----------|---------|------|
| **JLCPCB** | China | 7-10 days + shipping | ★★★★☆ | $ |
| **PCBWay** | China | 5-7 days + shipping | ★★★★☆ | $$ |
| **OSH Park** | USA | 14-21 days | ★★★★★ | $$$ |

### Selection Criteria
1. **Lead time** (TEKNOFEST deadline critical)
2. **Quality standards** (IPC compliance)
3. **Cost** (student budget friendly)
4. **Communication** (Turkish support preferred)
5. **Experience** (automotive PCBs)

## Order Checklist

### Pre-Order Verification
- [ ] Gerber files DRC clean
- [ ] Drill file verified (no errors)
- [ ] Layer stack-up confirmed
- [ ] Impedance requirements specified
- [ ] Assembly drawings included
- [ ] BOM cross-referenced

### Order Parameters
```
Quantity: 10 pcs (3 for assembly + 7 spare)
Material: FR4 (Tg 170°C)
Color: Green solder mask
Silkscreen: White (both sides)
Surface finish: HASL (lead-free)
Via filling: None
Gold fingers: No
Controlled impedance: No (single-ended)
```

### Order Tracking

#### Order Form Template
| Field | Value |
|-------|-------|
| Project Name | AVT_AKS_v1.0 |
| Order Date | YYYY-MM-DD |
| Order Number | #tracking_number |
| Quantity | 10 pcs |
| Expected delivery | YYYY-MM-DD |
| Cost | $$$ TL |

#### Status Tracking
- [ ] Order placed
- [ ] Payment confirmed  
- [ ] Production started
- [ ] DFM check passed
- [ ] Fabrication complete
- [ ] Shipped (tracking: #)
- [ ] Received
- [ ] Quality inspection passed

## Incoming Inspection

### Visual Inspection Checklist

#### PCB Aesthetics
- [ ] Surface finish uniform
- [ ] No scratches or contamination
- [ ] Solder mask properly aligned
- [ ] Silkscreen legible and complete
- [ ] Edge cuts clean
- [ ] No delamination

#### Dimensional Check
- [ ] PCB dimensions within tolerance (±0.1mm)
- [ ] Hole sizes verified (drill chart)
- [ ] Component footprint alignment
- [ ] Connector positions correct

### Electrical Testing

#### Continuity Tests
```bash
# Test equipment: Digital multimeter
# Resistance threshold: <1Ω for connections, >10MΩ for isolation

Tests to perform:
1. Power plane continuity (3V3, 5V, GND)
2. Critical signal paths
3. Inter-layer connections (vias)
4. Connector pin mapping
```

#### Test Results Log
| Test | Expected | Measured | Pass/Fail | Notes |
|------|----------|----------|-----------|-------|
| 3V3 plane | <1Ω | ___ Ω | ☐ | |
| 5V plane | <1Ω | ___ Ω | ☐ | |
| GND plane | <1Ω | ___ Ω | ☐ | |
| USB_D+ | <1Ω | ___ Ω | ☐ | |
| USB_D- | <1Ω | ___ Ω | ☐ | |
| CAN_H | <1Ω | ___ Ω | ☐ | |
| CAN_L | <1Ω | ___ Ω | ☐ | |

#### Insulation Test
- [ ] Inter-plane isolation >10MΩ
- [ ] Power-to-ground isolation >10MΩ
- [ ] No shorts between signal layers

## Assembly Preparation

### Component Preparation
- [ ] All components received and verified
- [ ] BOM quantities checked
- [ ] Component orientation verified
- [ ] Static protection measures in place

### Assembly Tools Required
| Tool | Purpose | Required |
|------|---------|----------|
| Soldering iron | THT assembly | ✅ |
| Hot air station | SMD rework | ✅ |
| Tweezers | Component placement | ✅ |
| Flux | Improved soldering | ✅ |
| Solder wick | Desoldering | ✅ |
| Magnifying glass | Visual inspection | ✅ |
| Anti-static mat | ESD protection | ✅ |

### Assembly Sequence
1. **SMD components first** (smallest to largest)
2. **Critical ICs** (MCU, power management)
3. **Passive components** (R, C, L)
4. **Through-hole components**
5. **Connectors and mechanical parts**
6. **Final cleaning and inspection**

## Quality Control

### Post-Assembly Inspection

#### Visual Quality Check
- [ ] All components properly seated
- [ ] No solder bridges
- [ ] No cold solder joints
- [ ] Component polarity correct
- [ ] No missing components
- [ ] Clean flux residue

#### Electrical Verification
- [ ] Power-on test (current limit 100mA)
- [ ] Supply voltage levels verified
- [ ] MCU programming connection OK
- [ ] Basic functionality test

### Rework Procedures

#### Common Rework Scenarios
| Issue | Tools Needed | Procedure |
|-------|-------------|-----------|
| Solder bridge | Wick + flux | Heat and wick excess solder |
| Cold joint | Iron + flux | Reheat with fresh flux |
| Wrong component | Hot air | Remove, clean, replace |
| Lifted pad | Jumper wire | Alternative connection |

#### Rework Quality Standards
- Reworked joints must meet IPC-610 Class 2
- Maximum 2 rework attempts per component
- Document all rework actions
- Re-test after rework completion

## Test & Validation

### Functional Test Stages

#### Stage 1: Power-On Test (POT)
```
Objective: Verify power distribution
Test setup: Current-limited PSU (100mA)
Success criteria: 
- 3V3 rail: 3.25-3.35V
- 5V rail: 4.95-5.05V  
- Current <50mA at idle
```

#### Stage 2: Programming Test
```
Objective: MCU programming capability
Test setup: ST-Link programmer
Success criteria:
- SWD connection successful
- Flash memory accessible
- Basic blinky code runs
```

#### Stage 3: Peripheral Test
```
Objective: All interfaces working
Test setup: Individual module tests
Success criteria:
- ADC readings stable
- I2C communication OK
- CAN transceiver functional
- GPIO control working
```

### Test Documentation

#### Test Report Template
```
Test Date: YYYY-MM-DD
PCB Serial: AVT-AKS-001
Tester: Name
Test Stage: POT/Programming/Functional

Results:
[ ] Power supplies OK
[ ] Programming OK  
[ ] Peripherals OK
[ ] Final acceptance

Notes: _______________
Disposition: PASS/FAIL/REWORK
```

## Storage & Handling

### Anti-Static Precautions
- ESD-safe storage containers
- Anti-static bags for transport
- Grounded work surfaces
- ESD wrist straps during handling

### Environmental Conditions
- Temperature: 15-35°C
- Humidity: 30-70% RH
- Clean, dust-free environment
- Away from magnetic fields

## Documentation Package

### Delivery Package Contents
- [ ] Assembled and tested PCBs
- [ ] Test reports (each unit)
- [ ] Assembly drawings
- [ ] Component placement guide
- [ ] Spare components list
- [ ] Rework documentation
- [ ] Quality certificates

## Backup Plan

### Risk Mitigation
| Risk | Probability | Impact | Mitigation |
|------|------------|--------|------------|
| Manufacturing defect | Medium | High | Order extra quantities |
| Delivery delay | High | High | Multiple supplier backup |
| Assembly error | Medium | Medium | Careful review process |
| Component shortage | Low | High | Alternative part numbers |

### Emergency Actions
- Fast-track production available (+cost)
- Alternative suppliers identified
- Manual assembly capability
- Simplified design fallback option

## İlgili Dokümanlar
- [[AKS-Board]] - Schematic ve PCB layout files
- [[Malzeme-Listesi]] - Complete BOM with suppliers
- [[Test-Proseduru]] - Detailed test procedures
- [[Assembly-Guide]] - Step-by-step assembly
- [[Quality-Standards]] - IPC and internal standards

## Referanslar
- IPC-2221: Generic Standard on Printed Board Design
- IPC-610: Acceptability of Electronic Assemblies
- IPC-A-600: Acceptability of Printed Boards
- KiCad PCB Design Guidelines
- TEKNOFEST Technical Regulations