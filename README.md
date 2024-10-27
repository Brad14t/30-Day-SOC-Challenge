# Elastic-Home-Lab

# Setup

First I need a cloud enviorment, I use VULTUR due to the free $300.

https://www.vultr.com/

Once account is created.

Im going to start up a VPC (Virtual Private Cloud) to host VM's inside of.

![image](https://github.com/user-attachments/assets/4dde5389-8605-4a7a-ad3d-83ef139d1ce6)

Select “Add VPC 2.0 Network”

![image](https://github.com/user-attachments/assets/c52e9d6d-28fb-4a0b-98e3-5d75e7869d72)

Then seletc "Configure manually" for this IP: 172.31.0.0 Subnet: 24. 

And name new newtwork.

![1](https://github.com/user-attachments/assets/08047e5c-7793-4479-9a8e-66290b686241)

Now to make the VM's

Select "Deploy" in the top right, then "deploy new server"

![1](https://github.com/user-attachments/assets/75c33e1b-f60e-43d8-ac11-48703908a287)

For type the dedicated GPU option is best for this use case.

And location, selecting the same region as VPC.

![1](https://github.com/user-attachments/assets/b99b4b7b-1bb9-4c93-adb2-ecd89f714ba1)

For the OS Ubuntu will be used. 22.04 Ubuntu.

![1](https://github.com/user-attachments/assets/38dbcab6-42d5-47ee-9378-e97b18526bd6)

For the size of the VM, 80 Gigs, 4 CPU's, 16 GB memory wil be enough.

![1](https://github.com/user-attachments/assets/e6eca8f2-8a4f-4869-bf3e-57f7d54de0b5)

Then deselect auto backups and IPv6.

Select Virtual Private Cloud 2.0 

![1](https://github.com/user-attachments/assets/d67d20b4-e69d-4a4c-8324-30475352abb2)

This gave my VPC an IP address of: 172.31.0.3

![1](https://github.com/user-attachments/assets/818a0652-80f0-4ab2-a9f4-354e7e7bf305)

Add name for server Hostname. Then select "Deploy"

After a few minutes the status will change to "Runing" after finishing creating VM.

![1](https://github.com/user-attachments/assets/12e0524e-6f0c-4d14-ab5f-dbf05bf3e686)

Once the machine is running I will SSH direcly to the machine.

To do this, in my personal computer open up the powershell.

I use this command: ssh root@45.32.90.59

ssh = (Secure shell), root = username of VM, IP = will be different for every VM

![1](https://github.com/user-attachments/assets/45a2f93a-f7bd-4818-bfa1-7646388e377e)


![1](https://github.com/user-attachments/assets/0109d720-feaf-4d54-ac4d-e5f4a1453d3d)


Enter password given.

Once you see:

![1](https://github.com/user-attachments/assets/a3222782-52a9-4068-8c6f-0790cbecbf55)

Your inside the VM.

Now inside, it is best prcactive to update repositories.

To do this enter this command: apt-get update && apt-get upgrade -y

What this command is doing is getting all the update AND upgrade packages then if they are there, install them and dont ask to upgrade the packages.

Once you see this all installation has completted:

![1](https://github.com/user-attachments/assets/9e920370-6d23-4a33-9889-e2ab886087b2)

Now I want to install Elastic on the VM

Go to this link: https://www.elastic.co/downloads/elasticsearch

Choose deb x86_64 as the platform. 

Hover over the big blue download button, right click and select "copy link address"

![1](https://github.com/user-attachments/assets/89befe32-ddaf-4e3d-bb59-28402f282e1f)

Back inside PowerShell

Enter this command to download Elastic: wget [link coppied from Elastic]

![1](https://github.com/user-attachments/assets/9a250de6-45c7-4845-a035-a02a29b10285)

We can confirm it is installed correctly, by runnning the ls (list) command. This will show that it is installed.

![1](https://github.com/user-attachments/assets/e266239f-2464-43e5-97af-f804bf605634)

Now to install Elasticsearch run this command: dpkg -i  [your elasticsearch file]

While installing make sure to take note of the password for the elastic superuser

![1](https://github.com/user-attachments/assets/141ebdfb-3f03-42c0-92de-ea79e9c0394f)

Next using the cd (change directory) command: cd /etc/elasticsearch/

![1](https://github.com/user-attachments/assets/3d488724-4fdd-4857-9393-afaea03899c7)

I would like to note the IP address of this machine for future use. To do this, use the command: ip a.

![1](https://github.com/user-attachments/assets/59dcbf51-9fed-448f-b983-2db4edc167dd)

Looking for the elasticsearch.yml file to edit, use the ls command to list files, confirm I am in the correct directory.

![1](https://github.com/user-attachments/assets/1c1b60ab-5bdd-41c5-ae3d-5455a76ace96)

Using the nano command to edit the file: nano elasticsearch.yml

In this I am looking to change the network host, since it is normally only available on a 127 local IP.

Scroll down to the Network section.

Paste in that IP we found previously. And delete the # before the network host and http port. This makes the code active and says if the analyst connects, it will be from this IP and Port.

![1](https://github.com/user-attachments/assets/8e7c27b2-f47b-49ba-a695-96923efba01c)

Before starting, some firewall adjustment is needed to not allow the whole internet to see the VM.

Bake in VULTURE, select products > Network > Firewall

Once there, select "Add Firewall Group"

![1](https://github.com/user-attachments/assets/a5c53367-1751-4cb1-83d6-e0c5db5e75d6)

Name the firewall group

![1](https://github.com/user-attachments/assets/6f41dae2-032b-471a-b572-b97c48f77205)

By default the VM has a rule to allow all traffic.

![1](https://github.com/user-attachments/assets/509d0d72-f7fa-4694-b5d7-466bba4cc7a8)

Change the source tab to your host machine

![1](https://github.com/user-attachments/assets/418b0bd9-c626-4f37-8003-b18363b202de)

Now that the firewall group was made, to apply it to the VM:

Select Comnpute > VM > Settings > Firewall

Once  there select the drop down arrow and select the firewall group just made.

![1](https://github.com/user-attachments/assets/22c4ce1e-a87c-43de-acff-6e510676931c)

Once updated, confirm this pop up:

![1](https://github.com/user-attachments/assets/15620adf-66df-452e-8a13-11b8b5ff49f5)

Back in PowerShell I want to start up Elasticsearch.

First command: systemctl daemon-reload

reloads all systemd unit files and recreates the entire dependency tree, which is necessary after making changes to service files or creating new ones to make the system aware of the modifications.































