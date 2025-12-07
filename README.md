# sleepd

> sleep as android status integration for home assistant

[![HACS Custom](https://img.shields.io/badge/HACS-Custom-41BDF5?style=flat-square&logo=homeassistant)](https://hacs.xyz/docs/faq/custom_repositories)
[![License](https://img.shields.io/badge/license-MIT-green?style=flat-square)](LICENSE)

---

## what it does

sleepd listens for Sleep As Android MQTT messages and exposes sleep state, events, and controls to Home Assistant. track when you're asleep, detect snoring, control alarms, and automate your smart home based on your sleep.

## sensors

| sensor | description |
|--------|-------------|
| wake status | `awake` or `asleep` based on configurable states and durations |
| sleep stage | current stage from sleep as android (light, deep, rem, awake) |
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

   [![Add Integration](https://my.home-assistant.io/badges/config_flow_start.svg)](https://my.home-assistant.io/redirect/config_flow_start/?domain=sleepd)

## configuration

| option | description |
|--------|-------------|
| name | identifier for this user/device |
| topic | mqtt topic (must match sleep as android settings) |
| awake duration | seconds of awake events before marking as awake |
| sleep duration | seconds of sleep events before marking as asleep |
| awake states | which states indicate being awake |
| sleep states | which states indicate being asleep |
| mobile app | companion app target for buttons (optional) |

## sleep as android setup

1. open sleep as android > settings > services > automation > MQTT

2. connection string format:
   ```
   tcp://user:pass@your-ha-ip:1883
   ```
   or with ssl:
   ```
   ssl://user:pass@your-ha-ip:8883
   ```

3. set topic to match your sleepd config

4. enable automatic tracking

## compatible wearables

tested with:
- xiaomi mi band 7 (via notify for mi band)
- garmin fenix 7x (via garmin alternative - paid)
- amazfit gtr3 pro (may require root for auth key)

<details>
<summary>mi band 7 setup with notify</summary>

1. pair band with mi fitness app
2. extract auth key via adb:
   ```bash
   adb shell
   grep -E "authKey=[a-z0-9]*," /sdcard/Android/data/com.xiaomi.wearable/files/log/XiaomiFit.device.log | \
   awk -F ", " '{print $17}' | grep authKey | tail -1 | awk -F "=" '{print $2}'
   ```
3. uninstall mi fitness
4. install [notify for mi band](https://play.google.com/store/apps/details?id=com.mc.miband1)
5. enter auth key during setup
6. enable sleep as android in notify settings

</details>

<details>
<summary>zepp auth key extraction (rooted)</summary>

```bash
su
cd /data/data/com.huami.watch.hmwatchmanager/databases/
sqlite3 origin_db_* "SELECT AUTHKEY FROM DEVICE;"
```

</details>

## changelog

**1.0.0** - rebranded from saas to sleepd, removed next alarm sensor

**0.2.1** - fixed manifest error, config reconfigure now works

**0.2.0** - fixed deprecation warnings for HA 2025.12 (breaking: re-add integration after update)

**0.1.0** - fixed wake status timing, sound sensor bugs

---

*formerly known as [saas](https://github.com/sudoxnym/saas)*
