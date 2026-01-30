# IEC 62056‑21 TTL Infrared Optical Probe – Complete DIY Development Story and Full ESPHome Integration (Q/A Based Documentation)

*Created by an engineer who wanted full local access to smart‑meter data and ended up designing a professional‑grade optical read head.*

---

## 📸 Image: Final IEC 62056‑21 TTL Infrared Read Head  
![IEC 62056-21 Optical Probe RJ12 Connector](https://github.com/rwanrooy/iec62056-21-IR-optical-smart-meter-reader/blob/main/images/iec62056-21-optical-probe-ttl-rj-connector.png?raw=true)

<p align="center"><em>IEC 62056‑21 infrared optical smart‑meter probe showing the RJ12 connector wiring used for ESP32 and ESPHome integrations.</em></p>

---

## What motivated the development of this optical probe?  
The journey began with a simple frustration: even though many electricity, heat, gas, and water meters use the IEC 62056‑21 optical port for data access, it was surprisingly difficult to find a reliable TTL‑level infrared probe that works seamlessly with ESPHome and an ESP32 microcontroller. The existing hardware on the market often suffered from weak magnets, unstable IR output, noisy phototransistor stages, handshake failures during baud‑rate negotiation, or simply poor mechanical construction.  

The motivation therefore came from a need for a dependable, stable, and developer‑friendly optical read head. Rather than relying on inconsistent hardware, the logical step was to design a probe that provided consistent infrared performance, strong magnetic alignment, full ESP32 compatibility, and transparent feedback during communication.

---

## How did the early prototyping process begin?  
The first prototypes were built from loose components taped to the front of a smart meter. An IR LED and a phototransistor were manually aligned while an oscilloscope captured the signal quality. The experiments revealed how critical proper optical power, angle, and filtering are for receiving valid IEC 62056‑21 telegrams.  

These crude setups led to the first successful 300‑baud identification handshake, proving that reliable optical communication was possible. This marked the transition from improvised testing to structured hardware design.

---

## What design decisions shaped the final PCB?  
After confirming the electrical behavior, the next challenge was creating a compact and noise‑free PCB layout. Key choices included using a stable IR LED driver circuit to ensure predictable optical output, a low‑noise phototransistor amplifier stage, and careful routing to minimize interference.  

![IEC 62056-21 Infrared Optical Probe PCB](https://github.com/rwanrooy/iec62056-21-IR-optical-smart-meter-reader/blob/main/images/iec62056-21-infrared-optical-probe-pcb.png?raw=true)

<p align="center">
  <em>Internal PCB of the IEC 62056‑21 infrared optical smart‑meter probe, including IR LED driver, phototransistor receiver stage, TTL interface and RJ12 connector for ESP32 and ESPHome.</em>
</p>

A circular PCB shape ensured optimal alignment with a wide variety of meters, and high‑strength neodymium magnets provided consistent mechanical coupling. Two status LEDs — green for TX and white for RX — were integrated to visually display communication activity. This greatly simplified field debugging and made the probe more intuitive to work with.

---

## What problems were encountered during testing, and how were they solved?  
Testing revealed several issues common in IEC 62056‑21 communication. Too much IR light caused the meter to reject messages, while too little light produced incomplete or corrupted telegrams. The angle of the optical components proved equally important; even a slight misalignment could prevent the initial handshake. UART noise, baud‑rate fallbacks, and inconsistent timing further complicated the process.

Through repeated iterations of component tuning, optical adjustments, and PCB refinement, these issues were corrected one by one. Eventually, the probe reached a level of stability where communication succeeded consistently across multiple meter brands such as Kamstrup, Landis+Gyr, and Itron.

---

## Why were TX and RX status LEDs added?  
A common question is why the probe includes status LEDs. The answer lies in practicality: infrared communication is invisible, and without feedback it is difficult to determine whether the meter is responding or whether ESPHome is sending the correct handshake.

![IEC 62056-21 Optical Probe Status LEDs](https://github.com/rwanrooy/iec62056-21-IR-optical-smart-meter-reader/blob/main/images/iec62056-21-optical-probe-status-indicator-leds.png?raw=true)

<p align="center">
  <em>
    IEC 62056‑21 infrared optical smart‑meter probe showing the dual status indicator LEDs  
    (green TX and white RX) used to visualize bidirectional IEC 62056‑21 communication  
    with ESP32, ESPHome and ASCII Mode A/B/C smart meters.
  </em>
</p>

With TX and RX indicators, the exact communication state becomes visible. If only TX flashes, the meter is not responding. If only RX flashes, the meter is rejecting the request. If both flash, the data exchange is active. This small addition dramatically improves usability.

---

## How is the optical read head connected to an ESP32 development board?  
One of the most frequently asked questions is how to connect the TTL optical probe to an ESP32. The connection is simple: the probe operates at 3.3V TTL levels and must never be connected to a 5V logic interface. The included RJ12 cable has four wires, each mapped to a specific signal.

![IEC 62056-21 Optical Probe Status LEDs](https://github.com/rwanrooy/iec62056-21-IR-optical-smart-meter-reader/blob/main/images/iec62056-21-optical-probe-ttl-50cm-cable.png?raw=true)
<p align="center">
  <em>
    IEC 62056‑21 infrared optical probe with integrated TTL interface,  
    50 cm RJ12 cable and strong neodymium magnetic mounting — designed for  
    ESP32, ESPHome and ASCII Mode A/B/C/D smart‑meter communication.
  </em>
</p>

The yellow wire from the probe carries the meter’s data output and must be connected to GPIO18 on the ESP32. The green wire carries data from the ESP32 to the meter and is connected to GPIO05. The black wire provides the ground reference, and the red wire delivers the 3.3V supply.

For users searching for a compatible ESP32 development board, the following option integrates perfectly with this probe:  
https://smartgateways.nl/en/product/esp32-developer-board-nodemcu-4mb-240mhz-dual-core-wifi-bluetooth/

This setup ensures a clean communication path and excellent compatibility with ESPHome.

---

## How is ESPHome configured to communicate with IEC 62056‑21 meters?  
Setting up ESPHome requires enabling the external IEC 62056 component, configuring the UART interface, and defining the OBIS codes the meter reports. Meters typically use 7E1 framing, and communication begins at 300 baud before switching to a negotiated rate such as 9600 baud.  

Below is a complete working configuration:

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

logger:
  baud_rate: 0

iec62056:
  update_interval: 60s
  baud_rate_max: 9600
```

Meters that broadcast data using Mode D can be configured with:

```yaml
iec62056:
  mode_d: true
```

A numeric sensor example:

```yaml
sensor:
  - platform: iec62056
    obis: "1-0:15.8.0"
    name: "Total Energy"
    unit_of_measurement: kWh
    state_class: total_increasing
    device_class: energy
```

ESPHome will automatically parse telegrams, extract OBIS values, and publish the data to Home Assistant.

---

## What questions do users most frequently ask about IEC 62056‑21 and this probe?

### Does this probe work with all IEC 62056‑21 meters?  
It works with all meters that use the ASCII‑based IEC 62056‑21 protocol in Modes A, B, C, and D. Pure HDLC Mode E meters are not supported.

### Why is 3.3V TTL required?  
The ESP32 communicates using 3.3V logic levels. Higher voltages can damage both the microcontroller and the optical probe.

### How can the OBIS codes be discovered?  
By enabling ESPHome’s debug logger, every raw OBIS code transmitted by the meter becomes visible during a readout.

### Why do some meters fail to complete the handshake?  
Handshake issues generally result from insufficient optical power, excessive optical noise, misalignment, high baud‑rate negotiation, or dust on the meter’s optical window.

### Can the optical probe remain mounted permanently?  
Yes. The strong neodymium magnet ensures perfect long‑term alignment, even in environments with vibrations or temperature variation.

### Does this solution support DLMS/COSEM?  
Meters using DLMS/COSEM over ASCII IEC 62056‑21 work correctly. Pure binary HDLC interfaces are outside the scope of this probe.

---

## What is the final outcome of this project?  
The result of this engineering journey is a robust IEC 62056‑21 TTL Optical Infrared Read Head designed for both professionals and hobbyists. It provides stable infrared transmission, noise‑free reception, reliable baud‑rate negotiation, clear status feedback, and direct compatibility with ESPHome and ESP32 development boards.

The project delivers a practical and dependable solution for long‑term energy monitoring in Home Assistant, making it possible to access meter data locally without vendor restrictions.

---

## What conclusion can be drawn from this engineering effort?  
The overarching conclusion is that reliable access to smart‑meter data does not require expensive equipment or proprietary solutions. By carefully designing the optical interface, refining the electronics, and integrating ESPHome support, this probe offers a complete, local, future‑proof way to monitor energy consumption.  

For anyone looking to understand their electricity, heat, gas, or water usage — or to integrate professional meters into a home automation system — this project demonstrates that a well‑designed IEC 62056‑21 optical probe can deliver accuracy, stability, and simplicity.

---
