# Emochi: A Responsive Emotional Support Plush Device
<img width="365" height="487" alt="image" src="https://github.com/user-attachments/assets/4e75fe4d-65b5-4a94-913c-b9b8f521a6eb" />


Emochi is a responsive plush device developed to explore low-demand, physical and largely screen-free emotional-support interaction. It combines touch sensing, non-verbal audio, vibration feedback, guided breathing and device-initiated comfort-seeking behaviour within a soft plush body.

This repository contains the firmware, audio files, circuit/PCB files, 3D-printable enclosure files and sewing templates used to build the prototype.

---

## 1. Main Interactions

Emochi supports three main user interactions:

### Wake
A single tap on the head wakes Emochi and produces a short vibration and audio response.

### Meditation
A double tap starts a 90-second vibration-guided breathing sequence. Vibration intensity gradually increases during inhalation, pauses, decreases during exhalation, and pauses again before the next cycle.

### Seek Soothe
Emochi maintains an internal `happiness` value. As this value decreases, Emochi can enter a comfort-seeking state and request attention through vibration and non-verbal audio. The user responds by stroking the FSR sensing area on Emochi's back. Accepted strokes progress the soothing sequence.

---

## 2. Repository Structure

The repository is intentionally organised into four main folders:

```text
Dissertation_Emochi_emotional-support-device/
├── code/
│   ├── AudioPlayer.cpp
│   ├── AudioPlayer.h
│   ├── EmotionalPlush.ino
│   ├── First_emo.ino
│   └── Speaker_test.ino
│
├── enclosure/
│   ├── Emochi heart enclosure.stl
│   ├── Emochi heart top.stl
│   ├── Sew component.pdf
│   ├── arm front.pdf
│   ├── body front.pdf
│   ├── feet sew.pdf
│   ├── hand bottom.pdf
│   └── leg sew.pdf
│
├── hardware/
│   ├── EmoPlush.eprj2
│   └── e42e7d8aadac9659d9494902a8174b56.png
│
├── voice/
│   ├── calm_done.wav
│   ├── comfort_1.wav
│   ├── comfort_2.wav
│   ├── comfort_3.wav
│   ├── meditation_end.wav
│   ├── meditation_start.wav
│   ├── sleep.wav
│   ├── soothe_start.wav
│   └── wake.wav
│
└── README.md
```

### Which code should be used?

The main version for reproducing the audio-haptic prototype is:

[`code/EmotionalPlush.ino`](code/EmotionalPlush.ino)

It works together with:

[`code/AudioPlayer.cpp`](code/AudioPlayer.cpp)  
[`code/AudioPlayer.h`](code/AudioPlayer.h)

`First_emo.ino` is an earlier development version and `Speaker_test.ino` is a component test sketch. They are retained as development references.

> **Important:** the `code/` folder contains more than one `.ino` file. Arduino compiles all `.ino` files inside the same sketch folder, so do **not** compile the complete `code/` directory directly. Follow the local build instructions below and copy only the files required for the final build into a separate Arduino sketch folder on your computer.

---

## 3. Hardware Required

| Component | Purpose |
|---|---|
| ESP32-S3 N16R8 | Main controller |
| Force Sensitive Resistor (FSR) | Detects stroking/pressure on the back |
| Piezoelectric sensor module | Detects head taps |
| Vibration motor | Haptic feedback and breathing guidance |
| MAX98357A | I2S Class-D audio amplifier |
| Speaker | Non-verbal audio feedback |
| LM2596 | Step-down DC-DC voltage regulator |
| 4 × 1.5 V AA batteries | Portable power supply |
| Custom PCB | Integrates the electronic connections |
| 3D-printed PCB enclosure | Protects and secures the electronics |
| Soft fabric | Plush exterior |
| Polyester filling | Plush body filling |
| Concealed zipper | Rear access to electronics |
| Heat-shrink tubing | Insulation for exposed wire joints |

The EasyEDA project used for the PCB is available at:

[`hardware/EmoPlush.eprj2`](hardware/EmoPlush.eprj2)

The circuit schematic is included below:
<img width="591" height="367" alt="image" src="https://github.com/user-attachments/assets/5f93a2cb-46b6-4a19-98b0-ef33cff71b09" />

The System architecture of Emochi below：
<img width="774" height="436" alt="image" src="https://github.com/user-attachments/assets/e16e695d-95b5-4f4a-a5ea-125d9996bacb" />

---

## 4. ESP32-S3 Pin Connections

The current `EmotionalPlush.ino` uses the following GPIO assignments:

| Function | ESP32-S3 GPIO |
|---|---:|
| FSR signal | GPIO 1 |
| Piezo sensor output | GPIO 4 |
| Vibration motor control | GPIO 5 |
| MAX98357A DIN | GPIO 18 |
| MAX98357A BCLK | GPIO 17 |
| MAX98357A LRC / WS | GPIO 16 |

Use the circuit schematic above for power and ground connections.

> The vibration motor should be driven through the motor circuit shown in the hardware design. Do not connect a motor directly to an ESP32 GPIO if its current requirement exceeds the GPIO rating.

---

## 5. Software Requirements

The main firmware is written in Arduino C++ for the ESP32-S3.

You will need:

- Arduino IDE
- ESP32 board support by Espressif Systems
- An ESP32-S3 compatible board configuration
- A LittleFS filesystem uploader compatible with your Arduino setup

The audio system uses the custom `AudioPlayer` implementation included in this repository. It reads WAV files from LittleFS and sends digital audio to the MAX98357A through I2S.

The main firmware includes:

```cpp
#include <Arduino.h>
#include "AudioPlayer.h"
```

`AudioPlayer.cpp` additionally uses the ESP32 LittleFS and I2S APIs.

---

## 6. Prepare a Local Arduino Sketch

You do **not** need to change the repository structure.

After downloading or cloning the repository, create a new local folder on your computer named:

```text
EmotionalPlush
```

Copy only these three files from `code/` into that folder:

```text
EmotionalPlush.ino
AudioPlayer.cpp
AudioPlayer.h
```

Your local working folder should initially look like this:

```text
EmotionalPlush/
├── EmotionalPlush.ino
├── AudioPlayer.cpp
└── AudioPlayer.h
```

This avoids Arduino trying to compile `First_emo.ino` and `Speaker_test.ino` together with the final sketch.

---

## 7. Prepare the Audio Files for LittleFS

The repository stores the source WAV files in the `voice/` folder. LittleFS upload tools normally expect a `data/` directory inside the active Arduino sketch folder.

Inside your local `EmotionalPlush/` folder, create:

```text
data/
```

Copy the following files from the repository's `voice/` folder into the new local `data/` folder:

```text
wake.wav
sleep.wav
comfort_1.wav
comfort_2.wav
comfort_3.wav
soothe_start.wav
calm_done.wav
meditation_start.wav
meditation_end.wav
```

The final **local working** folder should be:

```text
EmotionalPlush/
├── EmotionalPlush.ino
├── AudioPlayer.cpp
├── AudioPlayer.h
└── data/
    ├── wake.wav
    ├── sleep.wav
    ├── comfort_1.wav
    ├── comfort_2.wav
    ├── comfort_3.wav
    ├── soothe_start.wav
    ├── calm_done.wav
    ├── meditation_start.wav
    └── meditation_end.wav
```

### Recommended WAV format

The included `AudioPlayer` is designed for short PCM WAV files. The recommended format is:

```text
WAV
16-bit PCM
Mono
22050 Hz
```

The player checks the WAV header and supports 16-bit PCM mono or stereo input. File paths in the firmware begin with `/`, for example:

```cpp
audio.play("/wake.wav");
```

---

## 8. Configure Arduino IDE

1. Install the ESP32 board package from Espressif Systems using Arduino Boards Manager.
2. Connect the ESP32-S3 to the computer by USB.
3. Select an ESP32-S3 board profile compatible with the N16R8 board used in this project.
4. Select the correct serial port.
5. Use a flash/partition configuration that provides enough filesystem space for the WAV files.
6. Open the local `EmotionalPlush/EmotionalPlush.ino` sketch.

The prototype uses an ESP32-S3 N16R8 board, which provides 16 MB flash and 8 MB PSRAM. Match the Arduino board settings to the actual ESP32-S3 board being used.

---

## 9. Upload the LittleFS Audio Data

The WAV files must be uploaded to the ESP32-S3 filesystem before audio playback will work.

1. Confirm that the local `data/` folder contains all nine WAV files.
2. Select the correct ESP32-S3 board and serial port.
3. Run the LittleFS filesystem upload command provided by your Arduino/LittleFS uploader.
4. Wait until the upload completes.
5. Restart the board if required by the uploader.

If the filesystem is missing, the Serial Monitor will show:

```text
AudioPlayer error: LittleFS mount failed.
Please upload the data folder to ESP32 LittleFS first.
```

If this appears, upload the `data/` folder again before debugging the speaker wiring.

---

## 10. Upload the Firmware

After the LittleFS data has been uploaded:

1. Compile `EmotionalPlush.ino`.
2. Upload it to the ESP32-S3.
3. Open Serial Monitor at **115200 baud**.

At startup the firmware performs a motor test and then plays `wake.wav`.

A successful startup should end with:

```text
If motor vibrated and wake.wav played, hardware and LittleFS audio are OK.
```

If the motor vibrates and the wake sound is audible, the basic haptic and audio hardware is working.

---

## 11. Current Firmware Parameters

The values below are taken from the current `code/EmotionalPlush.ino` and can be adjusted during calibration.

### Sensor thresholds

```cpp
FSR_TOUCH_THRESHOLD   = 150
FSR_RELEASE_THRESHOLD = 70
```

### Tap detection

```cpp
TAP_DEBOUNCE_MS      = 40
TAP_SEQUENCE_GAP_MS  = 1200
```

### Happiness behaviour

```cpp
Initial happiness            = 65
Maximum happiness            = 100
Minimum happiness            = 0
Happiness recovery           = +30
Wake happiness bonus         = +3
Happiness decay interval     = 60000 ms
Happiness decay amount       = -2
```

### Comfort thresholds

| Happiness value | Comfort level |
|---:|---|
| 60 or above | No comfort request |
| 45–59 | Level 1 |
| 25–44 | Level 2 |
| Below 25 | Level 3 |

The current request timing is:

| Level | Request interval | Vibration duration |
|---|---:|---:|
| 1 | 60 s | 2.5 s |
| 2 | 30 s | 4 s |
| 3 | 15 s | 6 s |

If Emochi receives no soothing while seeking comfort for 60 seconds, the current firmware performs a self-recovery and returns the happiness value to its maximum.

### Soothing interaction

```cpp
Required strokes: 5–7
Stroke debounce: 350 ms
Soothing timeout: 15 s
```

### Sleep and snooze

```cpp
Automatic sleep after no interaction: 90 s
Comfort-request snooze: 5 min
```

### Meditation

```cpp
Total meditation time: 90 s
Inhale: 5.0 s
Pause after inhale: 0.5 s
Exhale: 5.0 s
Pause after exhale: 0.5 s
Minimum motor power: 120
Maximum motor power: 255
```

---

## 12. FSR Calibration

The FSR is positioned beneath the fabric on Emochi's back. Fabric and filling change how pressure reaches the sensor, so final calibration should be carried out **after the FSR is installed inside the plush**.

The firmware reads both the raw and smoothed FSR values. The smoothed value is calculated as:

```text
SmoothFSR = 0.65 × PreviousSmoothFSR + 0.35 × RawFSR
```

Open Serial Monitor and observe:

```text
Raw FSR
Smooth FSR
```

Stroke the final sensing area through the fabric. If normal back strokes do not cross the touch threshold, adjust:

```cpp
FSR_TOUCH_THRESHOLD
FSR_RELEASE_THRESHOLD
```

Do not copy the original threshold values blindly if a different fabric, FSR position or filling density is used.

---

## 13. Piezo Tap Calibration

The piezo sensor is installed in the head and its digital output is connected to GPIO 4.

The firmware distinguishes single and double taps using the debounce and sequence-window values shown above.

Use Serial Monitor and tap the head several times. A valid tap produces a message such as:

```text
Head tap detected. Count: 1
```

Check that:

- one physical tap normally produces one detection;
- two quick taps are detected as a double tap;
- accidental vibration or handling does not continuously trigger taps.

If one physical tap produces repeated detections, increase `TAP_DEBOUNCE_MS` or adjust how the piezo module is mounted.

---

## 14. Interaction Logic

### 14.1 Wake

Emochi starts in `SLEEP`.

A single head tap while sleeping:

```text
SLEEP
  ↓ single tap
AWAKE
```

The device produces wake vibration feedback and plays:

```text
/wake.wav
```

---

### 14.2 Meditation

A double head tap starts the 90-second Meditation mode:

```text
AWAKE / SLEEP
     ↓ double tap
  MEDITATION
```

The motor gradually increases from the minimum vibration power to the maximum during inhalation, switches off during the pause, then decreases from maximum to minimum during exhalation.

The sequence uses:

```text
5.0 s inhale
0.5 s pause
5.0 s exhale
0.5 s pause
```

The following sounds are played at the start and end:

```text
/meditation_start.wav
/meditation_end.wav
```

---

### 14.3 Seek Soothe

The happiness value decreases over time. Once it falls below a comfort threshold, Emochi enters:

```text
SEEKING_COMFORT
```

The request uses vibration plus one of the graded audio files:

```text
Level 1 → /comfort_1.wav
Level 2 → /comfort_2.wav
Level 3 → /comfort_3.wav
```

Lower happiness values produce more frequent requests.

---

### 14.4 Soothe Emochi

Back stroking is accepted only when Emochi is already seeking comfort or being soothed.

The first accepted stroke:

1. stops the active seeking vibration;
2. enters `BEING_SOOTHED`;
3. plays `/soothe_start.wav`;
4. starts counting accepted strokes.

The target is randomly selected between **5 and 7 strokes**.

Each accepted stroke produces a short vibration response. When the target is reached, Emochi plays:

```text
/calm_done.wav
```

and the happiness value increases.

---

### 14.5 Snooze a Request

While Emochi is in `SEEKING_COMFORT`, a single head tap postpones the current proactive request for five minutes.

---

### 14.6 Automatic Sleep

When Emochi remains in `AWAKE` without interaction for the configured timeout, it returns automatically to `SLEEP` and plays:

```text
/sleep.wav
```

---

## 15. Serial Monitor Debugging

Serial Monitor is the easiest way to check whether the sensors and state logic are working correctly.

Use:

```text
115200 baud
```

The firmware prints values in the following form:

```text
Raw FSR: ... | Smooth FSR: ... | Happiness: ... | Comfort Level: ... | Soothe: .../... | Mode: ...
```

Use these values to check:

- FSR response;
- smoothing;
- happiness decay;
- comfort-level transitions;
- soothing progress;
- device mode;
- audio events;
- state transitions.

---

## 16. Build the Plush Enclosure

The sewing patterns are already included in the `enclosure/` folder.

Main pattern files:

- [`Sew component.pdf`](enclosure/Sew%20component.pdf)
- [`arm front.pdf`](enclosure/arm%20front.pdf)
- [`body front.pdf`](enclosure/body%20front.pdf)
- [`feet sew.pdf`](enclosure/feet%20sew.pdf)
- [`hand bottom.pdf`](enclosure/hand%20bottom.pdf)
- [`leg sew.pdf`](enclosure/leg%20sew.pdf)

The original physical form was developed by creating a clay model, marking seam divisions, covering the surface with tape, flattening the sections and refining them into paper sewing templates.

To reproduce the enclosure:

1. Print the supplied sewing pattern PDFs at the intended scale.
2. Transfer the pattern outlines to the reverse side of the fabric.
3. Cut the individual pieces.
4. Sew the facial/head sections and body sections.
5. Add a concealed zipper to the rear body seam before fully closing the enclosure.
6. Leave sufficient access for installation of the sensors, PCB, speaker, motor and battery system.
7. Add polyester filling gradually during final assembly.

The rear zipper is important because it allows the electronics to be installed, calibrated and maintained without reopening stitched seams.

---

## 17. Print the PCB Enclosure

The 3D-printable enclosure files are included in:

- [`Emochi heart enclosure.stl`](enclosure/Emochi%20heart%20enclosure.stl)
- [`Emochi heart top.stl`](enclosure/Emochi%20heart%20top.stl)

Print both parts using a material suitable for a lightweight electronics enclosure.

Before putting the enclosure inside the plush:

1. place the assembled PCB inside the lower enclosure;
2. confirm that the board does not move freely;
3. check that wires can exit without being sharply bent;
4. confirm that the top can be removed for maintenance;
5. test the complete electronic system again.

---

## 18. Recommended Electronics Installation Order

After the circuit works outside the plush, install the components in the following order:

1. Place the piezo sensor in the head.
2. Place the FSR beneath the back sensing area.
3. Position the vibration motor inside the torso.
4. Position the speaker inside the main body.
5. Install the custom PCB inside the 3D-printed enclosure.
6. Install the LM2596 and battery holder.
7. Insulate exposed wire joints with heat-shrink tubing.
8. Route the wiring so that the rear zipper remains usable.
9. Add filling gradually around the electronics.
10. Re-test the FSR, piezo, motor and speaker before closing the zipper.

Do not pack filling too tightly around the vibration motor. The plush material attenuates vibration, and excessive filling can make the breathing guidance difficult to perceive.

---

## 19. Power System

The portable prototype uses four 1.5 V AA batteries, giving a nominal 6 V supply.

An LM2596 step-down DC-DC converter is used between the battery supply and the electronics. Adjust and verify the converter output with a multimeter **before connecting it to the ESP32-S3 and other electronics**.

Do not assume the converter output is correct from its factory setting.

---

## 20. Final Test Checklist

Before placing the prototype into normal use, check the complete system:

- [ ] ESP32-S3 powers on correctly
- [ ] Startup motor test works
- [ ] `wake.wav` plays at startup
- [ ] Single head tap is detected
- [ ] Double head tap starts Meditation
- [ ] FSR readings respond to back stroking
- [ ] Smooth FSR crosses the configured touch threshold during normal strokes
- [ ] Happiness decreases over time
- [ ] Comfort levels change at the expected thresholds
- [ ] Seek Soothe produces vibration and the correct comfort sound
- [ ] 5–7 accepted strokes complete soothing
- [ ] `calm_done.wav` plays after completion
- [ ] A single tap snoozes a comfort request
- [ ] Meditation vibration rises and falls correctly
- [ ] `meditation_start.wav` and `meditation_end.wav` play
- [ ] Automatic Sleep works
- [ ] PCB remains secure inside the enclosure
- [ ] Rear zipper can be opened without pulling on wiring

---

## 21. Troubleshooting

### LittleFS mount fails

Serial Monitor shows:

```text
AudioPlayer error: LittleFS mount failed.
```

Check that:

- a `data/` folder exists in your local Arduino sketch;
- all WAV files were copied from `voice/` into `data/`;
- the LittleFS upload completed successfully;
- the selected flash/partition configuration provides filesystem space.

---

### Audio file not found

If Serial Monitor reports a missing file, check the exact filename. The firmware expects paths such as:

```text
/wake.wav
/comfort_1.wav
/meditation_start.wav
```

Do not rename the files unless the corresponding paths in the firmware are also changed.

---

### No sound but LittleFS mounts successfully

Check the MAX98357A I2S connections:

```text
DIN  → GPIO 18
BCLK → GPIO 17
LRC  → GPIO 16
```

Also check amplifier power, speaker wiring and common ground.

---

### FSR does not detect stroking

Open Serial Monitor and compare `Raw FSR` and `Smooth FSR` while stroking the back.

If the signal changes but does not reach the touch threshold, recalibrate:

```cpp
FSR_TOUCH_THRESHOLD
FSR_RELEASE_THRESHOLD
```

after the sensor is installed beneath the final fabric.

---

### One tap is detected several times

Increase:

```cpp
TAP_DEBOUNCE_MS
```

or adjust the physical mounting of the piezo sensor.

---

### Vibration becomes weak inside the plush

Test the motor before and after adding filling.

If it is strong outside the plush but weak after installation:

- reduce filling immediately around the motor;
- move the motor closer to the inner fabric surface;
- make sure the motor can vibrate freely;
- check the motor driver and power supply.

---

## 22. Notes on Physical Calibration

The numerical values in the firmware reproduce the settings used in this prototype, but a second build may require different thresholds because soft-material construction changes sensing and feedback.

The most important values to re-check after final assembly are:

- FSR touch and release thresholds;
- piezo debounce timing;
- vibration motor placement;
- amount of filling around the vibration motor;
- speaker volume and position.

A successful reproduction should preserve the interaction logic even if some calibration values need to be adjusted for a different physical build.

---

## Author

**Xintong Shao**  
MSc Connected Environments  
University College London
