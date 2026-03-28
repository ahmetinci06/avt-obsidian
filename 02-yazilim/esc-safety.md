# 🛡️ ESC Güvenlik — esc_safety.c/h
#yazilim #esc #güvenlik #critical

> [!warning] Güvenlik Kritik Modül
> Overcurrent ve overtemperature koruması. Hata → motor anında durur.

## Fault Bayrakları

| Flag | Değer | Tetikleyici | Aksiyon |
|------|-------|-------------|---------|
| `ESC_FAULT_OVERCURRENT` | 0x00001000 | current_raw > 3000 | Motor off |
| `ESC_FAULT_OVERTEMP` | 0x00002000 | temp > 80.0°C | Motor off |

> [!important] 0x1000+ range
> ESC fault'ları `vcu_state` fault_code'a OR'lanır. VCU fault'ları 0x0001-0x0FFF, ESC fault'ları 0x1000+ aralığında — çakışma yok.

## Eşik Değerleri

```c
#define MOTOR_TEMP_LIMIT_C     80.0f   // °C — TMP102'den
#define CURRENT_ADC_LIMIT      3000U   // Raw ADC (0-4095)
```

### Akım Eşik Hesabı
- Rshunt = 1mΩ = 0.001Ω
- ADC 3000 → `3000 × (3.3/4096)` = ~2.42V op-amp çıkışı
- Gerçek akım: op-amp kazancına bağlı (TLV9064 ch2 feedback dirençleri)
- **⚠️ Kalibrasyon gerekli:** bilinen akım ile ADC raw değer eşleştirmesi

## Fonksiyonlar

| Fonksiyon | Açıklama |
|-----------|----------|
| `ESC_Safety_Init()` | Bayrakları sıfırla |
| `ESC_Safety_Update()` | Sensörlerden oku, eşik kontrol |
| `ESC_Safety_IsFaultActive()` | 1 = hata var |
| `ESC_Safety_GetFaultFlags()` | OR'lanmış flag bitmask |

## main.c Entegrasyonu
```c
ESC_Safety_Update();
uint32_t esc_faults = ESC_Safety_GetFaultFlags();
if (esc_faults != 0U) {
    VCU_State_SetFault(&state_machine, esc_faults);
}
```

## Test Prosedürü
- [ ] Motor kapalı → `ESC_Safety_IsFaultActive()` = 0
- [ ] TMP102'yi ısıt (sıcak hava/parmak) → 80°C geçince fault tetiklenmeli
- [ ] Bilinen yüksek akım → ADC > 3000 olunca fault tetiklenmeli
- [ ] Fault aktifken motor duty = 0 mı kontrol et

## İlişkili Dosyalar
- [[esc-sensors]] — akım ve sıcaklık kaynağı
- [[vcu-state]] — fault_code entegrasyonu
- [[esc-commutation]] — AllOff() çağrısı
