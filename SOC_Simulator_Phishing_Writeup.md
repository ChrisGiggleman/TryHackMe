# 🛡️ TryHackMe SOC Simulator – Introduction to Phishing  
**Author:** Chris Giggleman  
**Date Completed:** 09/03/2025  

---

## 📌 Overview  
This write-up documents my experience completing the **SOC Simulator: Introduction to Phishing** module on TryHackMe.  

This was my first time running the simulator, and my goals were:  
- Learn how to triage alerts in a SIEM environment  
- Follow structured phishing playbooks  
- Practice documenting case reports with clear findings  

For this lab, I used the **Splunk Edition** of the simulator with the following setup:  
- **Splunk SIEM Dashboard** on one monitor  
- **Splunk Enterprise Console** on another  
- **Analyst VM** on a third  

The phishing investigation playbook steps used throughout:  
1. Assign the alert  
2. Triage the alert  
3. Analyze email artifacts (sender reputation, attachments, and links)  
4. Investigate related alerts  
5. Document findings in a case report  

---

## 📂 Case Investigations  

### Case 1 – Medium Severity (Phishing Email)  
| Field | Value |
|-------|-------|
| **Timestamp** | 09/03/2025 18:47:53.433 |
| **Sender** | onboarding@hrconnex.thm |
| **Recipient** | j.garcia@thetrydaily.thm |
| **Subject** | Action Required: Finalize Your Onboarding Profile |
| **Attachment** | None |
| **URL** | `https://hrconnex.thm/onboarding/15400654060/j.garcia` |

**Analysis**  
- Inbound email with no attachment, single URL.  
- URL and IP tested with **TryDetectThis** → **Clean**.  
- Splunk showed two related events → also **Clean**.  

**Result:** ❌ **False Positive**  
- Legitimate onboarding request.  
- No malicious indicators detected.  
<img width="975" height="922" alt="image" src="https://github.com/user-attachments/assets/51864d4d-4b24-462e-96f0-9f9c27cddf6d" />
<img width="975" height="311" alt="image" src="https://github.com/user-attachments/assets/d14df049-090c-4e30-9b5a-17e783ca9e62" />

---

### Case 2 – High Severity (Blacklisted URL)  
| Field | Value |
|-------|-------|
| **Timestamp** | 09/03/2025 18:50:16.433 |
| **Source IP** | 10.20.2.17 |
| **Destination IP** | 67.199.248.11 |
| **URL** | `http://bit.ly/3sHkX3da12340` |
| **Action** | Blocked by firewall |

**Analysis**  
- Destination IP flagged as **malicious**.  
- Splunk confirmed firewall rule blocked the request.  

**Result:** ✅ **True Positive**  
- **Indicators:** Malicious URL + IP.  
- **Remediation:** Notify user; reinforce awareness training.  
<img width="975" height="924" alt="image" src="https://github.com/user-attachments/assets/c7461d27-e8fb-484f-a5df-9279b178e112" />
<img width="975" height="487" alt="image" src="https://github.com/user-attachments/assets/21ed41a2-13fb-40fc-a5a1-5bfc8d6d27f2" />

---

### Case 3 – Medium Severity (Amazon Delivery Scam)  
| Field | Value |
|-------|-------|
| **Timestamp** | 09/03/2025 18:49:02.433 |
| **Sender** | urgents@amazon.biz |
| **Recipient** | h.harris@thetrydaily.thm |
| **Subject** | Your Amazon Package Couldn’t Be Delivered – Action Required |
| **URL** | `http://bit.ly/3sHkX3da12340` |

**Analysis**  
- Phishing email using fake delivery notice.  
- URL flagged as **malicious**.  
- Splunk confirmed firewall blocked connection.  

**Result:** ✅ **True Positive**  
- **Indicators:** Same malicious URL + IP (67.199.248.11).  
- **Remediation:** Add IP to deny list for future prevention.  
<img width="975" height="942" alt="image" src="https://github.com/user-attachments/assets/0030a255-5f4f-433d-a8ad-a85c707c2994" />
<img width="975" height="580" alt="image" src="https://github.com/user-attachments/assets/a335352e-fed5-415c-84e5-c41e0b9d802b" />

---

### Case 4 – Medium Severity (Microsoft Credential Harvesting)  
| Field | Value |
|-------|-------|
| **Timestamp** | 09/03/2025 18:51:20.433 |
| **Sender** | no-reply@m1crosoftsupport.co |
| **Recipient** | c.allen@thetrydaily.thm |
| **Subject** | Unusual Sign-In Activity |
| **URL** | `https://m1crosoftsupport.co/login` |

**Analysis**  
- Typosquatting domain (`m1crosoft` vs `microsoft`).  
- Destination IP (45.148.10.131) flagged **malicious**.  
- Connection **allowed** due to firewall misconfiguration.  

**Result:** ✅ **True Positive**  
- **Indicators:** Malicious domain + IP.  
- **Remediation:** Add to deny list; adjust firewall rules.  
<img width="975" height="701" alt="image" src="https://github.com/user-attachments/assets/c73d1528-0722-46f3-bf43-de7e7debcd90" />
<img width="975" height="950" alt="image" src="https://github.com/user-attachments/assets/d0e432ec-df65-4ab9-9aae-b7a3c773c724" />

---

### Case 5 – Medium Severity (Duplicate Onboarding Email)  
| Field | Value |
|-------|-------|
| **Timestamp** | 09/03/2025 18:51:48.433 |
| **Sender** | onboarding@hrconnex.thm |
| **Recipient** | j.garcia@thetrydaily.thm |
| **Subject** | Action Required: Finalize Your Onboarding Profile |
| **URL** | `https://hrconnex.thm/onboarding/15400654060/j.garcia` |

**Analysis**  
- Identical to Case 1.  
- URL tested and came back **clean**.  
- Splunk logs showed no malicious behavior.  

**Result:** ❌ **False Positive**  
<img width="975" height="935" alt="image" src="https://github.com/user-attachments/assets/51cfe9a8-5351-4209-8df5-aeb1ec31c84c" />
<img width="975" height="275" alt="image" src="https://github.com/user-attachments/assets/43e184dd-c37e-479a-b3f6-35a23292837c" />

---

## 📊 Summary of Cases  

| Case | Severity | Result | Notes |
|------|----------|--------|-------|
| 1 | Medium | False Positive | Clean onboarding email |
| 2 | High | True Positive | Malicious URL blocked |
| 3 | Medium | True Positive | Amazon phishing attempt |
| 4 | Medium | True Positive | Microsoft credential harvest, bypassed firewall |
| 5 | Medium | False Positive | Duplicate onboarding email |

---

## 🔍 Lessons Learned  
- Always include **source and destination IPs** in reports for clarity.  
- Strengthen reporting for **True Positives** with detailed escalation reasoning.  
- Be cautious with **typosquatting domains** (e.g., `m1crosoft`).  
- Firewall misconfigurations can allow malicious traffic — playbooks should include rule validation.  

---

## 📝 Reflection  
- Time to complete was longer than usual since I was documenting step-by-step and learning the Splunk/Analyst VM workflow.  
- Correctly classified all alerts, though report quality can improve with more detailed IOC documentation.  
- Biggest takeaway: Writing high-quality SOC reports is as important as technical analysis.  

---

## 📷 Screenshots (Optional)  
 <img width="975" height="1284" alt="image" src="https://github.com/user-attachments/assets/a88e7792-408a-49cb-a1fb-69dc867edcc4" />
<img width="975" height="1305" alt="image" src="https://github.com/user-attachments/assets/5c219bc1-3c5e-45fb-872c-96121584f448" />
<img width="975" height="1317" alt="image" src="https://github.com/user-attachments/assets/f780aa86-2cb7-4a89-8953-28e64405517d" />
<img width="975" height="900" alt="image" src="https://github.com/user-attachments/assets/459709f4-75ce-4aee-ac86-77375966c3d3" />
<img width="975" height="897" alt="image" src="https://github.com/user-attachments/assets/33fc32e0-c3af-4729-a3dd-a08be9f44e90" />
<img width="975" height="862" alt="image" src="https://github.com/user-attachments/assets/a3a04a2c-4eea-4fb3-886a-d583817303cc" />


---

## ✅ Final Thoughts  
The TryHackMe SOC Simulator is an excellent hands-on exercise for practicing **alert triage, phishing investigation, and case reporting**.  

I recommend it to anyone looking to:  
- Improve SOC analyst skills  
- Gain confidence with phishing playbooks  
- Practice documenting incidents in a structured way  

---  
