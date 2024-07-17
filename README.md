# SOAR-EDR

## Objective

The SOAR-EDR Project objective is to automate the response by using tines (SOAR) and the detection tools are LimaCharlie (EDR). We will create a detection rule as well as implementing playbooks in this project. 

### Skills Learned
[Bullet Points - Remove this afterwards]

- Understanding of LimaCharlie EDR.
- Proficiency in analyzing and interpreting network logs.
- Ability to generate and recognize attack signatures and patterns.
- Enhanced knowledge of network protocols and security vulnerabilities.
- Development of critical thinking and problem-solving skills in cybersecurity.

### Tools Used
[Bullet Points - Remove this afterwards]

- Security Information and Event Management (SIEM) system for log ingestion and analysis.
- Network analysis tools (such as Wireshark) for capturing and examining network traffic.
- Telemetry generation tools to create realistic network traffic and attack scenarios.

## Steps:
## PART1: DIAGRAM
This Diagram shows the workflow of our project. From LimaCharlie that send the detection to Tines, then Tines send a message with details in Slack and Email as well as Tines will gives a user a prompt if they want to isolate it or not. If Yes, LimaCharlie will isolate the workstation automatically and sends a message to slack that it is isolated. If No, It will send message to a slack that it is not isolated and need to investigate. 

*Ref 1: SOAR-EDR Playbook Workflow*
![SOAR-EDR Project](https://github.com/user-attachments/assets/3a35dd79-cad8-4cc8-bd33-bc63cd4325d5)


