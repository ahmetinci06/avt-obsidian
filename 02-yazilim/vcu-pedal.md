# VCU Pedal Module

> **Dosya:** `vcu_pedal.c` / `vcu_pedal.h`
> **Amaç:** Gaz pedalı ADC okuma, filtreleme, hata tespiti ve kalibrasyonu
> **Pin:** PC0 (ADC1_IN6) - Throttle input (0-5V)

---

## 📋 Data Structures

### PedalInput_t
```c
typedef struct {
    uint16_t raw_adc;           // Ham ADC değeri (0-4095)
    uint16_t filtered_adc;      // Filtrelenmiş ADC değeri
    float    percent;           // Pedal yüzdesi (0.0-100.0)
    uint32_t last_update_ms;    // Son güncelleme zamanı
    uint8_t  fault_flags;       // Hata bayrakları (PedalFault_t)
    uint16_t min_cal;          // Kalibrasyondaki minimum değer
    uint16_t max_cal;          // Kalibrasyondaki maksimum değer
    uint8_t  calibration_valid; // Kalibrasyon geçerli mi? (0/1)
} PedalInput_t;
```

### PedalFault_t
```c
typedef enum {
    PEDAL_FAULT_OK             = 0x00,
    PEDAL_FAULT_WIRE_BREAK     = 0x01,  // Kablo kopması (ADC < MIN_VALID)
    PEDAL_FAULT_SHORT_CIRCUIT  = 0x02,  // Kısa devre (ADC > MAX_VALID)
    PEDAL_FAULT_OUT_OF_RANGE   = 0x04,  // Kalibrasyon aralığı dışı
    PEDAL_FAULT_NO_CALIBRATION = 0x08   // Kalibrasyon yapılmamış
} PedalFault_t;
```

---

## 🔧 Sabitler ve Threshold'lar

### Hardware Limits
```c
#define PEDAL_MIN_VALID      200   // Minimum geçerli ADC değeri (wire break detection)
#define PEDAL_MAX_VALID     3800   // Maksimum geçerli ADC değeri (short circuit detection)
#define PEDAL_ADC_MAX       4095   // 12-bit ADC maksimum değeri

// Default calibration values (if no user calibration)
#define PEDAL_IDLE_DEFAULT   250   // Varsayılan boşta değeri
#define PEDAL_FULL_DEFAULT  3600   // Varsayılan tam basıldı değeri
#define PEDAL_DEADBAND        50   // Alt %5 deadband bölgesi
```

### Filter Constants
```c
#define PEDAL_FILTER_ALPHA   0.1f  // Low-pass filter katsayısı (0.1 = smooth)
#define PEDAL_UPDATE_RATE_MS  10   // 10ms update rate (100Hz)
```

---

## 📚 Function Prototypes

### Initialization
```c
/**
 * @brief Pedal modülünü başlat
 * @param pedal Pointer to PedalInput_t structure
 * @return HAL_StatusTypeDef
 */
HAL_StatusTypeDef VCU_Pedal_Init(PedalInput_t *pedal);
```

### Core Functions
```c
/**
 * @brief Ham ADC değerini oku ve işle
 * @param pedal Pointer to PedalInput_t structure
 * @param adc_raw Ham ADC değeri (0-4095)
 * @return HAL_StatusTypeDef
 */
HAL_StatusTypeDef VCU_Pedal_Read(PedalInput_t *pedal, uint16_t adc_raw);

/**
 * @brief Mevcut pedal yüzdesini al
 * @param pedal Pointer to PedalInput_t structure
 * @return float Pedal percentage (0.0-100.0), hata durumunda -1.0
 */
float VCU_Pedal_GetPercent(PedalInput_t *pedal);

/**
 * @brief Pedal hata durumunu kontrol et
 * @param pedal Pointer to PedalInput_t structure
 * @return uint8_t Hata bayrakları (PedalFault_t)
 */
uint8_t VCU_Pedal_HasFault(PedalInput_t *pedal);
```

### Calibration Functions
```c
/**
 * @brief Manuel kalibrasyon değerlerini ayarla
 * @param pedal Pointer to PedalInput_t structure
 * @param min_val Minimum ADC değeri (boşta)
 * @param max_val Maksimum ADC değeri (tam basıldı)
 * @return HAL_StatusTypeDef
 */
HAL_StatusTypeDef VCU_Pedal_SetCalibration(PedalInput_t *pedal, uint16_t min_val, uint16_t max_val);

/**
 * @brief Otomatik kalibrasyon başlat
 * @param pedal Pointer to PedalInput_t structure
 * @return HAL_StatusTypeDef
 */
HAL_StatusTypeDef VCU_Pedal_StartAutoCalibration(PedalInput_t *pedal);

/**
 * @brief Mevcut ADC değerini minimum olarak kaydet
 * @param pedal Pointer to PedalInput_t structure
 * @return HAL_StatusTypeDef
 */
HAL_StatusTypeDef VCU_Pedal_CalibrateMin(PedalInput_t *pedal);

/**
 * @brief Mevcut ADC değerini maksimum olarak kaydet
 * @param pedal Pointer to PedalInput_t structure
 * @return HAL_StatusTypeDef
 */
HAL_StatusTypeDef VCU_Pedal_CalibrateMax(PedalInput_t *pedal);
```

---

## 💻 Implementation Details

### VCU_Pedal_Init
```c
HAL_StatusTypeDef VCU_Pedal_Init(PedalInput_t *pedal) {
    if (pedal == NULL) return HAL_ERROR;
    
    // Initialize structure
    pedal->raw_adc = 0;
    pedal->filtered_adc = 0;
    pedal->percent = 0.0f;
    pedal->last_update_ms = HAL_GetTick();
    pedal->fault_flags = PEDAL_FAULT_NO_CALIBRATION;
    
    // Set default calibration
    pedal->min_cal = PEDAL_IDLE_DEFAULT;
    pedal->max_cal = PEDAL_FULL_DEFAULT;
    pedal->calibration_valid = 0;  // User calibration required
    
    return HAL_OK;
}
```

### VCU_Pedal_Read
```c
HAL_StatusTypeDef VCU_Pedal_Read(PedalInput_t *pedal, uint16_t adc_raw) {
    if (pedal == NULL) return HAL_ERROR;
    
    uint32_t current_time = HAL_GetTick();
    
    // Rate limiting - only update every PEDAL_UPDATE_RATE_MS
    if ((current_time - pedal->last_update_ms) < PEDAL_UPDATE_RATE_MS) {
        return HAL_OK;  // Skip this reading
    }
    
    // Store raw value
    pedal->raw_adc = adc_raw;
    pedal->last_update_ms = current_time;
    
    // Clear previous faults
    pedal->fault_flags = PEDAL_FAULT_OK;
    
    // Hardware fault detection
    if (adc_raw < PEDAL_MIN_VALID) {
        pedal->fault_flags |= PEDAL_FAULT_WIRE_BREAK;
        pedal->percent = 0.0f;
        return HAL_OK;
    }
    
    if (adc_raw > PEDAL_MAX_VALID) {
        pedal->fault_flags |= PEDAL_FAULT_SHORT_CIRCUIT;
        pedal->percent = 0.0f;
        return HAL_OK;
    }
    
    // Check calibration validity
    if (!pedal->calibration_valid) {
        pedal->fault_flags |= PEDAL_FAULT_NO_CALIBRATION;
        // Use default values for basic operation
    }
    
    // Low-pass filter: y[n] = α×x[n] + (1-α)×y[n-1]
    pedal->filtered_adc = (uint16_t)(PEDAL_FILTER_ALPHA * adc_raw + 
                                   (1.0f - PEDAL_FILTER_ALPHA) * pedal->filtered_adc);
    
    // Range check against calibration
    if (pedal->calibration_valid && 
        (pedal->filtered_adc < (pedal->min_cal - PEDAL_DEADBAND) ||
         pedal->filtered_adc > (pedal->max_cal + PEDAL_DEADBAND))) {
        pedal->fault_flags |= PEDAL_FAULT_OUT_OF_RANGE;
    }
    
    // Calculate percentage
    uint16_t min_val = pedal->min_cal;
    uint16_t max_val = pedal->max_cal;
    
    if (pedal->filtered_adc <= min_val + PEDAL_DEADBAND) {
        pedal->percent = 0.0f;  // Deadband: force to 0%
    } else if (pedal->filtered_adc >= max_val) {
        pedal->percent = 100.0f;  // Cap at 100%
    } else {
        // Linear mapping: (adc - min) / (max - min) * 100
        pedal->percent = ((float)(pedal->filtered_adc - min_val) / 
                         (float)(max_val - min_val)) * 100.0f;
        
        // Ensure bounds
        if (pedal->percent < 0.0f) pedal->percent = 0.0f;
        if (pedal->percent > 100.0f) pedal->percent = 100.0f;
    }
    
    return HAL_OK;
}
```

### VCU_Pedal_GetPercent
```c
float VCU_Pedal_GetPercent(PedalInput_t *pedal) {
    if (pedal == NULL) return -1.0f;
    
    // Return 0% if any fault is present
    if (pedal->fault_flags != PEDAL_FAULT_OK) {
        return 0.0f;
    }
    
    return pedal->percent;
}
```

### VCU_Pedal_SetCalibration
```c
HAL_StatusTypeDef VCU_Pedal_SetCalibration(PedalInput_t *pedal, uint16_t min_val, uint16_t max_val) {
    if (pedal == NULL) return HAL_ERROR;
    if (min_val >= max_val) return HAL_ERROR;
    if (min_val < PEDAL_MIN_VALID || max_val > PEDAL_MAX_VALID) return HAL_ERROR;
    
    pedal->min_cal = min_val;
    pedal->max_cal = max_val;
    pedal->calibration_valid = 1;
    
    // Clear calibration fault
    pedal->fault_flags &= ~PEDAL_FAULT_NO_CALIBRATION;
    
    return HAL_OK;
}
```

---

## 📏 Calibration Procedure

### Manual Calibration (Recommended)

#### Step 1: Idle Calibration
```c
// 1. Ensure pedal is not pressed (idle position)
// 2. Read current ADC value
uint16_t idle_adc = /* current ADC reading */;

// 3. Validate reading
if (idle_adc < PEDAL_MIN_VALID || idle_adc > PEDAL_MAX_VALID) {
    // ERROR: Hardware fault detected
    return HAL_ERROR;
}

// 4. Store minimum value
pedal.min_cal = idle_adc;
```

#### Step 2: Full Press Calibration
```c
// 1. Press pedal to 100% position (full throttle)
// 2. Read current ADC value  
uint16_t full_adc = /* current ADC reading */;

// 3. Validate reading
if (full_adc < PEDAL_MIN_VALID || full_adc > PEDAL_MAX_VALID) {
    // ERROR: Hardware fault detected
    return HAL_ERROR;
}

// 4. Ensure proper range
if (full_adc <= pedal.min_cal + 100) {
    // ERROR: Insufficient range (less than ~2.5% of ADC range)
    return HAL_ERROR;
}

// 5. Store maximum value and validate calibration
pedal.max_cal = full_adc;
pedal.calibration_valid = 1;
```

#### Step 3: Verification
```c
// Test various pedal positions and verify smooth operation
for (int i = 0; i <= 10; i++) {
    printf("Position %d0%: Press pedal and press Enter\n", i);
    // Read ADC and display percentage
    float expected = i * 10.0f;
    float actual = VCU_Pedal_GetPercent(&pedal);
    printf("Expected: %.1f%%, Actual: %.1f%%\n", expected, actual);
}
```

### Automatic Calibration (Alternative)
```c
// Auto-calibration over a period of time
void VCU_Pedal_AutoCalibrate(PedalInput_t *pedal, uint16_t current_adc) {
    static uint16_t min_seen = 4095;
    static uint16_t max_seen = 0;
    static uint32_t start_time = 0;
    
    if (start_time == 0) {
        start_time = HAL_GetTick();
        min_seen = current_adc;
        max_seen = current_adc;
    }
    
    // Track extremes
    if (current_adc < min_seen) min_seen = current_adc;
    if (current_adc > max_seen) max_seen = current_adc;
    
    // After 30 seconds of operation, set calibration
    if (HAL_GetTick() - start_time > 30000) {
        if ((max_seen - min_seen) > 200) {  // Sufficient range observed
            VCU_Pedal_SetCalibration(pedal, min_seen, max_seen);
        }
        start_time = 0;  // Reset for next calibration cycle
    }
}
```

---

## 🧪 Test Results Template

### Test Configuration
```yaml
Test Date: ____/____/____
Hardware Version: VCU v2.1
Software Version: ____
Tester: ____
Temperature: ____°C
Supply Voltage: ____V
```

### Test Cases

#### TC1: Hardware Limits
| Test | Expected ADC | Measured ADC | Pass/Fail | Notes |
|------|--------------|--------------|-----------|-------|
| Wire disconnected | < 200 | ____ | _____ | Should trigger WIRE_BREAK fault |
| 5V applied directly | > 3800 | ____ | _____ | Should trigger SHORT_CIRCUIT fault |
| Ground short | < 200 | ____ | _____ | Should trigger WIRE_BREAK fault |

#### TC2: Calibration Test
| Position | ADC Value | Percentage | Pass/Fail | Notes |
|----------|-----------|------------|-----------|-------|
| Idle (0%) | ____ | 0.0% | _____ | |
| 25% pressed | ____ | ~25% | _____ | ±5% tolerance |
| 50% pressed | ____ | ~50% | _____ | ±5% tolerance |
| 75% pressed | ____ | ~75% | _____ | ±5% tolerance |
| Full (100%) | ____ | 100.0% | _____ | |

#### TC3: Filter Response
```c
// Inject step input and measure filter response
uint16_t test_values[] = {500, 3000, 500, 3000, 500};
for (int i = 0; i < 5; i++) {
    VCU_Pedal_Read(&pedal, test_values[i]);
    printf("Input: %d, Filtered: %d, Output: %.1f%%\n", 
           test_values[i], pedal.filtered_adc, pedal.percent);
    HAL_Delay(20);  // 20ms delay
}
```

#### TC4: Fault Detection
| Fault Type | Trigger Condition | Expected Flag | Measured | Pass/Fail |
|------------|------------------|---------------|----------|-----------|
| Wire Break | ADC < 200 | 0x01 | ____ | _____ |
| Short Circuit | ADC > 3800 | 0x02 | ____ | _____ |
| Out of Range | ADC outside calibration | 0x04 | ____ | _____ |
| No Calibration | Before calibration | 0x08 | ____ | _____ |

### Performance Metrics
- **Filter settling time:** _____ ms (90% of final value)
- **Update rate:** _____ Hz (should be ~100Hz)
- **Linearity error:** _____% (max deviation from linear)
- **Repeatability:** _____% (standard deviation over 100 readings)

---

## 🔧 Troubleshooting Guide

### Common Issues

#### Issue 1: Pedal percentage always 0%
**Possible Causes:**
- No calibration performed (`PEDAL_FAULT_NO_CALIBRATION`)
- ADC reading below `PEDAL_MIN_VALID` (`PEDAL_FAULT_WIRE_BREAK`)
- Any fault flag set

**Solution:**
1. Check `VCU_Pedal_HasFault()` return value
2. Perform proper calibration
3. Verify hardware connections

#### Issue 2: Erratic pedal readings
**Possible Causes:**
- Electrical noise on ADC input
- Poor connections
- Filter constant too high

**Solution:**
1. Check ADC reference voltage stability
2. Verify cable shielding
3. Reduce `PEDAL_FILTER_ALPHA` (more smoothing)
4. Add hardware RC filter

#### Issue 3: Slow response
**Possible Causes:**
- Filter constant too low (over-smoothing)
- Update rate too low

**Solution:**
1. Increase `PEDAL_FILTER_ALPHA` (less smoothing)
2. Reduce `PEDAL_UPDATE_RATE_MS`

#### Issue 4: Non-linear response
**Possible Causes:**
- Potentiometer non-linearity
- Incorrect calibration
- Temperature effects

**Solution:**
1. Re-calibrate with proper mechanical positioning
2. Consider 3-point calibration for better linearity
3. Use lookup table instead of linear mapping

### Debug Commands
```c
// Diagnostic function for debugging
void VCU_Pedal_Debug(PedalInput_t *pedal) {
    printf("=== Pedal Debug Info ===\n");
    printf("Raw ADC: %d\n", pedal->raw_adc);
    printf("Filtered ADC: %d\n", pedal->filtered_adc);
    printf("Percentage: %.2f%%\n", pedal->percent);
    printf("Calibration: %d - %d (valid: %d)\n", 
           pedal->min_cal, pedal->max_cal, pedal->calibration_valid);
    printf("Fault flags: 0x%02X\n", pedal->fault_flags);
    printf("Last update: %lu ms ago\n", 
           HAL_GetTick() - pedal->last_update_ms);
}
```

---

## 🏷️ Tags
#yazilim #vcu #pedal #adc #sensor #kalibrasyon #fault-detection #filter

## 🔗 İlgili Dosyalar
- [[CubeMX-Config]] - ADC1 konfigürasyonu
- [[vcu-state]] - Pedal hatalarının state machine'e etkisi
- [[esc-motor-control]] - Pedal yüzdesi motor kontrolüne aktarımı
- [[Test-Proseduru]] - TC4: Throttle ADC testi

## ⚠️ Safety Notes

> [!WARNING] Güvenlik Uyarıları
> - Pedal hatası durumunda MUTLAKA motor outputu kesilmeli
> - Kalibrasyonsuz çalışma sadece test amaçlı yapılmalı
> - Filtreleme agresif olmalı - ani değişimler güvenlik riski

> [!INFO] Kalibrasyon Önerisi  
> - Her araç açılışında kalibrasyon validitesi kontrol edilmeli
> - Sıcaklık değişimlerinde yeniden kalibrasyon önerilir
> - Min/Max arasındaki fark en az 500 ADC count olmalı (yeterli çözünürlük)