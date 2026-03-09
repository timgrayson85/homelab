# 🏠 Homelab

My home lab setup for learning, experimenting, and future-proofing my engineering skills.

---

## 📋 Overview

This repo documents my self-hosted AI assistant setup running on a Raspberry Pi. The goal: hands-on experience with AI/ML, DevOps practices, and infrastructure management.

---

## 🖥️ Hardware

| Component | Specs |
|-----------|-------|
| **Device** | Raspberry Pi 4 Model B Rev 1.1 |
| **RAM** | 4GB |
| **Storage** | 32GB SD Card + 1TB External SSD |
| **OS** | Raspberry Pi OS (Linux 6.12.62+rpt-rpi-v8, arm64) |
| **Node.js** | v25.6.1 |

---

## 🧠 AI Stack

### OpenClaw

Self-hosted AI assistant running locally on the Pi.

- **Model:** glm-5:cloud (via Ollama cloud)
- **Channel:** WhatsApp integration
- **Workspace:** External SSD (`/mnt/openclaw`)
- **Config:** Custom assistant named "Anton" 🐧

### Ollama

Model management for local and cloud LLMs.

**Installed models:**
| Model | Size | Location |
|-------|------|----------|
| glm-5:cloud | — | Cloud (ollama.com) |
| qwen2.5:1.5b-instruct | 986 MB | Local |
| tinyllama:1.1b-chat | 637 MB | Local |

> 💡 **Lesson learned:** Local LLMs on a Pi 4 with 4GB RAM struggle. Cloud models via Ollama work much better!

---

## 🛠️ Development Tools

### VS Code

Installed and configured on the external SSD for editing configs and workspace files.

```bash
# Install VS Code on Raspberry Pi
curl -L "https://code.visualstudio.com/sha/download?build=stable&os=linux-deb-arm64" -o /tmp/vscode.deb
sudo apt install -y /tmp/vscode.deb

# Open OpenClaw workspace
code /home/pi/.openclaw/openclaw.code-workspace
```

### Git & GitHub CLI

Authenticated and ready for repo management.

```bash
# Install GitHub CLI
sudo apt install gh

# Authenticate
gh auth login
```

---

## 📁 Storage Layout

```
SD Card (29GB, 73% used)
├── /usr          # System packages (11GB)
├── /home         # User files (5.2GB)
└── /var          # Logs, caches (3.2GB)

External SSD (1TB, 1% used)
├── /mnt/openclaw           # OpenClaw data
├── /mnt/openclaw/alertR    # Cloned repos
├── /mnt/openclaw/homelab   # This repo
└── vscode-data/            # VS Code settings
```

---

## 🚀 Quick Start

### 1. Install Ollama

```bash
curl -fsSL https://ollama.com/install.sh | sh
ollama pull glm-5:cloud
```

### 2. Install OpenClaw

```bash
npm install -g openclaw
openclaw onboard
```

### 3. Configure WhatsApp

Scan QR code when prompted to link your WhatsApp account.

### 4. Customise Your Assistant

Edit the workspace files:
- `IDENTITY.md` — Name and personality
- `SOUL.md` — Core behaviour
- `USER.md` — Your details

---

## 🔧 Useful Commands

```bash
# Check CPU temp
vcgencmd measure_temp

# Check disk space
df -h

# Restart OpenClaw
openclaw gateway restart

# View logs
tail -f ~/.openclaw/logs/*.log
```

---

## 🔮 Future Plans

- [ ] Connect second WhatsApp number for dedicated AI assistant
- [ ] Set up GitHub Copilot integration
- [ ] Add monitoring/alerting (Prometheus/Grafana?)
- [ ] Containerise with Docker
- [ ] Set up Tailscale for remote access
- [ ] Experiment with local models on more powerful hardware

---

## 📚 Resources

- [OpenClaw Docs](https://docs.openclaw.ai)
- [OpenClaw GitHub](https://github.com/openclaw/openclaw)
- [Ollama](https://ollama.com)
- [Raspberry Pi Documentation](https://www.raspberrypi.com/documentation/)

---

<p align="center">
  <i>Built with 🐧 on a Raspberry Pi</i>
</p>