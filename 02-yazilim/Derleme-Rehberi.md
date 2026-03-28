# 🔧 Derleme & Flash Rehberi
#yazilim #başlangıç

## 1. STM32CubeIDE Kurulumu
- [ ] [st.com/stm32cubeide](https://www.st.com/en/development-tools/stm32cubeide.html) adresinden indir
- [ ] İşletim sistemine göre kur (Windows/Linux/Mac)
- [ ] İlk açılışta workspace seç

## 2. Projeyi Aç
```bash
git clone https://github.com/ahmetinci06/AVT-VCU-DEV.git
```
- File → Import → Existing Projects into Workspace → AVT-VCU-DEV dizini seç

## 3. Derle
- **Project → Build All** (Ctrl+B)
- Hedef: **0 error, 0 warning**
- Build çıktısı: `Debug/AVT-VCU-DEV.elf`

## 4. Flash (ST-LINK)
- [ ] ST-LINK USB kablosunu Nucleo/board'a bağla
- Run → Debug As → STM32 C/C++ Application
- İlk seferde debug configuration oluştur
- **Flash başarılı** → program çalışmaya başlar

## 5. UART Monitor
- **Windows:** PuTTY veya Tera Term
- **Linux/Mac:** `minicom -D /dev/ttyACM0 -b 115200`
- **Ayarlar:** 115200 baud, 8-N-1, no flow control
- COM port: Device Manager'dan bak (Windows) veya `ls /dev/ttyACM*`

## 6. Debug İpuçları
- Breakpoint koy (satır numarasına tıkla)
- Variables penceresinden değişkenleri izle
- Live Expressions: `pedal.pedal_percent`, `state_machine.current`
- SWV (Serial Wire Viewer): `printf` debug için ITM

## İlişkili Dosyalar
- [[CubeMX-Config]] — peripheral ayarları
- [[Test-Proseduru]] — sensör testleri
