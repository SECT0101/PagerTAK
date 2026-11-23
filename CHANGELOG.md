# Changelog

PagerTAK versions (the payload in `library/user/general/PagerTAK`). Newest first.

## 2.3.2
- **Web console on every address by default**: the console now listens on all interfaces, so it opens by the Pager's host name, its IP on your network or the USB address. *Listen on* can still narrow it to USB + VPN or USB only.
- **Listen on in the web console**: *Settings > Web console access* sets it from the browser and lists the addresses the console answers on. Saving restarts the console.
- The QR code always uses an IP address, which every phone can open.

## 2.3.1
- **Clearer enrollment errors**: says whether the server name could not be looked up (no internet or DNS), the port refused the connection, or nothing answered, instead of "Cannot reach".
- The Drone Remote ID card in the web console mentions Bluetooth only when the Bluetooth tools are installed.
- Log file names can be set per device (PineappleTAK uses `/tmp/pineappletak-*.log`); the Pager's stay `/tmp/pagertak-*.log`.

## 2.3
- **Enrollment with a public certificate**: when the TAK Server's enrollment port (8446) uses a public certificate (Let's Encrypt and others), enrollment checks it against the public CA list after your TAK CA, instead of failing with "Cannot reach". The server is always checked. On OpenWrt, `opkg install ca-bundle` adds the list.
- **Connection checks without `timeout`**: *Test connection* and reconnects no longer need the `timeout` command or Bash's `/dev/tcp` (a Python link is used when it is missing), and they say why a port did not answer (refused, unreachable, no answer in 8 s...).
- **Evil-twin learning in the web console**: *Sensors > Evil-twin watch > Find networks* lists what recon heard in the last 5 to 240 minutes; tick your networks, name the site and save.
- **Bluetooth tools from the web console**: the tracker watch has an *Install Bluetooth tools* button, and warns when no Bluetooth adapter is found.
- Fix: drone Remote ID could not be turned on from the web console.
- Fix: *Pick on map* for your manual position was overwritten before it was saved.
- Fix: read receipts for your own messages did not show in the web console when the payload and the browser side used different time zones.
- Fix: a host entered with `https://` or a path is cleaned up to the bare address.
- Certificate files are wrapped with awk, so OpenWrt builds without `fold` can import them.
- The engine and web console are shared with [PineappleTAK](https://github.com/SECT0101/PineappleTAK-MK7) for the WiFi Pineapple Mark VII. Names in messages, the position report's platform and the UIDs of markers, reports and alerts come from one setting, so each device shows its own name (PagerTAK on the Pager, unchanged).

## 2.2
- **No download for the Pager map**: `map/pagerfb.py`, PagerTAK's own pure-Python screen driver, replaces the pagerctl library that was downloaded on first use. It draws to the framebuffer, reads the buttons and decodes PNG tiles; `python3-ctypes` is no longer needed. USGS basemaps are fetched as PNG for the Pager screen.
- **Set up in a web browser**: the first run asks *On the Pager* or *In a web browser*. The browser way starts the web console in setup mode, where you do the whole setup and press *Finish setup*.
- **TAK Server certificate enrollment** with your TAK username and password (port 8446), from the web console or the Pager.
- **ATAK connection package import** and **.p12 upload** in the web console; the Pager converts the files.
- **Test connection** names the first thing that fails (address, port, TLS, client certificate, server certificate). The client certificate's days left show in the status, with a warning 30 days before it expires.
- **Faster Pager map**: text is drawn from a cache in a few large copies (about half the time per frame), and buttons are handled in the order pressed, so *hold B + UP / DOWN* zooms reliably even while the map is busy. Buttons are read from one input device, as pagerctl did; the last 60 presses are kept in `/tmp/pagertak-keys.log` for checking.
- **Clear chat**: delete one message (web console) or the whole chat history (web console and Pager *Chat* menu). Only the Pager's copy is removed.
- Fix: KML / KMZ overlay uploads failed on the Pager (the upload is stored in `/tmp` and could not be moved to the payload's disk), and an overlay whose name the Pager tidied (for example `.KML` to `.kml`) was saved but not shown. Files that are not KML / KMZ are now refused with a reason.
- Fix: in the web console, zones, your markers and the Settings status could stay empty until the Pager next changed something; they now show as soon as the page opens.
- New README screenshots, all demo data.
- **SEND ALERT** replaces 911 on the web button, banner and Pager menu; 911 stays one of the alert types.
- **Chat receipts**: direct messages to the Pager send *delivered* back at once and *read* when you open Chat, so ATAK and CloudTAK show their ticks. Receipts for your own messages show as *✓ delivered* / *✓✓ read* in the web console. Can be turned off (`CHAT_RECEIPTS`, or the web console).
- **Unread chat**: *Chat > (3 new)* in the menu, the count in Status, and a slow magenta LED until you open Chat on the Pager or in the web console.
- **Reply** to the last message from the Chat menu (quick or typed; a room message can be answered in the room or directly to the sender).
- **Quick messages** are editable in the web console (*Settings > Quick messages*) and shared with the Pager; new defaults include *Copy*, *Say again* and *On scene*.
- **Navigate to** a contact: a live arrow with range, bearing and last-update age, north up or along your course while moving. **A** (green) opens the map on the contact.
- **Geofences on the Pager**: *Sensors > Geofences* sets a zone around your position, and *Contacts > Geofence alert* watches one chosen contact.
- Fix: TAK users (ATAK, CloudTAK...) no longer vanish at their stale time. Like ATAK, they stay greyed out until `TRACK_MAX_AGE` without an update. Stale times are measured from each event's own time, so a clock difference between the Pager and other devices no longer removes fresh contacts.
- Fix: chats and contacts from busy TAK Servers no longer arrive minutes late. The incoming stream is split into events by awk instead of Bash, and data feeds (aircraft, ships, lightning...) are limited to one update per item every `RX_FEED_MIN_SEC` seconds (default 10). Chats, alerts and TAK users always go through right away.
- Fix: photo / file sharing failed on the Pager because Python's ZIP module needs `urllib`, which python3-light does not include. Site-survey packages had the same problem. Both now use a small built-in ZIP writer. Share progress and results show in the chat.
- **Own Remote ID receiver** (`map/rid_decode.py`): decodes Wi-Fi beacon, Wi-Fi NAN and Bluetooth Remote ID itself. Nothing is downloaded from other payloads any more.
- **Leaflet is included** in `web/static/leaflet` (with its license) instead of downloaded on first use. The web console only needs `python3-light`.
- Fix: photo / file sharing failed over TLS when the TAK Server API certificate did not match the address used (or used a public certificate). It now falls back and still sends; TAK channels use the same fix.
- When an upload fails, the web console says why (cannot connect, timed out, HTTP code, TLS); details in `/tmp/pagertak-share.log`.
- Credits section updated.

## 2.1
- Received items now expire like in ATAK: their own stale time, deletes from other clients (`t-x-d-d`), or `TRACK_MAX_AGE` without an update. Expired items are also removed from the Pager's memory every minute.
- **Clear received items** (web console *Items* tab and Pager *Settings*). Your own markers and zones are kept.
- Show/hide filters by group: TAK users, drones, aircraft, ground units / vehicles / incidents, vessels, markers and other. Applies to the web map and the Pager map.
- Web console: *Contacts* lists TAK users only; new *Items* tab for everything else.
- Fix: ADS-B and other aircraft were listed as drones. Drones are now Remote ID detections and UAS types (`-Q`).
- Spot markers (`b-m-p-*`) from other TAK users now show on the map.

## 2.0
- **Alerts**: 911 Alert, Ring The Bell, Geo-fence Breached, Troops In Contact and named Custom alerts, to everyone or chosen contacts. 5-second countdown with cancel in the web console. The cancel goes to the same people. The Pager menu has the same choices.
- **Man-down watchdog**: warns you and then sends 911 by itself if you stop moving; can watch chosen TAK users and alert (optionally by GeoChat) when one stops moving or reporting. Any Pager button resets it.
- **Geofences** can send a TAK *Geo-fence Breached* alert on entry.
- **Web console**: offline map download for the area on screen, breadcrumb trails, KML/KMZ overlays, photo and file sharing as TAK data packages, range filter (also on the Pager map), CoT details with the raw event.
- **QR code** of the web address on the Pager screen.
- New options: `COT_RADIUS_KM`, `WEB_PORT`, `MANDOWN_MINUTES`.
- Fix: a background job started from the web console could hang its command queue.

## 1.9.1
- Markers and zones: keep local, send to everyone, or send only to one TAK user; send later, resend or delete (also removed in TAK).
- Contacts tab separates TAK users, drones, your markers and other markers.
- TAK channels card moved to the top of Settings.

## 1.9
- Faster tiles (cached on the Pager, the rest straight from the internet with a background copy), connection reuse, change-only polling.
- TAK channels (server groups In / Out).
- Map tools: measure, range rings, MGRS, drop markers, geofence zones with enter / leave alerts, browser alert sound.
- SIGINT tab: radio lock / hop, Wi-Fi AP / client / probe tables, fox hunt with a signal trail.

## 1.8
- Web console: map, chat, 911, reports, sensors and settings in a browser, with a login, USB / VPN / all-interface listening and optional HTTPS. Certificate upload from the browser.

## 1.7
- Site survey to an ATAK data package uploaded to the TAK Server.

## 1.6
- Drone Remote ID to TAK (drones and operators).

## 1.5
- Tracker / counter-surveillance watch (AirTag following, Tile, SmartTag, Google FMDN, Flock, Axon).

## 1.4
- Evil-twin / rogue AP watch with a learned site profile.

## 1.3
- Organized menus; field reports: SALUTE, SITREP, MEDEVAC 9-line, damage assessment.

## 1.2.x
- ATAK-format basemaps (USGS Topo / Imagery); map uses the manual position without a fix; legacy `.p12` handling and detection of an unusable OpenSSL legacy module; finds the real payload folder when run from `/tmp`.

## 1.1
- Map view on the Pager screen, contacts with range and bearing, sensor stats in position reports.

## 1.0
- Pager GPS to TAK as CoT, GeoChat send and receive, 911, and the deauth alert payload.
