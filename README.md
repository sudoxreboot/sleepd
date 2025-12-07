# sleepd

> real-time sleep state tracking for home assistant via mqtt

[![HACS Custom](https://img.shields.io/badge/HACS-Custom-41BDF5?style=flat-square&logo=homeassistant)](https://hacs.xyz/docs/faq/custom_repositories)
[![License](https://img.shields.io/badge/license-MIT-green?style=flat-square)](LICENSE)

---

## what it does

sleepd processes sleep-related mqtt messages from android apps that track sleep in real time. it exposes sleep states, events, and controls to home assistant - letting you automate your smart home based on whether you're awake or asleep.

**compatible apps:**
- [sleep as android](https://sleep.urbandroid.org/) (primary)
- any app that publishes sleep events via mqtt in a compatible format

## sensors

| sensor | description |
|--------|-------------|
| wake status | `awake` or `asleep` based on configurable states and durations |
| sleep stage | current stage (light, deep, rem, awake) |
| sleep tracking | whether tracking is active or paused |
| alarm event | alarm events like snooze, dismiss, skip |
| sound | snore, talk, cough, and other detected sounds |
| disturbance | apnea and anti-snoring events |
| lullaby | lullaby playback status |
| state | raw event from last mqtt message |

## buttons

*requires home assistant companion app*

- alarm dismiss / snooze
- lullaby stop
- sleep tracking start / stop / pause / resume
- sleep tracking start with smart alarm

## installation

1. add this repo to HACS custom repositories:
   ```
   https://github.com/sudoxnym/sleepd
   ```

2. search for **sleepd** in HACS and install

3. restart home assistant

4. add integration via UI or click below:

   [![Add Integration](https://my.home-assistant.io/badges/config_flow_start.svg)](https://my.home-assistant.io/redirect/config_flow_start/?domain=saas)

## configuration

| option | description |
|--------|-------------|
| name | identifier for this user/device |
| topic | mqtt topic to subscribe to |
| awake duration | seconds of awake events before marking as awake |
| sleep duration | seconds of sleep events before marking as asleep |
| awake states | which states indicate being awake |
| sleep states | which states indicate being asleep |
| mobile app | companion app target for buttons (optional) |

## mqtt message format

sleepd expects json messages with an `event` field:

```json
{"event": "sleep_tracking_started"}
{"event": "sleep_tracking_stopped"}
{"event": "alarm_alert_start", "value1": "1733580000000"}
{"event": "light_sleep"}
{"event": "deep_sleep"}
{"event": "rem"}
```

## sleep as android setup

1. open sleep as android > settings > services > automation > MQTT

2. connection string:
   ```
   tcp://user:pass@your-ha-ip:1883
   ```

3. set topic to match your sleepd config

4. enable automatic tracking

## compatible wearables

tested with sleep as android:
- xiaomi mi band 7 (via notify for mi band)
- garmin fenix 7x (via garmin alternative)
- amazfit gtr3 pro

<details>
<summary>mi band 7 auth key extraction</summary>

```bash
adb shell
grep -E "authKey=[a-z0-9]*," /sdcard/Android/data/com.xiaomi.wearable/files/log/XiaomiFit.device.log | \
awk -F ", " '{print $17}' | grep authKey | tail -1 | awk -F "=" '{print $2}'
```

</details>

## changelog

**1.0.0** - rebranded, removed next alarm sensor, generic mqtt focus

---

*formerly known as saas*
