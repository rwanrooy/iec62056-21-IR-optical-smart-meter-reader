🌐 Project page:
https://rwanrooy.github.io/iec62056-21-IR-optical-smart-meter-reader/

# IEC 62056‑21 TTL Infrared Optical Probe – Complete DIY Development Story and Full ESPHome Integration (Q/A Based Documentation)

*Created by an engineer who wanted full local access to smart‑meter data and ended up designing a professional‑grade optical read head.*

If you want to purchase the professionally manufactured version of this probe, it is available here:  
👉 **https://smartgateways.nl/en/product/iec-62056-21-ttl-infrared-optical-read-head/**

---

## Table of Contents
1. Introduction  
2. What motivated the development of this optical probe?  
3. How did the early prototyping process begin?  
4. What design decisions shaped the final PCB?  
5. What problems were encountered during testing, and how were they solved?  
6. Why were TX and RX status LEDs added?  
7. How is the optical read head connected to an ESP32 development board?  
8. 3D‑Printed Enclosure  
9. How is ESPHome configured for IEC 62056‑21?  
10. FAQ – Frequently Asked Questions  
11. What is the final outcome of this project?  
12. What conclusion can be drawn?  

---

## Introduction  
This project describes the complete process of designing a reliable IEC 62056‑21 optical smart‑meter probe, covering hardware engineering, infrared communication tuning, PCB design, 3D‑printed enclosure development, ESP32 integration, and full ESPHome support. It is intended for Home Assistant users, embedded engineers, hobbyists working with IEC 62056‑21 or DLMS/COSEM meters, and anyone wanting accurate and local access to smart‑meter data without vendor lock‑in.  

---

## 📸 Final IEC 62056‑21 TTL Infrared Read Head  

<a href="https://smartgateways.nl/en/product/iec-62056-21-ttl-infrared-optical-read-head/" target="_blank">
<img src="https://github.com/rwanrooy/iec62056-21-IR-optical-smart-meter-reader/blob/main/images/iec62056-21-optical-probe-ttl-rj-connector.png?raw=true" alt="IEC 62056-21 Optical Probe RJ12 Connector">
</a>

<p align="center"><em>
IEC 62056‑21 infrared optical smart‑meter probe showing the RJ12 connector wiring used  
for ESP32 and ESPHome integrations.
</em></p>

---

## What motivated the development of this optical probe?  
The journey began with a simple frustration: even though many electricity, heat, gas, and water meters use the IEC 62056‑21 optical port for data access, it was surprisingly difficult to find a reliable TTL‑level infrared probe that works seamlessly with ESPHome and an ESP32 microcontroller. Existing products often suffered from weak magnets, unstable IR output, noisy phototransistor stages, handshake failures or fragile mechanical construction.

To solve this, I designed my own probe. If you prefer a professional version instead of building one yourself, it is available here:  
👉 **https://smartgateways.nl/en/product/iec-62056-21-ttl-infrared-optical-read-head/**

---

## How did the early prototyping process begin?  
The first prototypes were assembled from loose components taped to the front of a smart meter. An IR LED and a phototransistor were manually aligned while oscilloscope readings revealed how small variations in angle and light intensity affected communication.

These crude setups led to the first successful 300‑baud identification handshake, proving reliable readout was achievable.

---

## What design decisions shaped the final PCB?  
Once the concept was validated, a custom PCB was designed to ensure clean signal paths and consistent optical performance.

<a href="https://smartgateways.nl/en/product/iec-62056-21-ttl-infrared-optical-read-head/" target="_blank">
<img src="https://github.com/rwanrooy/iec62056-21-IR-optical-smart-meter-reader/blob/main/images/iec62056-21-infrared-optical-probe-pcb.png?raw=true" alt="IEC 62056-21 Infrared Optical Probe PCB">
</a>

<p align="center"><em>
Internal PCB of the IEC 62056‑21 infrared optical probe, showing the IR LED driver,  
phototransistor receiver stage, TTL interface and RJ12 connector.
</em></p>

A circular PCB shape ensured perfect alignment on meters. Neodymium magnets guaranteed strong mounting, while TX/RX LEDs provided valuable real‑time feedback during debugging.

---

## What problems were encountered during testing, and how were they solved?  
Testing revealed the extreme sensitivity of IEC 62056‑21 communication. Too much IR light caused the meter to reject messages; too little produced corrupted frames. Angular misalignment blocked handshakes entirely.

Through optimized IR control, multiple PCB iterations and refined filtering, the probe became compatible with various meter brands, including Landis+Gyr, Kamstrup and Itron.

---

## Why were TX and RX status LEDs added?  
Infrared communication is invisible. Without diagnostic LEDs, you have no clue whether the meter is responding or rejecting.

<a href="https://smartgateways.nl/en/product/iec-62056-21-ttl-infrared-optical-read-head/" target="_blank">
<img src="https://github.com/rwanrooy/iec62056-21-IR-optical-smart-meter-reader/blob/main/images/iec62056-21-optical-probe-status-indicator-leds.png?raw=true" alt="IEC 62056-21 Optical Probe Status LEDs">
</a>

<p align="center"><em>
IEC 62056‑21 optical probe with dual TX/RX LEDs for bidirectional ASCII Mode A/B/C diagnostics.
</em></p>

TX blinking without RX means the meter is not responding. RX without TX means the meter ignores the request. Both blinking means communication is active.

---

## How is the optical read head connected to an ESP32 development board?  
The probe uses 3.3V TTL logic and connects with a four‑wire RJ12 cable:  
Yellow → GPIO18  
Green → GPIO05  
Black → GND  
Red → 3.3V  

<a href="https://smartgateways.nl/en/product/iec-62056-21-ttl-infrared-optical-read-head/" target="_blank">
<img src="https://github.com/rwanrooy/iec62056-21-IR-optical-smart-meter-reader/blob/main/images/iec62056-21-optical-probe-ttl-50cm-cable.png?raw=true" alt="IEC 62056-21 Optical Probe 50cm Cable">
</a>

<p align="center"><em>
IEC 62056‑21 infrared optical probe with TTL interface and  
50 cm RJ12 cable for ESP32 and ESPHome smart‑meter communication.
</em></p>

Compatible ESP32:  
https://smartgateways.nl/en/product/esp32-developer-board-nodemcu-4mb-240mhz-dual-core-wifi-bluetooth/

---

## 3D‑Printed Enclosure (Fusion 360, BambuLab X1C & Magnetic Mount)
The enclosure was designed in Fusion 360 and printed on a BambuLab X1C for dimensional precision. Neodymium magnets embedded in the housing provide strong attachment to the meter’s optical port. A rear RJ12 connector offers full flexibility.

<a href="https://smartgateways.nl/en/product/iec-62056-21-ttl-infrared-optical-read-head/" target="_blank">
<img src="https://github.com/rwanrooy/iec62056-21-IR-optical-smart-meter-reader/blob/main/images/iec62056-21-optical-probe-ttl-3dprint-magnets.jpeg?raw=true" alt="IEC 62056-21 Optical Probe TTL 3D Print Magnets">
</a>

<p align="center"><em>
Fusion 360‑designed, BambuLab X1C‑printed enclosure with  
integrated neodymium magnets and rear RJ12 connector.
</em></p>

---

## How is ESPHome configured for IEC 62056‑21 communication?  
Below is a complete working ESPHome configuration:

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

Mode D:

```yaml
iec62056:
  mode_d: true
```

And an OBIS example:

```yaml
sensor:
  - platform: iec62056
    obis: "1-0:15.8.0"
    name: "Total Energy"
    unit_of_measurement: kWh
    state_class: total_increasing
    device_class: energy
```

---

## FAQ – Frequently Asked Questions  

### Does this probe work with all IEC 62056‑21 meters?  
Yes, all ASCII‑based modes (A, B, C, D). Not Mode E HDLC.

### Why is 3.3V TTL required?  
ESP32 logic is 3.3V. Higher voltages risk damage.

### How can OBIS codes be discovered?  
Enable DEBUG logging in ESPHome.

### Why does handshake sometimes fail?  
Usually misalignment, dust, too much or too little IR light, or unsupported baud rates.

### Can the probe remain mounted permanently?  
Yes — the neodymium magnets guarantee perfect positioning.

### Does this support DLMS/COSEM?  
Yes, as long as the meter outputs ASCII IEC 62056‑21.

---

## What is the final outcome of this project?  
The result is a robust IEC 62056‑21 TTL optical infrared read head offering stable IR transmission, clear status LEDs, strong magnetic mounting, a precision‑printed enclosure and seamless ESPHome integration.

---

## What conclusion can be drawn?  
This project demonstrates that a reliable smart‑meter optical probe can be built with accessible tools, careful design and thorough testing. It provides a local, future‑proof method for retrieving utility‑grade data via ESPHome and ESP32.

For users who prefer a professionally assembled probe, including high‑quality magnets, molded optical sensor alignment, premium cabling and rigorous testing, it is available here:  
👉 **https://smartgateways.nl/en/product/iec-62056-21-ttl-infrared-optical-read-head/**

---
