# Репозиторий для моего канала в Дзен - "Мой умный дом"
Здесь будут примеры кода для автоматизаций и скриптов Home Assistant и сопутствующих проектов для удобного просмотра. 
Наполнение будет пополняться по мере выхода новых статей.

[Ссылка на канал](https://dzen.ru/mysmart "Ссылка на канал")

Также есть телеграм-канал со скидками на "умные устройства"

[Ссылка на канал со скидками](https://dzen.ru/mysmart "Ссылка на канал")


# Примеры
## Как я автоматизировал включение света в прихожей (Home Assistant)
Свет включается автоматически, когда подходишь к зеркалу и выключается, когда рядом никого нет

[Ссылка на статью](https://dzen.ru/a/ZQ8wHmtCVGPIYFvy "Ссылка на статью")

### Подключение датчика hc-sr04 в ESP Home
```yaml
sensor:  
  - platform: ultrasonic    
    trigger_pin: D3    
    echo_pin: D4    
    name: "Ultrasonic Sensor"
    id: ultrasonic_distance   
    update_interval: 0.25s
    unit_of_measurement: "cm"
```

### Создание бинарного сенсора, который срабатывает, если расстояние до цели меньше 1 метра
```yaml
binary_sensor:
  - platform: template
    name: "Ultrasonic Presence"
    lambda: |-
      if (id(ultrasonic_distance).state < id(1)) {
        // Presence is detected
        return true;
      } else {
        // No presence detected.
        return false;
      }
    filters:
      - delayed_off: 250ms
```

### Автоматизация для включения света
```yaml
alias: Aвтоматическое включение света в прихожей (1)
description: He, ну реально бесит
trigger:
  - type: opened
    platform: device
    device_id: e6be8bc3ae7f5c24c6a079da49a8ebc5
    entity_id: binary_sensor.tuyatec_d79sfnqe_rh3001_iaszone
    domain: binary_sensor
  - platform: state
    entity_id:
    - binary_sensor.distance_sensor_ultrasonic_presence
    from: "off"
    to: "on"
    for:
    hours: 0
    minutes: 0
    seconds: 0
condition: []
action:
  - service: light.turn_on
    data: {}
    target:
    entity_id: light.gs_brhm@e27w70_i1_light
  - service: timer.cancel
    data: {}
    target:
    entity_id: timer.tainer_dlia_upravleniia_svetom_v_prikhozheimode: single
```


Передаем сообщение через Siri на колонку с Алисой используя Home Assistant
Как я упрощаю автоматизации в умном доме
Делаем несовсем глупый вентилятор чуть умнее
