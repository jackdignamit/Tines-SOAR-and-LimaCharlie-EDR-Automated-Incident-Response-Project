# (WIP) Tines SOAR + LimaCharlie EDR Automated Incident Response Project
*Completed: November 8, 2025*

**This project showcases the design, configuration, and automation of **Tine's** SOAR workflow integrated with an EDR platform called **LimaCharlie**. The goal was to build an end‑to‑end incident response pipeline that detects suspicious activity, performs automated triage, enriches event data, and initiates coordinated response actions.**

Tutorial by [MyDFIR](https://www.youtube.com/@MyDFIR).  
All implementation, exploration, and documentation performed independently as part of my cybersecurity learning journey.

- - - 
# Project Overview
This is a hands-on lab environment which utilizes **Tines** as a *security orchestration, automation, and response (SOAR)* plaform with **LimaCharlie** as an *endpoint detection & response (EDR)* tool to simulate and automate incident response.
A malicious program (LaZagne.exe) runs on a hosted virtual machine, which will flag **LimaCharlie** to detect it and send it to our SOAR. 
Then, our **SOAR (Tines)** triggers automated actions such as alerting Slack channels, sending emails containing analytics, and a website to optionally isolate the infected machine.

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
If the decision is to isolate the host, LimaCharlie will be instructed to isolate the sensor to prevent further malicious activity. If not, it will remain online.

## Tools & Technologies Used
| Tool / Technology | Purpose / Use Case | Links / References |
|------------------|-----------------|-----------------|
| LimaCharlie       | Cloud EDR to detect malicious activity, enroll endpoints, and perform isolation | [https://www.limacharlie.io](https://www.limacharlie.io) |
| Tines             | SOAR platform to automate alerts, decision workflows, and endpoint isolation | [https://tines.com](https://tines.com) |
| Slack             | Real-time alerting with dedicated channels | [https://slack.com](https://slack.com) |
| LaZagne           | Open-source password retrieval tool used to simulate malicious activity | [https://github.com/AlessandroZ/LaZagne](https://github.com/AlessandroZ/LaZagne) |
| Windows VM        | Endpoint environment to run LaZagne and test EDR detection | [https://www.vultr.com/](https://www.vultr.com/) |
| Draw.io           | Visualizing architecture and workflow diagrams | [https://www.draw.io](https://www.draw.io) |

- - - 
## Architecture Diagram

- - - 
## Step-by-Step Walkthrough

- - - 
## Detection & Response Scenario

- - - 
## Key Skills Demonstrated

- - - 
## Reflection
