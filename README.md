# sleepd

**real-time sleep state tracking for home assistant**

[![HACS Custom](https://img.shields.io/badge/HACS-Custom-00ffff?style=for-the-badge&logo=homeassistant)](https://hacs.xyz/docs/faq/custom_repositories)
[![License](https://img.shields.io/badge/license-MIT-ff00ff?style=for-the-badge)](LICENSE)

*processes sleep mqtt messages and exposes states, events, and controls to home assistant - automate your smart home based on whether you're awake or asleep.*

---

## ✨ features

- **wake status sensor** — `awake` or `asleep` based on configurable thresholds
- **sleep stage tracking** — light, deep, rem, awake
- **alarm controls** — dismiss, snooze, skip via HA buttons
- **sound detection** — snore, talk, cough events
- **lullaby control** — playback status and stop button
- **disturbance alerts** — apnea and anti-snoring events

## 📱 compatible apps

| app | notes |
|-----|-------|
| [sleep as android](https://sleep.urbandroid.org/) | primary |
| any mqtt sleep app | publishes sleep events via mqtt |

## 📊 sensors

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

## 🎮 buttons

*requires home assistant companion app*

| button | description |
|--------|-------------|
| alarm dismiss | dismiss active alarm |
| alarm snooze | snooze active alarm |
| lullaby stop | stop lullaby playback |
| sleep tracking start | begin tracking session |
| sleep tracking stop | end tracking session |
| sleep tracking pause | pause tracking session |
| sleep tracking resume | resume paused session |
| sleep tracking start with smart alarm | start with smart alarm enabled |

## 📥 installation

### option 1: hacs (recommended)

1. add to hacs

    [![open your hacs](https://my.home-assistant.io/badges/hacs_repository.svg)](https://my.home-assistant.io/redirect/hacs_repository/?owner=sudoxreboot&repository=sleepd&category=integration)

3. search for **sleepd** in HACS and install

4. restart home assistant

5. add integration via UI:

   [![Add Integration](https://my.home-assistant.io/badges/config_flow_start.svg)](https://my.home-assistant.io/redirect/config_flow_start/?domain=saas)

### option 2: manual

1. copy `custom_components/saas` to your HA `custom_components` directory
2. restart home assistant

## 📡 mqtt setup

sleepd expects json messages with an `event` field:

```json
{"event": "sleep_tracking_started"}
{"event": "sleep_tracking_stopped"}
{"event": "alarm_alert_start", "value1": "1733580000000"}
{"event": "light_sleep"}
{"event": "deep_sleep"}
{"event": "rem"}
```

<details>
  <summary><strong>📲 set up Notify for Mi Band 7</strong></summary>
  <ol>
    <li>pair **Mi Band 7** as you normally would with <a href="https://play.google.com/store/apps/details?id=com.xiaomi.wearable&hl=en_US">Mi Fitness</a></li>
    <li>obtain auth key for Notify app using ADB</li>
  </ol>

  <pre>
adb shell
grep -E "authKey=[a-z0-9]*," /sdcard/Android/data/com.xiaomi.wearable/files/log/XiaomiFit.device.log |
awk -F ", " '{print $17}' | grep authKey | tail -1 | awk -F "=" '{print $2}'
  </pre>

  <p>credit: <a href="https://www.reddit.com/r/miband/comments/15j0rfq/comment/kxlyzc6/">iamfosscad</a></p>

  <ol start="3">
    <li>uninstall **Mi Fitness**</li>
    <li>download/install <a href="https://play.google.com/store/apps/details?id=com.mc.miband1&hl=en_US">Notify for Mi Band</a></li>
    <li>follow prompts, input auth key, select Mi Fitness is not installed</li>
    <li>enable Sleep As Android in Notify settings</li>
  </ol>
</details>

<details>
  <summary><strong>🔐 extracting the Zepp <code>authKey</code> on a rooted android device</strong></summary>
  <pre>
su
cd /data/data/com.huami.watch.hmwatchmanager/databases/
ls origin_db_*
sqlite3 origin_db_1234567890 "SELECT AUTHKEY FROM DEVICE;"
  </pre>

  <ul>
    <li>⚠️ do not unpair before extracting</li>
    <li>use with caution – root required</li>
    <li>modified apps are available on <a href="https://geekdoing.com">GeekDoing</a> and <a href="https://freemyband.com">FreeMyBand</a></li>
  </ul>
</details>

<h3>🛌 <a href="https://play.google.com/store/apps/details?id=com.urbandroid.sleep&hl=en_US">sleep as android setup</a></h3>
<ol>
  <li>open the app and follow setup</li>
  <li>settings wheel > services > automation > MQTT</li>
</ol>

<pre>
(tcp/ssl)://(MQTT User):(MQTT Pass)@(HA URL):(port)
</pre>

<ul>
  <li>topic: must match config</li>
  <li>client id: any unique id</li>
</ul>

<ol start="4">
  <li>enable automatic tracking</li>
  <li>sensor: sonar or accelerometer</li>
  <li>wearables > **Xiaomi Mi Band** > test sensor</li>
</ol>

## ⚙️ configuration

| option | description |
|--------|-------------|
| name | identifier for this user/device |
| topic | mqtt topic to subscribe to |
| awake duration | seconds of awake events before marking as awake |
| sleep duration | seconds of sleep events before marking as asleep |
| awake states | which states indicate being awake |
| sleep states | which states indicate being asleep |
| mobile app | companion app target for buttons (optional) |

## ⌚ tested wearables

| device | method |
|--------|--------|
| xiaomi mi band 7 | notify for mi band |
| garmin fenix 7x | garmin alternative |
| amazfit gtr3 pro | native |

---

<div align="center">

made by [sudoxnym](https://sudoxreboot.com) ⚡

*formerly known as [saas - sleep as android status](https://github.com/sudoxreboot/saas/tree/main) — the app is capable of working with any mqtt sleep app now*

</div>
