# IEC 62056-21 Optical Smart Meter Reader

### ESPHome · ESP32 · ESP8266 · Arduino

> Built out of frustration, curiosity, and way too many late-night debugging sessions.
> This project exists because “almost working” wasn’t good enough.

---

## 📸 Hardware overview

[![IEC 62056-21 Optical Probe mounted on meter](images/probe-mounted.jpg)](https://smartgateways.nl/product/iec-62056-21-ttl-optische-infrarood-leeskop-smart-meter-probe/)

*(Click the image to see the exact optical probe used in this project)*

---

## Why this project exists

I started with a simple goal:
**read my own smart meter locally, reliably, without cloud dependencies.**

What I actually ran into:

* meters that only partially follow IEC 62056-21
* optical probes that worked on one meter but failed on the next
* unexplained CRC/BCC errors
* zero visibility into what was happening on the optical interface

So instead of fighting symptoms, I decided to **understand the entire stack**:
protocol → timing → optics → UART → firmware → mechanics.

This repository is the result of that process.

---

## What this project is (and is not)

**This is:**

* a well-tested IEC 62056-21 optical reader setup
* designed for ESP32, ESP8266 and Arduino-class boards
* primarily targeting ESPHome, but usable standalone
* tested against real electricity, water and heat meters
* built with debugging and observability in mind

**This is not:**

* a plug-and-play consumer gadget
* a cloud-based solution
* a “just trust me” hack

It’s a nerd project. On purpose.

---

## Supported protocol modes

IEC 62056-21 looks simple on paper. In reality, it’s not.

This setup supports:

* **Modes A / B / C**
  Bidirectional communication
  Automatic baud-rate negotiation (starting at 300 baud)

* **Mode D**
  Unidirectional broadcast
  Passive listening, fixed UART settings

Mode E (binary / HDLC) is intentionally **not supported**.
If a meter *claims* Mode E but silently falls back to ASCII, this setup can still work.

---

## Hardware design philosophy

### Optical probe (TTL level)

The optical interface turned out to be the single most important component.

Things that matter more than you think:

* IR LED output stability
* receiver sensitivity
* exact alignment with the meter port
* mechanical stability over time
* **immediate visual feedback**

Cheap probes usually fail quietly.
This one doesn’t.

---

### Status LEDs (non-negotiable)

[![TX RX status LEDs](images/status-leds.jpg)](https://smartgateways.nl/product/iec-62056-21-ttl-optische-infrarood-leeskop-smart-meter-probe/)

TX/RX LEDs were added very early in the process and proved invaluable.

They instantly tell you:

* whether the meter responds at all
* if baud-rate switching actually happens
* if alignment is off by a millimeter
* if you’re debugging protocol or hardware

> Debugging IEC 62056-21 without LEDs is like debugging serial without logs.

---

## Microcontroller compatibility

Tested setups include:

* **ESP32** (recommended)
  Multiple UARTs, stable timing, easiest debugging experience.

* **ESP8266**
  Works fine, but UART logging conflicts are common.
  Disable serial logging on shared UARTs.

* **Arduino (UNO / Nano)**
  Ideal for:

  * Mode D (listen-only)
  * simple polling setups

Bidirectional modes (A/B/C) require proper UART control and timing precision.

---

## ESPHome integration

ESPHome makes this setup usable long-term.

### Example: bidirectional meters (Modes A/B/C)

```yaml
external_components:
  - source: github://aquaticus/esphome-iec62056

uart:
  rx_pin: GPIO18
  tx_pin: GPIO05
  baud_rate: 9600
  data_bits: 7
  parity: EVEN
  stop_bits: 1

iec62056:
  update_interval: 60s
  baud_rate_max: 9600
  battery_meter: false

sensor:
  - platform: iec62056
    obis: "1-0:15.8.0"
    name: "Total Energy Consumption"
    unit_of_measurement: kWh
    state_class: total_increasing
    device_class: energy
```

### Example: Mode D (broadcast meters)

```yaml
iec62056:
  mode_d: true
```

During setup, always enable:

```yaml
logger:
  level: DEBUG
```

You want raw data when things go wrong — and they will.

---

## OBIS codes: where reality starts

OBIS codes identify meter values:

```
A-B:C.D.E*F
```

Example:

```
1-0:15.8.0  → Total imported energy (kWh)
```

Every meter exposes a different subset.
The only reliable approach is:

* enable DEBUG logging
* capture a full readout
* map OBIS codes manually

There are no shortcuts here.

---

## Troubleshooting highlights (aka: earned knowledge)

### CRC / BCC errors everywhere

Usually not firmware bugs.

Common causes:

* weak optical signal
* poor magnetic alignment
* baud rate too high
* unstable power

Fixes:

* lower `baud_rate_max`
* improve alignment
* shorten cables
* verify ground reference

---

### Meter responds once, then stops

Timing issue.

Some meters:

* require long idle times
* dislike frequent polling
* expect exact framing delays

Solution:

* increase update interval
* introduce deliberate delays
* study logs frame by frame

---

### ESP8266 behaving randomly

UART conflict.

Fix:

```yaml
logger:
  baud_rate: 0
```

---

## Mechanical design & enclosure

I eventually designed and printed a custom enclosure because:

* probe movement causes flaky reads
* magnet quality really matters
* strain relief saves cables long-term

Iterations focused on:

* magnet strength and placement
* LED visibility
* cable routing
* consistent pressure against meter face

Several redesigns later, it became boringly reliable — which is exactly what I wanted.

---

## Tools that saved my sanity

* Logic analyzer (indispensable)
* USB-UART adapter (probe testing without MCU)
* ESPHome DEBUG logging
* Oscilloscope (optional, but helpful)

---

## Project status

This setup is:

* stable
* used daily
* tested against multiple real meters
* intentionally simple where possible

It’s not magic.
It’s just well-understood.

---

## Reference hardware

The optical probe used throughout this project can be found here:
[https://smartgateways.nl/product/iec-62056-21-ttl-optische-infrarood-leeskop-smart-meter-probe/](https://smartgateways.nl/product/iec-62056-21-ttl-optische-infrarood-leeskop-smart-meter-probe/)

(Linked for reference and reproducibility — not required, but it’s what I tested against.)

---

## Contributing

If you:

* have a weird meter
* found an undocumented edge case
* enjoy protocol archaeology

Issues and PRs are very welcome.

Every meter tells a slightly different story.

---

## Final note

This project exists because I wanted:

* local-only operation
* zero vendor lock-in
* something that keeps working years from now

I wanted it to be **boringly reliable**.

Now it is.

Happy hacking ⚡
