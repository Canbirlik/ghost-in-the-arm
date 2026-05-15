# 👻 ghost-in-the-arm

> *"What if your hand wasn't entirely yours?"*

An open-source brain-computer interface project that reads muscle signals via EMG, generates involuntary muscle movement via EMS stimulation, and enables voice command control through Claude AI.

---

## 🧠 What It Does

**Mode 1 — Human-Human Interface (HHI)**
Person A flexes their arm → EMG sensor captures the signal → Person B's arm moves involuntarily.

**Mode 2 — AI Control**
You speak a command (*"gently flex your left index finger"*) → Claude AI interprets it → your arm moves accordingly.

---

## 🏗️ System Architecture

```
[Person A] --> [EMG Sensor] --> [Arduino Nano] --> [X9C103S] --> [XL6009] --> [DRV8871] --> [Electrode Cable] --> [Electrode Pads] --> [Person B]
                                      |
                               [Claude API]
                                      |
                              [Voice Command]
```

### Component Roles

| Component | Role |
|-----------|------|
| **Arduino Nano** | Central processor — controls all modules |
| **EMG Sensor** | Reads muscle signal from Person A |
| **X9C103S** | Digital potentiometer — adjusts stimulation intensity via software |
| **XL6009** | Boost converter — steps up 9V → ~40V (required for muscle stimulation) |
| **DRV8871** | H-Bridge — delivers biphasic waveform from Arduino to electrode |
| **Relay Module** | Opens/closes the stimulation channel |
| **Electrode Lead Cable** | Connects DRV8871 OUT1/OUT2 to electrode pads via 2mm snap connector |
| **Electrode Pads** | Attached to Person B's arm, delivers EMS signal |
| **Claude API** | Analyzes voice commands, sends Serial commands to Arduino |

---

## 📦 Bill of Materials

### Amazon.de — Total: €102.12 (Free Shipping)

| # | Product | Qty | Unit Price | Total | Notes |
|---|---------|-----|------------|-------|-------|
| 1 | AZDelivery Arduino Nano V3 USB-C | 3-pack | €6.78 | €20.33 | ATmega328P, CH340, pre-soldered |
| 2 | AZDelivery 2-Channel Relay Module 5V Optocoupler | 3-pack | €3.38 | €10.15 | Low-level trigger, galvanic isolation |
| 3 | DRV8871 H-Bridge Motor Driver | 2-pack | €5.59 | €11.18 | 6.5V–45V, 3.6A, PWM control |
| 4 | AZDelivery MB102 Breadboard Kit | 1 set | €7.31 | €7.31 | 830-pin + 65 jumper wires + power module |
| 5 | Warriors 9V Alkaline Block Battery | 4-pack | €1.52 | €6.09 | 500mAh, long shelf life |
| 6 | 9V Battery Clip Cable | 15-pack | €0.37 | €5.58 | 15cm cable, I-type snap connector |
| 7 | axion TENS-EMS Electrode Pads 4x4cm | 16-pack | €0.63 | €10.12 | Reusable, 2mm plug, certified medical device |
| 8 | axion TENS/EMS Electrode Lead Cable 2mm | 2-pack | €5.06 | €10.12 | 120cm, connects DRV8871 to electrode pads |
| 9 | Beizkna 80W LCD Soldering Iron Kit | 1 set | €21.24 | €21.24 | 5 tips, solder wire, desoldering pump, stand |

### AliExpress — Total: €18.63

| # | Product | Qty | Unit Price | Shipping | Total | Notes |
|---|---------|-----|------------|----------|-------|-------|
| 1 | SNVI X9C103S Digital Potentiometer Module | 2 pcs | €1.75 | Free | €3.49 | 10KΩ, 100 steps, SPI, DC3V–5V |
| 2 | EMG Muscle Sensor + Cable + Electrodes | 1 pcs | €4.09 | Free | €4.09 | A+A+A Store, analog output, 3 electrodes included |
| 3 | XL6009 Boost Converter Module (with display) | 2 pcs | €0.87 | €4.08 | €5.82 | 4A, 5–32V → 5–50V, display version |

### 💰 Grand Total

| Store | Amount |
|-------|--------|
| Amazon.de | €102.12 |
| AliExpress | €18.63 |
| **GRAND TOTAL** | **€120.75** |

---

## 🔌 Wiring Overview

```
9V Battery
    └── XL6009 (boost to ~40V)
            └── DRV8871 (H-Bridge)
                    ├── OUT1 ──┐
                    └── OUT2 ──┴── Electrode Lead Cable (2mm snap)
                                        └── Electrode Pads → Arm

Arduino Nano
    ├── D9  ──── DRV8871 IN1
    ├── D10 ──── DRV8871 IN2
    ├── A0  ──── EMG Sensor SIG
    └── SPI ──── X9C103S (intensity control)
```

---

## 📁 Repository Structure

```
ghost-in-the-arm/
│
├── README.md
├── hardware/
│   └── wiring/        ← Breadboard wiring diagrams
├── firmware/
│   └── arduino/       ← Arduino firmware (EMG + EMS control)
└── software/
    └── ai_control/    ← Python + Claude API integration
```

---

## 🔌 Operating Modes

### Mode 1 — Classic HHI
1. Person A flexes arm → EMG sensor reads the signal
2. Arduino detects threshold crossing → triggers stimulation
3. EMS signal delivered to Person B's arm via DRV8871 → arm moves involuntarily

### Mode 2 — AI Control
1. User gives a voice command (e.g. *"gently flex your left index finger"*)
2. Python → Whisper (speech-to-text) → Claude API
3. Claude analyzes the command → determines channel, intensity, duration
4. Sends Serial command to Arduino
5. User's arm moves accordingly

---

## ⚠️ Safety

- **NEVER** place electrodes near the **heart** or **carotid artery**
- Electrodes from the **same channel** must not be placed on **opposite sides** of the body
- Limit XL6009 output voltage to **max 40V** (DRV8871 safety limit)
- Always work in a **well-ventilated area** when soldering
- People with **pacemakers** or serious heart conditions must **NOT** use this project

---

## 🚀 Roadmap

- [x] Design system architecture
- [x] Source and order all components
- [ ] Breadboard wiring
- [ ] Arduino firmware — EMG mode
- [ ] Arduino firmware — EMS stimulation (based on OpenVstim FastPulse.ino)
- [ ] Python + Claude API integration — AI mode
- [ ] Test and calibrate
- [ ] Phase 2: Multi-channel finger-level control

---

## 📖 References

- [OpenVstim](https://github.com/MonzurulAlam/OpenVstim) — Open source transcutaneous voltage stimulator
- [openEMSstim](https://github.com/PedroLopes/openEMSstim) — Open hardware EMS/TENS controller
- [Backyard Brains HHI](https://backyardbrains.com/products/human-human-interface) — Original inspiration

---

## 📄 License

MIT License — feel free to build, modify and share.

> *Built with curiosity, Claude AI, and a slightly reckless willingness to put electrodes on arms.* ⚡

