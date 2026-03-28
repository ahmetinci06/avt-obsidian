# ESC Motor Commutation

#esc #commutation #bldc #hall #tim1

[[esc-motor-control]] | [[esc-sensors]] | [[vcu-state]] | [[Sensor-Baglantilari]]

## Genel Bakış

BLDC motor 6-step commutation implementasyonu. Hall sensör feedback ile rotor pozisyonu belirlenir ve uygun fazlara PWM uygulanır. TIM1 advanced timer ile motor fazlarının kontrolü.

> [!warning] Yüksek Güç Sistemi
> Yanlış commutation motor ve sürücü devrelerinde hasara neden olabilir.

## BLDC Motor Teorisi

### 3-Phase BLDC Fundamentals
- **Stator:** 3 faz (A, B, C) - 120° electrical spacing
- **Rotor:** Permanent magnet (2 pole = 1 pole pair)
- **Hall Sensörler:** 3 adet, 120° electrical spacing
- **Commutation:** 6-step switching sequence

### Electrical vs Mechanical Degrees
```
Pole Pairs = Motor Poles / 2
Electrical Degrees = Mechanical Degrees × Pole Pairs

Example: 14-pole motor
Pole Pairs = 7
360° mechanical = 2520° electrical
```

## Hall Sensor Reading

### Hall State Mapping
Hall sensörler rotora göre dijital pattern üretir:

| Hall A | Hall B | Hall C | Binary | Decimal | Sector | Rotor Position |
|--------|--------|--------|--------|---------|--------|----------------|
| 0 | 0 | 1 | 001 | 1 | 1 | 0-60° |
| 0 | 1 | 1 | 011 | 3 | 2 | 60-120° |
| 0 | 1 | 0 | 010 | 2 | 3 | 120-180° |
| 1 | 1 | 0 | 110 | 6 | 4 | 180-240° |
| 1 | 0 | 0 | 100 | 4 | 5 | 240-300° |
| 1 | 0 | 1 | 101 | 5 | 6 | 300-360° |

### Invalid Hall States
| Binary | Decimal | Status |
|--------|---------|--------|
| 000 | 0 | INVALID - All sensors OFF |
| 111 | 7 | INVALID - All sensors ON |

### Hall Reading Function
```c
uint8_t Read_Hall_Sensors(void) {
    uint8_t hall_state = 0;
    
    if (HAL_GPIO_ReadPin(HALL_A_GPIO_Port, HALL_A_Pin)) hall_state |= 0x04;
    if (HAL_GPIO_ReadPin(HALL_B_GPIO_Port, HALL_B_Pin)) hall_state |= 0x02; 
    if (HAL_GPIO_ReadPin(HALL_C_GPIO_Port, HALL_C_Pin)) hall_state |= 0x01;
    
    return hall_state;
}
```

## 6-Step Commutation Table

### Switching Pattern
Her sector için hangi fazların nasıl sürülmesi gerektiği:

| Sector | Hall | Phase A | Phase B | Phase C | Description |
|--------|------|---------|---------|---------|-------------|
| 1 | 001 (1) | FLOAT | HIGH (PWM) | LOW (ON) | B→C current |
| 2 | 011 (3) | LOW (ON) | HIGH (PWM) | FLOAT | B→A current |
| 3 | 010 (2) | LOW (ON) | FLOAT | HIGH (PWM) | C→A current |
| 4 | 110 (6) | FLOAT | LOW (ON) | HIGH (PWM) | C→B current |
| 5 | 100 (4) | HIGH (PWM) | LOW (ON) | FLOAT | A→B current |
| 6 | 101 (5) | HIGH (PWM) | FLOAT | LOW (ON) | A→C current |

### PWM State Definitions
```c
typedef enum {
    PWM_FLOAT = 0,  // High-Z (both MOSFETs OFF)
    PWM_LOW = 1,    // Low side ON, high side OFF
    PWM_HIGH = 2    // High side PWM, low side OFF
} PWM_State_t;
```

## TIM1 Configuration

### Timer Setup for 3-Phase PWM
```c
TIM_HandleTypeDef htim1;

void TIM1_Init(void) {
    TIM_ClockConfigTypeDef sClockSourceConfig;
    TIM_MasterConfigTypeDef sMasterConfig;
    TIM_OC_InitTypeDef sConfigOC;
    TIM_BreakDeadTimeConfigTypeDef sBreakDeadTimeConfig;
    
    htim1.Instance = TIM1;
    htim1.Init.Prescaler = 0;           // 80MHz / (0+1) = 80MHz
    htim1.Init.CounterMode = TIM_COUNTERMODE_CENTERALIGNED1;
    htim1.Init.Period = 1999;           // 80MHz / (2000) = 40kHz PWM
    htim1.Init.ClockDivision = TIM_CLOCKDIVISION_DIV1;
    htim1.Init.AutoReloadPreload = TIM_AUTORELOAD_PRELOAD_ENABLE;
    
    if (HAL_TIM_Base_Init(&htim1) != HAL_OK) {
        Error_Handler();
    }
    
    // PWM configuration
    sConfigOC.OCMode = TIM_OCMODE_PWM1;
    sConfigOC.Pulse = 0;                // Initial duty = 0%
    sConfigOC.OCPolarity = TIM_OCPOLARITY_HIGH;
    sConfigOC.OCNPolarity = TIM_OCNPOLARITY_HIGH;
    sConfigOC.OCFastMode = TIM_OCFAST_DISABLE;
    sConfigOC.OCIdleState = TIM_OCIDLESTATE_RESET;
    sConfigOC.OCNIdleState = TIM_OCNIDLESTATE_RESET;
    
    // Configure all 3 channels
    HAL_TIM_PWM_ConfigChannel(&htim1, &sConfigOC, TIM_CHANNEL_1); // Phase A
    HAL_TIM_PWM_ConfigChannel(&htim1, &sConfigOC, TIM_CHANNEL_2); // Phase B  
    HAL_TIM_PWM_ConfigChannel(&htim1, &sConfigOC, TIM_CHANNEL_3); // Phase C
    
    // Dead time configuration (prevent shoot-through)
    sBreakDeadTimeConfig.OffStateRunMode = TIM_OSSR_DISABLE;
    sBreakDeadTimeConfig.OffStateIDLEMode = TIM_OSSI_DISABLE;
    sBreakDeadTimeConfig.LockLevel = TIM_LOCKLEVEL_OFF;
    sBreakDeadTimeConfig.DeadTime = 40;     // 500ns @ 80MHz
    sBreakDeadTimeConfig.BreakState = TIM_BREAK_ENABLE;
    sBreakDeadTimeConfig.BreakPolarity = TIM_BREAKPOLARITY_LOW;
    sBreakDeadTimeConfig.AutomaticOutput = TIM_AUTOMATICOUTPUT_DISABLE;
    
    if (HAL_TIMEx_ConfigBreakDeadTime(&htim1, &sBreakDeadTimeConfig) != HAL_OK) {
        Error_Handler();
    }
}
```

### PWM Channel Mapping
| Channel | Phase | High Side Pin | Low Side Pin |
|---------|-------|---------------|--------------|
| TIM1_CH1 | Phase A | PA8 | PA7 (TIM1_CH1N) |
| TIM1_CH2 | Phase B | PA9 | PB0 (TIM1_CH2N) |
| TIM1_CH3 | Phase C | PA10 | PB1 (TIM1_CH3N) |

## Commutation Implementation

### Main Commutation Function
```c
void Commutate_Motor(uint8_t hall_state, uint16_t pwm_duty) {
    // Validate hall state
    if (hall_state == 0 || hall_state == 7) {
        // Invalid hall state - turn off all phases
        Motor_Emergency_Stop();
        Set_Fault(FAULT_HALL_INVALID);
        return;
    }
    
    // Apply commutation pattern
    switch (hall_state) {
        case 1: // Sector 1: B+, C-
            Set_Phase_State(PHASE_A, PWM_FLOAT);
            Set_Phase_State(PHASE_B, PWM_HIGH, pwm_duty);
            Set_Phase_State(PHASE_C, PWM_LOW);
            break;
            
        case 3: // Sector 2: B+, A-  
            Set_Phase_State(PHASE_A, PWM_LOW);
            Set_Phase_State(PHASE_B, PWM_HIGH, pwm_duty);
            Set_Phase_State(PHASE_C, PWM_FLOAT);
            break;
            
        case 2: // Sector 3: C+, A-
            Set_Phase_State(PHASE_A, PWM_LOW);
            Set_Phase_State(PHASE_B, PWM_FLOAT);
            Set_Phase_State(PHASE_C, PWM_HIGH, pwm_duty);
            break;
            
        case 6: // Sector 4: C+, B-
            Set_Phase_State(PHASE_A, PWM_FLOAT);
            Set_Phase_State(PHASE_B, PWM_LOW);
            Set_Phase_State(PHASE_C, PWM_HIGH, pwm_duty);
            break;
            
        case 4: // Sector 5: A+, B-
            Set_Phase_State(PHASE_A, PWM_HIGH, pwm_duty);
            Set_Phase_State(PHASE_B, PWM_LOW);
            Set_Phase_State(PHASE_C, PWM_FLOAT);
            break;
            
        case 5: // Sector 6: A+, C-
            Set_Phase_State(PHASE_A, PWM_HIGH, pwm_duty);
            Set_Phase_State(PHASE_B, PWM_FLOAT);
            Set_Phase_State(PHASE_C, PWM_LOW);
            break;
            
        default:
            // Unknown state - emergency stop
            Motor_Emergency_Stop();
            Set_Fault(FAULT_HALL_SEQUENCE);
            break;
    }
}
```

### Phase State Control
```c
void Set_Phase_State(uint8_t phase, PWM_State_t state, uint16_t duty) {
    uint32_t channel;
    
    // Select appropriate channel
    switch (phase) {
        case PHASE_A: channel = TIM_CHANNEL_1; break;
        case PHASE_B: channel = TIM_CHANNEL_2; break;
        case PHASE_C: channel = TIM_CHANNEL_3; break;
        default: return;
    }
    
    switch (state) {
        case PWM_FLOAT:
            // Disable both high and low side
            HAL_TIM_PWM_Stop(&htim1, channel);
            HAL_TIMEx_PWMN_Stop(&htim1, channel);
            break;
            
        case PWM_LOW:
            // Low side ON, high side OFF
            HAL_TIM_PWM_Stop(&htim1, channel);
            HAL_TIMEx_PWMN_Start(&htim1, channel);
            __HAL_TIM_SET_COMPARE(&htim1, channel, htim1.Init.Period); // 100% duty for low side
            break;
            
        case PWM_HIGH:
            // High side PWM, low side OFF
            HAL_TIMEx_PWMN_Stop(&htim1, channel);
            __HAL_TIM_SET_COMPARE(&htim1, channel, duty);
            HAL_TIM_PWM_Start(&htim1, channel);
            break;
    }
}
```

## CCER Register Control

### TIM1_CCER Bit Mapping
Capture/Compare Enable Register kontrolü:

```c
// CCER register bits for TIM1
#define TIM1_CCER_CC1E   (1 << 0)  // Channel 1 output enable
#define TIM1_CCER_CC1P   (1 << 1)  // Channel 1 polarity
#define TIM1_CCER_CC1NE  (1 << 2)  // Channel 1 complementary output enable
#define TIM1_CCER_CC1NP  (1 << 3)  // Channel 1 complementary polarity

#define TIM1_CCER_CC2E   (1 << 4)  // Channel 2 output enable  
#define TIM1_CCER_CC2P   (1 << 5)  // Channel 2 polarity
#define TIM1_CCER_CC2NE  (1 << 6)  // Channel 2 complementary output enable
#define TIM1_CCER_CC2NP  (1 << 7)  // Channel 2 complementary polarity

#define TIM1_CCER_CC3E   (1 << 8)  // Channel 3 output enable
#define TIM1_CCER_CC3P   (1 << 9)  // Channel 3 polarity  
#define TIM1_CCER_CC3NE  (1 << 10) // Channel 3 complementary output enable
#define TIM1_CCER_CC3NP  (1 << 11) // Channel 3 complementary polarity
```

### Direct CCER Manipulation
```c
void Set_CCER_Direct(uint8_t hall_state, uint16_t duty_cycle) {
    // Disable all outputs first
    TIM1->CCER = 0x0000;
    
    // Set duty cycles
    TIM1->CCR1 = duty_cycle;
    TIM1->CCR2 = duty_cycle;  
    TIM1->CCR3 = duty_cycle;
    
    // Configure CCER based on hall state
    switch (hall_state) {
        case 1: // B+ C- (B high-side PWM, C low-side ON)
            TIM1->CCER = TIM1_CCER_CC2E | TIM1_CCER_CC3NE;
            break;
            
        case 3: // B+ A- (B high-side PWM, A low-side ON)
            TIM1->CCER = TIM1_CCER_CC2E | TIM1_CCER_CC1NE;
            break;
            
        case 2: // C+ A- (C high-side PWM, A low-side ON)
            TIM1->CCER = TIM1_CCER_CC3E | TIM1_CCER_CC1NE;
            break;
            
        case 6: // C+ B- (C high-side PWM, B low-side ON)  
            TIM1->CCER = TIM1_CCER_CC3E | TIM1_CCER_CC2NE;
            break;
            
        case 4: // A+ B- (A high-side PWM, B low-side ON)
            TIM1->CCER = TIM1_CCER_CC1E | TIM1_CCER_CC2NE;
            break;
            
        case 5: // A+ C- (A high-side PWM, C low-side ON)
            TIM1->CCER = TIM1_CCER_CC1E | TIM1_CCER_CC3NE;
            break;
    }
}
```

## Emergency Stop & Safety

### Motor Emergency Stop
```c
void Motor_Emergency_Stop(void) {
    // Immediately disable all PWM outputs
    TIM1->CCER = 0x0000;
    
    // Force MOE (Master Output Enable) low
    TIM1->BDTR &= ~TIM_BDTR_MOE;
    
    // Clear all duty cycles
    TIM1->CCR1 = 0;
    TIM1->CCR2 = 0;
    TIM1->CCR3 = 0;
    
    // Set emergency stop flag
    motor_control.emergency_stop = true;
    motor_control.state = MOTOR_STATE_FAULT;
}
```

### Safe Restart Procedure
```c
bool Motor_Safe_Restart(void) {
    // Clear emergency stop flag
    motor_control.emergency_stop = false;
    
    // Verify hall sensor readings
    uint8_t hall_state = Read_Hall_Sensors();
    if (hall_state == 0 || hall_state == 7) {
        return false; // Still invalid
    }
    
    // Re-enable master output
    TIM1->BDTR |= TIM_BDTR_MOE;
    
    // Start with zero duty cycle
    motor_control.duty_cycle = 0;
    motor_control.state = MOTOR_STATE_READY;
    
    return true;
}
```

## Troubleshooting Guide

### Common Issues & Solutions

#### 1. Motor Jerky/Vibrating
**Symptoms:** Motor doesn't run smoothly, jerks or vibrates
**Possible Causes:**
- Hall sensor alignment incorrect
- Commutation sequence wrong
- PWM frequency too low
- Dead time too long

**Debug Steps:**
```c
// Check hall sequence during manual rotation
void Debug_Hall_Sequence(void) {
    static uint8_t last_hall = 0;
    uint8_t current_hall = Read_Hall_Sensors();
    
    if (current_hall != last_hall) {
        printf("Hall: %d -> %d\n", last_hall, current_hall);
        last_hall = current_hall;
    }
}

// Expected forward sequence: 1->3->2->6->4->5->1
// Expected reverse sequence: 1->5->4->6->2->3->1
```

#### 2. Motor Won't Start
**Symptoms:** No movement despite PWM signals
**Possible Causes:**
- Hall sensors not connected
- MOSFET gate drive insufficient
- Motor phases swapped
- Back-EMF too high (motor spinning)

**Debug Steps:**
```c
// Test individual phase outputs
void Test_Phase_Outputs(void) {
    // Test Phase A
    Set_Phase_State(PHASE_A, PWM_HIGH, 1000);  // 50% duty
    HAL_Delay(1000);
    Set_Phase_State(PHASE_A, PWM_FLOAT);
    
    // Repeat for B and C phases
}
```

#### 3. Hall Sensor Errors
**Symptoms:** Invalid hall states, erratic readings
**Possible Causes:**
- Sensor wiring incorrect
- Pull-up resistors missing
- EMI interference
- Sensor position wrong

**Debug Steps:**
```c
// Monitor hall sensors continuously  
void Debug_Hall_Sensors(void) {
    while (1) {
        uint8_t hall = Read_Hall_Sensors();
        printf("Hall A:%d B:%d C:%d State:%d\n",
            !!(hall & 0x04), !!(hall & 0x02), !!(hall & 0x01), hall);
        HAL_Delay(100);
    }
}
```

#### 4. MOSFETs Overheating
**Symptoms:** Power MOSFETs getting very hot
**Possible Causes:**
- Shoot-through current (dead time too short)
- PWM frequency too high
- Gate drive voltage insufficient
- Thermal design inadequate

**Solutions:**
- Increase dead time to 1-2μs
- Reduce PWM frequency to 20kHz
- Verify gate drive voltage (>10V for logic level MOSFETs)
- Improve heatsinking

### Commutation Sequence Validation
```c
bool Validate_Hall_Sequence(void) {
    uint8_t sequence[6];
    uint8_t index = 0;
    uint8_t last_hall = Read_Hall_Sensors();
    
    // Manually rotate motor and record sequence
    for (int i = 0; i < 10000; i++) {  // Wait for 6 transitions
        uint8_t current_hall = Read_Hall_Sensors();
        
        if (current_hall != last_hall && index < 6) {
            sequence[index++] = current_hall;
            last_hall = current_hall;
        }
        
        if (index >= 6) break;
        HAL_Delay(1);
    }
    
    // Check for expected forward sequence: 1,3,2,6,4,5
    uint8_t expected[6] = {1, 3, 2, 6, 4, 5};
    for (int i = 0; i < 6; i++) {
        if (sequence[i] != expected[i]) {
            printf("Sequence error at position %d: expected %d, got %d\n", 
                   i, expected[i], sequence[i]);
            return false;
        }
    }
    
    return true;
}
```

## Performance Optimization

### High-Speed Commutation
```c
// Optimized commutation for high-speed operation
void Fast_Commutate(uint8_t hall_state, uint16_t duty) {
    // Use lookup table for fast switching
    static const uint16_t ccer_lut[8] = {
        0x0000,  // Invalid state 0
        0x0044,  // State 1: CC2E | CC3NE
        0x0022,  // State 2: CC3E | CC1NE  
        0x0042,  // State 3: CC2E | CC1NE
        0x0021,  // State 4: CC1E | CC2NE
        0x0041,  // State 5: CC1E | CC3NE
        0x0024,  // State 6: CC3E | CC2NE
        0x0000   // Invalid state 7
    };
    
    // Atomic update to minimize switching glitches
    __disable_irq();
    TIM1->CCR1 = duty;
    TIM1->CCR2 = duty;
    TIM1->CCR3 = duty;
    TIM1->CCER = ccer_lut[hall_state];
    __enable_irq();
}
```

### Current Measurements
```c
// Measure current during different commutation states
typedef struct {
    uint16_t phase_a_current;
    uint16_t phase_b_current; 
    uint16_t phase_c_current;
    uint8_t hall_state;
    uint32_t timestamp;
} Commutation_Data_t;

void Log_Commutation_Data(void) {
    static Commutation_Data_t log_buffer[100];
    static uint8_t log_index = 0;
    
    if (log_index < 100) {
        log_buffer[log_index].phase_a_current = ADC_Read_Phase_A_Current();
        log_buffer[log_index].phase_b_current = ADC_Read_Phase_B_Current();
        log_buffer[log_index].phase_c_current = ADC_Read_Phase_C_Current();
        log_buffer[log_index].hall_state = Read_Hall_Sensors();
        log_buffer[log_index].timestamp = HAL_GetTick();
        log_index++;
    }
}
```

## İlgili Dokümanlar
- [[esc-motor-control]] - Overall motor control strategy
- [[esc-sensors]] - Hall sensor interface details  
- [[Sensor-Baglantilari]] - Hardware sensor connections
- [[vcu-state]] - State machine integration
- [[Test-Proseduru]] - Motor testing procedures

## Referanslar
- AN4013: STM32 PMSM Motor Control SDK
- TIM1 Advanced Timer Reference Manual
- BLDC Motor Control Theory (Texas Instruments)
- 6-Step Commutation Application Note (Microchip)