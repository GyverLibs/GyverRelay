[![latest](https://img.shields.io/github/v/release/GyverLibs/GyverRelay.svg?color=brightgreen)](https://github.com/GyverLibs/GyverRelay/releases/latest/download/GyverRelay.zip)
[![PIO](https://badges.registry.platformio.org/packages/gyverlibs/library/GyverRelay.svg)](https://registry.platformio.org/libraries/gyverlibs/GyverRelay)
[![Foo](https://img.shields.io/badge/Website-AlexGyver.ru-blue.svg?style=flat-square)](https://alexgyver.ru/)
[![Foo](https://img.shields.io/badge/%E2%82%BD%24%E2%82%AC%20%D0%9F%D0%BE%D0%B4%D0%B4%D0%B5%D1%80%D0%B6%D0%B0%D1%82%D1%8C-%D0%B0%D0%B2%D1%82%D0%BE%D1%80%D0%B0-orange.svg?style=flat-square)](https://alexgyver.ru/support_alex/)
[![Foo](https://img.shields.io/badge/README-ENGLISH-blueviolet.svg?style=flat-square)](https://github-com.translate.goog/GyverLibs/GyverRelay?_x_tr_sl=ru&_x_tr_tl=en)  

[![Foo](https://img.shields.io/badge/ПОДПИСАТЬСЯ-НА%20ОБНОВЛЕНИЯ-brightgreen.svg?style=social&logo=telegram&color=blue)](https://t.me/GyverLibs)

# GyverRelay
GyverRelay - библиотека классического релейного регулятора для Arduino
- Обратная связь по скорости изменения величины
- Настройка гистерезиса, коэффициента усиления ОС, направления регулирования
- Возвращает результат по встроенному таймеру или в ручном режиме

### Совместимость
Совместима со всеми Arduino платформами (используются Arduino-функции)

### Документация
К библиотеке есть [расширенная документация](https://alexgyver.ru/GyverRelay/)

## Содержание
- [Установка](#install)
- [Инициализация](#init)
- [Использование](#usage)
- [Пример](#example)
- [Версии](#versions)
- [Баги и обратная связь](#feedback)

<a id="install"></a>
## Установка
- Библиотеку можно найти по названию **GyverRelay** и установить через менеджер библиотек в:
    - Arduino IDE
    - Arduino IDE v2
    - PlatformIO
- [Скачать библиотеку](https://github.com/GyverLibs/GyverRelay/archive/refs/heads/main.zip) .zip архивом для ручной установки:
    - Распаковать и положить в *C:\Program Files (x86)\Arduino\libraries* (Windows x64)
    - Распаковать и положить в *C:\Program Files\Arduino\libraries* (Windows x32)
    - Распаковать и положить в *Документы/Arduino/libraries/*
    - (Arduino IDE) автоматическая установка из .zip: *Скетч/Подключить библиотеку/Добавить .ZIP библиотеку…* и указать скачанный архив
- Читай более подробную инструкцию по установке библиотек [здесь](https://alexgyver.ru/arduino-first/#%D0%A3%D1%81%D1%82%D0%B0%D0%BD%D0%BE%D0%B2%D0%BA%D0%B0_%D0%B1%D0%B8%D0%B1%D0%BB%D0%B8%D0%BE%D1%82%D0%B5%D0%BA)
### Обновление
- Рекомендую всегда обновлять библиотеку: в новых версиях исправляются ошибки и баги, а также проводится оптимизация и добавляются новые фичи
- Через менеджер библиотек IDE: найти библиотеку как при установке и нажать "Обновить"
- Вручную: **удалить папку со старой версией**, а затем положить на её место новую. "Замену" делать нельзя: иногда в новых версиях удаляются файлы, которые останутся при замене и могут привести к ошибкам!


<a id="init"></a>
## Инициализация
```cpp
GyverRelay regulator;
GyverRelay regulator(mode); // mode: NORMAL / REVERSE
```

<a id="usage"></a>
## Использование
```cpp
// расчёт возвращает состояние для управляющего устройства (реле, транзистор) (1 вкл, 0 выкл)
boolean compute(float dt = 0);      // моментальный расчёт. Принимает dt в секундах для режима с ОС
boolean getResult();                // моментальный расчёт. Встроенный таймер для режима с ОС
boolean getResultTimer();           // расчёт по встроенному таймеру

void setDirection(boolean dir);     // направление регулирования (NORMAL, REVERSE)

float input = 0;                    // сигнал с датчика (например температура, которую мы регулируем)
float setpoint = 0;                 // заданная величина, которую должен поддерживать регулятор (температура)
bool output = 0;                    // выход регулятора (0 или 1)

float hysteresis = 0;               // ширина окна гистерезиса
float k = 0;                        // коэффициент усиления	по скорости (по умолч. 0)	
int16_t dT = 1000;                  // время итерации, мс (по умолч. секунда)
```

<a id="example"></a>
## Пример
Остальные примеры смотри в **examples**!
```cpp
/*
   Пример работы релейного регулятора в автоматическом режиме по встроенному таймеру
   Давайте представим, что на 3 пине у нас спираль нагрева, подключенная через реле
   И есть какой то абстрактный датчик температуры, на который влияет спираль
*/
#include "GyverRelay.h"

// установка, гистерезис, направление регулирования
GyverRelay regulator(REVERSE);
// либо GyverRelay regulator(); без указания направления (будет REVERSE)

void setup() {
  pinMode(3, OUTPUT);         // пин реле
  regulator.setpoint = 40;    // установка (ставим на 40 градусов)
  regulator.hysteresis = 5;   // ширина гистерезиса
  regulator.k = 0.5;          // коэффициент обратной связи (подбирается по факту)
  //regulator.dT = 500;       // установить время итерации для getResultTimer
}

// вариант с delay
void loop() {
  int temp;                 // например читаем с датчика температуру
  regulator.input = temp;   // сообщаем регулятору текущую температуру

  // getResult возвращает значение для управляющего устройства
  digitalWrite(3, regulator.getResult());  // отправляем на реле (ОС работает по своему таймеру)
  delay(100);
}

/*
  // вариант со встроенным таймером
  void loop() {
  int temp;                 // например читаем с датчика температуру
  regulator.input = temp;   // сообщаем регулятору текущую температуру

  // getResult возвращает значение для управляющего устройства
  digitalWrite(3, regulator.getResultTimer());  // отправляем на реле
  
  // также можно получить значение с выхода регулятора
  // regulator
  }
*/

/*
// вариант со своим таймером
void loop() {
  static uint32_t myTimer = 0;
  if (millis() - myTimer > 2000) {    // свой таймер на миллис, 2 секунды
    myTimer = millis();
    int temp;                 // например читаем с датчика температуру
    regulator.input = temp;   // сообщаем регулятору текущую температуру

    // getResult возвращает значение для управляющего устройства
    digitalWrite(3, regulator.compute(2));  // отправляем на реле. Время передаём вручную, у нас 2 секунды
  }
}
*/
```

<a id="versions"></a>
## Версии
- v2.1 - исправлена getResultTimer
- v2.2 - улучшен и упрощён алгоритм

<a id="feedback"></a>
## Баги и обратная связь
При нахождении багов создавайте **Issue**, а лучше сразу пишите на почту [alex@alexgyver.ru](mailto:alex@alexgyver.ru)  
Библиотека открыта для доработки и ваших **Pull Request**'ов!


При сообщении о багах или некорректной работе библиотеки нужно обязательно указывать:
- Версия библиотеки
- Какой используется МК
- Версия SDK (для ESP)
- Версия Arduino IDE
- Корректно ли работают ли встроенные примеры, в которых используются функции и конструкции, приводящие к багу в вашем коде
- Какой код загружался, какая работа от него ожидалась и как он работает в реальности
- В идеале приложить минимальный код, в котором наблюдается баг. Не полотно из тысячи строк, а минимальный код
