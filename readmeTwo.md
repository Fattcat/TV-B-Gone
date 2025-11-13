# 📺 TV-B-Gone for ATtiny85 (EU-Only Version)

> A tiny, battery-powered device that turns off TVs using IR signals.  
> This version includes **only European (EU) power-off codes**, is optimized for **ATtiny85**, and compiles using standard AVR-GCC toolchains on Linux.

---

## 📦 Required Components

| Component | Qty | Notes |
|----------|-----|-------|
| ATtiny85 | 1 | DIP-8 package preferred |
| Arduino Uno | 1 | Used temporarily as ISP programmer |
| IR LED | 1 | e.g., TSAL6200, TSAL7600 |
| Push button | 1 | Momentary tactile switch |
| Capacitor | 1 | **10 µF electrolytic**, 16 V+ |
| Transistor (recommended) | 1 | e.g., 2N2222, BC547, or PN2222A |
| Resistors | 2 | 1 kΩ (transistor base), 220 Ω (status LED, optional) |
| Battery | 1 | LiPo 3.7V 650mah |
| TP4056 usb A or C | 1 | For charging the battery |


---

## 🔌 Wiring

### 1. Programming (ATtiny85 → Arduino Uno as ISP)

<p align="center">
  <img src="attiny85-Schematic.png" alt="Flashing to Attiny85" width="300">
</p>

| ATtiny85 (physical pin) | Name | Arduino Uno |
|-------------------------|------|-------------|
| 1 | RESET | Pin 10 |
| 4 | GND | GND |
| 5 | **PB0 (IRLED)** | Pin 11 (MOSI) |
| 6 | PB1 (MISO) | Pin 12 |
| 7 | PB2 (SCK) | Pin 13 |
| 8 | VCC | **5 V** |

> ⚠️ **Critical**: Add a **10 µF capacitor** between **Uno’s RESET and GND** (– to GND, + to RESET).  
> Without it, the Uno resets when avrdude opens the serial port.

---

### 2. Standalone Operation

| ATtiny85 | Connection |
|---------|------------|
| Pin 1 (RESET) | → **3 leg switch** (not button) → GND |
| Pin 5 (PB0) | → 100 Ω → **IR LED anode** → **IR LED cathode** → GND  
| *(better option)* | → 1 kΩ → transistor base (2N2222), emitter → GND, collector → IR LED cathode, IR LED anode → VCC |
| Pin 8 | VCC (3–5 V) |
| Pin 4 | GND |

> 💡 Optional status LED: Pin 7 (PB2) → 220 Ω → LED → GND

---

## 🐧 Setup (Linux: Kali/Ubuntu/Debian)

```bash
# 1. Install toolchain
sudo apt update && sudo apt install -y gcc-avr avr-libc avrdude make git

# 2. Clone firmware
git clone https://github.com/adafruit/TV-B-Gone-kit.git
cd TV-B-Gone-kit/firmware
```

## ✏️ Code Modification (EU-Only)

- Edit main.c:
- Find this:
  - ```uint8_t region = US;```
- Replace with:
  - const uint8_t region = EU;

- Comment out region detection:
```
// if (PINB & _BV(REGIONSWITCH)) {
//   region = US;
// } else {
//   region = EU;
// }
```

- In the transmission loop, find>
```
if (region == US) {
  j = num_NAcodes;
} else {
  j = num_EUcodes;
}
```

- Replace with:
  - ```j = num_EUcodes;```

- Later, find:
```
if (region == US) {
  code_ptr = (PGM_P)pgm_read_word(NApowerCodes+i);
} else {
  code_ptr = (PGM_P)pgm_read_word(EUpowerCodes+i);
}
```

- Replace with:

```
code_ptr = (PGM_P)pgm_read_word(EUpowerCodes+i);
```

## ⚙️ Configure Makefile
- Update these lines:
```
AVRDUDE_PROGRAMMER = arduino
AVRDUDE_PORT = /dev/ttyACM0
AVRDUDE_FLAGS = -p attiny85 -c $(AVRDUDE_PROGRAMMER) -P $(AVRDUDE_PORT) -b 19200
```
- 🔍 Verify port with:
- ```ls /dev/ttyACM* /dev/ttyUSB* 2>/dev/null```

## 📤 Flash Firmware

- Upload ArduinoISP to Uno via Arduino IDE (ON WINDOWS)
- File → Examples → 11.ArduinoISP → ArduinoISP)
- Connect 10 µF capacitor (RESET–GND on Uno)
- Wire ATtiny85 as above (check main README.md images) [here](README.md).

## ! DOUBLECHECK WIRES !

### Run:
- compare your output with [this output on bottom](README.md#Output).
```
make clean
make
make program
```

- ✅ Success output:
- Something like this
```
avrdude: 7838 bytes of flash verified
avrdude done. Thank you.
```

- after sucessful flashing, Disconnect arduino uno from PC & attiny85
- connect IR diode, Power attiny85 with 5V (can be 3.3V or 5V)
- Point IR at TV, press button --> Boom it shlould Power it OFF
- Thanks for reading




















