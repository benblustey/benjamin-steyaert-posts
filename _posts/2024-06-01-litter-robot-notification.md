---
title: Automated Litter-Robot
description: 
date: 2024-06-01
categories: automation, home-assistant
tags:
  - Home-Assistant
image: /assets/img/posts/home-lab-tour-2024-sm.jpg
pin: false
---

## Intro


## Process
* Monitor the Litter-Robot on waste level change
* When the waste level becomes a concern, notify
* When the waste level becomes critical, notify and change lights
* If the status of the litter robot status is anomalous, notify and change lights
* create a snooze button to change the lights back to normal for X amount of time

## Code
``` yaml
alias: Litter-Robot
description: ""
trigger:
  - platform: state
    entity_id:
      - vacuum.litter_robot_litter_box
    attribute: status
    from: error
    to: docked
    id: litter-robot-reset
  - platform: numeric_state
    entity_id:
      - sensor.litter_robot_waste_drawer
    above: 95
    below: 99
    id: litter-robot-warning
  - platform: state
    entity_id:
      - vacuum.litter_robot_litter_box
    attribute: status
    to: error
    id: litter-robot-error
    enabled: true
condition: []
action:
  - alias: Litter Robot Warning Triggered
    if:
      - condition: trigger
        id:
          - litter-robot-warning
    then:
      - service: scene.create
        metadata: {}
        data:
          snapshot_entities:
            - light.living_room_lamp
            - light.media_room_lamp
          scene_id: lamps_before
      - service: scene.turn_on
        metadata: {}
        target:
          entity_id: scene.litter_robot_warning
      - service: notify.mobile_app_bstey2
        metadata: {}
        data:
          message: >-
            Litter-Robot drawer is at {{
            states('sensor.litter_robot_waste_drawer', with_unit=True) }}
          title: Litter-Robot Waste Warning
    else:
      - service: scene.turn_on
        metadata: {}
        target:
          entity_id: scene.lamps_before
  - if:
      - condition: trigger
        id:
          - litter-robot-error
    then:
      - service: scene.create
        metadata: {}
        data:
          scene_id: lamps_before
          snapshot_entities:
            - light.living_room_lamp
            - light.media_room_lamp
      - service: scene.turn_on
        metadata: {}
        target:
          entity_id: scene.litter_robot_error
      - service: notify.mobile_app_bstey2
        metadata: {}
        data:
          message: >-
            Litter-Robot drawer {{
            states('vacuum.litter_robot_litter_box').upper() }}
          title: Litter-Robot Waste Error
    else:
      - service: scene.turn_on
        metadata: {}
        target:
          entity_id: scene.lamps_before
    enabled: true
    alias: Litter Robot Error Triggered
  - alias: Litter Robot Reset
    if:
      - condition: trigger
        id:
          - litter-robot-reset
    then:
      - service: scene.turn_on
        metadata: {}
        target:
          entity_id: scene.lamps_before
      - service: notify.mobile_app_bstey
        metadata: {}
        data:
          message: >-
            Litter-Robot drawer {{
            states('vacuum.litter_robot_litter_box').upper() }}
          title: Litter-Robot Waste Reset
    enabled: true
mode: single

```


## Whats Next?
-