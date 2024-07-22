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


After Downloading the sensor, Just run it in PowerShell by navigating to it's path and run the downloaded installer. 

*Ref 2.3*

![Ref 2 3](https://github.com/user-attachments/assets/dae425c7-d3cd-42e7-95e7-b306eb74860b)



After running you can see the message in the PowerShell "Agent Installed Successfully!" (Ref 2.3). Check also the services.msc and find LimaCharlie if running. 

*Ref 2.4*

![Ref 2 4](https://github.com/user-attachments/assets/c7f28bc7-802a-4fbe-b37a-17e97fd4bd1b)


Now that the agent/sensor is installed. The host machine should be appeared in Sensors list in LimaCharlie Server


*Ref 2.5*

![Ref 2 5](https://github.com/user-attachments/assets/1d34bc4f-482a-46a1-aa02-3a0cf47ddee9)


Now You can see the details of Host machine when you click the host name in sensors list in LimaCharlie

*Ref 2.6*

![Ref 2 6](https://github.com/user-attachments/assets/c0e78cea-7f45-433e-a0ad-53918b684c02)


There are a lot of information that we can leverage for investigation in LimaCharlie. You can see below all the section, and each section has a content that you cant get and use it in investigation. 

*Ref 2.6*

![Ref 2 7](https://github.com/user-attachments/assets/134682b0-49c2-4c03-89ab-33f1bd63a50e)

#
## PART 3: GENERATE TELEMETRY AND CREATE DETECTION AND RESPONSE RULES
Let's talk first about lazagne which is the telemetry we will be using. Lazagne is used to retrieve lots of passwords stored on a local computer. This tool has been developed for the purpose of finding the passwords for the most commonly-used software. 

Now our first step in this part is to download lazagne in this link https://github.com/AlessandroZ/LaZagne/releases/. Make sure to turn off windows security to be able to run this tool. 

I used powershell to run lazagne, and after running the output should be like this:

Ref 3.1

![Ref 3 1](https://github.com/user-attachments/assets/34a13e75-2165-48c9-a781-0176c28089ef)


We already run the lazagne, so it should be detected in LimaCharlie. Now head to LimaCharlie, go to sensors list and choose your sensor, go to timeline section and search lazagne to find the events associated with it. We should be interested in the NEW_PROCESS events which we will be a good information when creating a detection rules. 

Ref 3.2
![Ref 3 2](https://github.com/user-attachments/assets/b7716481-6173-40b8-a1a4-e98363696734)



To create Detection rules and response, lets head to LimaCharlie Home -> Choose our organization -> Go to automation and choose D&R Rules. In D&R Rules top right corner, click New RUle.

Ref 3.3
![Ref 3 3](https://github.com/user-attachments/assets/6fe647d1-659a-4d78-8806-21be09d4494d)


When I first see the rule creation, it's very intimidating, but we can create rule based on the other rule that is created, so we don't need to create rule from the scratch. We'll just need to copy and edit the existing rule and make it customize as we want it to function. Let's go back to D&R rules and search for the rules that have similar in the rule that we want to create.

We are creating rule based on the tool called lazagne, which is a password recovery tool. Now we need to find a similar rule that function like a password recovery tool or credential access rule. Now we need to search "credential" for the credential related rule. 

Ref 3.4
![Ref 3 4](https://github.com/user-attachments/assets/438382ea-37f8-45c0-bd1a-28f5fc9972f3)


Now we're interested in windows process creation rule. Why? Like I said before when we search for the lazagne events, this events has a lot of good information that is useful when creating a rule. Take a look at ref 3.2 the LimaCharlie generated an event that is NEW_PROCESS related to lazagne. So we will be creating a detection when lazagne was executed and there was a new process generated. 

Now Back to LimaCharlie, let's click to the rule that we want to the edit and after clicking you can see the "View the content of this rule in the GitHub repository" so let's click on the link.

Ref 3.5
![Ref 3 5](https://github.com/user-attachments/assets/3e366a41-603e-4a05-b756-2b2fc4556aa4)


Now we will be redirecting to GitHub page, click on raw and copy the content. 

Ref 3.6
![Ref 3 6](https://github.com/user-attachments/assets/95fb81eb-bfff-4f73-a9da-9fb54b0b3ca8)


The content will be like this:

Ref 3.7

![Ref 3 7](https://github.com/user-attachments/assets/aba57ff8-babf-4386-973f-1572782b9cfb)


Now we need to edit our newly created rule by clicking the pencil button alongside with it and paste the content. And now we're ready to create our own detection rule. 

Ref 3.8
![Ref 3 8](https://github.com/user-attachments/assets/1c55a089-7015-4347-bc97-02359a8534dc)

To create a custom rules, you have 2 fields, the Detect and the respond. As we can see in the image 3.7 there are Detect and Respond section and we need to paste the Detect script to Detect field and the Respond script to Respond field. We can also see the operators in the right side that we can use later for creation of our rule. 

Ref 3.9

![3 9](https://github.com/user-attachments/assets/afa022d7-f256-49a0-b946-b0797b468164)


Now let's get to rule creation script now. The events specifies you what event you need to trigger the alert? It is NEW_PROCESS event? EXISTING PROCESS event? FILE_TYPE_ACCESSED event? In our case NEW_PROCESS event and EXISTING PROCESS event. Our first operator are and, we can see that in "op:and". 

Now Let's proceed to the rules. Rules is a criteria, you must follow this criteria to trigger an alert. It should be; 
	operator is windows, 
	case sensitive is false 
And follow by another operator which is "ends with" the 
	path: event/FILE_PATH
	value: C:\Users\Client\Download\Lazagne.exe
File path is what is the specific metrics, or it answers in the question, What is basis of our detection? It is File path? It is Hash? or it is IP? then the value is what is the value of file_path? what is the value of hash? what is the value of IP address? (you can see it in Ref 3.2)

In our case we use file path with the value of Lazagne.exe

Now to summarize our Detection. The event type must be either New_Process or Existing_Process AND case sensitive doesn't matter, and must be a windows and the file path must be Lazagne.exe. 

Ref 3.10
![3 10](https://github.com/user-attachments/assets/355e453c-a276-4b33-ada8-348553a8e39c)

In PowerShell, we can run the script ".\lazagne.exe all". Which means, it can extract all the password that is available in the system. To Detect this event we need to create a detection that can describe this action. It will be the detection of the following:

- The event must be New_process or Existing_process
- The file path must be end with "lazagne.exe"
- The command line must be end with "all"
- The command line must contains lazagne
- The hash is equal to lazagne hash. 
