# CADET-UP: Docent Robot System

> **C**losed-loop **A**rchitecture for **D**ocent **E**xperience with **T**eleop & **U**nified **P**ipeline

A full-stack robotic communicate docent platform for museum/indoor navigation, integrating voice commands, visual verification, and adaptive mobility control.

---

## 🤖 System Overview

**CADET-UP is designed as a platform-independent support solution, enabling its core technologies and framework to be extended and integrated into various robotic and mobility systems in the future.**
Enhanced with advanced autonomous navigation and intelligent interaction capabilities.


- 🎤 **Voice-Commanded Navigation** — Real-time STT → NUL → Navigation pipeline
- 👁️ **Visual Verification** — Camera for post-execution arrival confirmation or Visual Detective
- 🧠 **LLM-Driven Intelligence** — Cascade NLU (keyword → LLM interpretation) or LLM Model
- 📊 **Web Dashboard** — 15-stage boot health monitor + map editor + navigation viewer
- 🎮 **Joystick Teleoperation** — Manual control fallback


### 1. Auto Check-UP and Wake-Up system
![Auto Check-UP and Wake-Up System](cadet_system_1.png)
https://github.com/user-attachments/assets/024c38c0-a5e6-44ad-bb63-92949c84e684

### 2. Navigate tool
![Navigate](CADET_SYSTEM_2.png)

### 3. Voice via Move(Navi)
https://github.com/user-attachments/assets/1a6b042b-9a90-4d49-93da-be9e23cf0096

---

## 📈 Performance Notes

| Metric | Value | Notes |
|--------|-------|-------|
| **E2E Latency** | ~800ms | Voice command → motion start |
| **Navigation Accuracy** | ±0.3m | With visual verification |
| **Battery Life** | ~4-6 hrs | Typical museum operation |
| **Max Speed** | 0.5 m/s | Safe indoor navigation |



## 📄 License

[Specify your license here — MIT, Apache 2.0, etc.]

## Contact

junh.lee@gist.ac.kr
