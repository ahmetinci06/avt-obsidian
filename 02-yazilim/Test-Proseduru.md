# 🧪 Test Prosedürü — 7 Sensör Testi
#yazilim #test #critical

> [!warning] Test Sırası
> Aybüke'nin belirlediği sıra. Her test bağımsız, main.c USER CODE'a yapıştır.

## Ön Koşullar (Tüm Testler)
- [ ] STM32CubeIDE kurulu
- [ ] ST-LINK bağlı (USB)
- [ ] Serial monitor açık (PuTTY/minicom, 115200 baud, COM port)
- [ ] AKS board'a 3.3V güç verildi
- [ ] Test kodu: `tests/test_X_xxx.c` dosyasından kopyala

---

## Test 1: TMP102 Sıcaklık (I2C)

**Dosya:** `tests/test_1_tmp102.c`
**CubeMX:** I2C1 aktif, USART2 aktif

| Adım | İşlem |
|------|-------|
| 1 | CubeMX'te I2C1 + USART2 enable |
| 2 | Test kodunu USER CODE bölümlerine yapıştır |
| 3 | Derle + flash |
| 4 | Serial monitor izle |

**Beklenen:** `TMP102: 25.3 C` (oda sıcaklığı ±5°C)
**PASS:** 15-40°C arası okuyor, parmakla dokunulunca artıyor
**FAIL:** `I2C TX HATA` → kablo/lehim kontrol, `0.0 C` sabit → register okunamıyor

---

## Test 2: DC Bus Gerilim (ADC PA0/IN1)

**Dosya:** `tests/test_2_dc_bus_voltage.c`
**CubeMX:** ADC1 tek kanal IN1 (PA0)

| Adım | İşlem |
|------|-------|
| 1 | Güç kaynağından bilinen voltaj bağla (ör. 24V) |
| 2 | Test kodunu yapıştır, derle, flash |
| 3 | Serial monitor izle |

**Beklenen:** `VBUS: raw=1489 → 24.1V` (24V kaynak için)
**Hesap:** raw = V_bus × 0.5 × 4096/3.3
**PASS:** Ölçüm ±5% doğru
**FAIL:** raw=0 → ADC pin bağlantısı yok, raw=4095 → kısa devre

---

## Test 3: Akım Ölçümü (ADC PC1/IN7)

**Dosya:** `tests/test_3_current_sense.c`
**CubeMX:** ADC1 tek kanal IN7 (PC1)

| Adım | İşlem |
|------|-------|
| 1 | Motor BAĞLAMA — yüksüz test |
| 2 | Test kodunu yapıştır |
| 3 | Serial monitor izle |

**Beklenen:** `CURR: raw=125 → ~0.1V` (sıfır akım offset)
**Offset not et:** Bu değer kalibrasyon için lazım!
**PASS:** Sabit, kararlı okuma (±10 raw dalgalanma OK)
**FAIL:** raw=0 → op-amp beslemesi yok, raw=4095 → kısa devre

---

## Test 4: Gaz Pedalı (ADC PC0/IN6)

**Dosya:** `tests/test_4_throttle.c`
**CubeMX:** ADC1 tek kanal IN6 (PC0)

| Adım | İşlem |
|------|-------|
| 1 | J1 konektöre pot/throttle bağla (Pin1=3V3, Pin2=sinyal, Pin3=GND) |
| 2 | Test kodunu yapıştır |
| 3 | Potansiyometreyi yavaşça çevir |

**Beklenen:** `GAZ: raw=250 → 0.0%` ... `GAZ: raw=3600 → 100.0%`
**PASS:** 0-100% smooth geçiş, wire-break/short detection çalışıyor
**FAIL:** `WIRE BREAK` → kablo kopuk, `SHORT CIRCUIT` → VCC'ye kısa

---

## Test 5: Fren (GPIO PC5)

**Dosya:** `tests/test_5_brake.c`
**CubeMX:** PC5 GPIO Input, No Pull

| Adım | İşlem |
|------|-------|
| 1 | Fren butonu bağlı (SW1 → 3V3) |
| 2 | Test kodunu yapıştır |
| 3 | Butona bas/bırak |

**Beklenen:** `FREN: SERBEST` ↔ `FREN: BASILI`
**PASS:** Basınca HIGH, bırakınca LOW, debounce sorunsuz
**FAIL:** Sürekli BASILI → pull-down kontrol, sürekli SERBEST → buton bağlantısı

---

## Test 6: Hall Sensörleri (GPIO PB0/PB1/PB2)

**Dosya:** `tests/test_6_hall_sensors.c`
**CubeMX:** PB0/PB1/PB2 GPIO Input

| Adım | İşlem |
|------|-------|
| 1 | Motor hall kabloları bağlı |
| 2 | Test kodunu yapıştır |
| 3 | Motoru ELLE yavaşça 1 tur çevir |

**Beklenen:** 6 farklı code sırayla geçmeli (1→3→2→6→4→5→1 veya benzeri)
**PASS:** 6 geçerli code, sıra tutarlı, 0 veya 7 yok
**FAIL:** `HATA: hepsi LOW` → kablo kopuk, `HATA: hepsi HIGH` → besleme yok, code atlaması → hall arızalı

---

## Test 7: PWM Çıkışları (TIM1 — Osiloskop)

**Dosya:** `tests/test_7_pwm_output.c`
**CubeMX:** TIM1 PWM CH1/2/3 + CH1N/2N/3N, Period=9999, DeadTime=170

> [!danger] MOTOR BAĞLAMAYIN — sadece osiloskop ile test!

| Adım | İşlem |
|------|-------|
| 1 | Osiloskop prob: PA8/PB13 (CH1/CH1N) |
| 2 | Test kodunu yapıştır, derle, flash |
| 3 | Her 2 saniye kanal değişir |

**Beklenen:** 17kHz PWM, %50 duty, ~1µs dead time
**PASS:** Temiz kare dalga, dead time görünüyor, complementary çıkışlar ters
**FAIL:** Sinyal yok → TIM1 config, dead time yok → BDTR ayarı, glitch → layout sorunu

---

## Test Sonuç Tablosu

| Test | Tarih | Yapan | Sonuç | Notlar |
|------|-------|-------|-------|--------|
| 1. TMP102 | | | ⬜ | |
| 2. DC Bus | | | ⬜ | |
| 3. Akım | | | ⬜ | |
| 4. Gaz | | | ⬜ | |
| 5. Fren | | | ⬜ | |
| 6. Hall | | | ⬜ | |
| 7. PWM | | | ⬜ | |

## İlişkili Dosyalar
- [[Derleme-Rehberi]] — nasıl derlenir ve flash'lanır
- [[Sensor-Baglantilari]] — kablo bağlantıları
- [[AKS-Board]] — şematik ve BOM
