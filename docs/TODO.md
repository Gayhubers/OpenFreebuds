# HUAWEI driver TODO

Long-term items found while comparing the HUAWEI AI Life 2.0.7.321 config with

the current OpenFreebuds driver set. Items here need a new handler, a new backend

or hardware verification; they were deliberately not forced into the 0.19.0

gesture pass.

See [huawei-model-capabilities.md](huawei-model-capabilities.md) for the current

per-model matrix.

## Unsupported product IDs

The official package contains configs for these models, but OpenFreebuds has no

driver mapping yet. Each needs its own protocol/handler review before it can be

registered:



* FreeBuds 3i — `ZA02`

* FreeBuds 4 / Lipstick (Gen 1) — `ZA09` / `ZAA0` (same platform; one future driver, Lipstick as alias)

* FreeLace Vitality Edition — `ZAA2`

* FreeBuds 4E — `ZAA4` / `000135` (`T0004L`; standard ANC version)

* FreeBuds 4E 无降噪版 — `ZAAN` / `000159` (`T0008`)

* FreeBuds SE 3 — `ZAAO`

* FreeArc — `ZAAP`

* FreeBuds SE 5 MAX — `2SFW`

* HUAWEI Eyewear — `ZCA0`, `ZCA1`

FreeBuds Lipstick 2 (`ZAAJ`) is supported as a FreeBuds 5 variant and is

registered as an alias of `Huawei5`. Both are `deviceModel=T0013`; the SKU ids

`ZAAB` / `ZAAJ` do not expose that link. Its official config differs in box

icon, default sub-product, gesture-guide resource, audio-setting order and two

display flags (`isShowTitle`, `isSupportEqCustom`), with no new gesture command

payload. Huawei's

[official setup guide](https://consumer.huawei.com/cn/support/content/zh-cn00759656/)

places it in the same gesture categories as FreeBuds 5.

FreeBuds 4 (`ZA09`, model `000129`, `Hero`) and FreeBuds Lipstick Gen 1

(`ZAA0`, model `00012A`, `Cooper`) are `deviceModel=T0004L` / `T0004K`, the same

`T0004` family. Their `touchsetting` pages are identical except

`imageBean.imageProductId`; config differences are shell/color, default

sub-product, fast-operation entry and self-service/close-cover flags. Add one

`Huawei4` driver and register Lipstick as an alias, not a second driver. Neither

model is registered yet.

FreeClip 2 (`ZAAS`, model `000167`) and FreeClip 2 S / Collector Edition

(`ZAB2`, model `000173`) are `deviceModel=T0027` / `T0027K`, the same `T0027`

family. `ZAAS` / `000167` alone is one SKU and cannot express the pair.

Use `deviceModel` to find the family, then compare touch payload before sharing

a driver. FreeBuds 4E has two SKUs: standard `ZAA4` / `000135` / `T0004L`, and

no-ANC `ZAAN` / `000159` / `T0008`. Both 4E variants have four touch pages

(double/triple/long/slide; the slide page has no `sendData`) and therefore do not

merge with FreeBuds 4's three-page layout. `T0008` is listed in

`files/00000A/00/device_whitelist_config.json` (version 41), with

`assets/000159.json`, `assets/guidance/guidance_ZAAN.json` and

`touchsetting/config_zaan.json`; the APK asset whitelist (version 35) only has

the standard `ZAA4` entry.

## Missing feature handlers / backends



* **Voice assistant and voice control** (`voice_assistant`, `voice_control`,

  `voice_wakeup`): no handler for wake-word, assistant activation or voice

  command settings. Present on FreeBuds 6/6i/7/7i/Pro 4/Pro 5/Neo/Clip 2/SE 4.

* **Smart record** (`smart_record`): no handler. Present on FreeBuds 7/Pro 5.

* **Hearing test / hearing aid** (`hearing_test`, `hearing_aid`): no handler.

  `hearing_test` is present on FreeBuds Pro 5 and Neo; `hearing_aid` is present

  on FreeBuds Pro 5.

* **Agent / Dola entries** (`agent`, `dola_smart_broadcasts`, `dola_translate`,

  `third_party_smart_translate`): no backend. Present on FreeBuds 7/Neo/Pro 5/

  Clip 2. The Clip 2 AI-key long-press page currently exposes only press-to-talk

  and continuous-conversation modes, not the full agent backend.

* **Smart scenario / big volume** (`smart_scenario`, `big_volume_new`): no

  handler. `smart_scenario` is present on Neo; `big_volume_new` is present on

  Clip 2.

* **Wear detection switch** (`wear_detection`): only in-ear state reporting exists;

  there is no handler to turn automatic wear detection on or off.

* **Earphone cleaning events**: implemented as a read-only `0x2B7F` handler
  (`OfbHuaweiPromptEventsHandler`) and forwarded to system notifications.
  The APK's `general_prompt_config.json` declares `serviceId 43`,
  `commandId 127`, TLV type `2`, value `1` for `01/05/09_earphone_cleaning`,
  with `type 6 value 20` as cancel and `type 6 value 21` as “not seated
  correctly”. The resulting application data is `2b7f020101`,
  `2b7f060114` and `2b7f060115`. Only FreeClip / FreeClip 2 / FreeClip 2 典藏版
  have cleaning entries. This is separate from the App's 30-day
  `HeadphonesCleaningCard`, which is a periodic banner. No `2b7f02` has been
  captured on OpenFreebuds' SPP channel yet; the handler deliberately assumes
  the captured IConnect TLV can arrive over SPP, and no IConnect transport is
  implemented. Verify SPP on the same earbud before changing that assumption.
  Do not treat the observed `2b7f06020200` status packet as a cleaning event.
  See `docs/清洁提醒-协议标定.md`.

* **Earbud care and health fields** (`isSupportHeadphoneCleaning`,

  `isSupportHeadsetHealthAlert`, `isSupportTelephoneSpeech`, `isSupportSoh`):

  no handler or backend yet. These need per-model capability checks before any

  UI is added.

* **App-side reminders and short audio** (`super_remind`, `morning_greeting`,

  `short_audio`): these are HUAWEI AI Life content features, not earbud protocol

  settings. No OpenFreebuds backend is planned for them.

* **Volume sync** (`sync_volume`): no dedicated handler. Present on FreeBuds 5 /

  Lipstick 2 / 5i / 6 / Pro 5 / SE 4 / FreeLace Pro 2.

* **Earbud hearing switch** (`earbudhearSwitch`): no handler. Present on Pro 4/

  Pro 5.

* **Earplug fit detection** (`earplugdetection`): only in-ear state is exposed;

  no fit-test workflow. Present on FreeBuds 5i/6i/Pro 2/Pro 3/Pro 4/Pro 5.

* **Drop detection events**: `config_drop_reminder` writes the switch, and
  `OfbHuaweiPromptEventsHandler` now consumes `2b7f050101` as an event and
  shows a high-priority system notification. On 2026-11-19 phone-side IConnect
  received `2b7f05` when a FreeClip 2 was dropped and `2b7f06` about 2.5 s
  later, then Huawei posted notification id 127. That phone capture used
  `C0:E5:79:94:6D:CC`; OpenFreebuds was connected to another FreeClip 2,
  `C0:E5:79:45:6C:95`, so its SPP log without `2b7f05` is not a valid
  cross-check. The handler assumes SPP delivery of the same TLV; SPP delivery
  and real system popups are still unverified. Visible models are FreeBuds 7,
  FreeBuds 6, FreeClip 2, FreeClip 2 S and FreeClip; FreeBuds Neo is mounted
  because its config declares `drop_detection`, and runtime support depends on
  the device response.

* **FreeBuds 3 ANC**: the official config has `active_noise_cancel`, but the

  `Huawei3` driver has no ANC handler. The older protocol needs verification.

* **FreeBuds Studio gestures**: official touch pages exist, but the driver has

  no gesture handler at all. The pages contain little writable data; protocol

  review is required before exposing anything.

* **FreeBuds Pro 2 spatial audio**: official `SpatialAudioWidget` is present and

  the model is in the ordinary head-tracking/fixed list, but not the independent

  list. The driver has no spatial handler. Confirm whether this is a phone-side

  effect or an earbud switch before adding it.

* **FreeBuds 6i spatial sound effects**: official `spatialAudio` is a widget

  type 2 sound-effect entry. It is not in the ordinary head-tracking/fixed list,

  so do not reuse `OfbHuaweiSpatialAudioHandler`; this needs a separate

  UI/backend decision.

* **Laboratory volume gestures** (`lab_volume`, not `touchsetting`): no

  OpenFreebuds handler. FreeClip 1 uses two official lab modes: quick

  click-and-hold (`VolumeAdjust`) and continuous-click (`ContinuumClick`),

  with left/right touch zones configured separately. The APK's

  `VolumeAdjustView` explicitly lists model `00014B` (ZAAI/Clip 1);

  `VolumeAdjustHelper` uses MBB `0x2BB2`: `03 01` capability,

  `03 01 06 01 07 01` status, `02 01` master switch, `04 01`/`05 01`

  sub-settings. The same view also supports `000169` (SE 4 ANC) and `00017D`

  (SE 5 MAX). Need a handler and GUI before exposing; do not enable

  `gesture_swipe` for Clip 1.

## Spatial audio follow-ups



* **Independent (earbud-side) spatial audio** — only these models, and only at

  the minimum earbud firmware shown. The versions are earbud firmware, not the

  host phone/PC OS:

  Do not describe FreeBuds Pro 2 / Pro 3 / Pro 4, FreeBuds 6 or FreeClip 1 as

  independent.


  * FreeBuds Neo — HarmonyOS 6.1.0.258+

  * FreeClip 2 series (FreeClip 2 / 2 S / Collector's Edition) — HarmonyOS 6.1.0.370+

  * FreeBuds 7i — HarmonyOS 5.1.0.166+

  * FreeBuds Pro 5 — HarmonyOS 6.1.0.272+

  * FreeBuds 7 — HarmonyOS 7.0.0.238+

* **Ordinary spatial audio (head tracking + fixed)** — FreeBuds 7, FreeBuds Neo,

  FreeBuds Pro 5, FreeClip 2 Collector's Edition, FreeClip 2, FreeBuds 7i,

  FreeBuds 6, FreeBuds Pro 4, FreeClip, FreeBuds Pro 3, FreeBuds Pro 2 series.

  These may expose head-tracking/fixed modes through a phone-side feature even

  when the earbuds themselves are not in the independent list.

* **Fixed-only spatial audio** — FreeBuds Lipstick 2 and FreeBuds 5. Their

  current drivers do not mount `OfbHuaweiSpatialAudioHandler`; decide whether

  fixed-only control is worth exposing and verify the command path.

* `OfbHuaweiSpatialAudioHandler` is independent-only. Do not mount it on
  FreeBuds Pro 2 / Pro 3 / Pro 4, FreeBuds 6 or FreeClip 1: those are
  Huawei-phone-side high-definition spatial audio models. The same applies to
  the fixed-only FreeBuds 5 and Lipstick 2.

* Mounting the handler does not enforce the minimum firmware version. Treat
  `0x2BB4` returning `supported=false` as unavailable, and verify below-threshold
  firmware before claiming independent spatial audio support.

## Custom EQ follow-ups



* Do not use `soundQuality.isSupportEqCustom` alone to enable or disable custom

  EQ. The field is absent or `false` on several models whose new EQ interface

  still works (FreeBuds 5i `ZAAD`, FreeBuds 6/6i, FreeBuds 7/7i, Pro 4/Pro 5,

  FreeClip 2/2 S, SE 2/SE 4). Keep the current `w_custom` behavior and verify

  the row layout per model.

* FreeBuds SE 2 and SE 4 use the preset EQ handler but have no custom rows yet.

  Their `EqAdjustWidget` tag suggests a future custom-EQ page; protocol support

  is unconfirmed.

* FreeBuds Pro 2 and 5i now share the FreeBuds 5 custom-EQ row layout (10 rows,

  up to 3 custom profiles). Hardware validation is still needed.

## Gesture follow-ups

Reference: [Huawei official setup guide, tables 1-3](https://consumer.huawei.com/cn/support/content/zh-cn00759656/).



* FreeBuds 3 and FreeBuds SE have a single official touch page with one send

  payload. The current double-tap mapping is a best-effort interpretation; verify

  the actual gesture name and action list.

* FreeBuds Studio: official double/long/swipe pages have little or no writable

  data. Do not expose a gesture UI until the protocol is understood.

* FreeBuds Pro (1st gen): the official guide uses a stem pinch for

  ANC/play/call, but `config_ZA07` has no writable light-hold page. The driver

  maps the stem press to `LongTapSplit`; keep it until hardware check.

* FreeClip (1st gen): no swipe. Volume is the lab quick click-and-hold

  (listening ball = increase, comfort bean = decrease) and five-tap mode,

  with left/right touch zones configured separately. `config_ZAAI` only has

  double/triple pages; the lab page is a separate module. The APK's

  `VolumeAdjustView` explicitly lists model `00014B` (ZAAI/Clip 1), and

  `VolumeAdjustHelper` uses MBB `0x2BB2` (`03 01` capability, `03 01 06 01`

  `07 01` status, `02 01` master switch, `04 01`/`05 01` sub-settings).

  OpenFreebuds still has no lab-volume handler; this is a backend gap, not a

  touchsetting page.

* FreeBuds SE 4 ANC: the official guide adds a five-tap volume mode. The

  current driver exposes double/triple/long and long-press volume options, but

  not the five-tap mode.

* FreeBuds SE 2 / SE: the official guide confirms no volume gesture; SE 2 also

  has only an in-call long page. Keep the swipe handler and normal long split

  absent.

* FreeBuds Pro 2/3 have no official double/triple page. Do not inherit the Pro 4

  double/triple handlers into them.

* FreeBuds 7, Pro 4, Pro 5 and Clip 2 AI-key pages have no `isInCall` field.

  Keep in-call long-press disabled for those models.

* Clip 1st gen official `spatialAudio` is ordinary head-tracking/fixed

  (phone-side), not earbud-independent. `charging_tip` is also phone-side.

  Keep the current filters.

## Charging-case tone follow-ups



* Master-only models: FreeBuds Pro 3, Pro 4 and FreeClip 1. They have other

  charging-case prompt tones but no writable lid-open tone page. Keep

  `w_lid_tone=False`, hide tone selection/upload, and expose only the

  `box_tone_enabled` master switch.

* Full models: FreeClip 2, FreeClip 2 S, FreeBuds 6, FreeBuds 7 and FreeBuds

  Pro 5. Keep `w_lid_tone=True` and expose the lid switch, volume and tone

  selection.

* Hardware validation is still needed for the master switch on Pro 3/4 and

  FreeClip 1.

## Validation and documentation



* Most 0.19.0 handler sets were derived from config, not device-tested. Add

  hardware smoke tests for at least one model from each family (5/5i/6/6i/7/7i/

  Neo/SE 4/Pro 2/Pro 3/Pro 4/Pro 5/Clip/Clip 2).

* Left-right auto-switch (`0x2B9A`) is mounted on FreeClip 1/2/2 S and W Buds

  1/2, but no device read/write has succeeded. FreeClip 2 currently hits SPP

  `10048`; retry on a free port and confirm `param 1` length/state.

* W Buds 1/2 (`000137` / `00016F`) has only a minimal driver. Smoke-test info

  and battery, then confirm whether its firmware answers `0x2B9A`; add no other

  settings until a profile or device evidence exists.

* Keep the capability matrix updated when a handler is added or a product ID is

  registered.

* Add the new model capability links to the README or release notes when the

  next portable build is published.