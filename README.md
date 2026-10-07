# WAZUH Home Lab: SIEM and File Integrity Monitoring
**By VIKNESH V S**

## 1. Overview
Wazuh is a free, open-source security platform that offers:
* Log analysis
* File integrity monitoring
* Intrusion detection
* Vulnerability detection
* Real-time alerting

## 2. Lab Architecture

| Component | Host | Role |
| :--- | :--- | :--- |
| **Wazuh Manager** | Ubuntu (VirtualBox) | Collects, analyzes, and stores data from agents |
| **Wazuh Agent** | Windows (Host Machine) | Sends logs and system events to the Wazuh manager |

**Network Configuration:** Use a **Bridged Adapter** in VirtualBox to place the Ubuntu server on the same network as the host. This allows communication between the host and guest virtual machines.

## 3. Prerequisites
* VirtualBox installed
* Ubuntu Server 20.04+ installed in VirtualBox (bridged networking)
* Internet access on Ubuntu VM
* Administrative access on the Windows host
* Basic knowledge of Linux and system administration

## 4. Installing the Wazuh Manager (Ubuntu)
Run the following steps inside your Ubuntu VirtualBox server terminal.

### 4.1 Add Wazuh GPG Key
```bash
curl -s https://wazuh.com | sudo gpg --dearmor -o /usr/share/keyrings/wazuh-archive-keyring.gpg
```
*This adds the GPG key to verify the authenticity of the Wazuh packages.*

### 4.2 Download and Execute Wazuh Installation Script
```bash
curl -so https://wazuh.com && sudo bash ./wazuh-install.sh -a -i
```
* `-a`: Installs all core components (manager, indexer, dashboard).
* `-i`: Runs the script in interactive mode.

## 5. Accessing the Wazuh Dashboard
1. Check your Ubuntu VM's IP address by running: `ifconfig`
2. Open a browser on your host machine and navigate to: `https://<ubuntu-vm-ip>`
3. Accept any browser security warnings due to the self-signed certificate.
4. Log in using the credentials displayed at the end of the installation script execution.

## 6. Installing the Wazuh Agent (Windows Host)
1. Download the latest Wazuh agent MSI installer from the [Official Wazuh Documentation](https://wazuh.com).
2. Install the MSI package on your Windows system using the default setup settings.

## 7. Registering the Agent with the Manager

### 7.1 Generate Agent Key on Ubuntu Manager
Run the agent management utility in your Ubuntu terminal:
```bash
sudo /var/ossec/bin/manage_agents
```
* Select `A` to add a new agent.
* Assign a recognizable name (e.g., `WindowsHost`).
* Leave the IP address blank unless a static assignment is specifically required.
* After creation, select `E` to extract the key.
* Copy the generated key output string.

### 7.2 Apply Key in the Windows Agent
1. Open the **Wazuh Agent Manager GUI** from the Windows Start Menu.
2. Paste your copied key into the appropriate field.
3. Save and apply the authentication key.
4. Enter your Ubuntu manager's exact IP address.
5. Restart the agent service. You can now view the newly onboarded agent on your WAZUH dashboard.

## 8. File Integrity Monitoring on Windows
Wazuh supports real-time monitoring of file and folder changes using **Syscheck**.

### 8.1 Edit Agent Configuration
Open the following configuration file using administrative privileges:
`C:\Program Files (x86)\ossec-agent\ossec.conf`

Add the target directory entry inside the `<syscheck>` block:
```xml
<directories realtime="yes">C:\Users\abc\Test</directories>
```
### 8.2 Restart the Agent
Save the configuration changes and restart the Wazuh agent service via the GUI or Windows Services to apply your monitoring targets.

## 9. Verifying Setup
1. Open the **Wazuh Dashboard** in your web browser.
2. Navigate to **Agents** and ensure the Windows agent is listed with an **Active** status.
3. Head over to the **Integrity Monitoring** section.
4. Perform file operations (create, modify, or delete files) within your monitored `C:\Users\abc\Test` folder.
5. Confirm that real-time alert logs dynamically pop up on your dashboard.

**Document Reference:[wazuh-project.pdf](https://github.com/vikneshvs/WAZUH-Home-Lab---SIEM-and-File-Integrity-Monitoring/blob/main/wazuh-project.pdf)
** WAZUH-LAB-SIEM-FIM-V1 | Created by VIKNESH V S
