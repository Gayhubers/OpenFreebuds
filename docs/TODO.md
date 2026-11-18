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

* FreeBuds 4 — `ZA09`

* FreeBuds Lipstick — `ZAA0`

* FreeLace Vitality Edition — `ZAA2`

* FreeBuds 4E — `ZAA4`

* FreeBuds SE 3 — `ZAAO`

* FreeArc — `ZAAP`

* FreeBuds SE 5 MAX — `2SFW`

* HUAWEI Eyewear — `ZCA0`, `ZCA1`

FreeBuds Lipstick 2 (`ZAAJ`) is supported as a FreeBuds 5 variant and is
registered as an alias of `Huawei5`. Its official config differs only in
shell/color assets and the legacy `isSupportEqCustom` display flag. Huawei's
[official setup guide](https://consumer.huawei.com/cn/support/content/zh-cn00759656/)
places it in the same gesture categories as FreeBuds 5.

## Missing feature handlers / backends



* **Voice assistant and voice control** (`voice_assistant`, `voice_control`,

  `voice_wakeup`): no handler for wake-word, assistant activation or voice

  command settings. Present on FreeBuds 6/6i/7/7i/Pro 4/Pro 5/Neo/Clip 2/SE 4.

* **Smart charging** (`smart_charge`): no handler. Present on FreeBuds

  6/7/7i/Pro 5/Clip 2.

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

* **Drop detection events**: the current `config_drop_reminder` handler only

  writes the switch. Parsing the earbud's drop event for a notification/toast is

  still open (previously tracked as task 40). Visible models are FreeBuds 7,

  FreeBuds 6, FreeClip 2, FreeClip 2 S and FreeClip; FreeBuds Neo is excluded.

* **FreeBuds 3 ANC**: the official config has `active_noise_cancel`, but the

  `Huawei3` driver has no ANC handler. The older protocol needs verification.

* **FreeBuds Studio gestures**: official touch pages exist, but the driver has

  no gesture handler at all. The pages contain little writable data; protocol

  review is required before exposing anything.

* **FreeBuds Pro 2 spatial audio**: official `SpatialAudioWidget` is present,

  but the driver has no spatial handler. Confirm whether this is a phone-side

  effect or an independent earbud switch before adding it.

* **FreeBuds 6i spatial sound effects**: official `spatialAudio` is a widget

  type 2 sound-effect entry, not the standalone spatial-audio handler. Needs a

  separate UI/backend decision.

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
  `VolumeAdjustHelper` uses MBB `0x2BB2` (`03 01` capability, `03 01 06 01
  07 01` status, `02 01` master switch, `04 01`/`05 01` sub-settings).
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

* Clip 1st gen official `spatialAudio` and `charging_tip` are phone-side

  features, not earbud-independent switches. Keep the current filters.

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

* Keep the capability matrix updated when a handler is added or a product ID is

  registered.

* Add the new model capability links to the README or release notes when the

  next portable build is published.