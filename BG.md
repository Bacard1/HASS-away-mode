![BANNER](/img/banner.png)

# 🔋 HASS-AWAY-MODE / БЕЗ ПРИСЪСТВИЕ
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg?color=ff00d8)](https://opensource.org/licenses/MIT)
![GitHub last commit](https://img.shields.io/github/last-commit/Bacard1/HASS-away-mode.svg?color=ff00d8)
[![hacs_badge](https://img.shields.io/badge/HACS-2025.5.3-orange.svg?color=ff00d8)](https://github.com/hacs/integration)

[![Home Assistant](https://img.shields.io/badge/.-HOME_ASSISTANT-blue?logo=homeassistant)](https://www.home-assistant.io/) 
[![Donate via PayPal](https://img.shields.io/badge/PayPal-DONATE-blue?logo=paypal)](https://www.paypal.com/donate/?hosted_button_id=AAWFZVF2XCP5A)
![Script](https://img.shields.io/badge/Script-YAML-blue?logo=yaml)

[![Български](https://img.shields.io/badge/BG-ЕЗИК-gree?logo=translate&labelColor=gray&style=flat-square&link=https://example.com/bg
)](BG.md)
[![Deutch](https://img.shields.io/badge/DE-SPRACHE-gree?logo=translate&labelColor=gray&style=flat-square&link=https://example.com/bg
)](DE.md)
[![English](https://img.shields.io/badge/EN-LANGUAGE-gree?logo=translate&labelColor=gray&style=flat-square&link=https://example.com/bg)](README.md)

Умна автоматизация с Home Assistant, която при отсъствие автоматично изключва медийни устройства, прекъсва захранването на неизползвани уреди и намалява температурата за пестене на енергия.

Искаш ли и кратка версия за социални мрежи?

---

## 📦 СЪДЪРЖАНИЕ

- [🔋 HASS-AWAY-MODE / БЕЗ ПРИСЪСТВИЕ](#-hass-away-mode--без-присъствие)
  - [📦 СЪДЪРЖАНИЕ](#-съдържание)
  - [🖋️ ПОДГОТОВКА](#️-подготовка)
    - [👨‍👩‍👧‍👦 ФАМИЯ:](#-фамия)
    - [🧩 СТРАТЕГИЯ:](#-стратегия)
    - [📎 АВТОМАТИЗАЦИЯ:](#-автоматизация)

## 🖋️ ПОДГОТОВКА

### 👨‍👩‍👧‍👦 ФАМИЯ:
Създадох група която да обединява всички хора който обитават домакинството за улеснение при автоматизации.

> [!TIP]
> Обединяването на сензори, хора и така на татък, е добра практика когато трябва те да бъдат фактора за активиране на автоматизация.
> **Причина -** активирането на автоматизаци при промяната на статуса на всеки един член е извършване на действия и проверки от "HomeAssistent", който така или ще бъдат спрени от условието на автоматизацията. Това ги прави безмисленни!
> Когато те обаче бъдат обединени статувът на групата приема друга стойност само ако всички членове са си сменили статуса и активирането на автоматизацията придубива смисъл.

В /config/groups.yaml съсдадох следната група:
```yaml
family:
  name: Family
  icon: mdi:home-heart
  entities:
    - person.user1
    - person.user2
    - person.user3
    - person.user4
    - person.user5
    - person.user6
```

### 🧩 СТРАТЕГИЯ:
Автоматизациите в "HomeAssistent" предоставят възможност за превключване, изключване, изгасяне, настройване на мноцество устройства с определен етикет.<br>
Моят "HomeAssistent" при отсъствие обече няма да изключва само лампи, телевизори и така на татък, а и почти всички електро уреди сързани просто към електро мрежата като кафемашина, аспиратор, монитори и така на татък. Поради тази причина част от електро уредите трябва да се включват отново след като някой от семеиството се прибере в къщи. за целта създадох два етикета:<br>

> Етикет: **Няма присъствие** - *който ще управлява устройствата който трябва да се изключат.*
![away](/img/nqma_prisastwie.png)

> Етикет: **Има присъствие** - *който ще управлява устройствата който трябва да се включът.*
![img](/img/ima_prisastwie.png.png)

### 📎 АВТОМАТИЗАЦИЯ:
Добавям към всяка автоматизация всички видове устройства, бутони и т.н но "HomeAssistent" не показва етикетите преди да има поно устройство с етикетът. Това не е проблем аз ги добавям ръчно, понеже ми е много по лесно да добавям и махам етикети от колко да променям всеки път автоматизацията. във края на кода добавям тези редове.:

```yaml
target:
  label_id: bez_prisstvie
```

>Резултат:
![img](/img/add_label.png)

- **Няма присъствие**- автоматизацията ще изглежда по следният начин.

```yaml
alias: Без присъствие
description: ""
triggers:
  - trigger: state
    entity_id:
      - person.user1
    to: not_home
    for:
      hours: 0
      minutes: 5
      seconds: 1
    enabled: true
  - trigger: state
    entity_id:
      - person.user2
    to: not_home
    for:
      hours: 0
      minutes: 5
      seconds: 1
    enabled: true
conditions:
  - condition: and
    conditions:
      - condition: state
        entity_id: person.user1
        state: not_home
      - condition: state
        entity_id: person.user2
        state: not_home
    enabled: true
actions:
  - action: fan.turn_off
    metadata: {}
    data: {}
    target:
      label_id: bez_prisstvie
  - action: climate.set_temperature
    metadata: {}
    data:
      hvac_mode: "off"
    target:
      label_id: bez_prisstvie
  - action: media_player.turn_off
    metadata: {}
    data: {}
    target:
      label_id: bez_prisstvie
  - action: switch.turn_off
    metadata: {}
    data: {}
    target:
      label_id: bez_prisstvie
  - action: light.turn_off
    metadata: {}
    data: {}
    target:
      label_id: bez_prisstvie
  - action: button.press
    metadata: {}
    data: {}
    target:
      label_id: bez_prisstvie
  - action: automation.turn_off
    metadata: {}
    data:
      stop_actions: true
    target:
      label_id: bez_prisstvie
  - action: script.turn_on
    metadata: {}
    data: {}
    target:
      label_id: bez_prisstvie
mode: restart
```

> [!CAUTION]
> Обърнете внимание че автоматизацията завършва на "**mode: restart**"!!!
>  Това гарантира, че ако някой от тригерите се активира отново (напр. човек се е върнал за малко, после пак е напуснал), автоматизацията ще прекъсне текущото изпълнение и ще започне отначало.<br>
>Освен това винаги се изпълнява при ново задействане – най-гъвкав вариант тук.
> Информация за видовете автоматизации можете да намерите [ТУК](https://github.com/Bacard1/homeassistant/blob/main/automations/README.md)

- **Има присъствие**- автоматизацията ще изглежда по следният начин.

```yaml
alias: "Има присъствие "
description: ""
triggers:
  - trigger: state
    entity_id:
      - person.user1
    to: home
  - trigger: state
    entity_id:
      - person.user2
    to: home
conditions: []
actions:
  - action: fan.turn_on
    metadata: {}
    data: {}
    target:
      label_id: ima_prisstvie
  - action: climate.set_temperature
    metadata: {}
    data:
      hvac_mode: "off"
    target:
      label_id: ima_prisstvie
  - action: media_player.turn_on
    metadata: {}
    data: {}
    target:
      label_id: ima_prisstvie
  - action: switch.turn_on
    metadata: {}
    data: {}
    target:
      label_id: ima_prisstvie
  - action: light.turn_on
    metadata: {}
    data: {}
    target:
      label_id: ima_prisstvie
  - action: button.press
    metadata: {}
    data: {}
    target:
      label_id: ima_prisstvie
  - action: automation.turn_on
    metadata: {}
    data:
      stop_actions: true
    target:
      label_id: ima_prisstvie
  - action: script.turn_on
    metadata: {}
    data: {}
    target:
      label_id: ima_prisstvie
mode: restart
```

Всичко е готово, ако обаче имате като мен деца който ходят на училище то можете да направите за всяки по отделно да изключва всичко в стаята им докато не си е в къщи!!!

>Резултат:
![alt text](/img/device_with_lanel.png)

> [!TIP]
> Ако този проект ви е харесъл, [ТУК](https://github.com/Bacard1?tab=repositories) ще намерите още интересни гранилища направени от мен.<br>
> Ако срещате затруднения или имате въпроси не се колебайте да се свържете с мен.