# NodeMcu, Home Assistant, MQTT and Google Home

## CC2500 to NodeMcu Pin Connections
```
 *  Pin Connections
 * CC2500     NodeMCU 
 * VDD    ->      3V3
 * SI     ->      D7
 * SCLK   ->      D5
 * SO     ->      D6
 * VSS    ->      GND
 * CSn    ->      D8
```

## NodeMcu_BedControl.ino

The file NodeMcu_SertaBedControl.ino contains the code to build the bed remote using a NodeMcu module and MQTT. This can be used in a Home Assisant setup. In this setup MQTT is used by Home Assistant to publish commands to a **bed/command** topic which the nodeMCU will subscribe to. Feel free to change the topic. But if you do, the topic in the ino must match that in your Home Assistant configuration.yaml. You must edit the ino file, the below fields must be changed to your wifi and mqtt connection info.

```
#define wifi_ssid "YourWifiSSID" //type your WIFI information inside the quotes
#define wifi_password "YourWifiPassword"
#define mqtt_server "YourMQQTIpAddress"
#define mqtt_user "yourMqttusername" 
#define mqtt_password "yourMqttPassword"
#define mqtt_port YourMqttPort
```
Once you have loaded the NodeMcu with the program we can now move onto setting up the remote in Home Assistant.

## Home Assistant Setup
This next section assumes you have already setup MQTT in Home Assistant. If not there is plenty of info on doing that on the Home Assistant website.
In your Home Assistant configuration.yaml file add the following scripts(you can also place them in the scripts.yaml if you are using it). This will setup scripts to publish the commands to the mqtt bed/command topic.
```
script:
  flat:
    sequence:
    - service: mqtt.publish
      data:
        topic: "bed/command"
        payload: "FLAT"
  zerog:
    sequence:
    - service: mqtt.publish
      data:
        topic: "bed/command"
        payload: "ZEROG"
  headup:
    sequence:
    - service: mqtt.publish
      data:
        topic: "bed/command"
        payload: "HEADUP"
  headdown:
    sequence:
    - service: mqtt.publish
      data:
        topic: "bed/command"
        payload: "HEADDOWN"
  footup:
    sequence:
    - service: mqtt.publish
      data:
        topic: "bed/command"
        payload: "FOOTUP"
  footdown:
    sequence:
    - service: mqtt.publish
      data:
        topic: "bed/command"
        payload: "FOOTDOWN"
  tvpc:
    sequence:
    - service: mqtt.publish
      data:
        topic: "bed/command"
        payload: "TVPC"
  lounge:
    sequence:
    - service: mqtt.publish
      data:
        topic: "bed/command"
        payload: "LOUNGE"
  head:
    sequence:
    - service: mqtt.publish
      data:
        topic: "bed/command"
        payload: "HEAD"
  foot:
    sequence:
    - service: mqtt.publish
      data:
        topic: "bed/command"
        payload: "FOOT"
  timer:
    sequence:
    - service: mqtt.publish
      data:
        topic: "bed/command"
        payload: "TIMER"
  wave:
    sequence:
    - service: mqtt.publish
      data:
        topic: "bed/command"
        payload: "WAVE"
```
Next we need to create the ui elements which will trigger the scripts.

From the Home Assistant Main Overview Page click the three dot menu button in the upper right corner and select configure UI(![Image](configureUI.png)). Then click the + button to add a card(![Image](plusButton.png)), select the Vertical Stack. Click the Show Code Editor in the bottom left corner(![Image](verticalStack.png)). And paste the following in the editor and then hit save.
```
cards:
  - cards:
      - entity: script.head
        hold_action:
          action: more-info
        name: HEAD
        show_icon: false
        show_name: true
        show_state: false
        tap_action:
          action: toggle
        type: button
      - entity: script.timer
        hold_action:
          action: more-info
        name: TIMER/ ALL OFF
        show_icon: false
        show_name: true
        show_state: false
        tap_action:
          action: toggle
        type: button
      - entity: script.foot
        hold_action:
          action: more-info
        name: FOOT
        show_icon: false
        show_name: true
        show_state: false
        tap_action:
          action: toggle
        type: button
    type: horizontal-stack
  - cards:
      - entity: script.tvpc
        hold_action:
          action: more-info
        name: TV/PC
        show_icon: false
        show_name: true
        show_state: false
        tap_action:
          action: toggle
        type: button
      - entity: script.wave
        hold_action:
          action: more-info
        icon: 'mdi:approximately-equal'
        icon_height: 25px
        show_icon: true
        show_name: false
        show_state: false
        tap_action:
          action: toggle
        type: button
      - entity: script.lounge
        hold_action:
          action: more-info
        name: LOUNGE
        show_icon: false
        show_name: true
        show_state: false
        tap_action:
          action: toggle
        type: button
    type: horizontal-stack
  - cards:
      - entity: script.headup
        hold_action:
          action: more-info
        name: HEAD UP
        show_icon: false
        show_name: true
        show_state: false
        tap_action:
          action: toggle
        type: button
      - entity: script.flat
        hold_action:
          action: more-info
        name: FLAT
        show_icon: false
        show_name: true
        show_state: false
        tap_action:
          action: toggle
        type: button
      - entity: script.footup
        hold_action:
          action: more-info
        name: FOOT UP
        show_icon: false
        show_name: true
        show_state: false
        tap_action:
          action: toggle
        type: button
    type: horizontal-stack
  - cards:
      - entity: script.headdown
        hold_action:
          action: more-info
        name: HEAD DOWN
        show_icon: false
        show_name: true
        show_state: false
        tap_action:
          action: toggle
        type: button
      - entity: script.zerog
        hold_action:
          action: more-info
        name: ZERO G
        show_icon: false
        show_name: true
        show_state: false
        tap_action:
          action: toggle
        type: button
      - entity: script.footdown
        hold_action:
          action: more-info
        name: FOOT DOWN
        show_icon: false
        show_name: true
        show_state: false
        tap_action:
          action: toggle
        type: button
    type: horizontal-stack
title: Bed Remote
type: vertical-stack
```
That should give you the buttons needed to contol the bed(![Image](BedRemoteCard.png)).


## Google Home

If you would like to use google home to control your bed using voice commands you need to have Home Assistant setup to communicate with Google Home. Look here for setup instructions, https://www.home-assistant.io/integrations/google_assistant/. Once Home Assistant is setup for Google you can edit your configuration.yaml's Google Assistant section to look like the following.

```
google_assistant:
  project_id: home-assistant-2019-5156c
  service_account:
    private_key: -----BEGIN PRIVATE KEY----- Example - Your Private Key Here\n-----END PRIVATE KEY-----\n
    client_email: your client email address here
  report_state: true
  exposed_domains:
    - switch
    - light
    - climate
    - group
    - fan
    - script
  entity_config:
    script.flat:
      name: Bed Flat
      expose: true
    script.lounge:
      name: Bed Lounge
      expose: true
    script.foot:
      name: Bed  Massage
      expose: true
    script.head:
      name: Bed Head Massage
      expose: true
    script.headup:
      name: Bed Head Up
      expose: true
    script.headdown:
      name: Bed Head Down
      expose: true
    script.footup:
      name: Bed Foot Up
      expose: true
    script.footdown:
      name: Bed Foot Down
      expose: true
    script.tvpc:
      name: Bed TV PC
      expose: true

```
Once you save the configuration.yaml and restart Home Assistant simply ask Google to resync devices.
You can then use a voice command like *Hey Google Activate Lounge*.  I found it even better to create routines in Google Home, such as Bed Head Up that calls Activate Head Up. That way I can say *Hey Google Bed Head Up*, which is much more natural.

Good Luck and Enjoy!