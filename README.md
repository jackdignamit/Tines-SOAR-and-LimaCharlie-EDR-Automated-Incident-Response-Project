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

<img width="990" height="1063" alt="Screenshot 2025-11-08 143039" src="https://github.com/user-attachments/assets/2870de3a-b02e-40d6-841d-dc285ca35b26" />

- - - 

# Tools & Technologies Used
| Tool & Technology | Purpose | Links |
|------------------|-----------------|-----------------|
| LimaCharlie       | Cloud EDR to detect malicious activity, enroll endpoints, and perform isolation | [https://www.limacharlie.io](https://www.limacharlie.io) |
| Tines             | SOAR platform to automate alerts, decision workflows, and endpoint isolation | [https://tines.com](https://tines.com) |
| Slack             | Real-time alerting with dedicated channels | [https://slack.com](https://slack.com) |
| LaZagne           | Open-source password retrieval tool used to simulate malicious activity | [https://github.com/AlessandroZ/LaZagne](https://github.com/AlessandroZ/LaZagne) |
| Windows 10 VM        | Endpoint environment to run LaZagne and test EDR detection | [https://www.vultr.com/](https://www.vultr.com/) |
| Draw.io           | Visualizing architecture and workflow diagrams | [https://www.draw.io](https://www.draw.io) |

- - - 

# Architecture Diagram

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

- - - 
# 🔢 Step-by-Step Walkthrough 🔢
## 1️⃣ Setup a Host Virtual Machine
To start, create a virtual machine, either using your own VM or a cloud-based service such as [Vultr](https://www.vultr.com/).  
For my lab, I used a **Vultr VM** named ```mydfir-soar-edr``` with the following settings:
- Cloud Compute (Shared CPU)
- Windows 10 (2022) Standard x64
- 55 GB SSD, 1 vCPU, 2 GB Memory, 2 TB bandwidth ($24/month)
- Disabled Auto backups & IPv6 address (only need IPv4)

- - - 

## 2️⃣ Install LimaCharlie on Windows VM
1. Login to LimaCharlie, make a new organization, and then **create an installation key** under the "Installation Keys" tab.
   - *Installation keys are unique codes using during sensor deployment to link a sensor to a specific organization and assign it optional tags*  
<img width="2107" height="320" alt="Screenshot 2025-11-08 114045" src="https://github.com/user-attachments/assets/d81f89cf-f9e9-45a9-b604-ff139026b26a" />
  
2. In the **Installation Keys tab**, select **Sensor downloads** and choose the appropiate download under **"EDR"**. In my case, I am choosing the Windows 64 bit download to use on my VM.
   
3. **Copy your installation key**. On the VM, open PowerShell as an administrator and execute the download (```./lc_sensor.exe```) with ***```-i [INSTALLATION KEY]```***. It should state the agent was installed successfully.
   
4. If you view your services on the VM, you can verify that LimaCharlie was installed properly.
<img width="654" height="85" alt="Screenshot 2025-11-08 115451" src="https://github.com/user-attachments/assets/c7a03cbc-3dd5-437b-a42c-cf7813dbc743" />

  
5. On the LimaCharlie dashboard, you can see that your virtual machine is now a sensor. If you click on its name you can view its isolation status, IP address, sensor ID, installer ID, and more.
<img width="2077" height="570" alt="Screenshot 2025-11-08 115605" src="https://github.com/user-attachments/assets/7c550524-f0fb-47f4-b885-c55e6f9d3f57" />
  
**Your virtual machine is now a configured sensor on LimaCharlie! It will now begin to detect events on the machine.**

- - - 

## 3️⃣ Install 'The LaZagne Project' from AlessandroZ's GitHub
LaZagne is an open-source application that is used to retrieve passwords stored on a local computer.  
It is a form of malware, but since we are using a VM with nothing personable on it, it will just be used to flag our detection rule on LimaCharlie.
- **Review Source Code / Download here: [https://github.com/AlessandroZ/LaZagne](https://github.com/AlessandroZ/LaZagne)**
  
1. If you attempt to install LaZagne, it will be blocked by Windows Security. **Disable Windows Security** by turning off **real-time protection** under **Virus & Threat Protection** settings on YOUR VM.
   
2. Install LaZagne from the link listed above. If prompted to keep the file, select "keep anyway".
- *If it still fails to download, you can add exclusions for the Downloads folder or your entire drive if need be on Windows Defender.*
   
3. In the downloads folder, shift and right click to open an administrator PowerShell window in your directory.
   
4. Execute ```.\LaZagne.exe``` so LimaCharlie can detect it:
<img width="1508" height="831" alt="Screenshot 2025-11-08 120656" src="https://github.com/user-attachments/assets/3151fc58-9c17-4462-9e43-035e9a27438e" />

5. You can view LimaCharlie detections under the Timeline tab:
<img width="2065" height="350" alt="Screenshot 2025-11-08 120921" src="https://github.com/user-attachments/assets/352aae3d-25d0-4382-b8c7-7c48f71e24be" />

- - - 

## 4️⃣ Create Detection & Response Rule for LaZagne.exe
In LimaCharlie, we want to create a **detection and response rule** to **automate the process of identifying threats and taking immediate action to mitigate them**. This rule will also be utilized for our webhooks in our Tine's SOAR later on.

1. To create a D&R rule, open the main dashboard for LimaCharlie and select the **automation tab**. Then click **D&R rules**.

2. Click on the "new rule" button in the top right corner. Under your new rule, you can either create your own LaZagne detection rule from scratch or utilize a template of a previously existing rule and adjust the values.
But for convenience, I will list my rule for copy and pasting:

<img width="691" height="769" alt="Screenshot 2025-11-08 123127" src="https://github.com/user-attachments/assets/7629a606-f5e6-4f37-b9af-452a3747e203" />

```
events:
  - NEW_PROCESS
  - EXISTING_PROCESS
op: and
rules:
  - op: is windows
  - op: or
    rules:
    - case sensitive: false
      op: ends with
      path: event/FILE_PATH
      value: LaZagne.exe
    - case sensitive: false
      op: contains
      path: event/COMMAND_LINE
      value: LaZagne
    - case sensitive: false
      op: is
      path: event/HASH
      value: '3cc5ee93a9ba1fc57389705283b760c8bd61f35e9398bbfa3210e2becf6d4b05'
    - case sensitive: false
      op: ends with
      path: event/COMMAND_LINE
      value: all

- action: report
  metadata:
    author: Jack Dignam
    description: Detects Lazagne Usage (SOAR-EDR Tool)
    falsepositives:
    - ToTheMoon
    level: high
    tags:
    - attack.credential_access
  name: JD - HackTool - Lazagne
```
You can press "test event" at the bottom of the page to see if the D&R rule is functioning properly.

### **A quick summary of what this rule does:**  
This rule detects LaZagne and identifies its process name, command-line arguments, binary hash, LaZagne keywords (such as all), catches existing and new processes, and limits to only Windows events. It is not case sensitive and will detect anything containing the characters 'lazagne.exe' regardless of capitalization.

<img width="2452" height="517" alt="Screenshot 2025-11-08 123509" src="https://github.com/user-attachments/assets/eb8fa090-1795-41c2-91ea-921dcf842708" />

Now if you rerun LaZagne on your virtual machine, the LimaCharlie sensor will detect it and filter it using the newly-created D&R rule. You can view the detection under the detections tab on the main LimaCharlie dashboard.

- - - 

## 5️⃣ Establish a link between Tines and LimaCharlie
Tines allows teams to easily build, automate, and orchestrate security processes without heavy coding. It utilizes event-driven "stories" to connect tools and websites to make enriched alerts and decisions using user-friendly interfaces.
1. To start, create a [Tines](https://tines.com) account.
   
2. In Tines, lets establish a link between Tines and LimaCharlie to funnel alerts into our workflow. Drag a webhook from the left-hand side to the center of the screen.
<img width="468" height="192" alt="image" src="https://github.com/user-attachments/assets/34264e8b-e59b-4ea6-9778-3339c6af45d3" />

3. In the webhook settings, **copy the webhook URL** and navigate back to LimaCharlie. In the LimaCharlie dashboard, under the outputs tab, add a new ***detection*** output. Select Tines and paste in the webhook URL under "destination host".
   - Regenerate your event in your VM if no samples are listed  

4. If you've set up the link between Tines and LimaCharlie properly, you should be seeing the newly created events in the Tines webhook.
<img width="1552" height="552" alt="image" src="https://github.com/user-attachments/assets/2ae69499-ab87-4ae5-aabf-479c3ff4c27d" />
<img width="842" height="511" alt="Screenshot 2025-11-08 124543" src="https://github.com/user-attachments/assets/305c3fed-e980-45e8-9dd7-43cbb71d3c92" />

- - - 

## 6️⃣ Setup Slack in Tines
In SOAR workflows, Slack is often used as a fast, centralized communication channel for security analysts to receive alerts, collaborate, and trigger automated actions without leaving the chat environment. Let's set it up for our detection workflow!
1. To start, create a [Slack](https://slack.com) account.

2. In Slack, create a new workspace and an "alerts" channel. We will setup Tines to utilize this channel for security alerts.
<img width="423" height="586" alt="image" src="https://github.com/user-attachments/assets/34c3f68b-0aed-42c6-b369-3e4e2e5a82a6" />

3. Then install the Tines app on slack by pressing the "More" button and navigating to and installing the Tines app.
   - On Tines, add a new credential for Slack

4. On the Tine's storypage, add a Slack template. To sync the template with the alerts channel on Slack, copy the channel's channel ID from there and paste it into the Tine's template.
   
5. Now that the link is setup, we need to format messages for incoming security incidents including all necessary details. This ensures that security analysts can respond quickly. For my example, I used this formatting:

<img width="1663" height="373" alt="Screenshot 2025-11-08 132129" src="https://github.com/user-attachments/assets/9312766b-feb4-49d7-a580-308f59a883fc" />

```
Title: <<retrieve_detections.body.cat>>
Time: <<retrieve_detections.body.detect.routing.event_time>>
Computer: <<retrieve_detections.body.detect.routing.hostname>>
Source IP: <<retrieve_detections.body.detect.routing.int_ip>>
Username: <<retrieve_detections.body.detect.event.PARENT.USER_NAME>>
File Path: <<retrieve_detections.body.detect.event.FILE_PATH>>
Command Line: <<retrieve_detections.body.detect.event.COMMAND_LINE>>
Sensor ID: <<retrieve_detections.body.detect.routing.sid>>
Detection Link: <<retrieve_detections.body.link>>
```

After inputting my message and attaching the webhook to Slack, it achieves this output in the alerts channel we setup earlier:

<img width="210" height="330" alt="image" src="https://github.com/user-attachments/assets/adafde8a-3379-4af2-b7cc-754a06cc79f0" />

<img width="1952" height="1117" alt="Screenshot 2025-11-08 131806" src="https://github.com/user-attachments/assets/d909afe4-fb1e-4de7-ba49-5a1b6a7083b8" />

- - - 

## 7️⃣ Add Emails to Tines Workflow
Email notifications in a SOAR workflow is essential to keep the right people informed at the right time without requiring them to constantly monitor dashboards or tools.

1. To add email notifications to our Tines SOAR, drag the **"send email"** icon to the storyboard and **connect it to the webhook**.
   
2. Configure email addresses, sender names, subjects, and the email body using HTML formatting.
   
```
Title: <<retrieve_detections.body.cat>>
<br> Time: <<retrieve_detections.body.detect.routing.event_time>>
<br> Computer: <<retrieve_detections.body.detect.routing.hostname>>
<br> Source IP: <<retrieve_detections.body.detect.routing.int_ip>>
<br> Username: <<retrieve_detections.body.detect.event.PARENT.USER_NAME>>
<br> File Path: <<retrieve_detections.body.detect.event.FILE_PATH>>
<br> Command Line: <<retrieve_detections.body.detect.event.COMMAND_LINE>>
<br> Sensor ID: <<retrieve_detections.body.detect.routing.sid>>
<br>
<br> Detection Link: <<retrieve_detections.body.link>>
```

<img width="1667" height="508" alt="Screenshot 2025-11-08 132220" src="https://github.com/user-attachments/assets/fa3fff35-b971-4a5d-bda1-c2ddd9a84a33" />

- - - 

## 8️⃣ Setup Isolation Response webpage in Tines
The last section for this project is to create a webpage that offers to isolate the compromised machine.

1. In Tines storyboard, drag a "user prompt" to the storyboard.

- - - 

# Key Skills Demonstrated

- - - 

# Conclusion
This project demonstrates how to utilize LimaCharlie as an EDR platform in conjunction with Tine's flexible playbooks to detect malware and automate notifications to security teams. This automated workflow reduces the response time, minimzes human error, and strengthens the overall security posture. Beyond the technical proof-of-concept, this lab demonstrates the real-world value of utilizing SOARs and EDR platforms. They free analysts from repetitive tasks so they can focus on more important tasks.


