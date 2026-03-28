# AKS Board - Complete BOM and Assembly Guide

#elektronik #aks #bom #stm32 #todo

## Overview
The AKS (Araç Kontrol Sistemi) Board is the brain of our electric vehicle, built around the STM32G491RETx MCU. This board handles motor control, battery monitoring, sensor acquisition, and CAN communication.

## Complete Bill of Materials

### Microcontroller Unit
- **U3: STM32G491RETx (LQFP-64)**
  - Purpose: Main MCU - motor control, CAN, sensor processing
  - Package: LQFP-64 (10x10mm, 0.5mm pitch)
  - Features: 170MHz ARM Cortex-M4F, 512KB Flash, 128KB RAM, FDCAN, ADC
  - Supplier: Mouser/DigiKey/RS Components
  - Part Number: STM32G491RETx
  - [ ] Temin Edildi
  - [ ] Lehimlendi

### Signal Conditioning
- **U4: TLV9064IDR (Quad Op-Amp, SOIC-14)**
  - Purpose: Signal conditioning for current sense and VBUS measurement
  - Package: SOIC-14 (8.65x3.90mm)
  - Features: Rail-to-rail, low noise, 10MHz GBW
  - Supplier: Texas Instruments / DigiKey
  - Part Number: TLV9064IDR
  - [ ] Temin Edildi
  - [ ] Lehimlendi

### Temperature Sensor
- **U7: TMP102xxDRL (SOT-563)**
  - Purpose: Board temperature monitoring
  - Package: SOT-563 (1.6x1.6mm)
  - I2C Address: 0x48 (ADD0 pin tied to GND)
  - Features: ±0.5°C accuracy, I2C interface, 12-bit resolution
  - Supplier: Texas Instruments
  - Part Number: TMP102AIDRL
  - [ ] Temin Edildi
  - [ ] Lehimlendi

### Resistors (All 0805 package, 1% tolerance)

#### Current Sensing Circuit
- **R1: 3.3kΩ**
  - Purpose: Current sense amplifier input resistor
  - Power: 1/8W
  - [ ] Temin Edildi
  - [ ] Lehimlendi

- **R2: 3.3kΩ**
  - Purpose: Current sense amplifier input resistor (differential pair)
  - Power: 1/8W
  - [ ] Temin Edildi
  - [ ] Lehimlendi

- **R5: 100Ω**
  - Purpose: Current sense output filter
  - Power: 1/8W
  - [ ] Temin Edildi
  - [ ] Lehimlendi

- **R6: 100Ω**
  - Purpose: Current sense input protection
  - Power: 1/8W
  - [ ] Temin Edildi
  - [ ] Lehimlendi

#### Voltage Divider Networks
- **R3: 100kΩ**
  - Purpose: VBUS divider feedback resistor
  - Power: 1/8W
  - [ ] Temin Edildi
  - [ ] Lehimlendi

- **R4: 100kΩ**
  - Purpose: VBUS divider feedback resistor (pair)
  - Power: 1/8W
  - [ ] Temin Edildi
  - [ ] Lehimlendi

- **R8: 500kΩ**
  - Purpose: VBUS divider low side (scales HV down)
  - Power: 1/4W (handles HV)
  - [ ] Temin Edildi
  - [ ] Lehimlendi

- **R9: 36kΩ**
  - Purpose: VBUS feedback calibration
  - Power: 1/8W
  - [ ] Temin Edildi
  - [ ] Lehimlendi

- **R10: 330Ω**
  - Purpose: VBUS output filter resistor
  - Power: 1/8W
  - [ ] Temin Edildi
  - [ ] Lehimlendi

- **R11: 500kΩ**
  - Purpose: VBUS divider high side
  - Power: 1/4W (handles HV)
  - [ ] Temin Edildi
  - [ ] Lehimlendi

#### Pull-up/Pull-down Resistors
- **R14: 4.7kΩ**
  - Purpose: I2C SDA pull-up (TMP102)
  - Power: 1/8W
  - [ ] Temin Edildi
  - [ ] Lehimlendi

- **R15: 4.7kΩ**
  - Purpose: I2C SCL pull-up (TMP102)
  - Power: 1/8W
  - [ ] Temin Edildi
  - [ ] Lehimlendi

- **R16: 4.7kΩ**
  - Purpose: Brake switch pull-down
  - Power: 1/8W
  - [ ] Temin Edildi
  - [ ] Lehimlendi

#### Input Conditioning
- **R7: 100Ω**
  - Purpose: General purpose input protection
  - Power: 1/8W
  - [ ] Temin Edildi
  - [ ] Lehimlendi

- **R17: 220Ω**
  - Purpose: Throttle input filter resistor
  - Power: 1/8W
  - [ ] Temin Edildi
  - [ ] Lehimlendi

#### Current Shunt
- **Rshunt1: 1mΩ (Special Component)**
  - Purpose: Motor current measurement shunt
  - Package: 2512 or higher power rating
  - Power: 5W minimum
  - Accuracy: ±1%
  - Current Rating: 200A+
  - Supplier: Vishay/Ohmite
  - Part Number: WSR2-1E-3 or similar
  - [ ] Temin Edildi
  - [ ] Lehimlendi

### Capacitors

#### Filtering Capacitors (0805 package, X7R dielectric)
- **C1: 100nF**
  - Purpose: Current sense signal filtering
  - Voltage Rating: 50V
  - [ ] Temin Edildi
  - [ ] Lehimlendi

- **C2: 1nF**
  - Purpose: Differential input filter (op-amp)
  - Voltage Rating: 50V
  - [ ] Temin Edildi
  - [ ] Lehimlendi

- **C3: 1nF**
  - Purpose: Differential input filter (op-amp pair)
  - Voltage Rating: 50V
  - [ ] Temin Edildi
  - [ ] Lehimlendi

- **C4: 100nF**
  - Purpose: Throttle input filter
  - Voltage Rating: 50V
  - [ ] Temin Edildi
  - [ ] Lehimlendi

- **C5: 100nF**
  - Purpose: Op-amp power supply bypass
  - Voltage Rating: 25V
  - [ ] Temin Edildi
  - [ ] Lehimlendi

- **C6: 100nF**
  - Purpose: VBUS measurement filter
  - Voltage Rating: 50V
  - [ ] Temin Edildi
  - [ ] Lehimlendi

- **C7: 2.2nF**
  - Purpose: VBUS input compensation
  - Voltage Rating: 50V
  - [ ] Temin Edildi
  - [ ] Lehimlendi

- **C14: 100nF**
  - Purpose: TMP102 power supply bypass
  - Voltage Rating: 25V
  - [ ] Temin Edildi
  - [ ] Lehimlendi

- **C15: 100nF**
  - Purpose: Brake switch debounce
  - Voltage Rating: 25V
  - [ ] Temin Edildi
  - [ ] Lehimlendi

### Protection Diodes
- **D2: Clamping Diode (VBUS to 3V3)**
  - Purpose: Overvoltage protection for 3.3V rail
  - Type: Zener diode, 3.3V
  - Package: SOD-123 or SOD-323
  - Power: 1W
  - [ ] Temin Edildi
  - [ ] Lehimlendi

- **D3: Clamping Diode (VBUS to GND)**
  - Purpose: Reverse polarity protection
  - Type: Schottky diode
  - Package: SOD-123 or SOD-323
  - Forward Voltage: <0.4V
  - Current: 1A
  - [ ] Temin Edildi
  - [ ] Lehimlendi

### Switches & Connectors
- **SW1: Push Button (Brake Switch)**
  - Purpose: Digital brake signal input
  - Type: Momentary pushbutton, SPST-NO
  - Package: Through-hole or SMD tactile switch
  - Actuation Force: 2-5N
  - [ ] Temin Edildi
  - [ ] Lehimlendi

- **J1: 3-Pin Connector (Throttle Input)**
  - Purpose: Throttle pedal connection
  - Pinout:
    - Pin 1: 3V3 (red wire)
    - Pin 2: Throttle signal (white/yellow wire)
    - Pin 3: GND (black wire)
  - Type: JST-XH or terminal block
  - Current Rating: 1A minimum
  - [ ] Temin Edildi
  - [ ] Lehimlendi

## Assembly Notes

### Critical Components First
1. **Power Supply Section**: U3 (MCU) and associated bypass caps
2. **Signal Conditioning**: U4 (op-amp) and precision resistors
3. **Protection Circuits**: D2, D3 before connecting to external circuits
4. **Sensors**: U7 (temperature) last to avoid heat damage

### Soldering Tips
- Use flux for all SMD components
- Temperature profile: 350°C tip, 2-3 seconds per joint
- Inspect under microscope for bridges and cold joints
- Test continuity before power-up

### Testing Checklist
- [ ] Visual inspection complete
- [ ] No shorts between power rails
- [ ] All bypass capacitors in place
- [ ] Proper MCU orientation (Pin 1 marker)
- [ ] I2C pull-ups installed
- [ ] Current shunt properly oriented
- [ ] All connectors mechanically secure

## Signal Chain Overview

```
Motor Current → Rshunt1 → R1,R2 → U4 (TLV9064) → R5,C1 → MCU ADC
VBUS → R11,R8 → U4 (TLV9064) → R10,C6,D2,D3 → MCU ADC  
Throttle → J1 → R17,C4 → MCU ADC
Brake → SW1 → R16,C15 → MCU GPIO
Temperature → U7 (TMP102) → I2C → MCU
```

## Related Files
- [[Guc-Kati]] - Power stage design
- [[Sensor-Baglantilari]] - Detailed wiring
- [[CAN-Bus]] - Communication setup
- [[PCB-Uretim]] - Manufacturing specs

---
*Last Updated: 2026-03-28*
*Status: #todo - awaiting component procurement*