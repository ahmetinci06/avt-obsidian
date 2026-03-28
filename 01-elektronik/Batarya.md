# Batarya Sistemi - Battery Pack Design

#elektronik #batarya #bms #güvenlik #teknofest #todo

## TEKNOFEST Kısıtlamaları

### Enerji Sınırı
- **Maksimum**: 3 kWh (3000 Wh)
- **Hesaplama**: Nominal voltage × Nominal capacity
- **Örnek**: 48V × 62.5Ah = 3000Wh (limit'te)
- **Güvenlik Marjı**: 2.8kWh tasarım hedefi (limit altında kalmak için)

### Ek Kısıtlamalar
- Yangın güvenliği sertifikası gerekli
- Kaza durumunda acil kesim sistemi
- İzolasyon testi geçmeli (>1MΩ)
- Termal korunma sistemi zorunlu

## Cell Chemistry Options

### 1. Lithium-ion (Li-ion) - NMC
**Avantajlar:**
- Yüksek enerji yoğunluğu (250Wh/kg)
- Uzun çevrim ömrü (1500+ cycles)
- Hızlı şarj kapasitesi (1C+)
- Düşük iç direnç

**Dezavantajlar:**
- Termal runaway riski
- Pahalı BMS gereksinimi
- Güvenlik gereksinimleri

**Örnek Hücre**: Samsung INR21700-50E
- Nominal: 3.6V, 5000mAh (18Wh)
- Max şarj: 4.2V
- Cutoff: 2.5V
- Max akım: 9.8A sürekli

### 2. Lithium Iron Phosphate (LiFePO4)
**Avantajlar:**
- Çok güvenli (thermal runaway yok)
- Uzun ömür (3000+ cycles)  
- Geniş sıcaklık aralığı
- Daha basit BMS

**Dezavantajlar:**
- Düşük enerji yoğunluğu (160Wh/kg)
- Düşük voltaj (3.2V nominal)
- Daha büyük pack boyutu

**Örnek Hücre**: EVE LF280K
- Nominal: 3.2V, 280Ah (896Wh)
- Max şarj: 3.65V
- Cutoff: 2.5V
- Max akım: 1C (280A)

### Seçim Kriteri
- **TEKNOFEST için LiFePO4 önerisi**: Güvenlik öncelikli
- [ ] Hücre kimyası seçimi yapıldı
- [ ] Güvenlik analizi tamamlandı

## Pack Configuration

### LiFePO4 Prismatic Cells (Önerilen)
```
Configuration: 15S1P (EVE LF280K × 15)
Nominal Voltage: 15 × 3.2V = 48V
Capacity: 280Ah  
Energy: 48V × 280Ah = 13.44kWh

⚠️ TEKNOFEST limit aşımı!
```

### Alternatif: 15S1P Küçük Hücreler
```
Configuration: 15S1P (EVE LF200 × 15)  
Nominal Voltage: 48V
Capacity: 200Ah
Energy: 48V × 200Ah = 9.6kWh ⚠️ Yine aşım

Configuration: 15S1P (EVE LF100 × 15)
Nominal Voltage: 48V  
Capacity: 100Ah
Energy: 48V × 100Ah = 4.8kWh ⚠️ Limit aşımı
```

### TEKNOFEST Uyumlu Konfigürasyon
```
Option 1: 14S1P (LF200) = 44.8V × 200Ah = 8.96kWh ❌
Option 2: 13S1P (LF200) = 41.6V × 200Ah = 8.32kWh ❌  
Option 3: 12S1P (LF100) = 38.4V × 100Ah = 3.84kWh ❌

Viable Solution: 
15S1P with LF60 cells (if available)
48V × 60Ah = 2.88kWh ✅ Under limit!
```

### Gerçekçi Çözüm
```
18650 Configuration: 13S22P
Using Samsung INR18650-25R:
- Per cell: 3.6V × 2.5Ah = 9Wh
- String: 13S = 46.8V nominal  
- Parallel: 22P = 55Ah
- Total: 46.8V × 55Ah = 2574Wh = 2.57kWh ✅

Cell count: 13 × 22 = 286 cells
```

- [ ] Pack konfigürasyonu finalize edildi
- [ ] TEKNOFEST limitleri doğrulandı

## BMS Requirements

### Primary Functions
1. **Cell Balancing**: Passive balancing minimum (active preferred)
2. **Overvoltage Protection**: 4.25V per cell cutoff  
3. **Undervoltage Protection**: 2.5V per cell cutoff
4. **Overcurrent Protection**: 100A+ capability
5. **Temperature Monitoring**: Multiple sensors across pack
6. **State Estimation**: SOC, SOH calculation
7. **CAN Communication**: Interface to VCU

### BMS Selection Criteria
- **Cell Count Support**: 13S minimum
- **Current Rating**: 150A continuous, 200A peak
- **Communication**: CAN bus (500kbps)
- **Balancing Current**: 100mA+ per cell
- **Operating Temperature**: -20°C to +60°C

### Candidate BMS Units
| Model | Voltage | Current | Balancing | CAN | Cost |
|-------|---------|---------|-----------|-----|------|
| Orion BMS 2 | Up to 180S | 500A | Active | ✅ | $1500+ |
| EV-Power BMS | 16S max | 200A | Passive | ✅ | $400 |
| DALY Smart BMS | 16S max | 100A | Passive | ✅ | $150 |

**Recommendation**: DALY Smart BMS 13S 100A
- Cost effective
- CAN communication
- Android app for monitoring
- Proven in EV applications

- [ ] BMS seçimi yapıldı
- [ ] CAN protokol dokümanı alındı

## Electrical Wiring Diagram

```
                    Main Contactor (MC)
Battery+ ──────────────[MC]─────────────── HV_BUS+
    │                   │
    │              Pre-charge Circuit
    │              ┌─[Resistor 100Ω]─[PC_Contactor]─┐
    │              │                                 │
    └──────────────┼─────────────────────────────────┘
                   │
              [HV_Fuse_200A]
                   │
             ┌─────┴─────┐
             │    BMS    │
             │           │
             │  B1+──────┼──── To Cell Stack +
             │  B1-──────┼──── To Cell Stack -
             │           │
             │  CAN_H────┼──── To VCU  
             │  CAN_L────┼──── To VCU
             │           │
             │  TEMP1────┼──── Temp Sensor 1
             │  TEMP2────┼──── Temp Sensor 2  
             │  TEMP3────┼──── Temp Sensor 3
             └───────────┘
                   │
Battery- ──────────┴─────────────────────── HV_BUS-

HV+ ────────┬──────────────────┬──── To Inverter +
            │                  │
        [Current Sensor]   [Voltage Divider]
            │                  │
            └──── To VCU ──────┘
```

### Wire Specifications
- **Main Power**: 25mm² stranded copper, rated 200A
- **CAN Bus**: Twisted pair, 120Ω characteristic impedance  
- **BMS Sense**: 22AWG, individual wires per cell group
- **Temperature**: 24AWG, shielded cable
- **Control**: 18AWG for contactors and pre-charge

### Connector Types
- **High Voltage**: Anderson Powerpole PP180 (180A rating)
- **CAN Bus**: Deutsch DT04-2P (automotive grade)
- **BMS**: Molex MicroFit 3.0 (small signal)
- **Temperature**: JST-XH series

- [ ] Wiring harness tasarımı tamamlandı
- [ ] Connector listesi hazırlandı

## Safety Systems

### Physical Protection
1. **Enclosure**: 
   - IP65 rated aluminum box
   - Ventilation for gas escape
   - Mechanical mounting for cells
   - External disconnect switch

2. **Fusing**:
   - Main fuse: 200A ANL fuse
   - BMS fuse: 10A blade fuse
   - Aux systems: 5A fuses

3. **Isolation Monitoring**:
   - Measure pack-to-chassis resistance
   - Alert below 1MΩ (TEKNOFEST requirement)
   - Automatic shutdown capability

### Thermal Management
1. **Temperature Sensors**:
   - 3× NTC thermistors across pack
   - Redundant monitoring via BMS
   - Emergency cutoff at 55°C

2. **Cooling Strategy**:
   - Natural convection (passive)
   - Optional: Forced air (fans)
   - Cell spacing for airflow

3. **Thermal Interface**:
   - Thermal pads between cells
   - Heat spreader plates
   - Temperature distribution measurement

### Emergency Systems
1. **Manual Disconnect**:
   - External emergency switch
   - Removes main contactor power
   - Accessible from outside vehicle

2. **Crash Detection**:
   - G-force sensor input to BMS
   - Automatic pack disconnection
   - Cannot be overridden

- [ ] Güvenlik sistemleri tasarlandı
- [ ] TEKNOFEST safety checklist hazırlandı

## Charging Procedure

### Charging Parameters (LiFePO4)
- **Maximum Voltage**: 3.65V per cell (54.75V pack)
- **Charging Current**: 0.5C rate = 30A (for 60Ah)
- **CC/CV Profile**: Constant current then constant voltage
- **Termination**: 0.05C current taper (3A)

### Charging Sequence
1. **Pre-charge Check**:
   ```
   - [ ] Visual inspection of pack
   - [ ] BMS communication established  
   - [ ] Cell voltages within range (3.0V - 3.6V)
   - [ ] Temperature acceptable (0°C - 45°C)
   - [ ] No fault codes active
   ```

2. **Connect Charger**:
   ```
   - [ ] Charger voltage verified (54.8V max)
   - [ ] Current limit set (30A max)
   - [ ] Connect positive first, then negative
   - [ ] Verify BMS sees charging current
   ```

3. **Monitoring During Charge**:
   ```
   - [ ] Cell voltages stay balanced (±50mV)
   - [ ] Temperature remains <40°C
   - [ ] Charging current decreases in CV mode
   - [ ] No BMS warning codes
   ```

4. **Charge Completion**:
   ```
   - [ ] All cells reach 3.65V ±10mV
   - [ ] Current drops below 3A
   - [ ] Temperature stable
   - [ ] SOC reads 100%
   - [ ] Disconnect charger (negative first)
   ```

### Charger Selection
- **Type**: Programmable DC power supply or Li-ion charger
- **Output**: 55V, 50A capability
- **CAN Interface**: Optional for automated charging
- **Candidate**: MeanWell HEP-600 series or custom

- [ ] Charging prosedürü dokümente edildi
- [ ] Charger seçimi yapıldı

## Assembly and Testing Checklist

### Pre-Assembly
- [ ] All cells tested individually (capacity, IR)
- [ ] Cell matching within 2% (capacity and voltage)
- [ ] BMS firmware updated to latest
- [ ] Enclosure prepared (insulation, mounting)

### Assembly Process
1. **Cell Installation**:
   - [ ] Cells secured mechanically
   - [ ] Terminal torque to specification
   - [ ] Series connections verified
   - [ ] No loose hardware

2. **BMS Wiring**:
   - [ ] Sense wires connected per manual
   - [ ] Polarity verified on all connections
   - [ ] Temperature sensors placed and secured
   - [ ] CAN bus wiring installed

3. **High-Voltage Wiring**:
   - [ ] Main power cables installed
   - [ ] Contactors and fuses mounted
   - [ ] Pre-charge circuit wired
   - [ ] All connections torqued to spec

### Initial Testing
1. **Low Voltage Tests**:
   ```
   - [ ] BMS powers up correctly
   - [ ] All cell voltages read correctly
   - [ ] Temperature sensors functional  
   - [ ] CAN communication established
   - [ ] Balancing function works
   ```

2. **Isolation Testing**:
   ```
   - [ ] Pack-to-chassis >1MΩ (both polarities)
   - [ ] No shorts between cells
   - [ ] Insulation integrity verified
   ```

3. **Functional Tests**:
   ```
   - [ ] Main contactor operation
   - [ ] Pre-charge sequence works
   - [ ] Emergency disconnect functional
   - [ ] Protection systems trigger correctly
   ```

### Final Validation
- [ ] Pack capacity test (full discharge/charge cycle)
- [ ] Efficiency measurement (charge/discharge)
- [ ] Temperature rise test under load
- [ ] Vibration test (if required)
- [ ] Documentation package complete

## Performance Specifications

### Nominal Ratings
- **Pack Voltage**: 46.8V nominal (40.95V - 54.75V range)
- **Capacity**: 55Ah (calculated)
- **Energy**: 2.574 kWh (under TEKNOFEST limit)
- **Continuous Current**: 100A (1.8C rate)
- **Peak Current**: 150A for 30 seconds

### Expected Performance
- **Range**: 40-60km (depends on vehicle efficiency)
- **Charge Time**: 2 hours at 30A (0-90% SOC)
- **Cycle Life**: 2000+ cycles (to 80% capacity)
- **Self-discharge**: <3% per month
- **Operating Temperature**: -10°C to +50°C

### Testing Results (To Be Filled)
- [ ] Actual capacity: ______ Ah
- [ ] Internal resistance: ______ mΩ
- [ ] Efficiency: ______%
- [ ] Max continuous current: ______ A
- [ ] Thermal performance: ______ °C rise

## Related Files
- [[AKS-Board]] - Battery monitoring interface
- [[CAN-Bus]] - BMS communication protocol  
- [[Guc-Kati]] - Motor drive system
- [[Malzeme-Listesi]] - Battery pack BOM

---
*Last Updated: 2026-03-28*
*Status: #todo - awaiting cell procurement and BMS selection*