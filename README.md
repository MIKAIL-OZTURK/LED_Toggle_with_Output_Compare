# 🟢 STM32 Output Compare ile Periyodik LED Toggle

Bu proje, STM32F407VG mikrodenetleyicisi üzerinde **Timer Output Compare (OC)** modunu kullanarak belirli bir zaman aralığında bir LED’in toggle edilmesini (yak-sön) sağlamaktadır.


## Temel Bilgilendirme
### Timer Output Compare (OC) Mode Nedir ?                                      
Output compare yani çıkış karşılaştırma modu mikrodenetleyicinin timer biriminde çıkış karşılatırma registerına(CCRx) yazılan değer ile sayaç registerındaki(CNT) değer eşleştiği zaman mikrodenetleyicide çıkış (toggle, interrupt, PWM, vb.) oluşturmaya yarayan özelliktir. 

```c
if(CCRx == CNT) {
    // Toggle, Interrupt, PWM...
}
```
Elbette bu karşılaştırma donanım (peripheral) içinde otomatik olarak gerçekleşir. Kodun içinde manuel if (CNT == CCR) gibi bir şey göremeyiz. Karşılaştırmayı timer donanımı kendi içinde yapar, eğer eşitlik sağlanırsa donanım belirttiğimiz moda göre bir olay üretir (toggle, interrupt, PWM, vb.), eğer IT (interrupt) aktifse, kesme çağrılır. (Ben uygulamamda interrupt ile led toggle gerçekleştirdim.)

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
| GPIO        | GPIOD_PIN_14 (LED)     |



## Proje Kod Açıklaması
**1. İstenilen değerlere göre zamanlayıcının Prescaler ve ARR değerleri belirlenir.**

Uygulamada 0.5 saniyelik LED_Toggle işlemi istenmektedir. 
```
Timer_Frekans = (Sistem_Clock) / ((Prescaler + 1) x (ARR + 1))

0.5 saniye = 2 Hz (f = 1/T)-->

2 = ( 84 MHz ) / ((Prescaler + 1) x (ARR + 1))

Prescaler: 8399
ARR: 4999
```


**2. Timer başlatılır.**


```c
      HAL_TIM_OC_Start_IT(&htim2, TIM_CHANNEL_1);
```
Böylece Timer2'nin CNT (sayaç) değeri sürekli artar. Bu değer CCRx register’larda tanımladığımız eşik değerine ulaştığında, donanım kesme fonksiyonunu çağırır. 

**Peki uygulamamızda CCRx değerimiz nedir ?**

```c
sConfigOC.Pulse = 0;
```

Donanım, CNT değeri 0 olduğunda compare match tetiklenir ve kesme fonksiyonu çağrılır.

**3. Kesme Fonksiyonu**
```c
//stm32f4xxx.it.c
void TIM2_IRQHandler(void)
{
  HAL_TIM_IRQHandler(&htim2);
}

//main.c
void HAL_TIM_OC_DelayElapsedCallback(TIM_HandleTypeDef *htim)
{
	if(htim->Instance == TIM2 && htim->Channel == HAL_TIM_ACTIVE_CHANNEL_1)
	{
		HAL_GPIO_TogglePin(GPIOD, GPIO_PIN_14);        // 0.5 saniyede bir LED_Toggle işlemi gerçekleşir.
	}
}
```

**Temel Kod Yapısı -->**

1. CNT her clock tick'inde artar →
2. CNT == CCR1 olduğunda →
3. Compare Match olur →
4. Eğer kesme açıksa → NVIC kesmeyi alır →
5. TIM2_IRQHandler → HAL_TIM_IRQHandler →
6. HAL_TIM_OC_DelayElapsedCallback fonksiyonu çağrılır




## Proje Videosu



https://github.com/user-attachments/assets/8a47fbfe-2fbc-4c0d-bf8e-57f548d7181d





