# BLE Experimental FHSS Test Platform

An experimental multi-radio platform built around the **nRF24L01+** for investigating frequency hopping behavior across the 2.4 GHz ISM band.

> **Disclaimer**
>
> This project is intended solely for laboratory testing, RF characterization, and educational purposes inside controlled environments. It is **not** intended for interfering with wireless communications or transmitting on frequencies in violation of local regulations.

---

## Overview

This board contains:

- Raspberry Pi Pico W controller
- Three independent nRF24L01+ transceivers
- Three external RF power amplifiers
- Individual RF filtering stages
- SMA antenna connectors for each RF chain

The objective is to create a configurable RF test platform capable of:

- Continuous carrier generation
- Channel sweeping
- Experimental frequency hopping
- Timing analysis
- Multi-radio synchronization
- BLE advertising channel experiments

---

## Hardware

### Controller

- Raspberry Pi Pico W
- 3.3 V logic

### RF Chain (×3)

Each RF path contains:

```
Pico SPI
      │
      ▼
nRF24L01+
      │
      ▼
2.45 GHz RF Filter
      │
      ▼
RF2401C Power Amplifier
      │
      ▼
2450LP14A100T Matching Network
      │
      ▼
SMA Connector
```

Each radio has:

- Dedicated 16 MHz crystal
- Local decoupling
- Separate CE control
- Shared SPI bus
- Individual RF output chain

---

## Schematic

The design currently consists of three identical RF sections driven from a common SPI interface.

Features include:

- SPI shared between all radios
- Independent CE lines
- External RF front-end
- Matching network
- Filtered antenna output

---

## Current Firmware

Current firmware configures an nRF24L01+ as:

- Transmitter mode
- Auto ACK disabled
- CRC disabled
- 2 Mbps
- Maximum PA level
- Constant carrier enabled

Example initialization:

```cpp
radio.setAutoAck(false);
radio.stopListening();
radio.setRetries(0,0);
radio.setPayloadSize(5);
radio.setAddressWidth(3);
radio.setPALevel(RF24_PA_MAX);
radio.setDataRate(RF24_2MBPS);
radio.setCRCLength(RF24_CRC_DISABLED);

radio.startConstCarrier(RF24_PA_MAX, channel);
```

---

## Experimental Channel Sweep

The current proof-of-concept changes the RF channel by repeatedly calling

```cpp
radio.setChannel(channel);
```

Example sweep:

```cpp
for (int i = 0; i < 79; i++)
{
    radio.setChannel(i);
}
```

---

## BLE Advertising Channel Test

The firmware also demonstrates hopping across the three BLE advertising channels.

```cpp
byte ble_channels[] =
{
    37,
    38,
    39
};

for(int i=0;i<3;i++)
{
    radio.setChannel(ble_channels[i]);
}
```

This serves as a simple experiment in channel switching behavior rather than implementing the Bluetooth Low Energy protocol itself.

---

## Project Goals

- [x] Multi-radio PCB
- [x] External PA integration
- [x] RF filtering
- [x] Basic firmware
- [x] Constant carrier generation
- [x] Channel switching
- [ ] Precise timing control
- [ ] Simultaneous multi-radio hopping
- [ ] DMA-driven SPI control
- [ ] RP2040 PIO synchronization
- [ ] Measurement using SDR
- [ ] Spectrum characterization

---

## Requirements

### Hardware

- Raspberry Pi Pico W
- 3 × nRF24L01+
- RF2401C
- 2450LP14A1002T filter
- 3.3 V power supply
- Antennas or appropriate RF loads for testing

### Software

- Arduino IDE
- RF24 library
- Raspberry Pi Pico board package

