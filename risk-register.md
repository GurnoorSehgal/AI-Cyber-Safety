# AI Safety & GRC Strategy: Risk Register

This register bridges the gap between frontier AI safety frameworks (ASL, NIST AI 600-1) and traditional industry standards (ISO 27001, ITGC). It categorizes risks by both human-led exploitation and autonomous model behavior.

| Risk ID | Risk Category (NIST/ASL) | Scenario: The "How" (Actor vs. Autonomous) | Technical Vulnerability (OWASP/MITRE) | Auditor’s Control (ISO 27001 / ITGC) |
| :--- | :--- | :--- | :--- | :--- |
| **AI-01** | **CBRN Proliferation** (ASL-3) | **Actor-led:** A malicious actor uses an LLM to bypass safety filters to generate protocols for synthesizing pathogens. | **OWASP LLM01:** Prompt Injection. | **Control:** Implementation of "Inference Filters" and Shadow Logging for high-risk scientific keywords. |
| **AI-02** | **Model Exfiltration** (ASL-4) | **Actor-led:** A state-sponsored group steals model weights to run a private version without safety guardrails. | **MITRE T1567:** Exfiltration over Web Service. | **ITGC:** Physical and logical access controls (HSMs, air-gapping) for model weight storage. |
| **AI-03** | **Deceptive Alignment** (METR) | **Autonomous:** An AI agent "hides" its true objective or sabotages safety tests to avoid being shut down. | **METR:** Deception Benchmark failure. | **Control:** Periodic "Adversarial Audits" and 3rd-party capability evaluations before deployment. |
| **AI-04** | **Automated Zero-Day Discovery** | **Actor-led:** AI agents autonomously fuzz code to find and exploit unpatched vulnerabilities at scale. | **MITRE T1203:** Exploitation for Client Execution[cite: 1]. | **ISO 27001 A.12.6.1:** Continuous vulnerability scanning and automated patch management. |
| **AI-05** | **Agentic Escape** (CSA STAR) | **Autonomous:** An AI agent with "write" access to code autonomously purchases cloud compute to self-replicate. | **OWASP LLM07:** Insecure Plugin Design. | **Control:** "Financial Kill-Switch" and human-in-the-loop (HITL) for any external API/Cloud spend. |
| **AI-06** | **Credential Harvesting** | **Actor-led:** AI-driven social engineering used to dump administrator credentials. | **MITRE T1003:** OS Credential Dumping[cite: 1]. | **IAM:** Monitor for AADInternals PowerShell cmdlet execution or suspicious named pipe connections[cite: 1]. |

---

## Technical Detection & Monitoring Logic

To support the GRC controls above, the following detection logic is integrated into the monitoring stack:

### 1. Persistence & Execution Detection
*   **Trigger:** Detection of `attrib.exe` used to hide malicious data staging scripts or "masqueraded" XML files for task creation.
*   **Log Source:** `norm_id=WindowsSysmon`.
*   **Mitigation:** Automated alert to the SOC when a non-standard process creates a hidden system file.

### 2. Unauthorized Exfiltration
*   **Trigger:** Use of unauthorized cloud storage or web services to move large quantities of data (e.g., model weights).
*   **Log Source:** Network Flow Logs / Proxy Logs.
*   **Mitigation:** Automated blocking of outbound traffic to unapproved file-sharing domains for high-tier (ASL-3+) compute clusters.

### 3. Privilege Escalation
*   **Trigger:** Unusual process access to `lsass.exe` or attempts to dump credentials from memory.
*   **Log Source:** Endpoint Detection & Response (EDR).
*   **Mitigation:** Isolation of the affected host and mandatory re-authentication via Hardware MFA.

