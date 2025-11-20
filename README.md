# (WIP) Tines SOAR + LimaCharlie EDR Automated Incident Response Project
*Completed: November 8, 2025*

**This project showcases the design, configuration, and automation of **Tine's** SOAR workflow integrated with an EDR platform called **LimaCharlie**. The goal was to build an end‑to‑end incident response pipeline that detects suspicious activity, performs automated triage, enriches event data, and initiates coordinated response actions.**

Tutorial by [MyDFIR](https://www.youtube.com/@MyDFIR).  
All implementation, exploration, and documentation performed independently as part of my cybersecurity learning journey.

- - - 
# Project Overview
This is a hands-on lab environment which utilizes **Tines** as a *security orchestration, automation, and response (SOAR)* plaform with **LimaCharlie** as an *endpoint detection & response (EDR)* tool to simulate and automate incident response.
A malicious program (LaZagne.exe) runs on a hosted virtual machine, which will flag **LimaCharlie** to detect it and send it to our SOAR. 
Then, our **SOAR (Tines)** triggers automated actions such as alerting Slack channels, sending emails containing analytics, and a website to optionally isolate the infected machine from the network.

## Objectives
1. **Detect malicious behavior:**  
   Use LimaCharlie to detect when LaZagne.exe executes on a Windows VM by using a Detect & Response Rule. 
2. **Automate response:**  
   Once detected, send alert data to a SOAR (Tines) to automate the next steps.  
3. **Notify stakeholders**  
   Alert Stakeholders via Slack channels and email.
4. **Prompt a user using Times**  
   Prompt a user (such as an analyst) to decide whether to isolate the compromised host using a Time's webpage.
5. **Isolate the endpoint**  
If the decision is to isolate the host, LimaCharlie will be instructed to isolate the sensor to prevent further malicious activity. If not, it will remain as whitelisted on the network.

# Tools & Technologies Used
| Tool & Technology | Purpose | Links |
|------------------|-----------------|-----------------|
| LimaCharlie       | Cloud EDR to detect malicious activity, enroll endpoints, and perform isolation | [https://www.limacharlie.io](https://www.limacharlie.io) |
| Tines             | SOAR platform to automate alerts, decision workflows, and endpoint isolation | [https://tines.com](https://tines.com) |
| Slack             | Real-time alerting with dedicated channels | [https://slack.com](https://slack.com) |
| LaZagne           | Open-source password retrieval tool used to simulate malicious activity | [https://github.com/AlessandroZ/LaZagne](https://github.com/AlessandroZ/LaZagne) |
| Windows VM        | Endpoint environment to run LaZagne and test EDR detection | [https://www.vultr.com/](https://www.vultr.com/) |
| Draw.io           | Visualizing architecture and workflow diagrams | [https://www.draw.io](https://www.draw.io) |

- - - 
# Architecture Diagram

- - - 
# Step-by-Step Walkthrough
### 1️⃣ Setup a Host Virtual Machine
To start, create a virtual machine, either using your own VM or a cloud-based service such as [Vultr](https://www.vultr.com/).  
In my lab walkthrough, I used a **Vultr VM** named **mydfir-soar-edr** with the following settings:
- Cloud Compute (Shared CPU)
- Windows 10 2022 Standard x64
- 55 GB SSD, 1 vCPU, 2 GB Memory, 2 TB bandwidth ($24/month)
- Disabled Auto backups & IPv6 address (only need IPv4)

- - - 
### 2️⃣ Install LimaCharlie on Windows VM
1. Login to LimaCharlie, make a new organization, and then **create an installation key** under the "Installation Keys" tab.
   - *Installation keys are unique codes using during sensor deployment to link a sensor to a specific organization and assign it optional tags*  
<img width="2107" height="320" alt="Screenshot 2025-11-08 114045" src="https://github.com/user-attachments/assets/d81f89cf-f9e9-45a9-b604-ff139026b26a" />
  
2. In the **Installation Keys tab**, select **Sensor downloads** and choose the appropiate download under **"EDR"**. In my case, I am choosing the Windows 64 bit download to use on my VM.
3. **Copy your installation key**. On the VM, open PowerShell as an administrator and execute the download (./lc_sensor.exe) with ***-i [INSTALLATION KEY]***. It should state the agent was installed successfully.
4. If you view your services on the VM, you can verify that LimaCharlie was installed properly.
<img width="654" height="85" alt="Screenshot 2025-11-08 115451" src="https://github.com/user-attachments/assets/c7a03cbc-3dd5-437b-a42c-cf7813dbc743" />
  
5. On the LimaCharlie dashboard, you can see that your virtual machine is now a sensor. If you click on its name you can view its isolation status, IP address, sensor ID, installer ID, and more.
<img width="2077" height="570" alt="Screenshot 2025-11-08 115605" src="https://github.com/user-attachments/assets/7c550524-f0fb-47f4-b885-c55e6f9d3f57" />
  
**Your virtual machine is now a configured sensor on LimaCharlie! It will now begin to detect events on the machine.**

- - - 
### 3️⃣ Install 'The LaZagne Project' from AlessandroZ's GitHub
LaZagne is an open-source application that is used to retrieve passwords stored on a local computer.  
It is a form of malware, but since we are using a VM with nothing personable on it, it will just be used to flag our detection rule on LimaCharlie.
- **Review Source Code / Download here: [https://github.com/AlessandroZ/LaZagne](https://github.com/AlessandroZ/LaZagne)**
  
1. filler

- - - 
### 4️⃣ Create Detection & Response Rule for LaZagne.exe

- - - 
### 5️⃣ Setup Slack in Tines (SOAR)

- - - 
### 6️⃣ Add Emails to Tines workflow

- - - 
### 7️⃣ Setup Isolation Response webpage in Tines

- - - 
# Key Skills Demonstrated

- - - 
# Conclusion
This project demonstrates how to utilize LimaCharlie as an EDR platform in conjunction with Tine's flexible playbooks to detect malware and automate notifications to security teams. This automated workflow reduces the response time, minimzes human error, and strengthens the overall security posture. Beyond the technical proof-of-concept, this lab demonstrates the real-world value of utilizing SOARs and EDR platforms. They free analysts from repetitive tasks so they can focus on more important tasks.


