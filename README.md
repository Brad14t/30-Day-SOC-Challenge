# Elastic-Home-Lab

# Quick Summary of Sections

**Elastic Server Setup:**

* Setup first Virtual Private Cloud (VPC)
* VM running inside VPC
* Download Elastic onto VM via PowerShell
* Setup firewall for VM inside VULTR

**Installing Kibana:**

* Downloaded and installed Kibana in VM via PowerShell
* Adjusted Firewall settings

**Windows Server Setup**

* Deployed another VM, this time Windows.

**Elastic Agent & Fleet Server Setup**

* Deployed another VM, this time another Ubuntu instance, this will be the fleet server.
* Expanded the VPC
* Started a fleet inside Elastic
* Adjusted in PowerShell to make it so all servers could talk to each other, while filtering out the public data I dont want.
* Created and connected our first agent, data is now being output and visualized.

**Installing Sysmon**

* Installed Sysmon on Windows server, and started to look at logs and noticing potential bad logs via event codes.

# Elastic Server Setup

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

**First command**: `systemctl daemon-reload`

This command reloads all systemd unit files and recreates the entire dependency tree, which is necessary after making changes to service files or creating new ones to make the system aware of the modifications.

**Second commnand**: `systemctl enbable elasticsearch.service`

This command enables the service to start each bottup

**Third Command**: `systemctl start elasticsearch.service`

This command starts the service.

![1](https://github.com/user-attachments/assets/1a614f14-9610-4c0d-99d4-7864cdac26c6)

To check if the service has started correctly use this command:

**Fourth command**: `systemctl status elasticsearch.service`

![1](https://github.com/user-attachments/assets/262f3529-de99-46fc-a4e0-88dcbb3d8486)

# Installing Kibana

Now to download **Kibana.**

Go here: https://www.elastic.co/downloads/kibana

Then same steps as before, select the drop down, select DEB x86_64, right click the big blue download button, select copy link address.

![1](https://github.com/user-attachments/assets/88fa4a14-f29b-46a0-92f8-53174b625661)

Back inside of powersshell: `wget [copied link]`

![1](https://github.com/user-attachments/assets/759d988d-19c8-4100-a067-86c2bbae16f4)

Now that we have the Kibana file, to install: `dpkg -i kibana-8.15.3-amd64.deb`

![1](https://github.com/user-attachments/assets/56e57f49-81d8-4589-b8f5-6bf364e2f88d)

To inspect the file and make changes: nano /etc/kibana/kibana.yml

Inside the file delete the comment of server.port and server.host, delete the "local host" and replace with public IP of VM.

![1](https://github.com/user-attachments/assets/11f92a83-efdb-4781-b622-c4b9dbde213c)

Next is to update > start > and check status same as last time.

`systemctl daemon-reload`
`systemctl enable kibana.service`
`systemctl start kibana.service`
`systemctl status kibana.service`

![1](https://github.com/user-attachments/assets/4721d032-2566-4165-90e0-402f1ccb39ab)

Next is generating an elasticsearch token for Kibana.

To do this inside of SSH session. `cd /usr/share/elasticsearch/bin` > `ls` to list.

![1](https://github.com/user-attachments/assets/ded2946f-d969-4001-a19a-4fc04be62778)

Next: `./elasticsearch-create-enrollment-token --scope kibana`

This command generates an enrollment token specifically for Kibana to securely connect and authenticate with your Elasticsearch cluster.

Make sure to note your token down.

![1](https://github.com/user-attachments/assets/dfbdcbae-ed80-46d4-abb2-63c18ce7ec6d)

Now to start the Kibana instance, I need to adjust the firewall rules to access.

To do this, back in the firewall settings in the firewall. Select "Manage"

![1](https://github.com/user-attachments/assets/7934d328-c697-45b0-a01b-87be10499207)

Inside the firewall settings, chnage the protocol to TCP, port to the rang: 1-65535, and the source as the host machines public IP given by ISP.

![1](https://github.com/user-attachments/assets/11f9d7e0-68e6-4a3b-8a6d-0ff970b7f72f)

Make sure to update firewall connected to VM, then allow it update for 120 seconds.

![1](https://github.com/user-attachments/assets/5ea9e6ae-cd3e-4a08-9432-2788737c1d37)

Checking if I can coonect, I get times out error.

![1](https://github.com/user-attachments/assets/d0966499-a13f-42d8-a070-0e44f7bdaf93)

![1](https://github.com/user-attachments/assets/c973e8f7-be56-44b4-91f4-ca41a7c7633a)

The lastd thing that can be blocking me is the firewall rule on the Ubunu VM, to change this use this command: `ufw allow 5601`

This allows any incimg connection to this port.

That worked!

![1](https://github.com/user-attachments/assets/2119809e-4da8-4c4f-babb-e8aa3c0f3440)

We are prompted to paste the enrollment ticket.

Then I get this pop up.

![1](https://github.com/user-attachments/assets/82ad8d11-1720-43af-940d-4e8d53a63b8b)

To find verification code, `cd /usr/share/kibana/bin`

`ls` to locate verifcation code file

`./kibana-verification-code` to list the code file. 

![1](https://github.com/user-attachments/assets/69ee2342-833c-4cbd-98f2-415d2dfb7e7e)

Once code is entered, you are presented with an authentication screen. This inormation was provided to us earlier.

![1](https://github.com/user-attachments/assets/009763ff-e70d-4a32-9633-18e9ae765d48)

Enter provided username and password

![1](https://github.com/user-attachments/assets/a5af7dc1-9fea-42c0-b01c-05b4cb5a684f)

Select "Excplore on my own"

![1](https://github.com/user-attachments/assets/e5b4a84d-792c-42b8-98af-c67a5a55fb5e)

First thing to add is key stores.

To do this select the menu in the top left > scroll down to "security" > select "alerts"

![1](https://github.com/user-attachments/assets/bbf7588d-ed22-4d55-9eae-9ef01006cf3e)

This alert message appears on the top. To do this, back inside SSH session. We are already in the right directory, we are looking for the "kibana-encryption-keys" file.

![1](https://github.com/user-attachments/assets/0119719e-6cc6-4b4e-9c02-3da00aed792c)

![1](https://github.com/user-attachments/assets/85f0014b-2c85-4b6c-8808-329475d76000)

Use this command: ` ./kibana-encryption-keys generate`

Make sure to copy and paste these for later.

![1](https://github.com/user-attachments/assets/af03733f-06c1-4296-a4b4-3e7de9794930)

Next binary to look at is the keystore to add saved encryption keys: `./kibana-keystore add xpack.encryptedSavedObjects.encryptionKey`

![1](https://github.com/user-attachments/assets/15a20bd0-afbf-40b4-b019-4e31077fa5b8)

Next restart Kibana to refresh: `systemctl restart kibana.service`

![1](https://github.com/user-attachments/assets/577116b1-ea68-47b9-b795-40efad704bb5)

Make sure to refresh elastic in browser aswell.

Now the error message is gone.

![1](https://github.com/user-attachments/assets/8ac3f278-9bc4-476b-8ba6-cc15ff96e43f)

# Windows Server Setup

Inside VULTUR's compute dashboard, select the "Deploy" int he top right, select "deploy new server".

Inside the new settings, the type will be cloud compute, since it is not needing to be a powerful VM, and location the same as the rest.

![1](https://github.com/user-attachments/assets/89cf84b8-ec32-47c6-a29b-1be3648a2a6f)

Image will be Windows standard. 

For the size or power of the compute, choose the cheapest option.

![1](https://github.com/user-attachments/assets/759a23df-c87c-4a9d-911d-17920e9b7de8)

![1](https://github.com/user-attachments/assets/528ea98c-118c-47d7-94aa-2651de728a71)

Deselect Auto backup and IPv6. For this server we dont want to add it to the VPC for security reasons, it makes sense for this server to be outside of that internal network.

No firewall selected.

And Name the new server.

![1](https://github.com/user-attachments/assets/d0e9c3dc-09b4-4877-916d-aeab1e371f9f)

Now both VM's are running.

![1](https://github.com/user-attachments/assets/8bf60387-7991-42bd-80ec-6008340e329e)

# Elastic Agent & Fleet Server Setup

First in VULTR compute dashboard, deploy a new instance.

![1](https://github.com/user-attachments/assets/3b881e6e-6f0b-4e0c-b356-4b9656dd705a)

This instance, the optimized cloud compute would work.

![1](https://github.com/user-attachments/assets/42793519-4d2c-43e5-9e25-04e62c665308)

Location select the same location as previously

![1](https://github.com/user-attachments/assets/088e74da-d2cc-40aa-b608-427f69a26be9)

Image select Ubunu same as before.

![1](https://github.com/user-attachments/assets/9f0b64fd-0d96-4e76-aa0b-cdeb8ab0c32e)

Size of the image, just the cheapest in general purpose will work.

![1](https://github.com/user-attachments/assets/dffde3ee-6391-42f4-a122-2d158e0169eb)

Additinal features, deselct auto backups and IPv6, and select VPC.

![1](https://github.com/user-attachments/assets/facf8036-23c1-43b8-a011-c569faef8af5)

Select the correct VPC.

![1](https://github.com/user-attachments/assets/43ce8b4f-60cd-403f-979a-44a093597c24)

Then dont select SSH key or firewall for now. And select "Deploy now"

![1](https://github.com/user-attachments/assets/504190c5-a092-4b67-82e0-073e520db744)

Back inside the web GUI. Select the drop down menu > scroll down to "managment" > select "Fleet"

![1](https://github.com/user-attachments/assets/59da5753-2d2b-4c84-99e9-037188aaca47)

Select "Add Fleet Server"

![1](https://github.com/user-attachments/assets/19fd7f45-efab-47da-9efb-f8476f5b3ec4)

Name the new fleet server. 

For the URL, use your fleet servers public IP in VULTR.

Format for URL: https://[public IP]

![1](https://github.com/user-attachments/assets/10e87529-826c-4f93-b7e8-095cf730a54f)

![1](https://github.com/user-attachments/assets/3e659df9-0668-4406-add6-a4e396855a05)

After a couple of seconds of generating.

![1](https://github.com/user-attachments/assets/32fbaaf9-f5a2-4752-a0ba-5fa9db8ca335)

I need to install this fleet server on the fleet server I created in VULTR.

To do this, select the fleet server VM in VULTR.

![1](https://github.com/user-attachments/assets/eeb40b44-2816-4638-9a3f-d95200e9580c)

Select View in console.

![1](https://github.com/user-attachments/assets/805c4244-4b9b-4a26-9d83-510450b25261)

If it starts up then you know the server is operating. Open a new PowerShell window on host computer.

Commands: `ssh root@[ip of fleet server]` > `yes` > `enter password given`

![1](https://github.com/user-attachments/assets/5f120d44-1375-4384-b211-0e456ef8791e)

![1](https://github.com/user-attachments/assets/998324c8-2c98-4863-90b0-07400a924921)

Once inside fleet server, best practive is to update repositories.

Commands: `apt-get update && apt-get upgrade -y`

Once install is completed, a firewall adjustment is needed, the firewall is excluiding the fleet server.

To change, copy the IP of the fleet server.

![1](https://github.com/user-attachments/assets/d35b2391-416a-4436-ad4f-da4222a69fa2)

Select Products > Network > Firewall

For this rule I want from any TCP from the fleet server to be allowed.

![1](https://github.com/user-attachments/assets/4b59eea5-e450-4838-ac4c-c718fad9da32)

Then copy and paste into fleet server powershell. And wait for it to finish connecting.

![1](https://github.com/user-attachments/assets/d4bdbb2f-fbe3-4614-8257-4eb613900dd9)

If it doesnt finish click enter, then it will prompt you to confirm installation.

After that if it still doesnt connect we have to adjust the firewall on the VM. To do this type in the main VM: `ufw allow 9200` This is the default port for elastic search.

![1](https://github.com/user-attachments/assets/e026ebf8-b02d-4814-8f33-1bb94804ae4f)

Now try to re run the agent install.

![1](https://github.com/user-attachments/assets/5589d5a6-dfd6-48d2-af7c-7b95026deef1)

![1](https://github.com/user-attachments/assets/00e680bc-326c-4692-940f-0f615a940ef0)

Once you see either of the following, you know it was successful.

![1](https://github.com/user-attachments/assets/4096ac73-eb1a-4632-b481-572b21fa53ce)

AND

![1](https://github.com/user-attachments/assets/74f9cfe7-011e-4f13-939a-e28dbd6035ca)

Select "Continue Enrolling"

Name the new policy.

![1](https://github.com/user-attachments/assets/39b8aba1-4033-4164-bbf6-25608ef70573)

Scroll down to where you can select "windows" since this is going onto our Windows server.

Copy the agent code.

![1](https://github.com/user-attachments/assets/87f07194-7506-4ecf-8e64-5691e8275149)

# Installing Agent onto Machine

Select the Windows server from the compute dashboard in VULTR.

![1](https://github.com/user-attachments/assets/8961cbc9-c12d-4c99-9891-97dd0a6ae311)

Select view consol.

![1](https://github.com/user-attachments/assets/add3b3c8-65c2-492f-b14a-cb2778c5bcc6)

Use login that is provided, you can RDP to machine as well.

![1](https://github.com/user-attachments/assets/3afcf356-c6b8-47a3-b23c-545dca122124)

Inside of the windows server, open PowerShell as administrator.

Copy the gaent from before. Then paste inside ofg clipboard of Windows server.

![1](https://github.com/user-attachments/assets/f87588d3-cc19-41db-a38e-6cf26a9abd9f)

Due to the error being received, the port from the agent to server is being haulted.

The firewall with the VM still hasnt been changed.

To do this, inside the fleet server enter: `ufw allow 8220`

![1](https://github.com/user-attachments/assets/332a7fe3-769b-47ad-bdfb-80c94c9c1c50)

To find the 8220 (port number)

Visit this site: https://www.elastic.co/guide/en/fleet/current/add-fleet-server-on-prem.html

Scroll down to "default port assignment"

![1](https://github.com/user-attachments/assets/bda6662c-4822-4ee1-8857-0269c96894b4)

Try to instal agent again on windows server.

Get the same error! As we can see the error says we are using port 443 to send data. 

To make that change, back inside the fleet server ssh session: `ufw allow 443`

Try installing again.

Same error appeared. **Final fix** is to go to fleet homepage inside elastic.

Go to settings. Select ther pencil to the right to edit the host information.

By default the agent is sending data over port 443 but the server is only sending and accepting data from 8220.

Change the :443 to :8220

![1](https://github.com/user-attachments/assets/84444783-b5a8-46e6-b565-ea8972ee0dd1)

Now to correctly install the agent, past the agent link given back into the windows server. Delete the 443 and type 8220.

Once again an error, but this one is different it is a x509 failed certificate. To pass that, paste last agent install back into terminal and add `--insecure`, then type y.

Now the agent has correctly installed.

To double check it was installed correctly, in Elastic, go to fleet > agents.

![1](https://github.com/user-attachments/assets/c5db34ce-e520-45b8-80d1-70573833bc4c)

# Installing Sysmon

Sysmon documentation: https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon

First I am going to use Remote Desktoip Connection on my host machine.

Using the IP and credentials from my windows server in VULTR

![1](https://github.com/user-attachments/assets/529292cc-e8be-4c42-a1a1-63dcc78913fb)

Go to the link above inside microsoft edge, once inside the windows server.

Slect "Download Sysmon"

![1](https://github.com/user-attachments/assets/d94e5c14-68de-4bc8-8dc2-3f7616acb7bc)

Then right click and extract files.

![2](https://github.com/user-attachments/assets/d61984e4-09b8-46ae-a99e-9b5e4efca112)

Now to get a configuration file for Sysmon.

Google: "sysmon olaf configuration"

Or just go to this link: https://github.com/olafhartong/sysmon-modular

Scroll down to the "sysmonconfi.xml" > select "Raw" > Right click > select "save as" > save inside sysmon's directory

![1](https://github.com/user-attachments/assets/0e849a6e-3102-441c-9426-a0efbbabd714)

![1](https://github.com/user-attachments/assets/ccc79096-f1fd-475f-bceb-7c3298ea6afa)

Next

![1](https://github.com/user-attachments/assets/e1eacac6-a289-42c2-8206-fb0632f0472e)

To get into the Sysmon directory use: `cd C:\Users\Administrator\Downloads\Sysmon`

Then `dir` to confirm your in the correct directory

![1](https://github.com/user-attachments/assets/860fba72-80f3-4644-ab51-2491542bfe5b)

Now to install sysmon: `.\Sysmon64.exe -i sysmonconfig.xml

Agree to the license agreement.

![1](https://github.com/user-attachments/assets/d4774656-41f6-42ec-91b9-d4bf330d312a)

To check if Sysmon is installed, search "Services" scroll down to thee S's

![1](https://github.com/user-attachments/assets/86635fa3-4ddf-48e0-a1df-96862f07905a)

Or Ssearch "Event Viewer" > Select Application and services drop down > Microsoft > Windows > sysmon

![1](https://github.com/user-attachments/assets/6b350c21-aa00-4ee3-90cb-595cbd8bf034)































