# Airia-AI-SOC-HomeLab 
 
## Objective
 
The primary objective of this project was to build and deploy a fully automated **Security Operations Center (SOC) Triage System** using Python, virtual machines, and an AI agent powered by a structured SOC playbook. This hands-on home lab simulates real-world network attack detection and AI-driven alert analysis — skills that are critical in modern Security Operations Centers.
 
The system captures live network traffic on an internal server, detects suspicious source IPs based on packet thresholds, generates structured JSON alerts, and forwards them to an AI agent (hosted on Airia.ai) that performs automated triage and returns a professional SOC analyst report.
 
**Key goals included:**
 
- Deploying a two-VM lab environment (attacker + internal server) using VirtualBox
- Writing a Python automation script to capture ICMP traffic using TShark
- Implementing threshold-based detection logic to identify suspicious IPs
- Generating structured JSON security alerts with unique alert IDs
- Building an AI agent on Airia.ai trained with a custom SOC playbook
- Integrating the Python script with the Airia API for real-time AI-powered triage
- Receiving automated threat classification, MITRE ATT&CK mapping, and recommended actions
 
---
 
## Skills Learned
 
### Technical Skills
 
- **Python Scripting & Automation** — Built an end-to-end network monitoring and alerting pipeline in Python
- **Network Traffic Analysis** — Used TShark to capture and parse ICMP packets into structured CSV data
- **Threat Detection Logic** — Implemented IP-based packet threshold analysis to identify suspicious activity
- **JSON Alert Engineering** — Structured alert payloads with metadata, evidence fields, and unique identifiers
- **AI Agent Development** — Configured and deployed a SOC analyst AI agent using a custom playbook on Airia.ai
- **REST API Integration** — Sent alert data to an external AI API and parsed structured JSON responses
- **Linux System Administration** — Managed services, ran packet captures, and executed Python scripts on Kali Linux
- **MITRE ATT&CK Framework** — Interpreted AI-generated threat classifications mapped to ATT&CK techniques
- **SOC Triage Workflow** — Applied a real SOC playbook including risk scoring, escalation logic, and executive summaries
- **Virtual Machine Networking** — Configured bridged networking between attacker and server VMs for traffic simulation
 
---
 
## Tools Used
 
| Tool | Purpose |
|------|---------|
| **Python 3** | Core automation and scripting language |
| **TShark** | CLI-based packet capture and CSV conversion |
| **Airia.ai** | AI agent platform for hosting the SOC analyst agent |
| **VirtualBox** | Hypervisor for creating the isolated lab environment |
| **Kali Linux** | Internal server VM running the Python monitoring script |
| **Ubuntu** | Attacker VM used to generate simulated malicious traffic |
| **ping / ICMP flood** | Simulated network attack traffic for testing |
| **REST API (requests)** | Sending JSON alerts to the Airia AI agent |
| **Git / GitHub** | Version control and project portfolio documentation |
| **UUID / JSON** | Generating unique alert IDs and structured alert payloads |
 
---
 
## Architecture Overview
 
```
┌─────────────────────┐         ICMP Flood          ┌──────────────────────────┐
│                     │ ─────────────────────────►  │                          │
│   Attacker VM       │                             │   Internal Server VM     │
│   (Ubuntu)          │                             │   (Kali Linux)           │
│                     │                             │   Running Python Script  │
└─────────────────────┘                             └────────────┬─────────────┘
                                                                 │
                                                    TShark captures traffic
                                                                 │
                                                    Analyze packets per source IP
                                                                 │
                                                    If count > threshold → Alert
                                                                 │
                                                                 ▼
                                                    ┌────────────────────────┐
                                                    │   JSON Alert Payload   │
                                                    │   { alert_id, ip,      │
                                                    │     packet_count, ... } │
                                                    └────────────┬───────────┘
                                                                 │
                                                          API POST request
                                                                 │
                                                                 ▼
                                                    ┌────────────────────────┐
                                                    │   Airia AI Agent       │
                                                    │   (SOC Playbook)       │
                                                    │                        │
                                                    │ • Threat Classification│
                                                    │ • Risk Scoring (0-100) │
                                                    │ • MITRE ATT&CK Mapping │
                                                    │ • Recommended Actions  │
                                                    │ • Executive Summary    │
                                                    └────────────────────────┘
```
 
---
 
## Python Script Flow
 
```
1. capture_traffic()     →  TShark captures ICMP packets for 100 seconds
2. convert_to_csv()      →  Converts .pcap to structured CSV using TShark fields
3. analyze_traffic()     →  Counts packets per source IP; flags IPs above threshold (>40)
4. generate_alert()      →  Builds JSON alert with unique SOC alert ID
5. send_to_airia()       →  POSTs alert to Airia API; prints AI triage response
```
 
---
 
## Steps
 
### Step 1: Environment Setup
 
**Objective:** Build the isolated virtual lab for simulating attacks and monitoring
 
- Installed VMware on the host machine (16GB RAM recommended)
- Created two virtual machines:
  - **Attacker VM** — Ubuntu, used to generate malicious traffic
  - **Internal Server VM** — Kali Linux, runs the Python monitoring script
- Configured both VMs with **Bridged Networking** so they share the same subnet
- Verified both machines could communicate via ping
- Noted the internal server IP: `192.168.x.xxx`
 
---
 
### Step 2: Build the AI Agent on Airia.ai
 
**Objective:** Create and deploy the SOC Analyst AI agent
 
- Signed up at [airia.ai](https://airia.ai) and created a new project
- Selected an AI model (e.g., GPT-4o Nano or equivalent)
- Pasted the full SOC Analyst playbook as the system prompt — this defines:
  - Input validation rules
  - Threat classification categories
  - Risk scoring model (0–100)
  - MITRE ATT&CK mapping logic
  - Tier 1 analyst action recommendations
  - Escalation thresholds
  - Executive summary generation
  - Output format (strict JSON)
- Published the agent and retrieved:
  - **API Endpoint URL**
  - **API Key**
- Stored both values securely in a `.env` file (not committed to GitHub)
 
---
 
### Step 3: Write and Configure the Python Script
 
**Objective:** Deploy the network monitoring and alerting automation
 
- Wrote `sc.py` on the Kali Linux internal server
- Configured key parameters:
  ```python
  INTERFACE = "eth0"          # Network interface to monitor
  CAPTURE_DURATION = 100      # Capture window in seconds
  THRESHOLD = 40              # Packet count to trigger an alert
  DESTINATION_IP = "192.168.x.xxx"
  ```
- Installed required dependencies:
  ```bash
  sudo apt install tshark
  pip install requests python-dotenv
  ```
- Added `.env` file with Airia API credentials:
  ```
  AIRIA_API_URL=your_url_here
  AIRIA_API_KEY=your_key_here
  ```
 
---
 
### Step 4: Simulate the Attack (Attacker VM)
 
**Objective:** Generate suspicious ICMP traffic to trigger detection
 
- From the Ubuntu attacker VM, ran a ping flood targeting the internal server:
  ```bash
  ping 192.168.x.xxx -c 50
  ```
- Observed packets arriving on the internal server while the Python script was running
- The attacker IP exceeded the threshold of 40 packets within the capture window
 
---
 
### Step 5: Run the Python Monitoring Script
 
**Objective:** Capture traffic, detect the attack, and trigger AI triage
 
- On the Kali Linux server, executed:
  ```bash
  sudo python3 sc.py
  ```
- Script output confirmed:
  - TShark captured packets on `eth0` for 100 seconds
  - CSV conversion completed
  - Suspicious IP detected above threshold
  - JSON alert generated with a unique SOC alert ID (e.g., `SOC-A3F1B2C4`)
  - Alert sent to Airia API successfully
 
---
 
### Step 6: AI Triage Analysis (Airia Agent)
 
**Objective:** Receive automated SOC analyst report from the AI agent
 
The Airia agent processed the JSON alert and returned a structured triage report including:
 
---
 
### Step 7: Review and Validate Results
 
**Objective:** Confirm end-to-end pipeline is working correctly
 
- Verified the JSON alert file (`alert.json`) was correctly formatted
- Confirmed the Airia API returned HTTP 200 with a valid triage JSON response
- Reviewed the AI-generated risk score and MITRE mapping for accuracy
- Tested edge cases: traffic below threshold (no alert generated), multiple IPs
 
 
---
 
## Setup & Usage
 
### Prerequisites
 
- Python 3.x
- TShark: `sudo apt install tshark`
- Airia.ai account with a deployed SOC agent

 
### Run
 
```bash
sudo python3 sc.py
```

 
---
 
## SOC Playbook Summary
 
The AI agent was trained using a structured SOC playbook that enforces:
 
| Section | Description |
|--------|-------------|
| Input Validation | Confirms required JSON fields before analysis |
| Threat Classification | Maps activity to defined threat categories |
| Risk Scoring (0–100) | Calculates score based on packet count, time window, target |
| MITRE ATT&CK Mapping | Links detected behaviour to known adversary techniques |
| Action Plan | Provides Tier 1 analyst response steps matched to risk level |
| Escalation Logic | Auto-escalates to Tier 2 if risk score ≥ 80 |
| Executive Summary | Generates a 2–3 sentence plain-language business impact summary |
 
---
 
## References
 
- [Airia.ai Documentation](https://airia.ai)
- [TShark / Wireshark Docs](https://www.wireshark.org/docs/man-pages/tshark.html)
- [MITRE ATT&CK Framework](https://attack.mitre.org/)
- [Python Requests Library](https://docs.python-requests.org/)
- Project inspired by: [The Social Dork — AI SOC Analyst Home Lab](https://youtube.com)
 
---
 
## ⚠️ Disclaimer
 
This project is built **strictly for educational purposes**. All testing was conducted in a controlled, isolated virtual lab environment. Do not use these techniques on networks or systems you do not own or have explicit permission to test.
 
---
 
## Author
 
**Keyur Dobariya**  
Cybersecurity Enthusiast | Aspiring SOC Analyst

 
*This project demonstrates hands-on cybersecurity skills including network monitoring, Python automation, AI integration, and SOC triage workflows. Built as part of continuous learning and professional development in the cybersecurity field.*
 
---
 
**Project Status:** ✅ Completed | 🟢 Operational  
**Last Updated:** March 2026
