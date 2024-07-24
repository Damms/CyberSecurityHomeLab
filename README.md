# Cybersecurity Homelab for Detection & Monitoring

## Objective

This HomeLab was designed by Day [@Cyberwox](https://cyberwoxacademy.com/)

In this project I have used VMWare as the hypervisor for the host computer. Pfsense is used as the router, firewall and DHCP server so that we can segment the virtual networks. Kali has been setup to send attacks to our victom network, the victim network will consist of a domain controller and a couple windows machines (other OS can be added too). I have Security Onion as our SIEM, which will ingest data from the victim network via the span port, Security Onion will be used to complete threat hunting and security operations. I have also setup Splunk as a alternative SIEM which will recieve data from the universal forwarder installed on the domain controller.



![image](https://github.com/user-attachments/assets/8406140f-6445-4ae7-a803-72a7c5e3d1dc)

_Reference: HomeLab Network Design & Topology_



### Skills Learned


### Tools Used

- VMWare
- pfsense
- Kali
- Security Onion
- Windows Server (Domain Controller)
- Splunk

### Prerequisites 
Powerful host machine with VMWare or alternative hypervisor installed. I recommend the host machine to have at elast 32GB of RAM and 1TB+ of storage.

## Steps


### Part 1: Configuring pfSense

1. **Setup Objective**: Configure pfSense as a firewall for segmenting the private homelab network, accessible only from the Kali Linux machine.

2. **Download pfSense ISO**:
    - Download the pfSense Community Edition ISO file.

3. **Create a New Virtual Machine in VMware Workstation**:
    1. Click “Create a New Virtual Machine” on the VMware Workstation Homescreen.
    2. Select “Typical (recommended)” and click Next.
    3. Click “Browse” and locate the pfSense ISO file.
    4. Click Next.

4. **Name and Disk Configuration**:
    1. Rename the Virtual Machine to “pfsense”.
    2. Click Next.
    3. Set the disk size to 20GB.
    4. Ensure “Split virtual disk into multiple files” is selected.
    5. Click Next.

5. **Hardware Customization**:
    1. Click “Customize Hardware”.
    2. Increase the memory to 2GB.
    3. Add 5 network adapters and map them to a VMnet interface.

6. **Initial pfSense Configuration**:
    1. Power on the pfSense machine.
    2. Accept all defaults and allow pfSense to configure and reboot.
    3. Enter option 1 when prompted.
    4. Set VLANS: n
    5. Enter em0, em1, em2, em3, em4 & em5 respectively.
    6. Proceed with the configuration: y.

7. **LAN and OPT Interfaces Setup**:
    1. Enter option 2 for interface assignments.
    2. LAN Interface (2):
        - Set IP address: 192.168.1.1 for WebGUI access via Kali Machine.
    3. Configure OPT1, OPT2, and OPT4 interfaces.
    4. Leave OPT3 without an IP for span port traffic monitored by Security Onion.

8. **Completion**:
    - Finish the configuration on the pfSense VM.
    - Complete the remaining configuration via the Kali machine through the WebConfigurator.


### Part 2: Configuring Security Onion

1. **Setup Objective**: All-in-one IDS, Security Monitoring, and Log Management solution.

2. **Download Security Onion ISO**:
    - Download the Security Onion ISO file.

3. **Create a New Virtual Machine in VMware Workstation**:
    1. Select Typical installation and click Next.
    2. Choose Installer disc image file and locate the SO ISO file.
    3. Click Next.

4. **Virtual Machine Configuration**:
    1. Choose Linux, CentOS 7 64-Bit and click Next.
    2. Specify virtual machine name and click Next.
    3. Set disk size (minimum 200GB) and store as a single file.
    4. Click Next.

5. **Hardware Customization**:
    1. Click “Customize Hardware”.
    2. Set memory to 4-32GB.
    3. Add two Network Adapters and assign them to Vmnet 4 & Vmnet 5.
    4. Click “Finish”.

6. **Initial Security Onion Configuration**:
    1. Power on the virtual machine and click Enter when prompted.
    2. Type “yes” when prompted.
    3. Set a username & password.
    4. After reboot, enter the username & password.
    5. Select “Yes” and click Enter.

7. **Setup Options**:
    1. Select the EVAL option.
    2. Type “AGREE”.
    3. Select “Standard”.
    4. Set a hostname and a short description.
    5. Select ens33 as the management interface and set addressing to DHCP.
    6. Follow prompts: Select “YES”, “OK”, “Direct”, and ens35 as the Monitor Interface.
    7. Select “Automatic” for the OS patch schedule.
    8. Accept default home network IP.
    9. Accept all defaults.
    10. Enter an email address and password for the admin account.
    11. Select “IP”.
    12. Select “Yes” for the NTP server and accept defaults.
    13. Take note of final settings, especially the IP address for web access.
    14. Select “Yes”.

8. **Post-Installation Access**:
    1. Configure an external Ubuntu Desktop.
    2. Download and install Ubuntu Desktop.
    3. Run `ifconfig` on the Ubuntu Machine and note its IP Address.
    4. On Security Onion, run:
        ```sh
        sudo so-allow
        ```
    5. Enter the Ubuntu Desktop IP Address to create a firewall rule for web access.
    6. Navigate to the Security Onion IP Address on the Ubuntu Desktop.

9. **Completion**:
    - Configuration of the Security Onion VM is complete.


### Part 3: Configuring Kali Linux

1. **Setup Objective**: Use Kali Linux as an attack machine to perform offensive actions against the Domain Controller and other machines.

2. **Download Kali Linux ISO**:
    - Download the Kali Linux ISO file.

3. **Load Kali Linux VM**:
    - Click on the `.vmx` file from the downloaded Kali folder to load the default Kali image in VMware.

4. **Pre-Power On Configuration**:
    1. Change the Network Adapter to Vmnet2.
    2. Set Memory to 4GB.

5. **Power On and Initial Setup**:
    1. Power on the Kali machine and use the default credentials.
    2. Change the default password to a more secure one using:
        ```sh
        passwd
        ```

6. **Completion**:
    - The Kali machine is now ready for use.
  

### Part 4: pfSense Interfaces and Rules

1. **Objective**: Access the pfSense WebConfigurator to make changes to interfaces and firewall rules.

2. **Access pfSense WebConfigurator**:
    1. Navigate to `192.168.1.1` in a the kali machine.
    2. Select “Advanced…” and accept the risk to continue.
    3. Sign in with default credentials: `admin` & `pfsense`.

3. **Wizard Setup**:
    1. Navigate through the Wizard to Step 2 of 9.
    2. Add `8.8.8.8` as Primary DNS Server and `4.4.4.4` as Secondary DNS Server.
    3. Click Next.
    4. At Step 3 of 9, choose your Timezone and click Next.
    5. At Step 4 of 9, untick the last two options.
    6. At Step 5 of 9, click Next.
    7. At Step 6 of 9, set a new Admin Password and click Next.
    8. At Step 7 of 9, click Reload and Finish.

4. **Interface Configuration**:
    1. Click on Interfaces and select LAN.
    2. Change the description to "Kali" and click Save.
    3. If an error occurs, use the provided troubleshooting article.
    4. Configure the rest of the interfaces as needed.
    5. For OPT3, ensure to enable the interface.

5. **Bridges Configuration**:
    1. In Interfaces Assignment, select Bridges and click Add.
    2. Select "VictimNetwork" as the Member Interface.
    3. Select Display Advanced and choose "SPANPORT" under Advanced Configuration for Span Port.
    4. Scroll down and click Save.

6. **Firewall Rules Configuration**:
    1. Navigate to Firewall > Rules.
    2. Click the Add button with the downward arrow.
    3. Under "edit Firewall Rule," select Protocol as ANY.
    4. Scroll down and click Save.

7. **Completion**:
    - The majority of the firewall configuration needed for pfSense is complete.



