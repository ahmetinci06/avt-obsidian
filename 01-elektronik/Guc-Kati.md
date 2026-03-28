# Güç Katı - Power Stage Design

#elektronik #güç #mosfet #gate-driver #pwm #todo

## Overview
The power stage consists of 3 half-bridges (6 MOSFETs total) to drive the 3-phase BLDC motor. Each half-bridge switches between battery voltage and ground to create the sinusoidal drive waveforms.

## Topology: Three-Phase Half-Bridge

```
Battery+ ────┬──── High-Side MOSFET (Q1) ────┬──── Phase A
             │                                │
             ├──── High-Side MOSFET (Q3) ────┼──── Phase B  
             │                                │
             └──── High-Side MOSFET (Q5) ────┼──── Phase C
                                              │
Battery- ────┬──── Low-Side MOSFET (Q2) ─────┼──── Phase A
             │                                │
             ├──── Low-Side MOSFET (Q4) ─────┼──── Phase B
             │                                │  
             └──── Low-Side MOSFET (Q6) ─────┘──── Phase C
```

### Key Requirements
- **Battery Voltage**: 48V nominal (42V - 58.8V range)
- **Motor Current**: 100A peak, 60A continuous  
- **Switching Frequency**: 20kHz (PWM from STM32 TIM1)
- **Efficiency Target**: >95%

## Gate Driver Options

### Option 1: NCP5106B (Recommended)
- **Pros**: 
  - Bootstrap operation for high-side
  - Built-in dead time (25ns typical)
  - 4A peak gate drive current
  - Wide supply range (4.5V - 18V)
  - Integrated shoot-through protection
- **Cons**: 
  - Requires external bootstrap diode and capacitor
  - Limited to half-bridge configuration
- **Cost**: ~$3 per IC (need 3x)
- [ ] Gate driver seçimi tamamlandı
- [ ] Bootstrap hesaplamaları yapıldı

### Option 2: IR2110 (Alternative)
- **Pros**:
  - Well-established, proven design
  - Lower cost (~$2 per IC)
  - Good documentation and examples
- **Cons**:
  - Older technology
  - Manual dead time control required
  - Lower gate drive current (2A)
- [ ] Alternatif olarak değerlendirildi

### Option 3: DRV8301 (Integrated Solution)
- **Pros**:
  - Complete 3-phase solution in single IC
  - Integrated current sensing
  - Protection features
  - SPI interface for configuration
- **Cons**:
  - Much higher cost (~$15)
  - Complex PCB layout
  - Overkill for our application
- [ ] Maliyet analizi yapıldı

## MOSFET Selection Criteria

### Voltage Rating (Vds)
- **Minimum**: 80V (48V × 1.5 safety factor + transients)
- **Recommended**: 100V MOSFETs
- **Reasoning**: Allows for battery voltage spikes, regen braking

### Current Rating (Id)
- **Continuous**: 100A minimum
- **Pulsed**: 200A minimum  
- **Thermal Derating**: Consider junction temperature rise

### On-Resistance (Rds_on)
- **Target**: <2mΩ at 25°C
- **Power Loss Calculation**: 
  - P_loss = I²rms × Rds_on × thermal_factor
  - For 60A RMS: P = (60)² × 0.002 × 1.5 = 10.8W per MOSFET
- **Total Power Stage Loss**: ~65W (need proper heatsinking)

### Package Options
1. **TO-220**: Easy mounting, good thermal, through-hole
2. **D2PAK**: SMD, moderate thermal, better for automated assembly
3. **TO-247**: Best thermal performance, larger footprint

### Candidate MOSFETs
| Part Number | Vds | Id | Rds_on | Package | Cost |
|-------------|-----|----|---------|---------| -----|
| IRFB4110PBF | 100V | 120A | 3.7mΩ | TO-220 | ~$3 |
| IPB180P04P4L | 40V | 180A | 1.5mΩ | TO-263 | ~$5 |
| CSD19531Q5A | 100V | 100A | 5.9mΩ | SON 5×6 | ~$4 |

- [ ] MOSFET seçimi yapıldı
- [ ] Thermal analiz tamamlandı
- [ ] Prototip sipariş verildi

## Dead Time Calculation

### STM32 Timer Configuration
- **Timer**: TIM1 (advanced control timer)
- **DTG Register**: DeadTime = 170 (from firmware)
- **Clock**: 170MHz system clock

### DTG Formula (from STM32 reference manual)
```
Dead_time = DTG[7:5] × (2^DTG[4:0]) × T_clock

Where DTG = 170 = 0b10101010
DTG[7:5] = 101 (binary) = 5 (decimal)  
DTG[4:0] = 01010 (binary) = 10 (decimal)

Dead_time = 5 × (2^10) × (1/170MHz)
Dead_time = 5 × 1024 × 5.88ns = 30.1µs
```

> **⚠️ Warning**: This calculation gives 30µs dead time, which is too long!
> **Correct DTG value needed**: For ~1µs dead time, use DTG ≈ 29

### Proper Dead Time Target
- **Recommended**: 500ns - 1µs
- **Gate Driver + MOSFET**: NCP5106B adds 25ns, MOSFET switching ~100ns
- **Total Budget**: 300ns safety margin

### Recalculated DTG Value
```
Target: 1µs = 1000ns
DTG = 1000ns / (T_clock) = 1000ns / 5.88ns = 170 counts

But with DTG encoding:
For DTG = 29: Dead_time = 1 × (2^5) × 5.88ns = 188ns ❌ too short
For DTG = 60: Dead_time = 1 × (2^5) × 5.88ns = 188ns... wait

Need to check DTG[7:5] ranges in manual!
```

- [ ] DTG hesaplama doğrulandı
- [ ] Firmware güncellendi
- [ ] Scope ile ölçüm yapıldı

## Bootstrap Capacitor Design

### Bootstrap Circuit per Half-Bridge
```
Vdd ──┬── Bootstrap_Diode ──┬── Bootstrap_Cap ──┬── VB (High-side supply)
      │                     │                   │
      └── Control Logic     └── VS (Phase node) └── High-side Gate Driver
```

### Capacitor Sizing
```
C_bootstrap = Q_gate × Safety_Factor / ΔV_acceptable

Where:
Q_gate = Gate charge of MOSFET ≈ 100nC (typical)
Safety_Factor = 10 (for reliable operation)
ΔV_acceptable = 1V (max voltage droop)

C_bootstrap = 100nC × 10 / 1V = 1µF minimum
```

### Recommended Values
- **Bootstrap Capacitor**: 2.2µF, 25V ceramic (X7R)
- **Bootstrap Diode**: Fast recovery, Vf < 0.7V
  - Part: BAS316 (100V, 250mA, fast switching)

- [ ] Bootstrap hesaplama tamamlandı
- [ ] Komponenten seçildi
- [ ] PCB layout'ta yerleştirildi

## Heatsink Requirements

### Thermal Analysis
```
P_total = 6 × P_per_mosfet = 6 × 10.8W = 65W

Junction-to-ambient thermal resistance needed:
θ_JA = (T_junction_max - T_ambient) / P_total
θ_JA = (125°C - 40°C) / 65W = 1.3°C/W maximum

Assuming θ_JC = 0.5°C/W (MOSFET package):
θ_CA_required = θ_JA - θ_JC = 1.3 - 0.5 = 0.8°C/W
```

### Heatsink Selection
- **Thermal Resistance**: <0.8°C/W
- **Mounting**: Compatible with chosen MOSFET package  
- **Size**: Fit within enclosure constraints
- **Airflow**: Consider forced air cooling (fan)

### Recommended Options
1. **Fischer SK 68**: 0.45°C/W, 100mm length
2. **Aavid 531002B02500**: 0.7°C/W, compact
3. **Custom aluminum plate**: Design for enclosure

- [ ] Heatsink seçimi yapıldı
- [ ] Thermal interface material belirlendi
- [ ] Mechanical mounting çözüldü

## PCB Layout Considerations

### Power Stage Layout Rules
1. **Keep power loops small**: Battery+ to high-side to phase to low-side to Battery-
2. **Separate analog and power grounds**: Star grounding
3. **Gate drive traces short**: <10mm from driver to MOSFET gate
4. **Kelvin connections**: Separate sense lines for current measurement
5. **Copper weight**: Minimum 2oz (70µm) for power traces
6. **Via stitching**: Connect top and bottom ground pours

### Critical Trace Widths
- **Battery Bus**: 6mm width minimum (2oz copper, 50A)
- **Phase Outputs**: 4mm width minimum  
- **Gate Drives**: 0.5mm width, controlled impedance
- **Sense Lines**: 0.2mm width, differential routing

### Component Placement
- **MOSFETs**: Central location for heatsink mounting
- **Gate Drivers**: Close to MOSFETs (<20mm)
- **Bootstrap components**: Adjacent to respective drivers
- **Bypass capacitors**: Close to power pins

- [ ] PCB layout kuralları belirlendi
- [ ] Schematic → PCB transfer tamamlandı
- [ ] DRC (Design Rule Check) temiz

## Assembly and Test Checklist

### Pre-Assembly Inspection
- [ ] PCB manufacturing quality check
- [ ] Correct component values verified
- [ ] Solder paste stencil alignment
- [ ] Pick-and-place program verified

### Assembly Sequence
1. **SMD Components**: 
   - [ ] Gate drivers and bypass caps first
   - [ ] Bootstrap diodes and caps  
   - [ ] Thermal vias for heatsink mounting
2. **Power MOSFETs**:
   - [ ] Apply thermal interface material
   - [ ] Mount to heatsink before soldering
   - [ ] Verify mechanical alignment
3. **Connectors**:
   - [ ] Battery input terminals
   - [ ] Motor phase outputs
   - [ ] Gate drive signals from MCU

### Initial Testing (No Power)
- [ ] Visual inspection complete
- [ ] No solder bridges
- [ ] All components oriented correctly
- [ ] Mechanical fit with heatsink verified

### Low Power Testing (5V Logic Supply)
- [ ] Gate driver power supplies working
- [ ] Bootstrap circuits charging
- [ ] Gate signals from MCU visible on scope
- [ ] Dead time measurement correct

### Power Testing (With Current Limit)
- [ ] Start with reduced bus voltage (12V)
- [ ] Monitor MOSFET temperatures  
- [ ] Verify switching waveforms
- [ ] Check for electromagnetic interference

### Full Power Testing
- [ ] Gradually increase to full 48V
- [ ] Load testing with motor
- [ ] Thermal equilibrium reached
- [ ] Efficiency measurement >95%
- [ ] No overcurrent events

## Related Files
- [[AKS-Board]] - Control board schematic
- [[Sensor-Baglantilari]] - Signal routing  
- [[CAN-Bus]] - Communication with BMS
- [[Malzeme-Listesi]] - Complete BOM

---
*Last Updated: 2026-03-28*
*Status: #todo - gate driver selection pending*