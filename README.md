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
# Start ollama
ollama serve

# Run an LLM model
ollama run glm-5

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

## 🤖 Custom AI Agents

Experimenting with custom GitHub Copilot agents in VS Code — defining specialised AI assistants through prompt files and shared memory.

### The Concept

GitHub Copilot allows you to create custom agents by defining:
- **Agent instructions** — `.github/instructions/*.md` files that define behaviour
- **Prompt files** — `.github/prompts/*.md` for reusable prompt templates
- **Memory files** — Persistent knowledge the agent can read/write

### My Custom Agents

| Agent | Purpose |
|-------|---------|
| **SQL Performance Analyst** | Analyses SQL Server execution plans, identifies performance bottlenecks |
| **Splunk Query Helper** | Writes Splunk queries and dashboard widgets (because regex is painful) |

### Shared Memory Architecture

The key innovation is making agent memory **shareable across a team**:

```
┌─────────────────────────────────────────────────────────────────┐
│                         Local Machine                            │
│                                                                  │
│  ┌─────────────────┐         sync         ┌─────────────────┐   │
│  │  Virtual Memory │  ←────────────────→  │   memory.md     │   │
│  │  (Copilot's     │       bidirectional  │  (in repo)      │   │
│  │   context)      │                      │                 │   │
│  └─────────────────┘                      └────────┬────────┘   │
│                                                    │            │
└────────────────────────────────────────────────────┼────────────┘
                                                     │
                                                     │ Git push/pull
                                                     │
                              ┌──────────────────────┼──────────────────────┐
                              │                      ▼                      │
                              │            ┌─────────────────┐              │
                              │            │  Remote Repo    │              │
                              │            │  (GitHub)       │              │
                              │            └────────┬────────┘              │
                              │                     │                       │
                              │       ┌─────────────┼─────────────┐         │
                              │       ▼             ▼             ▼         │
                              │  ┌─────────┐  ┌─────────┐  ┌─────────┐     │
                              │  │ Team    │  │ Team    │  │ Team    │     │
                              │  │ Mate 1  │  │ Mate 2  │  │ Mate N  │     │
                              │  └─────────┘  └─────────┘  └─────────┘     │
                              │       │             │             │         │
                              │       ▼             ▼             ▼         │
                              │  Same memory.md on each machine             │
                              │                                              │
                              └──────────────────────────────────────────────┘
```

### How It Works

**1. Agent Instructions**

Each agent has an `instructions.md` file that defines its behaviour:

```
.github/
├── instructions/
│   ├── sql-performance-analyst.md
│   └── splunk-query-helper.md
└── memory/
    └── memory.md
```

**2. Memory Synchronisation**

The agent is instructed to mirror its virtual memory with `memory.md`:

```markdown
# Memory Synchronisation

You must keep your virtual memory in sync with the memory.md file:
- Before responding, read memory.md to load previous context
- After learning something new, update memory.md
- Use the file as your persistent knowledge store
```

**3. Git as the Transport Layer**

```
┌────────────┐    pull     ┌────────────┐    push    ┌────────────┐
│  Person A  │ ──────────→ │   Remote   │ ←───────── │  Person B  │
│            │             │   Repo     │            │            │
│ memory.md  │ ←────────── │ memory.md  │ ─────────→ │ memory.md  │
└────────────┘    latest   └────────────┘   updated  └────────────┘
```

### Benefits

| Aspect | Traditional Memory | Shared Memory |
|--------|-------------------|---------------|
| **Persistence** | Lost between sessions | Survives in Git |
| **Team sharing** | Personal only | Everyone gets the same knowledge |
| **Version control** | None | Full Git history |
| **Audit trail** | None | Every change tracked |

### Workflow Pattern

```
┌────────────────────────────────────────────────────────────────┐
│                        Agent Session                            │
│                                                                 │
│  1. Pull latest ──────────────────────────────────────────┐    │
│     git pull                                               │    │
│                                                            │    │
│  2. Load memory ──────────────────────────────────────┐    │    │
│     Agent reads memory.md                              │    │    │
│                                                        │    │    │
│  3. Work ────────────────────────────────────────┐    │    │    │
│     Agent assists using loaded context            │    │    │    │
│                                                    │    │    │    │
│  4. Learn ────────────────────────────────────┐  │    │    │    │
│     Agent updates memory.md with new knowledge │  │    │    │    │
│                                                │  │    │    │    │
│  5. Commit ────────────────────────────────┐  │  │    │    │    │
│     git add memory.md && git commit         │  │  │    │    │    │
│                                             │  │  │    │    │    │
│  6. Push ───────────────────────────────┐  │  │  │    │    │    │
│     git push                            │  │  │  │    │    │    │
│                                         ▼  ▼  ▼  ▼    ▼    ▼    │
└─────────────────────────────────────────────────────────────────┘
```

### Guardrails & Safety

When deploying AI agents in regulated environments, guardrails are critical. Even if the agent inherits your permissions, you need explicit constraints.

**Example from my SQL Performance Analyst:**

```markdown
# Safety Constraints

⚠️ CRITICAL: You must NEVER execute any query against a production database.
- No SELECT statements on production
- No EXPLAIN or SHOW PLAN on production
- No read-only queries on production
- Production is completely off-limits

You may only analyse:
- Execution plans provided to you as text
- Query snippets shared for review
- Non-production environments explicitly identified as safe
```

**Why this matters:**

```
┌─────────────────────────────────────────────────────────────────┐
│                    Agent Permission Model                        │
│                                                                  │
│   Without Guardrails:          With Guardrails:                 │
│   ────────────────────         ─────────────────────            │
│   Agent → "Run this query"     Agent → "I can't do that"        │
│      ↓                               ↓                          │
│   Database → Executes          Blocked by prompt constraint     │
│      ↓                                                          │
│   💥 Production incident                                          │
│                                                                  │
│   Guardrails act as a prompt-level firewall                     │
│   before the agent can even attempt the action                   │
└─────────────────────────────────────────────────────────────────┘
```

**Key principle:** The agent may have read-only permissions, but "read-only" on a critical production system still carries risk:
- Performance impact from expensive queries
- Accidental exposure of sensitive data
- Compliance violations from uncontrolled access

**Prompt engineering as security:**

| Traditional Security | Prompt-Based Guardrails |
|---------------------|------------------------|
| Firewalls, RBAC | Instructions in agent.md |
| Network-level blocks | Semantic understanding |
| Binary allow/deny | Contextual reasoning |
| Infrastructure control | Behavioural control |

Both are needed — prompt guardrails add a semantic layer that understands *why* certain actions are prohibited.

### Gotchas

- **Merge conflicts** — If two people use the agent simultaneously, memory.md may conflict
- **Sync discipline** — Need to pull before starting, push after finishing
- **Context limits** — memory.md can't be infinitely large; prune old knowledge
- **Guardrails are not guarantees** — Prompt instructions can be ignored; use them alongside proper access controls

### Future Exploration

This is essentially a **manual RAG system** — a stepping stone toward:
- Proper vector databases (ChromaDB, Pinecone)
- Embedding-based retrieval
- Automatic knowledge indexing

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
