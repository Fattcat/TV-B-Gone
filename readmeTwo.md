# 📺 TV-B-Gone pre ATtiny85 (Európska verzia)

> Malé, batériou napájané zariadenie na vypínanie televízorov pomocou IR signálov.  
> Táto verzia obsahuje **iba európske (EU) kódy**, je optimalizovaná pre **ATtiny85** a kompiluje sa pomocou štandardných nástrojov AVR-GCC na Linuxe.

---

## 📦 Potrebné komponenty

| Komponent | Množstvo | Poznámka |
|----------|----------|---------|
| ATtiny85 | 1 | DIP8 puzdro |
| Arduino Uno | 1 | ako ISP programátor (iba dočasne) |
| IR LED | 1 | napr. TSAL6200 alebo podobná |
| Tlačidlo | 1 | momentálne (push-button) |
| Kondenzátor | 1 | **10 µF elektrolytický**, 16 V+ |
| Tranzistor (odporúčané) | 1 | napr. 2N2222, BC547 alebo PN2222A |
| Rezistory | 2–3 | 100 Ω (IR LED), 1 kΩ (báza tranzistora), 220 Ω (LED – voliteľné) |
| Napájanie | 2× AA batérie | 3 V (ideálne), alebo 3.3–5 V externý zdroj |

---

## 🔌 Zapojenie

### 1. Programovanie (ATtiny85 → Arduino Uno ako ISP)

| ATtiny85 (fyz. pin) | Názov | Arduino Uno |
|---------------------|-------|-------------|
| 1 | RESET | Pin 10 |
| 4 | GND | GND |
| 5 | **PB0 (IRLED)** | Pin 11 (MOSI) |
| 6 | PB1 (MISO) | Pin 12 |
| 7 | PB2 (SCK) | Pin 13 |
| 8 | VCC | **5 V** |

> ⚠️ **Nezabudni na 10 µF kondenzátor** medzi **RESET a GND na Uno** (– na GND, + na RESET). Bez neho sa Uno resetuje pri otvorení portu.

---

### 2. Prevádzka (samostatné zariadenie)

| ATtiny85 | Komponent |
|---------|-----------|
| Pin 1 (RESET) | → tlačidlo → GND |
| Pin 5 (PB0) | → 100 Ω → **anóda IR LED** → **katóda IR LED** → GND  
| *(alebo lepšie)* | → 1 kΩ → báza tranzistora (2N2222), emitor → GND, kolektor → katóda IR LED, anóda IR LED → VCC |
| Pin 8 | VCC (3–5 V) |
| Pin 4 | GND |

> 💡 LED indikátor (voliteľný): Pin 7 (PB2) → 220 Ω → LED → GND

---

## 🐧 Príprava vývojového prostredia (Linux: Kali/Ubuntu/Debian)

```bash
# 1. Nainštaluj potrebné nástroje
sudo apt update && sudo apt install -y gcc-avr avr-libc avrdude make git

# 2. Stiahni zdrojový kód
git clone https://github.com/adafruit/TV-B-Gone-kit.git
cd TV-B-Gone-kit/firmware
