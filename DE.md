Hier ist die übersetzte Version des Dokuments auf Deutsch:


![BANNER](/img/banner.png)

# 🔋 HASS-AWAY-MODE / ABWESENHEITSMODUS
[![Home Assistant](https://img.shields.io/badge/🏠_Home_Assistant-41BDF5?logo=homeassistant)](https://www.home-assistant.io/) [![Donate via PayPal](https://img.shields.io/badge/PayPal-Donate-blue?logo=paypal)](https://www.paypal.com/donate/?hosted_button_id=AAWFZVF2XCP5A)
![Script](https://img.shields.io/badge/logo-yaml-green?logo=yaml)
[![Български](https://img.shields.io/badge/BG_Български-език-green?logo=translate&labelColor=gray&style=flat-square&link=https://example.com/bg
)](BG.md)
[![Deutch](https://img.shields.io/badge/DE_Deutsche-sprache-green?logo=translate&labelColor=gray&style=flat-square&link=https://example.com/bg
)](DE.md)
[![English](https://img.shields.io/badge/EN_English-language-green?logo=translate&labelColor=gray&style=flat-square&link=https://example.com/bg)](README.md)

Intelligente Automatisierung mit Home Assistant, die bei Abwesenheit automatisch Mediengeräte ausschaltet, die Stromversorgung ungenutzter Geräte unterbricht und die Temperatur senkt, um Energie zu sparen.

Möchtest du eine kurze Version für soziale Netzwerke?

---

## 📦 INHALT

- [🔋 HASS-AWAY-MODE / ABWESENHEITSMODUS](#-hass-away-mode--abwesenheitsmodus)
	- [📦 INHALT](#-inhalt)
	- [🖋️ VORBEREITUNG](#️-vorbereitung)
		- [👨‍👩‍👧‍👦 FAMILIE:](#-familie)
		- [🧩 STRATEGIE:](#-strategie)
		- [📎 AUTOMATISIERUNG:](#-automatisierung)

## 🖋️ VORBEREITUNG

### 👨‍👩‍👧‍👦 FAMILIE:
Ich habe eine Gruppe erstellt, die alle Personen im Haushalt vereint, um die Automatisierung zu vereinfachen.

> [!TIP]
> Das Zusammenfassen von Sensoren, Personen usw. ist eine gute Praxis, wenn sie der Auslöser für eine Automatisierung sein sollen.
> **Grund:** Das Auslösen von Automatisierungen bei Statusänderungen jedes einzelnen Mitglieds führt zu Aktionen und Überprüfungen durch "Home Assistant", die sowieso durch die Bedingung der Automatisierung gestoppt werden. Das macht sie sinnlos!
> Wenn sie jedoch in einer Gruppe zusammengefasst sind, ändert sich der Status der Gruppe nur, wenn alle Mitglieder ihren Status geändert haben, und das Auslösen der Automatisierung ergibt Sinn.

In /config/groups.yaml habe ich folgende Gruppe erstellt:
```yaml
family:
  name: Familie
  icon: mdi:home-heart
  entities:
    - person.user1
    - person.user2
    - person.user3
    - person.user4
    - person.user5
    - person.user6
```

### 🧩 STRATEGIE:
Die Automatisierungen in "Home Assistant" bieten die Möglichkeit, Geräte mit bestimmten Labels ein- oder auszuschalten, zu deaktivieren oder zu konfigurieren.<br>
Mein "Home Assistant" schaltet bei Abwesenheit nicht nur Lampen, Fernseher usw. aus, sondern auch fast alle elektrischen Geräte, die einfach an das Stromnetz angeschlossen sind, wie Kaffeemaschine, Staubsauger, Monitore usw. Aus diesem Grund müssen einige Geräte wieder eingeschaltet werden, sobald ein Familienmitglied nach Hause zurückkehrt. Dafür habe ich zwei Labels erstellt:<br>

> Label: **Keine Anwesenheit** - *das die Geräte steuert, die ausgeschaltet werden sollen.*
![away](/img/nqma_prisastwie.png)

> Label: **Anwesenheit** - *das die Geräte steuert, die eingeschaltet werden sollen.*
![img](/img/ima_prisastwie.png.png)

### 📎 AUTOMATISIERUNG:
Ich füge jeder Automatisierung alle Arten von Geräten, Schaltern usw. hinzu, aber "Home Assistant" zeigt die Labels erst an, wenn mindestens ein Gerät mit dem Label vorhanden ist. Das ist kein Problem, ich füge sie manuell hinzu, da es für mich viel einfacher ist, Labels hinzuzufügen oder zu entfernen, als jedes Mal die Automatisierung zu ändern. Am Ende des Codes füge ich diese Zeilen hinzu:

```yaml
target:
  label_id: bez_prisstvie
```

>Ergebnis:
![img](/img/add_label.png)

- **Keine Anwesenheit** - Die Automatisierung sieht folgendermaßen aus:

```yaml
alias: Abwesend
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
> Beachten Sie, dass die Automatisierung mit "**mode: restart**" endet!!!
> Dies stellt sicher, dass die Automatisierung, wenn einer der Trigger erneut ausgelöst wird (z.B. eine Person kurz zurückkehrt und dann wieder geht), die aktuelle Ausführung unterbricht und von vorne beginnt.<br>
> Außerdem wird sie bei jedem neuen Auslösen ausgeführt – die flexibelste Option hier.
> Informationen zu den Arten von Automatisierungen finden Sie [HIER](https://github.com/Bacard1/homeassistant/blob/main/automations/README.md)

- **Anwesenheit** - Die Automatisierung sieht folgendermaßen aus:

```yaml
alias: "Anwesend"
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

Alles ist fertig. Falls Sie jedoch wie ich Kinder haben, die zur Schule gehen, können Sie für jedes Kind separat alles in ihrem Zimmer ausschalten, solange sie nicht zu Hause sind!!!

>Ergebnis:
![alt text](/img/device_with_lanel.png)

> [!TIP]
> Wenn Ihnen dieses Projekt gefallen hat, finden Sie [HIER](https://github.com/Bacard1?tab=repositories) weitere interessante Repositories von mir.<br>
> Wenn Sie Schwierigkeiten haben oder Fragen haben, zögern Sie nicht, mich zu kontaktieren.
