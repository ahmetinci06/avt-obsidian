# VCU CAN Haberleşme Modülü

#can #vcu #haberleşme #bms #telemetry

[[vcu-state]] | [[CAN-Bus]] | [[esc-energy]] | [[esc-telemetry]]

## Genel Bakış

Vehicle Control Unit (VCU) CAN haberleşme modülü. FDCAN1 peripheral kullanarak BMS ile güvenilir data exchange ve telemetry gönderimi.

> [!important] Kritik Haberleşme
> CAN mesajlarının kaybı veya gecikmesi araç güvenlik sistemlerini etkiler.

## CAN Message Definitions

### BMS Incoming Messages (RX)

#### 0x100 - BMS Status
```c
typedef struct {
    uint8_t state;          // 0=OFF, 1=STANDBY, 2=READY, 3=CHARGE, 4=FAULT
    uint16_t fault_code;    // Error code (if any)
    uint8_t temp_high;      // Highest cell temp (°C + 40)
    uint8_t balancing;      // Balancing status bitmap
    uint8_t reserved[2];
    uint8_t checksum;       // XOR checksum
} BMS_Status_t;
```

#### 0x101 - BMS Voltage
```c
typedef struct {
    uint16_t pack_voltage;  // Total voltage (0.1V resolution)
    uint16_t cell_max;      // Highest cell (mV)
    uint16_t cell_min;      // Lowest cell (mV) 
    uint16_t cell_diff;     // Cell difference (mV)
} BMS_Voltage_t;
```

#### 0x102 - BMS Current  
```c
typedef struct {
    int32_t current;        // Current (mA, signed)
    uint32_t energy_used;   // Energy consumed (Wh)
} BMS_Current_t;
```

#### 0x103 - BMS Temperature
```c
typedef struct {
    uint8_t temp_1;         // Sensor 1 (°C + 40)
    uint8_t temp_2;         // Sensor 2 (°C + 40) 
    uint8_t temp_3;         // Sensor 3 (°C + 40)
    uint8_t temp_4;         // Sensor 4 (°C + 40)
    uint8_t temp_avg;       // Average temp (°C + 40)
    uint8_t reserved[3];
} BMS_Temperature_t;
```

#### 0x104 - BMS State of Charge
```c
typedef struct {
    uint8_t soc;            // State of charge (%)
    uint8_t soh;            // State of health (%)
    uint16_t remaining_cap; // Remaining capacity (Wh)
    uint16_t full_cap;      // Full capacity (Wh)
    uint16_t time_to_empty; // Minutes to empty
} BMS_SOC_t;
```

### VCU Outgoing Messages (TX)

#### 0x300 - VCU Status
```c
typedef struct {
    uint8_t state;          // VCU state machine
    uint8_t error_code;     // Current error
    uint8_t drive_mode;     // Driving mode
    uint8_t throttle_raw;   // Raw throttle (0-255)
    uint8_t brake_status;   // Brake engaged (0/1)
    uint8_t temperature;    // VCU temp (°C + 40)
    uint8_t reserved[2];
} VCU_Status_t;
```

#### 0x301 - VCU Pedal Data
```c
typedef struct {
    uint16_t throttle_adc;  // Raw ADC value
    uint16_t throttle_filt; // Filtered value
    uint16_t throttle_pct;  // Percentage (x100)
    uint8_t pedal_fault;    // Fault status
    uint8_t reserved;
} VCU_Pedal_t;
```

#### 0x302 - VCU Energy Data
```c
typedef struct {
    uint32_t distance_m;    // Distance traveled (meters)
    uint16_t speed_kmh;     // Current speed (km/h x10)
    uint16_t efficiency;    // Efficiency (Wh/km)
} VCU_Energy_t;
```

## Global Data Structures

### Combined BMS Data
```c
typedef struct {
    // Status data
    BMS_Status_t status;
    BMS_Voltage_t voltage;
    BMS_Current_t current;
    BMS_Temperature_t temperature;
    BMS_SOC_t soc;
    
    // Reception tracking
    uint32_t last_rx_time[5];   // Timestamp for each message
    uint8_t rx_error_count[5];  // Error counter per message
    bool data_valid[5];         // Validity flag per message
    
    // Derived values
    float pack_voltage_v;       // Calculated voltage (V)
    float current_a;            // Calculated current (A)
    int8_t temp_max_c;          // Max temperature (°C)
    int8_t temp_min_c;          // Min temperature (°C)
    
} BMS_Data_t;
```

### Combined VCU Data
```c
typedef struct {
    VCU_Status_t status;
    VCU_Pedal_t pedal;
    VCU_Energy_t energy;
    
    // Transmission tracking
    uint32_t last_tx_time[3];   // Timestamp for each message
    uint32_t tx_count[3];       // Transmission counter
    
} VCU_Data_t;
```

## FDCAN Configuration

### Initialization Parameters
```c
FDCAN_HandleTypeDef hfdcan1;

void CAN_Init(void) {
    hfdcan1.Instance = FDCAN1;
    hfdcan1.Init.FrameFormat = FDCAN_FRAME_CLASSIC;
    hfdcan1.Init.Mode = FDCAN_MODE_NORMAL;
    hfdcan1.Init.AutoRetransmission = ENABLE;
    hfdcan1.Init.TransmitPause = DISABLE;
    hfdcan1.Init.ProtocolException = DISABLE;
    
    // Bit timing for 500kbps @ 80MHz
    hfdcan1.Init.NominalPrescaler = 8;
    hfdcan1.Init.NominalSyncJumpWidth = 1;
    hfdcan1.Init.NominalTimeSeg1 = 13;
    hfdcan1.Init.NominalTimeSeg2 = 2;
    
    if (HAL_FDCAN_Init(&hfdcan1) != HAL_OK) {
        Error_Handler();
    }
}
```

### Message Filters Setup
```c
void CAN_ConfigFilters(void) {
    FDCAN_FilterTypeDef filter_config;
    
    // Filter 0: BMS Status Messages (0x100-0x104)
    filter_config.IdType = FDCAN_STANDARD_ID;
    filter_config.FilterIndex = 0;
    filter_config.FilterType = FDCAN_FILTER_RANGE;
    filter_config.FilterConfig = FDCAN_FILTER_TO_RXFIFO0;
    filter_config.FilterID1 = 0x100;    // BMS_STATUS
    filter_config.FilterID2 = 0x104;    // BMS_SOC
    
    if (HAL_FDCAN_ConfigFilter(&hfdcan1, &filter_config) != HAL_OK) {
        Error_Handler();
    }
    
    // Start FDCAN
    if (HAL_FDCAN_Start(&hfdcan1) != HAL_OK) {
        Error_Handler();
    }
    
    // Enable RX interrupt
    if (HAL_FDCAN_ActivateNotification(&hfdcan1, 
            FDCAN_IT_RX_FIFO0_NEW_MESSAGE, 0) != HAL_OK) {
        Error_Handler();
    }
}
```

## Message Reception

### RX Interrupt Handler
```c
void HAL_FDCAN_RxFifo0Callback(FDCAN_HandleTypeDef *hfdcan, uint32_t RxFifo0ITs) {
    if ((RxFifo0ITs & FDCAN_IT_RX_FIFO0_NEW_MESSAGE) != 0) {
        FDCAN_RxHeaderTypeDef rx_header;
        uint8_t rx_data[8];
        
        if (HAL_FDCAN_GetRxMessage(hfdcan, FDCAN_RX_FIFO0, 
                &rx_header, rx_data) == HAL_OK) {
            CAN_ProcessRxMessage(&rx_header, rx_data);
        }
    }
}
```

### Message Processing
```c
void CAN_ProcessRxMessage(FDCAN_RxHeaderTypeDef *header, uint8_t *data) {
    uint32_t current_time = HAL_GetTick();
    
    switch (header->Identifier) {
        case 0x100:  // BMS Status
            if (header->DataLength == FDCAN_DLC_BYTES_8) {
                memcpy(&bms_data.status, data, sizeof(BMS_Status_t));
                bms_data.last_rx_time[0] = current_time;
                bms_data.data_valid[0] = CAN_ValidateChecksum(data, 8);
                
                if (!bms_data.data_valid[0]) {
                    bms_data.rx_error_count[0]++;
                }
            }
            break;
            
        case 0x101:  // BMS Voltage  
            if (header->DataLength == FDCAN_DLC_BYTES_8) {
                memcpy(&bms_data.voltage, data, sizeof(BMS_Voltage_t));
                bms_data.last_rx_time[1] = current_time;
                bms_data.data_valid[1] = true;
                
                // Convert to float for calculations
                bms_data.pack_voltage_v = bms_data.voltage.pack_voltage * 0.1f;
            }
            break;
            
        case 0x102:  // BMS Current
            if (header->DataLength == FDCAN_DLC_BYTES_8) {
                memcpy(&bms_data.current, data, sizeof(BMS_Current_t));
                bms_data.last_rx_time[2] = current_time;
                bms_data.data_valid[2] = true;
                
                // Convert to float
                bms_data.current_a = bms_data.current.current / 1000.0f;
            }
            break;
            
        case 0x103:  // BMS Temperature
            if (header->DataLength == FDCAN_DLC_BYTES_8) {
                memcpy(&bms_data.temperature, data, sizeof(BMS_Temperature_t));
                bms_data.last_rx_time[3] = current_time;
                bms_data.data_valid[3] = true;
                
                // Calculate min/max temperatures
                bms_data.temp_max_c = bms_data.temperature.temp_1 - 40;
                bms_data.temp_min_c = bms_data.temperature.temp_1 - 40;
                
                uint8_t temps[4] = {
                    bms_data.temperature.temp_1,
                    bms_data.temperature.temp_2,
                    bms_data.temperature.temp_3,
                    bms_data.temperature.temp_4
                };
                
                for (int i = 1; i < 4; i++) {
                    int8_t temp = temps[i] - 40;
                    if (temp > bms_data.temp_max_c) bms_data.temp_max_c = temp;
                    if (temp < bms_data.temp_min_c) bms_data.temp_min_c = temp;
                }
            }
            break;
            
        case 0x104:  // BMS SOC
            if (header->DataLength == FDCAN_DLC_BYTES_8) {
                memcpy(&bms_data.soc, data, sizeof(BMS_SOC_t));
                bms_data.last_rx_time[4] = current_time;
                bms_data.data_valid[4] = true;
            }
            break;
    }
}
```

## Message Transmission

### VCU Status Transmission
```c
bool CAN_SendVCUStatus(void) {
    FDCAN_TxHeaderTypeDef tx_header;
    uint8_t tx_data[8];
    
    // Configure header
    tx_header.Identifier = 0x300;
    tx_header.IdType = FDCAN_STANDARD_ID;
    tx_header.TxFrameType = FDCAN_DATA_FRAME;
    tx_header.DataLength = FDCAN_DLC_BYTES_8;
    tx_header.ErrorStateIndicator = FDCAN_ESI_ACTIVE;
    tx_header.BitRateSwitch = FDCAN_BRS_OFF;
    tx_header.FDFormat = FDCAN_CLASSIC_CAN;
    tx_header.TxEventFifoControl = FDCAN_NO_TX_EVENTS;
    tx_header.MessageMarker = 0;
    
    // Populate data
    vcu_data.status.state = Get_VCU_State();
    vcu_data.status.error_code = Get_Error_Code();
    vcu_data.status.drive_mode = Get_Drive_Mode();
    vcu_data.status.throttle_raw = Get_Throttle_Raw();
    vcu_data.status.brake_status = Get_Brake_Status();
    vcu_data.status.temperature = Get_VCU_Temperature() + 40;
    
    memcpy(tx_data, &vcu_data.status, sizeof(VCU_Status_t));
    
    // Transmit
    if (HAL_FDCAN_AddMessageToTxFifoQ(&hfdcan1, &tx_header, tx_data) == HAL_OK) {
        vcu_data.last_tx_time[0] = HAL_GetTick();
        vcu_data.tx_count[0]++;
        return true;
    }
    
    return false;
}
```

### Pedal Data Transmission
```c
bool CAN_SendPedalData(void) {
    FDCAN_TxHeaderTypeDef tx_header;
    uint8_t tx_data[8];
    
    tx_header.Identifier = 0x301;
    tx_header.IdType = FDCAN_STANDARD_ID;
    tx_header.TxFrameType = FDCAN_DATA_FRAME;
    tx_header.DataLength = FDCAN_DLC_BYTES_8;
    tx_header.ErrorStateIndicator = FDCAN_ESI_ACTIVE;
    tx_header.BitRateSwitch = FDCAN_BRS_OFF;
    tx_header.FDFormat = FDCAN_CLASSIC_CAN;
    tx_header.TxEventFifoControl = FDCAN_NO_TX_EVENTS;
    tx_header.MessageMarker = 0;
    
    // Get current pedal data
    vcu_data.pedal.throttle_adc = ADC_Get_Throttle_Raw();
    vcu_data.pedal.throttle_filt = ADC_Get_Throttle_Filtered();
    vcu_data.pedal.throttle_pct = ADC_Get_Throttle_Percent() * 100;
    vcu_data.pedal.pedal_fault = ADC_Get_Pedal_Fault();
    
    memcpy(tx_data, &vcu_data.pedal, sizeof(VCU_Pedal_t));
    
    if (HAL_FDCAN_AddMessageToTxFifoQ(&hfdcan1, &tx_header, tx_data) == HAL_OK) {
        vcu_data.last_tx_time[1] = HAL_GetTick();
        vcu_data.tx_count[1]++;
        return true;
    }
    
    return false;
}
```

### Energy Data Transmission  
```c
bool CAN_SendEnergyData(void) {
    FDCAN_TxHeaderTypeDef tx_header;
    uint8_t tx_data[8];
    
    tx_header.Identifier = 0x302;
    tx_header.IdType = FDCAN_STANDARD_ID;
    tx_header.TxFrameType = FDCAN_DATA_FRAME;
    tx_header.DataLength = FDCAN_DLC_BYTES_8;
    tx_header.ErrorStateIndicator = FDCAN_ESI_ACTIVE;
    tx_header.BitRateSwitch = FDCAN_BRS_OFF;
    tx_header.FDFormat = FDCAN_CLASSIC_CAN;
    tx_header.TxEventFifoControl = FDCAN_NO_TX_EVENTS;
    tx_header.MessageMarker = 0;
    
    // Update energy calculations
    vcu_data.energy.distance_m = Energy_Get_Distance_m();
    vcu_data.energy.speed_kmh = Energy_Get_Speed_kmh() * 10;
    vcu_data.energy.efficiency = Energy_Get_Efficiency_WhKm();
    
    memcpy(tx_data, &vcu_data.energy, sizeof(VCU_Energy_t));
    
    if (HAL_FDCAN_AddMessageToTxFifoQ(&hfdcan1, &tx_header, tx_data) == HAL_OK) {
        vcu_data.last_tx_time[2] = HAL_GetTick();
        vcu_data.tx_count[2]++;
        return true;
    }
    
    return false;
}
```

## Periodic Transmission Task

### 10Hz Transmission Schedule
```c
void CAN_PeriodicTask(void) {
    static uint32_t last_run = 0;
    uint32_t current_time = HAL_GetTick();
    
    // Run every 100ms (10Hz)
    if (current_time - last_run >= 100) {
        last_run = current_time;
        
        // Send VCU messages in sequence
        CAN_SendVCUStatus();
        HAL_Delay(5);  // Small delay between messages
        
        CAN_SendPedalData();
        HAL_Delay(5);
        
        CAN_SendEnergyData();
        
        // Check for communication timeouts
        CAN_CheckTimeouts();
    }
}
```

## Error Handling & Diagnostics

### Communication Timeout Check
```c
void CAN_CheckTimeouts(void) {
    uint32_t current_time = HAL_GetTick();
    const uint32_t TIMEOUT_MS = 500;  // 500ms timeout
    
    for (int i = 0; i < 5; i++) {
        if (bms_data.data_valid[i]) {
            if (current_time - bms_data.last_rx_time[i] > TIMEOUT_MS) {
                // Mark data as invalid
                bms_data.data_valid[i] = false;
                
                // Increment error counter
                bms_data.rx_error_count[i]++;
                
                // Set appropriate fault
                switch (i) {
                    case 0: Set_Fault(FAULT_BMS_STATUS_TIMEOUT); break;
                    case 1: Set_Fault(FAULT_BMS_VOLTAGE_TIMEOUT); break;
                    case 2: Set_Fault(FAULT_BMS_CURRENT_TIMEOUT); break;
                    case 3: Set_Fault(FAULT_BMS_TEMP_TIMEOUT); break;
                    case 4: Set_Fault(FAULT_BMS_SOC_TIMEOUT); break;
                }
            }
        }
    }
    
    // Check for excessive errors (possible bus-off condition)
    if (HAL_FDCAN_GetErrorCounters(&hfdcan1, &error_counters) == HAL_OK) {
        if (error_counters.TxErrorCnt > 100 || error_counters.RxErrorCnt > 100) {
            Set_Fault(FAULT_CAN_BUS_ERROR);
        }
    }
}
```

### Checksum Validation
```c
bool CAN_ValidateChecksum(uint8_t *data, uint8_t length) {
    uint8_t calculated_checksum = 0;
    
    // XOR all bytes except the last one (checksum byte)
    for (int i = 0; i < length - 1; i++) {
        calculated_checksum ^= data[i];
    }
    
    return (calculated_checksum == data[length - 1]);
}
```

## API Functions

### Public Interface
```c
// Initialization
void CAN_Init(void);
void CAN_ConfigFilters(void);

// Data access
BMS_Data_t* CAN_GetBMSData(void);
VCU_Data_t* CAN_GetVCUData(void);

// Status checks
bool CAN_IsBMSDataValid(void);
bool CAN_IsCommActive(void);
uint32_t CAN_GetErrorCount(void);

// Manual transmission
bool CAN_SendVCUStatus(void);
bool CAN_SendPedalData(void);
bool CAN_SendEnergyData(void);

// Diagnostics
void CAN_GetDiagnostics(CAN_Diagnostics_t *diag);
void CAN_ClearErrorCounters(void);
```

### Usage Example
```c
int main(void) {
    // Initialize CAN
    CAN_Init();
    CAN_ConfigFilters();
    
    while (1) {
        // Periodic CAN tasks
        CAN_PeriodicTask();
        
        // Access BMS data
        BMS_Data_t *bms = CAN_GetBMSData();
        if (CAN_IsBMSDataValid()) {
            printf("Battery SOC: %d%%\n", bms->soc.soc);
            printf("Pack Voltage: %.1fV\n", bms->pack_voltage_v);
            printf("Current: %.1fA\n", bms->current_a);
        }
        
        // Other application tasks
        HAL_Delay(10);
    }
}
```

## Performance Metrics

### Target Parameters
| Metric | Target | Monitoring |
|--------|--------|------------|
| Message rate | 10Hz | TX counters |
| Latency | <5ms | Timestamp delta |
| Error rate | <0.1% | Error counters |
| Bus utilization | <30% | Frame timing |

### Diagnostic Counters
```c
typedef struct {
    uint32_t total_tx_count;
    uint32_t total_rx_count;
    uint32_t tx_error_count;
    uint32_t rx_error_count;
    uint32_t timeout_count;
    uint32_t checksum_errors;
    uint8_t bus_off_count;
    uint32_t last_error_time;
} CAN_Diagnostics_t;
```

## İlgili Dokümanlar
- [[CAN-Bus]] - Hardware implementation details
- [[vcu-state]] - State machine integration
- [[esc-energy]] - Energy calculation algorithms
- [[Test-Proseduru]] - CAN testing procedures

## Referanslar
- STM32G4 FDCAN Reference Manual RM0440
- CAN in Automation (CiA) Standards
- ISO 11898 CAN Specification
- AUTOSAR CAN Driver Specification