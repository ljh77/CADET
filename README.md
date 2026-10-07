# CADET-UP: Docent Robot System

> **C**losed-loop **A**rchitecture for **D**ocent **E**xperience with **T**eleop & **U**nified **P**ipeline

A full-stack robotic docent platform for museum/indoor navigation, integrating voice commands, visual verification, and adaptive mobility control.

---

## 🤖 System Overview

**CADET-UP** is a museum docent robot built on the Yahboom ROSMASTER M3 platform, enhanced with:

- 🎤 **Voice-Commanded Navigation** — Real-time STT → NLU → Navigation pipeline
- 👁️ **Visual Verification** — Arm-mounted camera for post-execution arrival confirmation
- 🧠 **LLM-Driven Intelligence** — Cascade NLU (keyword → LLM interpretation)
- 📊 **Web Dashboard** — 12-stage boot health monitor + map editor + navigation viewer
- 🎮 **Joystick Teleoperation** — Manual control fallback

---

## 🏗️ System Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                    CADET-UP System Stack                    │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│  Voice Layer (Port 5012)                                    │
│  ├─ Web Speech API (STT) — PC-based                        │
│  └─ Cascade NLU → LLM Interpretation                       │
│                                                              │
│  Orchestration Layer (Port 5030)                            │
│  ├─ Main Command Processor                                  │
│  ├─ Navigation Goal Publisher                              │
│  └─ Arm + Motor Coordinator                                │
│                                                              │
│  Robot Control Layer                                        │
│  ├─ ROS2 Navigation (Nav2)                                 │
│  ├─ Motor Controller (micro-ROS) — /odom_raw, /cmd_vel    │
│  ├─ Arm Servo Control — 6DOF Joint Array                  │
│  └─ SLAM/Localization — AMCL + EKF                        │
│                                                              │
│  Sensor Layer                                               │
│  ├─ Depth Camera (USB) — ARM pose estimation              │
│  ├─ LiDAR — SLAM / Obstacle Detection                      │
│  ├─ IMU — Motion Tracking                                  │
│  └─ Wireless Mic (Shure MVX2U) — Voice Input              │
│                                                              │
│  Output Layer                                               │
│  ├─ TTS Server (Port 5008) — Piper TTS                    │
│  ├─ USB Speaker — Audio Output                            │
│  └─ Arm Joints — Gesturing & Pointing                      │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

---

## 🚀 Quick Start

### 1. Boot the System

```bash
cd ~
./cadet_up.sh
```

Monitor boot progress at:
```
http://192.168.0.109:5010
```

**Boot Stages (12-LED Dashboard):**
1. ROS Core
2. Micro-ROS / Odometry
3. SLAM (LIDAR merge)
4. EKF Localization
5. AMCL Localization
6. Nav2 Global Planner
7. Nav2 Local Planner
8. Smoother
9. Camera System
10. Arm Control
11. TTS Server
12. Orchestrator (5030)

✅ All green = Ready to operate

### 2. Navigate via Voice

**Scenario 1: Go to Waypoint 2**
```bash
# Microphone input (via 5012 STT)
"이동 2번 위치" or "Go to position 2"

# System flow:
# STT (5012) → NLU (5030) → Nav2 → /cmd_vel
```

**Scenario 2: Manual Joystick Control**
```bash
# Joystick connected to /dev/input/js0
# joy_node → teleop_twist_joy → /cmd_vel → Motor Control
```

### 3. Check System Health

```bash
# ROS2 node status
ros2 node list

# Topic flow
ros2 topic list

# Verify odometry
ros2 topic echo /odom_raw --once

# Check motor command
ros2 topic echo /cmd_vel --once
```

---

## 🎯 Key Features

### Voice-Commanded Navigation
- **Input:** Wireless Shure MVX2U mic or Web Speech API (Port 5012)
- **Processing:** Cascade NLU (keyword matching → LLM fallback)
- **Output:** Nav2 goal publisher → /cmd_vel

### Visual Arrival Verification
- **Arm-Mounted Camera:** Post-execution visual confirmation
- **Use Case:** Closes the command loop — "I reached position 2"

### Real-Time Dashboard (Port 5010)
- 12-stage boot monitor with color indicators
- Map editor (Port 5014)
- Navigation viewer
- Target tool (Port 5011) — Waypoint management

### Hardware-Aware Design
Documented constraints for future optimization:
- **Mecanum Yaw:** 2.74× odometry over-reporting
- **USB 2.0:** Depth camera bandwidth limitation
- **EKF @ 6Hz:** IMU samples discarded
- **AMCL Rotation:** Divergence on sharp turns

---

## 📡 Port Reference

| Port | Service | Purpose |
|------|---------|---------|
| **5008** | Piper TTS | Text-to-Speech synthesis |
| **5010** | Boot Dashboard | 12-stage health monitor |
| **5011** | Target Tool | Waypoint registration & arm control |
| **5012** | STT Server | Speech-to-Text (Web Speech API) |
| **5013** | VoiceToControl | Web-based voice + button control |
| **5014** | Map Editor | Map visualization & waypoint editor |
| **5030** | Orchestrator | Main command processor (NLU + Navigation) |
| **8899** | File Server | Drag-and-drop file upload |

---

## 🛠️ Hardware Stack

- **Platform:** Yahboom ROSMASTER M3 (improved)
- **Compute:** NVIDIA Jetson Orin NX (ROS2, LLM serving)
- **Motion:** Mecanum wheels + 6DOF arm
- **Sensors:**
  - Depth camera (USB, ARM pose)
  - 2D LiDAR (SLAM, collision avoidance)
  - IMU (motion tracking)
  - Wireless mic (Shure MVX2U)
- **Output:**
  - USB speaker (TTS audio)
  - Arm servos (gesturing)

---

## 📊 Software Stack

- **OS:** Ubuntu 22.04 (Jetson)
- **Middleware:** ROS2 Humble
- **Navigation:** Nav2 (AMCL + EKF)
- **SLAM:** multi_lidar_merge + SLAM Toolbox
- **LLM:** Claude API (NLU interpretation via 5030)
- **Speech:** Web Speech API (STT) + Piper (TTS)
- **Rendering:** Unreal Engine MetaHuman (future integration)
- **Web UI:** FastAPI + React (dashboard, map editor)

---

## 🧪 Testing & Debugging

### Joystick Input Check
```bash
python3 /tmp/check_joystick.py
```

### Micro-ROS Board Recovery
```bash
# If /odom_raw is missing:
ros2 run micro_ros_agent micro_ros_agent serial --dev /dev/ttyUSB0 -b 115200
```

### Voice Pipeline Test
```bash
# Check STT server
curl http://192.168.0.113:5012/status

# Check TTS server
curl -X POST http://192.168.0.109:5008/synthesize -d '{"text":"안녕하세요"}'

# Check orchestrator
curl http://192.168.0.109:5030/status
```

### Navigation Goal Test
```bash
# Publish test goal to Nav2
ros2 action send_goal /navigate_to_pose nav2_msgs/action/NavigateToPose \
  "{goal: {pose: {header: {frame_id: 'map'}, pose: {position: {x: 1.0, y: 1.0}, orientation: {w: 1.0}}}}}"
```

---

## 📈 Performance Notes

| Metric | Value | Notes |
|--------|-------|-------|
| **E2E Latency** | ~800ms | Voice command → motion start |
| **Navigation Accuracy** | ±0.3m | With visual verification |
| **Battery Life** | ~4-6 hrs | Typical museum operation |
| **Max Speed** | 0.5 m/s | Safe indoor navigation |

---

## 🎓 Research & Portfolio

**CADET-UP** is developed as a portfolio/demo system for:
- Hardware constraint characterization (Mecanum yaw, EKF bandwidth, USB limits)
- Closed-loop voice command execution with visual feedback
- Real-world digital human docent integration (planned: Unreal MetaHuman)

**Target Venues:** King Sejong Museum, Korean cultural heritage sites

**Funding:** MCST/KOCCA (RS-2025-25459094)

---

## 📝 Future Enhancements

- [ ] Unreal Engine MetaHuman integration for digital docent
- [ ] Multi-robot coordination for large museums
- [ ] Visitor behavior tracking & adaptive narration
- [ ] Korean heritage VLM integration (K-HerVLM)
- [ ] Self-loop prevention (speech rejection during nav)
- [ ] Latency optimization for real-time feedback

---

## 📞 Support & Contact

**Maintainer:** Jun (GIST Cultural Technology Research Institute)  
**Project Lead:** System Integration for Humanoid Robotics  
**Institution:** GIST, Gwangju, South Korea

For issues, contact the robotics lab or check:
- Boot dashboard: `http://192.168.0.109:5010`
- System logs: `~/.ros/log/`

---

## 📄 License

[Specify your license here — MIT, Apache 2.0, etc.]

---

**Last Updated:** Oct 7, 2026  
**System Status:** ✅ Active (voice pipeline tested, navigation ready)
