# IoT Notes

> My study log for TFB2093 Internet of Things (UTP). Built from my lab reports and the ISS group project before the course folder was cleared out.

_Last updated: 31/10/2026_

---

## 📖 Core Concepts

### Microcontroller Basics (Arduino Uno vs ESP32)
| | Arduino Uno | ESP32 |
|---|---|---|
| ADC resolution | 10-bit, `analogRead()` gives 0 to 1023 | 12-bit, gives 0 to 4095 by default |
| Serial baud I used | 9600 | 115200 |
| Wi-Fi / Bluetooth | No | Yes |
| Sleep API | `LowPower` library | `esp_sleep.h` (ships with the ESP32 board core) |
| Program storage | 32,256 bytes | much larger |

- `setup()` runs once at boot, `loop()` runs forever after it.
- `Serial.begin(baud)` opens the Serial Monitor link. Both sides must use the same baud rate.
- `Serial.flush()` waits until everything in the output buffer is sent. Call it before sleeping, or the last line gets cut off.
- GPIO 34 on many ESP32 boards is **input-only**, which is fine for `analogRead()` but not for driving an LED.

### Analog Input, ADC and Mapping
- **ADC (Analog-to-Digital Converter)** turns a voltage into a number. On the Uno, 0 V is 0 and 5 V is 1023.
- **Mapping** is scaling a value from one range into another range.
  - To percent: `pct = (raw / 1023.0) * 100.0` (use `1023.0`, not `1023`, or integer division gives 0).
  - To servo angle: `map(raw, 0, 1023, 0, 180)`.
- **Potentiometer** is a knob (variable resistor). The middle pin gives a voltage between 0 and 5 V depending on the knob position.
- **LDR (Light Dependent Resistor)** changes resistance with light. It needs a **voltage divider** (LDR + fixed resistor) so the change in resistance becomes a change in voltage the ADC can read.
  - Limitation: slow response time, so it is bad for detecting fast light changes. A digital light sensor is faster and more accurate.

| LDR reading (raw) | Label I used |
|---|---|
| > 700 | Bright |
| 301 to 700 | Normal |
| <= 300 | Dark |

### Digital Input, Buttons and Debounce
- `pinMode(pin, INPUT_PULLUP)` turns on the board's internal pull-up resistor, so no external resistor is needed.
  - With a pull-up, the pin reads **HIGH when not pressed** and **LOW when pressed** (button connects the pin to GND).
- **Bouncing** is when a button's metal contacts flicker for a few ms when pressed, so one press can be read as many.
- **Debounce** fixes this by ignoring changes for a short window (I used 30 ms with `millis()`), or more crudely with a `delay(150)` in the loop.
- **Edge detection**: compare the current reading to the previous one (`prevArm`) so an action fires once per press, not every loop while the button is held.

### Timing: `delay()` vs `millis()`
| | `delay(ms)` | `millis()` |
|---|---|---|
| Blocking? | Yes, nothing else runs | No |
| How | Pause for ms | Returns ms since boot; compare `now - last >= interval` |
| Use when | Simple tests | Doing 2+ things at different rates (blink every 1 s and read A0 every 2 s) |

- Store `millis()` values in `unsigned long`, since they overflow an `int` after about 33 seconds.

### Low Power and Sleep Modes
- Sleeping saves battery, which matters for IoT nodes that run on batteries for months.
- **Uno**: `LowPower.powerDown(SLEEP_8S, ADC_OFF, BOD_ON)` sleeps about 8 s (8 s is the watchdog timer's maximum). `ADC_OFF` turns off the ADC to save power. `BOD_ON` keeps brown-out detection (reset on low voltage) on.
- **ESP32 light sleep**: CPU pauses, RAM is kept, and the program **resumes** where it stopped. `esp_sleep_enable_timer_wakeup(us)` then `esp_light_sleep_start()`.
- **ESP32 deep sleep**: almost everything is off, RAM is lost, and the board **reboots** into `setup()` on wake. That is why the deep sleep sketch puts everything in `setup()` and leaves `loop()` empty.
- Timer wakeup takes **microseconds**: 2 s = `2000ULL * 1000ULL`, 10 s = `10ULL * 1000000ULL`.

### Outputs: LEDs, Buzzer, Servo
- `digitalWrite(pin, HIGH/LOW)` turns an LED on or off.
- `tone(pin, 2000)` plays a 2000 Hz tone on a buzzer, `noTone(pin)` stops it.
- **Servo** moves to an angle 0 to 180 degrees with `myServo.write(angle)` after `myServo.attach(pin)`.
- **Deadband** ignores small changes (I ignored < 10 degrees) so the servo does not jitter from a noisy pot.
- **Rate limit** only allows a move every N ms (I used 20 ms).
- Trade-off: a bigger delay or deadband means smoother but slower to respond. You cannot have both maxed out.

### Sensors: PIR Motion
- **PIR (Passive Infrared) sensor** detects movement from body heat. It is a digital sensor: `digitalRead()` gives HIGH on motion, LOW otherwise.

### IoT System Architecture (from the ISS project)
```
Data source (API)  ->  Server (fetch + store)  ->  Database  ->  REST API  ->  Dashboard
WTIA API              Node.js, every 1 s         MySQL         Node.js       React
```
- **Data acquisition**: fetch on a schedule, respecting the API's **rate limit** (WTIA allows 1 request per second).
- **Storage**: one row per sample. 1 sample/s for 3 days = 259,200 rows, so storage planning matters.
- Store two times: the source's `timestamp` and your own `fetched_at`. The gap between them shows latency and missing samples.
- **REST API**: the server exposes the data through URLs so the frontend never touches the database directly.
- **Visualisation**: live card + chart + map/globe, plus offline analysis (CSV export into RStudio).

---

## 🧪 Labs & Practice

| Lab | Topic | What I built | Notes |
|---|---|---|---|
| Lab 3 (W5) | Low power | Uno `LowPower` 8 s sleep reading A0; ESP32 non-blocking blink + A34 read; ESP32 light sleep (2 s) and deep sleep (10 s) | 4b and 4c failed to compile: `esp_sleep.h: No such file or directory` |
| Lab 4 (W7) | PIR alarm (Tinkercad) | 1: PIR prints Motion / No Motion. 2: PIR triggers LED + buzzer. 3: Arm/disarm button with debounce. 4: Mini alarm with arm/disarm + silent mode | Silent mode = LED only, no buzzer |
| Lab 8 (W11) | 3x3 LED matrix game | 4: Move a lit "player" LED with 4 buttons, clamped inside the grid. 5: Reaction game - press OK on the lit LED within 2 s, score counter, smiley on win, cross on lose | [Activity 4](https://www.tinkercad.com/things/fopX6BWCx7X-lab8w11-4), [Activity 5](https://www.tinkercad.com/things/69T046f56Jp-lab8w11-5) |
| Lab Assignment | Analog sensors | 1: Pot to percent on Serial Monitor and Plotter. 2: LDR with Bright/Normal/Dark thresholds. 3: Pot-controlled servo with deadband + rate limit | Reflections on mapping, LDR limits, smoothness vs responsiveness |

Patterns I used for the 3x3 matrix (1 = LED on):

| Win (smiley) | Lose (cross) |
|---|---|
| `1 0 1` / `0 1 0` / `1 1 1` | `1 0 1` / `0 1 0` / `1 0 1` |

Reaction time = `millis() - targetTime`, printed in ms on win.

---

## 🛰️ Group Project: Real-Time ISS Tracking

Full files: [`projects/iot-iss-telemetry/`](../projects/iot-iss-telemetry/)

- **Team**: Andrew Tan Joe Wen, me, Muhammad Ikhwan bin Mohd Azahari, Muhammad Thaqif Adli bin Abdul Aziz.
- **Goal**: Fetch ISS latitude, longitude, altitude and velocity from the WTIA API every second for 3 days (from 12 November 2025), store it, analyse it and show it live.
- **Stack**: Node.js server, MySQL, React + r3f-globe + r3f-drei (3D globe), Leaflet (flat map), Recharts (altitude chart), Tailwind CSS, RStudio for analysis.
- **Cost**: RM0.00, all free tools, run locally. Next step would be hosting on Render or AWS.
- **Results (velocity)**: max 27,601, min 27,530, average 27,573.
- **Results (altitude)**: about 416 to 438, average 424.1.
- **Limitations**: 1 req/s rate limit plus network gaps, storage growth for longer runs, and API rounding/latency affecting altitude accuracy.

---

## 🔧 Handy Snippets
```cpp
// Non-blocking timer
unsigned long last = 0;
if (millis() - last >= 1000) { last = millis(); /* do thing */ }

// Analog to percent
float pct = (analogRead(A0) / 1023.0) * 100.0;

// Analog to servo angle
int angle = map(analogRead(A0), 0, 1023, 0, 180);

// Button with internal pull-up (pressed == LOW)
pinMode(BTN, INPUT_PULLUP);
if (digitalRead(BTN) == LOW) { /* pressed */ }

// Debounce + edge detect
int r = digitalRead(BTN);
if (r != prev && millis() - lastDebounce > 30) {
  lastDebounce = millis();
  if (r == LOW) armed = !armed;   // fires once per press
}
prev = r;

// ESP32 deep sleep for 10 s (board reboots on wake)
esp_sleep_enable_timer_wakeup(10ULL * 1000000ULL);
esp_deep_sleep_start();
```

---

## ❓ Questions to Revisit
- **Lab 3 compile error**: `esp_sleep.h` is not a library you download. It comes with the ESP32 board core. The fix is installing "esp32" in Boards Manager and **selecting an ESP32 board** before compiling. The error happened because the board was still set to Arduino Uno.
- **Lab Assignment Task 1B bug**: `Serial.println(sensorValue);` uses a variable that does not exist. It should be `Serial.println(raw);`.
- **ISS report units**: The altitudes were labelled miles and then converted, but the ISS flies at about 400 to 420 km. So 415.9 to 438.3 is already **km**. Same with velocity: about 27,600 is **km/h** (about 17,150 mph). Also "max" and "min" altitude were swapped (415.9 is the min). Check this before reusing the numbers anywhere.
- Why does deep sleep lose RAM but light sleep keeps it? (Deep sleep powers off the CPU and main RAM; only the RTC domain stays on.)

## 🔗 Resources
- [Where the ISS At? API docs](https://wheretheiss.at)
- ISS project repos: [server](https://github.com/AndrewTJW/iot_backend), [dashboard](https://github.com/AndrewTJW/iot_dashboard), [my analysis](https://github.com/affannajiy/iot_analysis)
- [ISS project video](https://www.youtube.com/watch?v=tsH1vv8k8K8)
- [Tinkercad Circuits](https://www.tinkercad.com/circuits), used for Labs 4, 8 and the assignment
