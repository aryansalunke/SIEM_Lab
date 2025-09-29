# Wazuh Home Lab - SIEM and File Integrity Monitoring
Welcome! This guide walks you through setting up a basic Security Information and Event Management (SIEM) system for learning and experimentation using Wazuh. This project was inspired by a fantastic guide by Royden Rebello (The Social Dork) and his companion YouTube video, which I highly recommend following along with for a complete walkthrough.

My setup includes a Wazuh manager, a Windows agent, and the configuration for file integrity monitoring.

Lab Architecture
The lab is designed with a simple two-component architecture:

Wazuh Manager: This runs on an Ubuntu virtual machine (VM). It's the brain of the operation, responsible for collecting, analyzing, and storing all the data sent by the agents.

Wazuh Agent: This runs on my Windows host machine. Its job is to collect system logs and events and send them securely to the Wazuh manager.

To allow seamless communication between the host and the Ubuntu VM, I've used a bridged adapter in VirtualBox. This makes the VM appear as a separate device on my network, just like the Windows machine.

Prerequisites
Before you begin, make sure you have the following ready to go:

VirtualBox installed.

An Ubuntu Server 20.04+ VM installed in VirtualBox with bridged networking already configured.

Internet access on the Ubuntu VM.

Administrative access on your Windows host machine.

Installation Steps
1. Install the Wazuh Manager on Ubuntu
First, you'll need to install the Wazuh manager on your Ubuntu VM. Open a terminal and run the following commands.

Add the Wazuh GPG key: This step verifies the packages you're about to install, ensuring their authenticity.

Bash

curl -s https://packages.wazuh.com/key/GPG-KEY-WAZUH | sudo gpg --dearmor -o /usr/share/keyrings/wazuh-archive-keyring.gpg
Download and execute the installation script: The -a flag installs all necessary components (the manager and the indexer), and -i runs the script in interactive mode.

Bash

curl -sO https://packages.wazuh.com/4.12/wazuh-install.sh && sudo bash ./wazuh-install.sh -a -i
2. Access the Wazuh Dashboard
Once the installation is complete, find your Ubuntu VM's IP address by running ifconfig in the terminal.

Then, on your Ubuntu VM, open a browser and navigate to https://<ubuntu-vm-ip>. You can log in using the credentials that were displayed in the terminal at the end of the installation script.

3. Install the Wazuh Agent on Windows
Next, you need to install the agent on the Windows machine you want to monitor.

Download the latest Wazuh agent MSI installer from the official Wazuh documentation.

Run the MSI package on your Windows system, using the default settings for the installation.

4. Register the Agent with the Manager
This is the final step to get the two components talking to each other.

Generate an agent key on the Ubuntu Manager:

Run the agent management utility: sudo /var/ossec/bin/manage_agents.

Select A to add a new agent.

Give it a name (e.g., WindowsHost) and leave the IP address blank.

After the agent is created, select E to extract the key. Copy the entire key from the terminal output.

Apply the key in the Windows Agent GUI:

Open the Wazuh Agent Manager from the Windows Start Menu.

Paste the key you just copied into the appropriate field.

Add the Ubuntu manager's IP address.

Save and apply the key, then restart the agent service.

Once the agent registers successfully, you'll be able to see it on the Wazuh dashboard.

Setting Up File Integrity Monitoring (FIM)
Wazuh uses a feature called Syscheck to monitor file and folder changes in real time.

Edit the agent configuration file: On your Windows host, open C:\Program Files (x86)\ossec-agent\ossec.conf.

Add a new entry: Inside the <directories> block, add the path to the folder you want to monitor in real time. In this example, I'm monitoring a test folder.

XML

<directories realtime="yes">C:\Users\abc\Test</directories>
Restart the agent: After saving the changes, restart the Wazuh agent service to apply the new configuration.

Verification
To make sure everything is working as expected, I recommend the following checks:

Open the Wazuh Dashboard in your browser.

Navigate to the Agents section and verify that your Windows agent is listed and has an "Active" status.

Go to the Integrity Monitoring section.

Now, create, modify, or delete a file in the folder you configured for FIM.

Confirm that an alert appears in the dashboard, indicating that the setup is working correctly!
