![BANNER](/img/banner.png)

# 🔋 HASS-AWAY-MODE / NO PRESENCE
[![PayPal Donate](https://img.shields.io/badge/PayPal-Donate-blue?logo=paypal)](https://www.paypal.com/donate/?hosted_button_id=AAWFZVF2XCP5A)
![Script](https://img.shields.io/badge/logo-yaml-green?logo=yaml)
[![ENGLISH](https://img.shields.io/badge/ENGLISH-language-green?logo=translate&labelColor=gray&style=flat-square&link=https://example.com/bg)](README.md)

Smart automation with Home Assistant that, when no one is home, automatically powers down media devices, cuts power to unused electronics, and reduces temperature to save energy.

---

## 📦 CONTENT

- [🔋 HASS-AWAY-MODE / NO PRESENCE](#-hass-away-mode--no-presence)
  - [📦 CONTENT](#-content)
  - [🖋️ SETUP](#️-setup)
    - [👨‍👩‍👧‍👦 FAMILY:](#-family)
    - [STRATEGY:](#strategy)
    - [AUTOMATION:](#automation)

## 🖋️ SETUP

### 👨‍👩‍👧‍👦 FAMILY:
I created a group to combine all people in the household for easier automation logic.

> [!TIP]
> Grouping sensors, people, etc., is a best practice when they are used as a trigger in automations.
> **Why?** Because if you use individual persons as triggers, HA may perform actions and evaluations even when just one person changes state — often pointlessly.
> But if they are in a group, the group's state changes **only** when all members are away — making the automation meaningful.

In `/config/groups.yaml`, I defined the following:

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

### STRATEGY:
Home Assistant automations allow switching, turning off, or setting devices based on labels.<br>
My setup doesn’t just control lights or TVs — it also powers off nearly all electric appliances like coffee machines, hoods, monitors, etc.  
Some devices should turn back on when someone returns — so I created **two labels**:

> Label: **No Presence** – *used to turn off devices*  
![away](/img/nqma_prisastwie.png)

> Label: **Presence Detected** – *used to turn on devices*  
![img](/img/ima_prisastwie.png.png)

### AUTOMATION:
I include all device types in my automations. Home Assistant doesn’t show labels until a device is assigned one — but I add them manually, which is easier than editing the automation each time.  
At the end of each automation I add:

```yaml
target:
  label_id: bez_prisstvie
```

> Result:
![img](/img/add_label.png)

### ✅ NO PRESENCE AUTOMATION

```yaml
alias: No Presence
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
> Make sure to use `mode: restart`!  
> This ensures that if the automation is triggered again (e.g. someone briefly returns), it cancels and restarts — the most flexible behavior.
> Learn more about automation modes [HERE](https://github.com/Bacard1/homeassistant/blob/main/automations/README.md)

---

### ✅ PRESENCE DETECTED AUTOMATION

```yaml
alias: Presence Detected
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

If, like me, you have kids who go to school, you can also create personalized room automations — disabling everything in their room while they're gone!

> Result:
![alt text](/img/device_with_lanel.png)

> [!TIP]
> If you like this project, check out more of my work [HERE](https://github.com/Bacard1?tab=repositories).  
> Feel free to contact me if you need help or have questions!
