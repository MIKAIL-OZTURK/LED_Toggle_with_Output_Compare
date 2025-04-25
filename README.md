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

## Hesaplama
![LED_TOGGLE_OC](https://github.com/user-attachments/assets/e7ca0cb0-b49c-444b-92ab-8759a29eb6c2)

## Proje Videosu



https://github.com/user-attachments/assets/8a47fbfe-2fbc-4c0d-bf8e-57f548d7181d





