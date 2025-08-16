📖 Automated Threat Response with Wazuh + n8n + VirusTotal
🔎 Overview

This project implements a SOAR (Security Orchestration, Automation, and Response) workflow using Wazuh (for detection), n8n (for automation), and VirusTotal (for threat intelligence).
The goal is to automate malicious IP reputation checks and notify the security team immediately if a threat is detected.

⚙️ Architecture & Workflow

Wazuh Detection & Integration

Edited Wazuh configuration file at:

/var/ossec/etc/ossec.conf


Configured Wazuh to send alerts to n8n Webhook using the n8n API endpoint.

Every suspicious event/alert (containing IPs, agent IDs, timestamps, and severity levels) is forwarded to n8n.

n8n Workflow
The automation pipeline in n8n is built as follows:

Webhook Node

Collects logs from Wazuh in real-time.

Forwards each log to the extractor for parsing.

Extractor Node

Extracts important fields:

Agent ID

Source IP

Timestamp

Alert Level

Passes structured data to VirusTotal node.

VirusTotal Node

Queries VirusTotal API with the extracted IP.

Retrieves threat intelligence (positives / detections count).

IF Node (Decision Logic)

Evaluates VirusTotal results.

If malicious score > 30 detections, trigger alerting actions.

Email Node

Sends an email to the manager.

Email includes: Agent ID, IP address, timestamp, alert level, and VirusTotal result.

Slack Node

Sends the same alert information to the security team Slack channel.

Ensures real-time visibility for immediate response.

🔐 Configuration
1. Wazuh → n8n Integration

Inside ossec.conf:

<integration>
  <name>custom-n8n</name>
  <hook_url>https://your-n8n-instance/webhook/wazuh</hook_url>
  <level>3</level>
  <alert_format>json</alert_format>
</integration>


hook_url points to your n8n webhook endpoint.

level ensures only alerts above a certain severity are forwarded.

2. VirusTotal API in n8n

Create an API key on VirusTotal.

Configure it in the VirusTotal node in n8n.

3. Email Notifications

SMTP credentials configured in n8n Email node.

Email subject:

🚨 Malicious IP Detected - [IP Address]


Email body includes: Agent ID, timestamp, alert level, VirusTotal detection score.

4. Slack Notifications

Slack Webhook or Bot Token configured in n8n.

Channel receives structured JSON alert with all details.

📊 Workflow Diagram
Wazuh (ossec.conf)
       │
       ▼
   n8n Webhook
       │
       ▼
   Extractor (Agent ID, IP, Time, Level)
       │
       ▼
   VirusTotal API
       │
       ▼
   IF Node (detections > 30?)
       ├── Yes → Email (Manager) + Slack (Team)
       └── No → Ignore

✅ Impact & Benefits

🔄 Automation: Removes repetitive manual IP lookups.

⚡ Faster Response: Immediate alerts via Email & Slack.

💰 Cost Effective: Fully open-source stack (Wazuh + n8n + VirusTotal).

📈 Scalable: Can be extended to include more threat feeds or automated blocking.

🚀 Future Improvements

Automate firewall blocking for malicious IPs.

Add enrichment from multiple threat intelligence sources.

Implement case management for alert tracking.
