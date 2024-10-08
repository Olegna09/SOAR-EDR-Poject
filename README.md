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

Ref 3.11

![3 11](https://github.com/user-attachments/assets/7145257e-7b6b-4ca6-99da-078fbf86f669)

Now we need to make a respond script to respond for the detection. Below is the sample script:

*action: report
  metadata:
    author: GELO
    description: Detects Lazagne (SOAR-EDR)
    falsepositives:
      - Nope
    level: medium
    tags:
      - attack.credentials_access
  name: Lazagne (SOAR-EDR)*


As we can see above, all the details of the respond report is in detailed. Now we can save our rules by clicking save rule.

We can test our rule by scrolling down and in the target event and pasting the event that we get from the lazagne. You can go back to sensors list -> navigate to the sensor -> and click timeline to get the event and paste it in the target rule. 


Ref 3.12

![3 12](https://github.com/user-attachments/assets/0ba93402-bcbd-47f3-a74e-fef29dd5330e)


I'm Kinda stuck testing the scripts because it say's it doesn't match. What I just did is to test it in the host machine by running the lazagne.exe and see if there are detection triggered in LimaCharlie. 

But first of all I clear the detection logs in LimaCharlie to see the output. 

Ref 3.13

![image](https://github.com/user-attachments/assets/fd196e8b-10c9-491c-9779-bed37247e3dd)

Then I run the Lazagne.exe in my host machine and wait if it will triggered an alert to LimaCharlie
And After waiting for around 5 minutes. It works! 

Ref 3.14

![image](https://github.com/user-attachments/assets/c36c4fbf-fd82-4d2c-93c2-c58bd062c322)

You can see the details in the alert like the Author, Filepath, Link and many more. So this basically means our Detection and Reponse creation are working perfectly fine.

Ref 3.15

![image](https://github.com/user-attachments/assets/bb5fabd6-773c-41ba-8e13-ff63d567680f)


## PART 4: SETUP SLACK AND TINES. TEST CONNECTION (LimaCharlie + Tines)

In this part, we will be setting up Slack and Tines.

All we have to do is to sign up or create account in slack or tines. First, we'll go with Slack. I created a workspace named ALOBAH-SOAR-EDR then I create a channel named alerts. I use my gmail account as my email in the slack since I'm using google it is already signed in when I choose my gmail account as a signed up option. 

Ref 4.1

![image](https://github.com/user-attachments/assets/3018b5f8-7e46-4e2f-a258-69cf76a5ebe5)

Next is the Tines. Same as Slack I use my gmail account in sign up option and created company ALOBAH-EDR. In the Tines, in the left side part there are actions we can use to help us create our story or Playbook, We just need to drag it to our story board. There are also templates that we can use. There are also Story Library that we can use when we want to create a story from that Library. 

Ref 4.2
![image](https://github.com/user-attachments/assets/f6da2a4f-4578-48c4-8ecf-59c008f0d780)

Now we need to established a connection between LimaCharlie and Tines so that Tines can determine the detection of LimaCharlie
- First, we need to delete all the items in the story board by clicking the Item and delete

Ref 4.3

![image](https://github.com/user-attachments/assets/4a4b053a-6f27-47dd-bfa0-8a6d705cad79)

- Now we need to drag the Webhook in storyboard and named it Retrieve Detection
- Put a Description Retrieves LimaCharlie Detection as it will function like this
- Copy the Webhook URL

Ref 4.4

![image](https://github.com/user-attachments/assets/30e0912b-e2af-482d-beb9-e448d96572ee)

Now we need to head to LimaCharlie
- Go to your Organization and select Output

Ref 4.5

![image](https://github.com/user-attachments/assets/08786d1d-bfe5-48ca-90e4-fce9a87e488a)

- Inside the output just clien Add Output
- Select Detection since we're interested in Detection

Ref 4.6
![image](https://github.com/user-attachments/assets/9d71ff07-e7a9-4597-83a5-94e24b98445b)

- For the Output Destination Let's choose Tines

Ref 4.7
![image](https://github.com/user-attachments/assets/cd4e623c-1a64-4e58-bcee-8be427f45265)

- For the name let's use 'ALOBA-SOAR-EDR'
- Paste the Webhook URL that we copied in Tines
- And Save Output
  
Ref 4.8
![image](https://github.com/user-attachments/assets/b765bdca-4a23-4a0b-b1f0-8ad6c35ae276)

And that's it! We've just created an output of our detection. 

Ref 4.9
![image](https://github.com/user-attachments/assets/d463ca46-8d5c-4e46-9927-3bc4e7641463)

Now Let's test If this works by running the Lazagne.exe into our host machine.
- After Running we should click refresh samples
- Actually it took me a while to detect the lazagne.exe so I restarted the LimaCharlie and head to detection
- It was in detection logs. 

Ref 4.10
![image](https://github.com/user-attachments/assets/a0efdfe8-aed3-48f9-aaa2-bc1e94a0fccf)

- Now, I head back to output and see the view samples if it captures the our lazagne.exe but it only captures the first 3 script.
- Basically, it is captured in Detection, so it means even if I can't see it in output (or there might be way). I should be seeing it in Tines.
- So I head to Tines to check the events.
- And Guess what? In the last 2 logs, I found it.

Ref 4.11

![image](https://github.com/user-attachments/assets/b01b7647-5385-4059-b576-787a2a71de2e)

We now have account in Slack and Tines. And we also confirmed that the detection in LimaCharlie are showing in Tines.

## PART 5: CREATE A PLAYBOOK. 
### OBJECTIVE: 
### - Send a Slack message and an email containing an information about the detection that LimaCharlie Generated
### - Tines Will prompt a user if they want to Isolate the machine
  ### - If Yes, LimaCharlie then automatically isolate the machine
  ### - If No, Ignore

When we head back to our diagram in ref 1, When an attacker initiates an attack, LimaCharlie will detect it and it will automatically send to Tines and Tines will send an email to Slack. So, we need to have a connection between Tines and Slack. Now Let's Do it
 - Go to slack and click more and automations
   
Ref 5.1

![image](https://github.com/user-attachments/assets/3d645702-eeb5-4303-b488-34a0a59f0dd4)

- Click the apps and search for Tines and click Add
  
Ref 5.2

![image](https://github.com/user-attachments/assets/db48aa60-bce2-463b-b8e1-2d815773e0ab)

- You will be redirecting to another page where you can see the details of a Slack.
- Just click "Add to Slack"
- After adding you will be prompt by a step guide on installing the app
- You need to follow this guide to install the apps in tines
  
Ref 5.3

![image](https://github.com/user-attachments/assets/d70d4c97-e33d-4d03-a529-80c389ff3f84)


Ref 5.4

![image](https://github.com/user-attachments/assets/367cfbab-05f8-450b-9ef6-a63e879edb4b)


Ref 5.5

![image](https://github.com/user-attachments/assets/6067991f-08b4-49f7-bd35-381efc9f9766)

- And When the page prompt just click "Use Tines app in Slack"

Ref 5.6

![image](https://github.com/user-attachments/assets/416d8eef-633c-41c4-82ba-a928b55ce23b)

- Now another page will be prompted which is the Tines requesting permission to Slack. You can review that then click "Allow"

Ref 5.7

![image](https://github.com/user-attachments/assets/926e91ca-5349-481e-b4de-dbce44fbb4b3)

Now We have established a connection to slack. We need to configure Slack now to Tines.
 - Go to Tines
 - Go to Templates and Select Slack

Ref 5.8
![image](https://github.com/user-attachments/assets/c2ba90aa-e9f5-47c9-a8e5-7e827ec18e96)

- Now, search for the action which is "send a message"
- And you need to fill out necessary information in the field

Ref 5.9

![image](https://github.com/user-attachments/assets/e844cc24-d220-4e21-93c2-adedbc902e56)

- The Channel user ID can be found in Slack -> Right click for the #alert channel and view channel detail

Ref 6.0

![image](https://github.com/user-attachments/assets/9924c952-fd1b-4bbb-97bc-9ba385f14a15)

- Copy the Channel ID and Paste in in the Tines
- Create a message and click "Connect to Slack"
- There will be prompt but just close it

Ref 6.1

![image](https://github.com/user-attachments/assets/a8b25ebe-712e-4ce3-8c3a-a454696eca71)

To Test the connectivity of a Tines to Slack, just hit the "Send a message" and click test. We can confirmed that it is working if the Tines send a message to Slack. 

Ref 6.1

![image](https://github.com/user-attachments/assets/02c27017-64e6-4398-a166-d6d82d382c1f)

And now can see that it is working.

Ref 6.2

![image](https://github.com/user-attachments/assets/474d325c-e102-4c01-8c40-f73014720e42)

Now, as we back to our diagram (ref 1). We also need to send an email from Tines. Now let's do it.

- Let's head to our Tines and lets drag the "send email" action to story board.
- Now connect the "Retreive detections webhook" to "send email" action
- And let's fill out the information needed on right side.

Ref 6.3

![image](https://github.com/user-attachments/assets/6f4dc7a2-850c-4ef9-a546-b91160490dea)

Now to Test it, we just need to click the "Send email" action and Test. Check your email if it's working and if it is, then the configuration is successful. 

Ref 6.4

![image](https://github.com/user-attachments/assets/9689ae4a-0c7d-432a-8751-d82698584b28)

We have now connection from Tines to Slack and Email. Now the next thing we need to do is the confirmation prompt. 
- To create a prompt we will be using page. Go to "tools" and select "Page"
- Now in Page in the right side just fill out the information needed
  	- Name: User Prompt
  	- Description: Isolate Computer (Yes/No)
  	- Access Control: Member of this Tines Tenant
  	- And I leave the rest default
  	- Now we need to connect the "Webhook" to "Page"
  	- We can also edit the page name in the bottom right where "Edit Page" located

Ref 6.5

![image](https://github.com/user-attachments/assets/aae68b32-37be-43fd-acb4-08121445c380)

Now we need to include the details of the event to the message and email. We can see the details that we need to send in Ref 1. Now we need to head to Tines, open the events and we can copy all the details that we need in that events. And because we're going to make it a content of a message, we need to add some title in it. It must look like this:

#### Title: <<retrieve_detection.body.cat>>
#### Time: <<retrieve_detection.body.detect.routing.event_time>>
#### Computer: <<retrieve_detection.body.detect.routing.hostname>>
#### Source IP: <<retrieve_detection.body.detect.routing.int_ip>>
#### Username: <<retrieve_detection.body.detect.event.USER_NAME>>
#### File Path: <<retrieve_detection.body.detect.event.FILE_PATH>>
#### Command Line: <<retrieve_detection.body.detect.event.COMMAND_LINE>>
#### Sensor ID: <<retrieve_detection.body.detect.routing.sid>
#### Detection Link: <<retrieve_detection.body.link>>

After doing this, we need to head to the slack in tines and paste this in the "Message" section. However, we can manually add the value by clicking the "+" button on the left bottom side of the message placeholder. 

Ref 6.6

![image](https://github.com/user-attachments/assets/cd79f7e7-8987-4376-8930-dfb26ad549c5)

When we click the "+" button, and click the "value" you will be prompted by data "retrieve detection". When you click the "retrieve detection" and hit enter, there will be another prompt that is the content of the previous data (retrieve detection). The arrow indicates that there are content inside that data

Ref 6.7

![image](https://github.com/user-attachments/assets/cc2ead6a-ccd7-438b-b7bf-9148bf35b3b5)

We can see the overview of that data in Tines. In the Webhook (retrieve detections), click in the events and you can see that under the "retrieve detections" there are 3 value, and under that 3 value, there are also a specific value for that. 

Ref 6.8

![image](https://github.com/user-attachments/assets/36f2723a-6c8b-4fec-96b0-f65bc6ad1784)

But we're not gonna add values in the message, we just copy and paste the events that we did earlier. After copying the events to the message, we need to test it if it is working. Just click test and we'll head back to slack to see if we received the message. 

Ref 6.9

![image](https://github.com/user-attachments/assets/b3b31913-acdb-4147-878c-ec43e1d3b195)

And now, It received the message with the right format that we want. 

Ref 6.10

![image](https://github.com/user-attachments/assets/4c34e08e-9aef-4b9a-a290-0d4fbee5bdcb)

Now, we'll do this also to our Email message. But first we need to make this message HTML format so that we can view it in organize. Head to email and in the 'Body' section, in the top of the message box, click the 'Open HTML Editor'. 

Ref 6.11

![image](https://github.com/user-attachments/assets/74f8054a-1436-4840-86eb-fb925b81bee9)

Add a 'br' or a break tag to make a new line for each. 

![image](https://github.com/user-attachments/assets/6b30f8a4-04e9-4676-9606-b4849a1639a0)

And now, we received the email with the details

Ref 6.12

![image](https://github.com/user-attachments/assets/268508d3-e797-4a6b-9c00-03fa49516b6b)

Now let's go ahead and setup our user prompt. 

Ref 6.13



NOTE:
If you want to check the connection of Tines in Slack, Head to Dashboard

![image](https://github.com/user-attachments/assets/3c087bd9-57b2-457c-9919-616596ea2467)

Dropdown "Your draft" and go to credentials

![image](https://github.com/user-attachments/assets/4c2df2e6-982b-4ca8-be72-d055d382a279)

There must be a slack on the list just like this

![image](https://github.com/user-attachments/assets/1f40ec21-15df-40d2-bd27-4365d51b8dc6)

