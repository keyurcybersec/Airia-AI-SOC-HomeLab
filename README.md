# AI SOC Analyst Home Lab

An automated Security Operations Center (SOC) triage system built with Python, virtual machines, and AI. Detects suspicious network traffic and uses an AI agent 
to analyze alerts in real time based on a structured SOC playbook.

## Architecture

- **Attacker VM**: Ubuntu (Vmware)
- **Internal Server VM**: Kali Linux (Vmware)
- **AI Agent**: Airia.ai with a custom SOC playbook
- **Detection**: Python + TShark packet capture

## How It Works

1. Python script captures ICMP traffic on the internal server
2. TShark converts the capture to CSV
3. Script analyzes packet counts per source IP
4. If a threshold is exceeded, a JSON alert is generated
5. Alert is sent to the Airia AI agent via API
6. AI agent returns a triage report based on the SOC playbook

## Setup & Usage

### Prerequisites
- Python 3.x
- TShark installed (`sudo apt install tshark`)
- Airia.ai account with a deployed SOC agent

### Installation

git clone https://github.com/keyurcybersec/soc-analyst-lab.git
cd soc-analyst-lab
pip install -r requirements.txt
cp .env.example .env   # Then fill in your API credentials

### Run
sudo python3 soc_capture.py

## Project Structure

soc-analyst-lab/
├── soc_capture.py       # Main automation script
├── soc_playbook.txt     # SOC analyst playbook used in Airia
├── requirements.txt
├── .env.example         # Template for credentials
├── .gitignore
└── README.md

## Disclaimer

This project is for educational purposes only. 
Only run in a controlled lab environment.

## Tutorial

Based on the lab by [The Social Dork](https://youtube.com)
```
