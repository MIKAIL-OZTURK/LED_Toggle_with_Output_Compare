# 🟢 STM32 Output Compare ile Periyodik LED Toggle

Bu proje, STM32F407VG mikrodenetleyicisi üzerinde Timer Output Compare (OC) modunu kullanarak, belirli aralıklarla bir LED’in periyodik olarak toggle edilmesini (yakıp söndürülmesini) sağlar.

## 📚 Genel Bilgilendirme
### 🧭 Output Compare (OC) Nedir?
Output Compare, bir zamanlayıcının sayacı (CNT) ile karşılaştırma register’ı (CCR) eşitlendiğinde bir olay (toggle, kesme, PWM vb.) üretmesini sağlayan bir zamanlayıcı modudur. Bu karşılaştırma mikrodenetleyicinin donanımı tarafından gerçekleştirilir.

```c
if (CNT == CCRx) {
    // Toggle, Interrupt, PWM...
}
```
> Uygulamada bu karşılaştırma donanım seviyesinde yapılır. Kodda doğrudan if (CNT == CCRx) gibi bir yapı görmeyiz. Eğer kesme (interrupt) aktifse, belirlenen olay kesme fonksiyonu aracılığıyla gerçekleştirilir.
> Bu projede LED toggle işlemi kesme ile yapılmaktadır.



## 🎯 Proje Amaçları
- Timer’ın Output Compare modunun pratik olarak uygulanması              
- Periyodik kesme (interrupt) mekanizmasının kurulması                 
- GPIO kontrolünün zamanlayıcı tabanlı gerçekleştirilmesi                     


## ⚙️ Geliştirme Ortamı

| Bileşen            | Sürüm / Donanım             |
|--------------------|-----------------------------|
| IDE                | STM32CubeIDE 1.14.0         |
| Konfigürasyon Aracı| STM32CubeMX                 |
| Hedef Donanım      | STM32F407VG Discovery       |



## 🔧 Donanım Ayarları

| Birim       | Değer / Ayar          |
|-------------|------------------------|
| Timer       | TIM2                   |
| Mode        | Output Compare (Timing) |
| Prescaler   | 8399                   |
| ARR         | 4999                   |
| GPIO        | GPIOD_PIN_14 (LED)     |



## 🧠 Uygulama Mantığı


### ⏱️ 1. Zamanlama Hesabı
LED’in 0.5 saniyede bir toggle olması için timer frekansı şöyle hesaplanır:

```
f = System Clock / ((Prescaler + 1) × (ARR + 1))
Uygulama frekansı: 2 Hz (0.5 saniyede bir toggle)
```


```
2 = 84 MHz / ((8399 + 1) × (4999 + 1))
Bu değerlere göre:
Prescaler = 8399
ARR = 4999
```

### ▶️ 2. Timer Başlatılması
```c
HAL_TIM_OC_Start_IT(&htim2, TIM_CHANNEL_1);
```

Bu komut ile TIM2 sayacı başlatılır ve Output Compare kesmesi aktif hale gelir.

### 💡 3. Compare Değeri ve Kesme
Compare değeri olarak sıfır atanmıştır:

```c
sConfigOC.Pulse = 0;
```

CNT değeri 0 olduğunda ilk karşılaştırma gerçekleşir ve kesme fonksiyonu tetiklenir.

### 🧩 4. Kesme Fonksiyonları
Interrupt Handler:

```c
void TIM2_IRQHandler(void)
{
    HAL_TIM_IRQHandler(&htim2);
}
```


Callback Fonksiyonu:
```c
void HAL_TIM_OC_DelayElapsedCallback(TIM_HandleTypeDef *htim)
{
    if(htim->Instance == TIM2 && htim->Channel == HAL_TIM_ACTIVE_CHANNEL_1)
    {
        HAL_GPIO_TogglePin(GPIOD, GPIO_PIN_14);  // LED 0.5 saniyede bir toggle olur.
    }
}
```


### 🔁 Akış Diyagramı
1. CNT her clock tick’inde artar
2. CNT == CCR olduğunda karşılaştırma gerçekleşir
3. Eğer kesme aktifse → NVIC tetiklenir
4. TIM2_IRQHandler çağrılır
5. HAL_TIM_OC_DelayElapsedCallback() içinde LED toggle yapılır



## Proje Videosu
https://github.com/user-attachments/assets/8a47fbfe-2fbc-4c0d-bf8e-57f548d7181d





