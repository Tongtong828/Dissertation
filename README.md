# Emochi: A Responsive Emotional Support Plush Device

Emochi is a responsive plush device designed to explore low-demand, physical and largely screen-free emotional-support interaction.

It combines:

- Touch sensing
- Non-verbal audio feedback
- Vibration feedback
- Vibration-guided breathing
- Device-initiated comfort-seeking behaviour
- A lightweight local web interface

The project was developed as part of the MSc Connected Environments dissertation at UCL CASA.

---

## Overview

Emochi supports three main forms of interaction:

### Wake

A single tap on the head wakes Emochi and triggers a short audio response.

### Meditation

A double tap starts a 90-second vibration-guided breathing interaction.

The vibration intensity changes over time to represent:

- Inhale
- Pause
- Exhale
- Pause

This allows the breathing rhythm to be followed without continuously looking at a screen.

### Seek Soothe

Emochi maintains an internal `happiness` value.

As this value decreases, Emochi can actively enter a comfort-seeking state and request attention through vibration and non-verbal audio.

The user can respond by gently stroking Emochi's back. Accepted strokes are detected by the FSR sensor and gradually progress the soothing interaction.

---

## System Architecture

Emochi is organised into three functional layers:

- **Input**
  - Piezoelectric sensor
  - Force Sensitive Resistor (FSR)

- **Processing**
  - ESP32-S3 N16R8

- **Output**
  - Vibration motor
  - Speaker
  - Local web interface

<img width="774" height="436" alt="image" src="https://github.com/user-attachments/assets/00560d93-dcbe-4de5-a65a-addc60e893e4" />


---

## Hardware

The prototype uses the following main components:

| Component | Function |
|---|---|
| ESP32-S3 N16R8 | Main controller |
| Piezoelectric sensor/module | Detects head tapping |
| Force Sensitive Resistor (FSR) | Detects back stroking and pressure |
| Vibration motor | Haptic feedback and breathing guidance |
| Speaker | Non-verbal audio feedback |
| MAX98357A | I2S Class-D audio amplifier |
| LM2596 | Step-down DC-DC voltage regulator |
| 4 × 1.5 V AA batteries | Portable power supply |
| Custom PCB | Integrates the electronic components |
| 3D-printed PCB enclosure | Protects and secures the PCB |

Electrical connections outside the PCB were insulated using heat-shrink tubing.

The electronics are installed inside the main plush body and remain accessible through a concealed rear zipper.

---

## Circuit

The electronic system connects the sensing, audio and vibration components to the ESP32-S3.

<img width="591" height="367" alt="image" src="https://github.com/user-attachments/assets/1c729c50-def1-48f2-8d40-893a7b2cc0a8" />


A custom PCB was produced after the initial circuit had been tested.

<img width="372" height="393" alt="image" src="https://github.com/user-attachments/assets/52bfd320-4f3d-4e73-9a25-99640c4e0d15" />


---

## Software

The firmware was developed in Arduino C++ for the ESP32-S3.

### Main software components

The project uses the following key libraries and modules:

- `Arduino.h`
- `WiFi.h`
- `WebServer.h`
- `DNSServer.h`
- `LittleFS.h`
- Custom `AudioPlayer` module

### Main source files

```text
EmotionalPlush.ino
AudioPlayer.cpp
AudioPlayer.h
