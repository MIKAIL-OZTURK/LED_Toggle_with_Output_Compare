# 🟢 STM32 Output Compare ile Periyodik LED Toggle

Bu proje, STM32F407VG mikrodenetleyicisi üzerinde **Timer Output Compare (OC)** modunu kullanarak belirli bir zaman aralığında bir LED’in toggle edilmesini (yak-sön) sağlamaktadır.

---

## 🎯 Proje Amacı

- Timer’ın Output Compare modunu pratik olarak öğrenmek
- Belirli bir periyotta çalışan kesme (interrupt) sistemi kurmak
- GPIO kontrolünü zamanlayıcı tabanlı hale getirmek

---

## ⚙️ Kullanılan Geliştirme Ortamı

- STM32CubeIDE 1.14.0
- STM32CubeMX
- STM32F407VG Discovery / Nucleo-64 (veya benzeri)

---

## 🔧 Donanım Ayarları

| Birim       | Değer / Ayar          |
|-------------|------------------------|
| Timer       | TIM2                   |
| Mode        | Output Compare (Timing) |
| Prescaler   | 8399                   |
| ARR         | 4999                   |
| Frekans     | 2 Hz (toggle için)     |
| GPIO        | GPIOD_PIN_14 (LED)     |

> **Hesaplama:**  
> - 84 MHz / (8399 + 1) = 10 kHz  
> - 10.000 Hz / (4999 + 1) = 2 Hz ⇒ Toggle her 0.5 saniyede bir

---

## 📂 Proje Yapısı

