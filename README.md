# SOAR-EDR

## Objective

The SOAR-EDR Project objective is to automate the response by using tines (SOAR) and the detection tools are LimaCharlie (EDR). We will create a detection rule as well as implementing playbooks in this project. 

### Skills Learned

- Understanding of LimaCharlie EDR.
- Proficiency in analyzing and interpreting network logs.
- Ability to generate and recognize attack signatures and patterns.
- Enhanced knowledge of network protocols and security vulnerabilities.
- Development of critical thinking and problem-solving skills in cybersecurity.

### Tools Used

- LimaCharlie for Event Detection
- Tines for SOAR Playbook/Story
- Slack and Email for detection messaging 

## Steps:
## PART1: DIAGRAM
This Diagram shows the workflow of our project. From LimaCharlie that send the detection to Tines, then Tines send a message with details in Slack and Email as well as Tines will gives a user a prompt if they want to isolate it or not. If Yes, LimaCharlie will isolate the workstation automatically and sends a message to slack that it is isolated. If No, It will send message to a slack that it is not isolated and need to investigate. 

*Ref 1: SOAR-EDR Playbook Workflow*
![SOAR-EDR Project](https://github.com/user-attachments/assets/3a35dd79-cad8-4cc8-bd33-bc63cd4325d5)

##
## PART2: INSTALL AND SETUP LIMACHARLIE AND CONFIRM EVENTS
In this step, we will be needed to login in LimaCharlie and have 1 windows machine that is connected to internet. 
In my case I use my google account login in LimaCharlie and installed windows 10 on my VirtualBox. 

In LimaCharlie I created a organization called ALOBA (Just for some reason :D). Inside LimaCharlie also I created an Installation key named ALOBA-SOAR-EDR-Project. As you can see below, my organization is selected and you can see the newly created installation key. I remove the other installation key to make it cleaner. 

*Ref 2.1*
![Ref 2 1](https://github.com/user-attachments/assets/45d68bcd-7099-4cec-b553-9ab834fea84a)

To install LimaCharlie agent/sensor, we need to scroll down in the installation keys section to download the sensor and run it in the host machine. In my case, I just copy the link address of Windows 64 bit since I'm running that system type and paste it in my host browser to download the sensor. 

*Ref 2.2*
![Ref 2 2](https://github.com/user-attachments/assets/5541d212-1673-4d2b-be95-439995567398)






