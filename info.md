# Home Assistant sensor for german AWB waste collection schedule

## Discussion
https://community.home-assistant.io/t/awb-waste-collection-schedule/140486

## Functionality
The sensor shows the bin which will be collected the next day. The complete collection schedule is available as attributes of the sensor 

![alt text](https://github.com/jensweimann/awb/blob/master/preview1.png "glance card")

![alt text](https://github.com/jensweimann/awb/blob/master/preview2.png "glance card details")

## Configuration

### Find street number and street code:
https://www.offenedaten-koeln.de/dataset/strassenverzeichnis  
Strassenverzeichnis Standard 2015 -> Vorschau  
  
Domkloster 4, 50667 Köln:
 ```
street_code: 745
street_number: 4
```

### sensor
```
- platform: awb
  name: awb
  scan_interval: 3600
  street_code: 745
  street_number: 4
```

### customize
```
sensor.awb:
  friendly_name: Heute Mülltonne rausstellen
  icon: mdi:delete
```

### automation
```
- alias: AWB Notification
  trigger:
    - platform: time
      at: "18:00:00"
    - entity_id: binary_sensor.someone_is_home
      from: 'off'
      platform: state
      to: 'on'
  condition:
    - condition: and
      conditions:
      - condition: time
        after: '09:00:00'
      - condition: time
        before: '23:00:00'
      - condition: template
        value_template: "{{ (states.sensor.awb.state != 'Keine') and (states.sensor.awb.state != 'unknown') }}"
  action:
    - service: notify.my_telegram
      data_template:
        message: "{{ states.sensor.awb.name }}: {{  states.sensor.awb.state }}"
```
