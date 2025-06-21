# 🧠 Jave Ethical Defender: System Architecture Overview

Jave Ethical Defender is a next-generation Layer 7 firewall built on deterministic AI, purpose-engineered to predict, analyze, and mitigate malicious intent through behavioral inference—not traditional rule-based logic.

This document outlines the architectural components that power Jave’s defense system and how network traffic flows through its intelligent processing layers.

---

## 🧩 System Modules

### 1. Reverse Proxy Layer (`jave-proxy`)
- **Ingress Gateway**: All traffic is funneled through a single entry point (typically port 8080).
- **Purpose**: Isolate live services from direct exposure, enforce consistent routing.
- **Bonus**: CLI injection never reaches live servers—sanitized upstream.

---

### 2. Packet & Header Inspector (`jave-parser`)
- **Task**: Extracts metadata, flags malformed headers, and identifies suspicious patterns (e.g., missing `User-Agent`, invalid content types).
- **Contributions to Risk Score**:
  - Header entropy
  - Known bad fingerprints
  - Domain spoofing attempts

---

### 3. Risk Scoring Engine (`daemon.py`)
- **Dynamic trust map per IP**:
  - Starts neutral (score = 0.6)
  - Adjusts based on cadence, volume, payload, and endpoint profile
- **Decay Logic**:
  - IPs slowly recover trust if idle
  - Bad behavior slows recovery

---

### 4. Behavioral Classifier
- **Core Logic**: Determines intent based on scoring thresholds, session variance, historical trends
- **Actions**:
  - Allow (trusted)
  - Sandbox (moderate risk)
  - Quarantine (malicious or automated behavior)

---

### 5. Honeypot Containment
- **Deceptive endpoints** exposed as bait
- **Behavioral reinforcement**:
  - Legit users never trigger honeypots
  - Bots engage, self-reveal, and are isolated

---

### 6. Logging & Forensic Tracker
- **Persistent event log** with:
  - Packet metadata
  - Payloads (if applicable)
  - Offense classification
  - Trust score delta
- **Use Case**: Auditing, reporting to hosts, regulators, and authorities

---

## 🔁 Data Flow Overview

```text
      Incoming Connection
               ↓
        [ Reverse Proxy ]
               ↓
   [ Packet Inspector & Parser ]
               ↓
      [ Risk Scoring Engine ]
               ↓
     [ Intent Classifier / AI ]
         ↙              ↘
    [ Sandbox ]     [ Quarantine ]
               ↘
            [ Application ]
