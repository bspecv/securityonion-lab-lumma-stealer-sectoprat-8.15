
# Security Onion Lab – Lumma Stealer + SecTopRAT

This repo documents my analysis of a malicious PCAP imported into **Security Onion 2**.  
Using Suricata + Zeek, I reconstructed a multi-stage malware infection chain involving **Lumma Stealer** and **SecTopRAT**.

---

## 📌 Incident Timeline

1. **DNS Lookup**  
   - Host `10.8.15.176` → `vishneviyjazz[.]ru`  
   - Suricata Alert: *ET MALWARE Win32/Lumma Stealer CnC Domain in DNS Lookup*

2. **TLS Beaconing**  
   - Same host initiated encrypted comms with `vishneviyjazz[.]ru`  
   - Suricata Alert: *ET MALWARE Lumma Stealer Domain in TLS SNI*

3. **HTTP GET Flood**  
   - 34+ suspicious GETs flagged as **Arechclient2 / SecTopRAT**  
   - Indicates RAT backdoor activity.

---

## 🔎 Key Indicators
- Domain: `vishneviyjazz[.]ru`  
- Historical IP (Zeek DNS log): `185.95.159.55`  
- Host: `10.8.15.176`  

📌 *Lesson learned*: **Threat actors are persistant, there's much more than a single attack**
                     **WHOIS or a DNS lookup today may not match what the domain resolved to during the incident**
                     
🔎 How to Detect Lumma Activity

Detection relies on a mix of network IDS and endpoint telemetry:

DNS Monitoring

Suricata/Zeek rules trigger when a host resolves a known Lumma C2 domain.

Example alert:

ET MALWARE Win32/Lumma Stealer Related CnC Domain in DNS Lookup


TLS SNI Inspection

Even if the traffic is encrypted, the TLS Server Name Indication (SNI) often exposes the domain (vishneviyjazz.ru in this case).

Suricata raises an alert when a client includes this domain in the TLS handshake.

Wireshark can also display the SNI under the TLS handshake details. (Insert your screenshot here →)

![Wireshark SNI Capture](images/wireshark-sni-example.png)


Endpoint Behavior

Watch for suspicious process chains (PowerShell, MSIHTA) or access to browser/crypto files.

EDR tools (Sysmon, CrowdStrike, etc.) can flag unusual file access or network beacons.

Emerging Threats (ET) Rulesets

Keep Suricata ET signatures up-to-date; Lumma domains rotate frequently.

Rules map to MITRE ATT&CK: TA0011 (C2), T1071 (Application Layer Protocol).                     

---

## 📊 Images

<table>
<tr>
  <td>
    <a href="images/timeline.png" target="_blank">
      <img src="images/timeline.png" width="420" alt="Incident Timeline">
    </a>
    <p align="center"><em>Incident Timeline</em></p>
  </td>
  <td>
    <a href="images/so-dashboard-overview.png" target="_blank">
      <img src="images/so-dashboard-overview.png" width="420" alt="Security Onion Dashboard">
    </a>
    <p align="center"><em>Security Onion Dashboard</em></p>
  </td>
</tr>
<tr>
  <td>
    <a href="images/so-suricata-alerts.png" target="_blank">
      <img src="images/so-suricata-alerts.png" width="420" alt="Suricata Alerts (Lumma Stealer & SecTopRAT)">
    </a>
    <p align="center"><em>Suricata Alerts (Lumma Stealer &amp; SecTopRAT)</em></p>
  </td>
  <td>
    <a href="images/so-hunt-results.png" target="_blank">
      <img src="images/so-hunt-results.png" width="420" alt="Hunt Results (Event Timeline)">
    </a>
    <p align="center"><em>Hunt Results (Event Timeline)</em></p>
  </td>
</tr>
<tr>
  <td colspan="2" align="center">
    <a href="images/domain-whois-vishneviyjazz.png" target="_blank">
      <img src="images/domain-whois-vishneviyjazz.png" width="520" alt="WHOIS Lookup (vishneviyjazz.ru)">
    </a>
    <p><em>WHOIS Lookup (vishneviyjazz.ru)</em></p>
  </td>
</tr>
</table>

---

## 🛠️ Tools Used

- Security Onion 2  
- Suricata (IDS alerts)  
- Zeek (DNS, TLS, HTTP logs)  
- Hunt App  

---

## ✅ Takeaways

- A single PCAP can reveal a **full attack chain**.  
- Suricata + Zeek = fast detection + validation.  
- Great exercise for SOC / IR skill building.  

---

*PCAP source: [malware-traffic-analysis.net](https://www.malware-traffic-analysis.net/)*

## 📊 Evidence Gallery

### Incident Timeline
![Incident Timeline](images/timeline.png)

### Security Onion Dashboard
![Security Onion Dashboard](images/so-dashboard-overview.png)

### Suricata Alerts (Lumma Stealer & SecTopRAT)
![Suricata Alerts](images/so-suricata-alerts.png)

### Hunt Results (Event Timeline)
![Hunt Results](images/so-hunt-results.png)

### WHOIS Lookup (vishneviyjazz.ru)
![WHOIS Lookup](images/domain-whois-vishneviyjazz.png)














    
