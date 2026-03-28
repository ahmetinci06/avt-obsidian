# CubeMX Konfigürasyonu - STM32G474RE

> **VCU (Vehicle Control Unit)** - AVT Racing Electric Vehicle
> **MCU:** STM32G474RE (LQFP64)
> **Proje:** AVT-VCU-2024

---

## 📋 Pin Assignments

### **Analog Inputs**
| Pin | Signal | ADC Channel | Peripheral | Usage |
|-----|--------|-------------|------------|-------|
| PC0 | Throttle | ADC1_IN6 | ADC1 | Gaz pedalı (0-5V) |
| PC1 | Current_Sense | ADC1_IN7 | ADC1 | Motor akımı (0-3.3V) |
| PA0 | DC_Bus_Voltage | ADC1_IN1 | ADC1 | Battery voltage divider |

### **Digital Inputs**
| Pin | Signal | Config | Pull | Usage |
|-----|--------|--------|------|-------|
| PC5 | Brake | GPIO_Input | No Pull | Fren sinyali (R16=4.7k pull-down) |
| PB0 | Hall_A | GPIO_EXTI0 | No Pull | Hall sensor A |
| PB1 | Hall_B | GPIO_EXTI1 | No Pull | Hall sensor B |
| PB2 | Hall_C | GPIO_EXTI2 | No Pull | Hall sensor C |

### **PWM Outputs (TIM1)**
| Pin | Signal | Timer Channel | Usage |
|-----|--------|---------------|-------|
| PA8 | U_High | TIM1_CH1 | Motor U faz high-side |
| PA9 | V_High | TIM1_CH2 | Motor V faz high-side |
| PA10 | W_High | TIM1_CH3 | Motor W faz high-side |
| PB13 | U_Low | TIM1_CH1N | Motor U faz low-side |
| PB14 | V_Low | TIM1_CH2N | Motor V faz low-side |
| PB15 | W_Low | TIM1_CH3N | Motor W faz low-side |

### **Communication**
| Pin | Signal | Peripheral | Config |
|-----|--------|------------|--------|
| PA11 | CAN_RX | FDCAN1_RX | Alternate Function |
| PA12 | CAN_TX | FDCAN1_TX | Alternate Function |
| PB6 | I2C_SCL | I2C1_SCL | Open Drain, Pull-up |
| PB7 | I2C_SDA | I2C1_SDA | Open Drain, Pull-up |
| PA2 | USART_TX | USART2_TX | Debug/telemetry |
| PA3 | USART_RX | USART2_RX | Debug/telemetry |

---

## ⏰ Clock Configuration

```
HSI 16MHz (Internal RC Oscillator)
    ↓
PLL Configuration:
    - Source: HSI (16MHz)
    - PLLM: /1  (16MHz)
    - PLLN: ×85 (1360MHz)
    - PLLP: /8  (170MHz) → SYSCLK
    - PLLQ: /8  (170MHz)
    - PLLR: /8  (170MHz)

System Clock Tree:
    SYSCLK:    170MHz (Max)
    HCLK:      170MHz (AHB)
    PCLK1:     170MHz (APB1)
    PCLK2:     170MHz (APB2)
    
Timer Clocks:
    TIM1_CLK:  170MHz
    TIM2_CLK:  170MHz
    
Peripheral Clocks:
    ADC_CLK:   170MHz
    I2C_CLK:   170MHz
    USART_CLK: 170MHz
    FDCAN_CLK: 170MHz
```

### **Clock Register Values**
```c
// RCC_CFGR
RCC->CFGR |= RCC_CFGR_SW_PLL;        // PLL as system clock
RCC->CFGR |= RCC_CFGR_HPRE_DIV1;     // AHB prescaler = 1
RCC->CFGR |= RCC_CFGR_PPRE1_DIV1;    // APB1 prescaler = 1
RCC->CFGR |= RCC_CFGR_PPRE2_DIV1;    // APB2 prescaler = 1

// RCC_PLLCFGR
RCC->PLLCFGR = (85 << 8) |           // PLLN = 85
               (0 << 4) |            // PLLM = 1
               RCC_PLLCFGR_PLLSRC_HSI | // HSI as source
               (7 << 25) |           // PLLP = 8
               (7 << 21) |           // PLLQ = 8
               (7 << 29);            // PLLR = 8
```

---

## 🔄 ADC1 Configuration

### **Basic Settings**
- **Resolution:** 12-bit (4095 max)
- **Data Alignment:** Right aligned
- **Scan Mode:** Enabled (2 channels)
- **Continuous Mode:** Disabled
- **Trigger:** Software trigger
- **External Trigger:** None
- **DMA:** Circular mode

### **Channel Configuration**
| Channel | Rank | Sampling Time | Pin |
|---------|------|---------------|-----|
| IN6 | 1 | 47.5 cycles | PC0 (Throttle) |
| IN7 | 2 | 47.5 cycles | PC1 (Current) |

### **DMA Configuration**
- **DMA:** DMA1 Channel 1
- **Direction:** Peripheral to Memory
- **Mode:** Circular
- **Data Width:** Half Word (16-bit)
- **Buffer Size:** 2 (for 2 channels)

### **Register Values**
```c
// ADC1_CFGR
ADC1->CFGR |= ADC_CFGR_DMAEN |       // DMA enable
              ADC_CFGR_DMACFG |      // Circular mode
              ADC_CFGR_OVRMOD;       // Overrun mode

// ADC1_SQR1
ADC1->SQR1 = (6 << 6) |              // SQ1 = IN6 (PC0)
             (7 << 12) |             // SQ2 = IN7 (PC1)
             (1 << 0);               // L = 2-1 = 1

// ADC1_SMPR1
ADC1->SMPR1 |= (4 << 18) |           // SMP6 = 47.5 cycles
               (4 << 21);            // SMP7 = 47.5 cycles

// Conversion time calculation:
// T_conv = (47.5 + 12.5) × T_adc = 60 × (1/170MHz) = 353ns per channel
```

---

## ⚡ TIM1 Configuration (Motor PWM)

### **Basic Timer Settings**
- **Prescaler:** 0 (170MHz)
- **Period (ARR):** 9999
- **PWM Frequency:** 170MHz / (0+1) / (9999+1) = **17kHz**
- **Resolution:** 10000 steps (0.01% duty resolution)
- **Counter Mode:** Up-counting (can be center-aligned)

### **Output Channels**
| Channel | Pin | Function | Polarity |
|---------|-----|----------|----------|
| CH1 | PA8 | U_High | Active High |
| CH2 | PA9 | V_High | Active High |
| CH3 | PA10 | W_High | Active High |
| CH1N | PB13 | U_Low | Active High |
| CH2N | PB14 | V_Low | Active High |
| CH3N | PB15 | W_Low | Active High |

### **Dead Time Configuration**
- **Dead Time Value:** 170
- **Dead Time:** 170 × (1/170MHz) = **1.0µs**
- **DTG Register:** 170 (0xAA)

### **Register Values**
```c
// TIM1_CR1
TIM1->CR1 = TIM_CR1_CEN |            // Counter enable
            TIM_CR1_ARPE;            // Auto-reload preload

// TIM1_PSC
TIM1->PSC = 0;                       // Prescaler = 1

// TIM1_ARR
TIM1->ARR = 9999;                    // Period = 10000 counts

// TIM1_CCMR1 (CH1, CH2)
TIM1->CCMR1 = (6 << 4) |             // OC1M = PWM mode 1
              (6 << 12) |            // OC2M = PWM mode 1
              TIM_CCMR1_OC1PE |      // CH1 preload enable
              TIM_CCMR1_OC2PE;       // CH2 preload enable

// TIM1_CCMR2 (CH3)
TIM1->CCMR2 = (6 << 4) |             // OC3M = PWM mode 1
              TIM_CCMR1_OC1PE;       // CH3 preload enable

// TIM1_CCER (Output enable)
TIM1->CCER = TIM_CCER_CC1E |         // CH1 output enable
             TIM_CCER_CC1NE |        // CH1N output enable
             TIM_CCER_CC2E |         // CH2 output enable
             TIM_CCER_CC2NE |        // CH2N output enable
             TIM_CCER_CC3E |         // CH3 output enable
             TIM_CCER_CC3NE;         // CH3N output enable

// TIM1_BDTR (Break and dead-time)
TIM1->BDTR = TIM_BDTR_MOE |          // Main output enable
             (170 << 0);             // DTG = 170 (1µs dead time)

// Duty cycle setting example:
TIM1->CCR1 = 5000;  // 50% duty cycle for U phase
TIM1->CCR2 = 0;     // 0% duty cycle for V phase
TIM1->CCR3 = 0;     // 0% duty cycle for W phase
```

---

## 🌐 FDCAN1 Configuration

### **Basic Settings**
- **Protocol:** Classical CAN (not CAN-FD)
- **Mode:** Normal Mode
- **Bit Rate:** 500 kbit/s
- **Automatic Retransmission:** Enabled
- **Transmit Pause:** Disabled

### **Bit Timing Parameters**
```
Clock: 170MHz
Bit Rate: 500 kbit/s
Bit Time: 1/500kHz = 2µs = 340 clock cycles

Timing Configuration:
- Prescaler: 17 
- Time Seg 1: 14 TQ
- Time Seg 2: 5 TQ
- Sync Jump Width: 1 TQ

Total TQ per bit: 1 + 14 + 5 = 20 TQ
Effective clock: 170MHz / 17 = 10MHz
Bit rate: 10MHz / 20 = 500kHz ✓
```

### **Register Values**
```c
// FDCAN_CCCR
FDCAN1->CCCR |= FDCAN_CCCR_INIT |    // Initialization mode
                FDCAN_CCCR_CCE;      // Configuration change enable

// FDCAN_NBTP (Nominal bit timing)
FDCAN1->NBTP = ((14-1) << 8) |       // NTSEG1 = 14-1
               ((5-1) << 16) |       // NTSEG2 = 5-1
               ((1-1) << 25) |       // NSJW = 1-1
               ((17-1) << 0);        // NBRP = 17-1

// FDCAN_CCCR (Exit initialization)
FDCAN1->CCCR &= ~FDCAN_CCCR_INIT;    // Normal operation mode
```

### **Message Filters**
```c
// Accept all messages (promiscuous mode for debugging)
// Standard ID filter 0: Accept all
FDCAN1->SIDFC = (0x0 << 2) | (1 << 0);  // 1 filter, start at offset 0

// Standard filter element 0
*((uint32_t*)(0x4000AC00)) = (0x7FF << 16) | // SFID2 = mask
                             (0x000 << 0) |  // SFID1 = ID
                             (0x2 << 30);    // SFT = classic filter

// Extended ID disabled
FDCAN1->XIDFC = 0;
```

---

## 💬 I2C1 Configuration (TMP102)

### **Basic Settings**
- **Mode:** I2C Master
- **Speed:** Standard Mode (100 kHz)
- **Clock Source:** PCLK1 (170MHz)
- **Address Length:** 7-bit
- **Device Address:** 0x48 (TMP102)

### **Timing Calculation**
```
PCLK1: 170MHz
Target: 100kHz I2C

PRESC = 8
SCLDEL = 4
SDADEL = 2
SCLH = 0xF4 (244)
SCLL = 0xF4 (244)

Actual frequency: 170MHz / (8 * (244+244+1)) ≈ 100kHz
```

### **Register Values**
```c
// I2C1_TIMINGR
I2C1->TIMINGR = (8 << 28) |          // PRESC = 8
                (4 << 20) |          // SCLDEL = 4
                (2 << 16) |          // SDADEL = 2
                (244 << 8) |         // SCLH = 244
                (244 << 0);          // SCLL = 244

// I2C1_CR1
I2C1->CR1 = I2C_CR1_PE;             // Peripheral enable
```

---

## 📡 USART2 Configuration

### **Basic Settings**
- **Baud Rate:** 115200
- **Word Length:** 8 bits
- **Stop Bits:** 1
- **Parity:** None
- **Flow Control:** None

### **Clock Calculation**
```
PCLK1: 170MHz
Baud Rate: 115200

USARTDIV = 170MHz / 115200 = 1475.69
BRR = 1476 (0x5C4)
```

### **Register Values**
```c
// USART2_BRR
USART2->BRR = 1476;                  // Baud rate register

// USART2_CR1
USART2->CR1 = USART_CR1_UE |         // USART enable
              USART_CR1_TE |         // Transmitter enable
              USART_CR1_RE;          // Receiver enable
```

---

## 🎯 GPIO Configuration

### **EXTI Configuration (Hall Sensors)**
```c
// Configure PB0, PB1, PB2 as EXTI
SYSCFG->EXTICR[0] |= SYSCFG_EXTICR1_EXTI0_PB |  // PB0 → EXTI0
                     SYSCFG_EXTICR1_EXTI1_PB |  // PB1 → EXTI1
                     SYSCFG_EXTICR1_EXTI2_PB;   // PB2 → EXTI2

// Enable rising and falling edge triggers
EXTI->RTSR1 |= EXTI_RTSR1_RT0 | EXTI_RTSR1_RT1 | EXTI_RTSR1_RT2;
EXTI->FTSR1 |= EXTI_FTSR1_FT0 | EXTI_FTSR1_FT1 | EXTI_FTSR1_FT2;

// Enable EXTI interrupts
EXTI->IMR1 |= EXTI_IMR1_IM0 | EXTI_IMR1_IM1 | EXTI_IMR1_IM2;

// Enable NVIC interrupts
NVIC_EnableIRQ(EXTI0_IRQn);
NVIC_EnableIRQ(EXTI1_IRQn);
NVIC_EnableIRQ(EXTI2_IRQn);
```

### **Pin Configuration Table**
| Pin | Mode | Speed | Pull | AF |
|-----|------|-------|------|-----|
| PA2 | AF_PP | High | None | AF7 (USART2) |
| PA3 | AF_PP | High | None | AF7 (USART2) |
| PA8 | AF_PP | High | None | AF6 (TIM1) |
| PA9 | AF_PP | High | None | AF6 (TIM1) |
| PA10 | AF_PP | High | None | AF6 (TIM1) |
| PA11 | AF_PP | High | None | AF9 (FDCAN1) |
| PA12 | AF_PP | High | None | AF9 (FDCAN1) |
| PB0 | Input | Low | None | GPIO |
| PB1 | Input | Low | None | GPIO |
| PB2 | Input | Low | None | GPIO |
| PB6 | AF_OD | High | Pull-up | AF4 (I2C1) |
| PB7 | AF_OD | High | Pull-up | AF4 (I2C1) |
| PB13 | AF_PP | High | None | AF6 (TIM1) |
| PB14 | AF_PP | High | None | AF6 (TIM1) |
| PB15 | AF_PP | High | None | AF6 (TIM1) |
| PC0 | Analog | Low | None | ADC1_IN6 |
| PC1 | Analog | Low | None | ADC1_IN7 |
| PC5 | Input | Low | None | GPIO |

---

## 🔄 DMA Configuration

### **DMA1 Channel 1 (ADC1)**
```c
// DMA1_CPAR1 (Peripheral address)
DMA1_Channel1->CPAR = (uint32_t)&(ADC1->DR);

// DMA1_CMAR1 (Memory address)
DMA1_Channel1->CMAR = (uint32_t)adc_buffer;  // uint16_t adc_buffer[2]

// DMA1_CNDTR1 (Number of data)
DMA1_Channel1->CNDTR = 2;                    // 2 channels

// DMA1_CCR1 (Configuration)
DMA1_Channel1->CCR = DMA_CCR_MSIZE_0 |       // Memory size: 16-bit
                     DMA_CCR_PSIZE_0 |       // Peripheral size: 16-bit
                     DMA_CCR_MINC |          // Memory increment
                     DMA_CCR_CIRC |          // Circular mode
                     DMA_CCR_EN;             // Enable
```

---

## 🔧 Initialization Sequence

```c
void SystemClock_Config(void) {
    // 1. Enable HSI
    RCC->CR |= RCC_CR_HSION;
    while(!(RCC->CR & RCC_CR_HSIRDY));
    
    // 2. Configure Flash latency (5 wait states for 170MHz)
    FLASH->ACR = FLASH_ACR_LATENCY_5WS;
    
    // 3. Configure PLL
    RCC->PLLCFGR = /* PLL configuration */;
    RCC->CR |= RCC_CR_PLLON;
    while(!(RCC->CR & RCC_CR_PLLRDY));
    
    // 4. Switch to PLL
    RCC->CFGR |= RCC_CFGR_SW_PLL;
    while((RCC->CFGR & RCC_CFGR_SWS) != RCC_CFGR_SWS_PLL);
}

void Peripheral_Init(void) {
    // Enable all peripheral clocks
    RCC->AHB2ENR |= RCC_AHB2ENR_GPIOAEN | RCC_AHB2ENR_GPIOBEN | 
                    RCC_AHB2ENR_GPIOCEN | RCC_AHB2ENR_ADC12EN;
    RCC->APB1ENR1 |= RCC_APB1ENR1_I2C1EN;
    RCC->APB1ENR2 |= RCC_APB1ENR2_USART2EN;
    RCC->APB2ENR |= RCC_APB2ENR_TIM1EN | RCC_APB2ENR_FDCAN1EN;
    RCC->AHB1ENR |= RCC_AHB1ENR_DMA1EN;
    
    GPIO_Init();
    ADC_Init();
    TIM1_Init();
    FDCAN_Init();
    I2C_Init();
    USART_Init();
    DMA_Init();
}
```

---

## 🏷️ Tags
#yazilim #stm32 #cubemx #konfigürasyon #timer #adc #can #i2c #pwm #vcu

## 🔗 İlgili Dosyalar
- [[vcu-pedal]] - ADC okuma ve pedal işleme
- [[esc-commutation]] - TIM1 PWM kontrolü
- [[vcu-can]] - FDCAN mesaj yapıları
- [[esc-sensors]] - I2C TMP102 ve ADC
- [[Test-Proseduru]] - Donanım test adımları