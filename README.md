# Cybersecurity Homelab for Detection & Monitoring

## Objective

This HomeLab was designed by Day [@Cyberwox](https://cyberwoxacademy.com/)

In this project I have used VMWare as the hypervisor for the host computer. Pfsense is used as the router, firewall and DHCP server so that we can segment the virtual networks. Kali has been setup to send attacks to our victom network, the victim network will consist of a domain controller and a couple windows machines (other OS can be added too). I have Security Onion as our SIEM, which will ingest data from the victim network via the span port, Security Onion will be used to complete threat hunting and security operations. I have also setup Splunk as a alternative SIEM which will recieve data from the universal forwarder installed on the domain controller.

_HomeLab Network Design & Topology_

![image](https://github.com/user-attachments/assets/8406140f-6445-4ae7-a803-72a7c5e3d1dc)




### Skills Learned
- Virtualization
- Networking & Segmentation
- Firewall Rules
- SIEM Configuration & Data Collection
- Active Directory Domains, Users and Computers

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
1. [Configuring pfSense](#part1)
2. [Configuring Security Onion](#part2)
3. [Configuring Kali Linux](#part3)
4. [pfSense Interfaces and Rules](#part4)
5. [Configuring Windows Server as a Domain Controller](#part5)
6. [Configuring Windows 10 Desktop & Adding a User to the AD Domain](#part6)
7. [Joining the PCs to the Domain](#part7)
8. [Installing Splunk on a Ubuntu Server](#part8)
9. [Installing Universal Forwarder on Windows Server](#part9)

<a id="part1"></a>
### Part 1: Configuring pfSense

1. **Setup Objective**: Configure pfSense as a firewall for segmenting the private homelab network, accessible only from the Kali Linux machine.

2. **Download pfSense ISO**:
    - [Download the pfSense Community Edition ISO file](https://www.pfsense.org/download/).

3. **Create a New Virtual Machine in VMware Workstation**:
    1. Click “Create a New Virtual Machine” on the VMware Workstation Homescreen.
    2. Select “Typical (recommended)” and click Next.

       ![image](https://github.com/user-attachments/assets/47513db8-17a2-41eb-812b-f19b90078df0)

    4. Click “Browse” and locate the pfSense ISO file.

       ![image](https://github.com/user-attachments/assets/24d45a5b-56aa-483b-b8c7-16b99332df67)

    6. Click Next.

4. **Name and Disk Configuration**:
    1. Rename the Virtual Machine to “pfsense”.
   
   ![image](https://github.com/user-attachments/assets/2bc60a2b-c1a6-4864-b552-138168d67b96)

   
    3. Click Next.
    4. Set the disk size to 20GB.
    5. Ensure “Split virtual disk into multiple files” is selected.
    6. Click Next.

5. **Hardware Customization**:
    1. Click “Customize Hardware”.
    2. Increase the memory to 2GB.
    3. Add 5 network adapters and map them to a VMnet interface.
  
![image](https://github.com/user-attachments/assets/f8117b80-2ac4-460e-b45c-eebf78340971)


6. **Initial pfSense Configuration**:
    1. Power on the pfSense machine.
    2. Accept all defaults and allow pfSense to configure and reboot.
    3. Enter option 1 when prompted.
    4. Set VLANS: n
    5. Enter em0, em1, em2, em3, em4 & em5 respectively.
   
       ![image](https://github.com/user-attachments/assets/8eeaf818-71a3-4534-9741-9408480c517e)

    7. Proceed with the configuration: y.
   
       ![image](https://github.com/user-attachments/assets/0300fd75-9911-4935-830b-1c602b4a8961)


7. **LAN and OPT Interfaces Setup**:
    1. Enter option 2 for interface assignments.
    2. LAN Interface (2):
        - Set IP address: 192.168.1.1 for WebGUI access via Kali Machine.
    3. Configure OPT1, OPT2, and OPT4 interfaces.
    4. Leave OPT3 without an IP for span port traffic monitored by Security Onion.
  
_LAN Interface Config_

![image](https://github.com/user-attachments/assets/61ff9f20-3167-4e36-bed2-172957aa1e48)

_OPT1 Interface Config_

![image](https://github.com/user-attachments/assets/36c54f48-9c6a-410f-a2fc-c9f3908e96e4)

_OPT2 Interface Config_

![image](https://github.com/user-attachments/assets/2e52ce22-7897-4070-995a-8ca5377d5569)

_OPT4 Interface Config_

![image](https://github.com/user-attachments/assets/3c4629ed-f831-4014-90b0-c7b99753e575)


<a id="part2"></a>
### Part 2: Configuring Security Onion

1. **Setup Objective**: All-in-one IDS, Security Monitoring, and Log Management solution.

2. **Download Security Onion ISO**:
   
    - [Download the Security Onion ISO file](https://github.com/Security-Onion-Solutions/securityonion/blob/master/VERIFY_ISO.md).

4. **Create a New Virtual Machine in VMware Workstation**:
    1. Select Typical installation and click Next.
    2. Choose Installer disc image file and locate the SO ISO file.

    ![image](https://github.com/user-attachments/assets/8f9dbc95-1913-438c-acc7-45dcdfb8b183)

    4. Click Next.

4. **Virtual Machine Configuration**:
    1. Choose Linux, CentOS 7 64-Bit and click Next.

    ![image](https://github.com/user-attachments/assets/a7991947-24c8-4704-a7ba-31f2ff31b617)

    3. Specify virtual machine name and click Next.
    4. Set disk size (minimum 200GB) and store as a single file.
    5. Click Next.

5. **Hardware Customization**:
    1. Click “Customize Hardware”.
    2. Set memory to 4-32GB.
    3. Add two Network Adapters and assign them to Vmnet 4 & Vmnet 5.
    4. Click “Finish”.

    ![image](https://github.com/user-attachments/assets/58008a35-712c-4f9b-bc47-c5a8a702cb6b)


6. **Initial Security Onion Configuration**:
    1. Power on the virtual machine and click Enter when prompted.

    ![image](https://github.com/user-attachments/assets/610ad094-a221-4fb5-9794-ac9e89270ab8)

    3. Type “yes” when prompted.
       
    ![image](https://github.com/user-attachments/assets/5311ffaf-87d1-4713-96fc-7f8080237ff3)

    5. Set a username & password.
    6. After reboot, enter the username & password.
    7. Select “Yes” and click Enter.
       
    ![image](https://github.com/user-attachments/assets/c105ef74-bda6-4fd6-993b-b4b85739f401)


7. **Setup Options**:
    1. Select the EVAL option.

    ![image](https://github.com/user-attachments/assets/f05d08a7-bdd9-4e0e-ba33-ddde8982bb36)

    3. Type “AGREE”.

    ![image](https://github.com/user-attachments/assets/49a87c23-fa91-486e-9bc4-248506fb7a8b)

    5. Select “Standard”.
    
    ![image](https://github.com/user-attachments/assets/36a6a741-87a1-4c00-aec6-79fdc5bf409d)

    5. Set a hostname and a short description.
    6. Select ens33 as the management interface and set addressing to DHCP.
       
    ![image](https://github.com/user-attachments/assets/67b91dd5-d994-4e4f-b2c7-379c6d73a217)

    ![image](https://github.com/user-attachments/assets/8b3167b9-20c8-41b3-b726-4370db13d17e)

    6. Follow prompts: Select “YES”, “OK”, “Direct”, and ens35 as the Monitor Interface.

    ![image](https://github.com/user-attachments/assets/087bfd37-365d-4539-8c37-72def9ca328e)
   
    7. Select “Automatic” for the OS patch schedule.
    8. Accept default home network IP.
    9. Accept all defaults.
    10. Enter an email address and password for the admin account.
    11. Select “IP”.

    ![image](https://github.com/user-attachments/assets/c48680a7-56d8-468e-819f-c3fffb604ef3)

    12. Select “Yes” for the NTP server and accept defaults.
    13. Take note of final settings, especially the IP address for web access.
    14. Select “Yes”.
    
    ![image](https://github.com/user-attachments/assets/62d8916a-17f7-4225-adea-1fb2ecf516f9)


8. **Setup Analyst Machine**:
    1. Configure an external Ubuntu Desktop to access the Security Onion web interface.
    2. Download and install Ubuntu Desktop.
    3. Run `ifconfig` on the Ubuntu Machine and note its IP Address.
    5. On Security Onion, run:
        ```sh
        sudo so-allow
        ```
    6. Enter your password then select the anayst role to add by typing _a_

    ![image](https://github.com/user-attachments/assets/1c245fa1-435b-449c-93c5-68e430a930a5)

    8. Enter the Ubuntu Desktop IP Address to create a firewall rule for web access.
    9. Navigate to the Security Onion IP Address on the Ubuntu Desktop to access the web interface


<a id="part3"></a>
### Part 3: Configuring Kali Linux

1. **Setup Objective**: Use Kali Linux as an attack machine to perform offensive actions against the Domain Controller and other machines.

2. **Download Kali Linux ISO**:
    - [Download the Kali Linux ISO file](https://www.offensive-security.com/kali-linux-vm-vmware-virtualbox-image-download/)

3. **Load Kali Linux VM**:
    - Click on the `.vmx` file from the downloaded Kali folder to load the default Kali image in VMware.

4. **Pre-Power On Configuration**:
    1. Change the Network Adapter to Vmnet2.
    2. Set Memory to 4GB.

![image](https://github.com/user-attachments/assets/0a1eb8a0-b5e0-4746-a02c-a4b98fc6be07)

5. **Power On and Initial Setup**:
    1. Power on the Kali machine and use the default credentials.
    2. Change the default password to a more secure one using:
        ```sh
        passwd
        ```
  

<a id="part4"></a>
### Part 4: pfSense Interfaces and Rules

1. **Objective**: Access the pfSense WebConfigurator to make changes to interfaces and firewall rules.

2. **Access pfSense WebConfigurator**:
    1. Navigate to `192.168.1.1` in a the kali machine.

    ![image](https://github.com/user-attachments/assets/53e57e75-5d32-4d91-a666-0d5f982d3a21)

    2. Select “Advanced…” and accept the risk to continue.
    3. Sign in with default credentials: `admin` & `pfsense`.

    ![image](https://github.com/user-attachments/assets/30c16295-7e31-4de5-9842-b9bc6e7f2514)

3. **Wizard Setup**:
    1. Navigate through the Wizard to Step 2 of 9.
    2. Add `8.8.8.8` as Primary DNS Server and `4.4.4.4` as Secondary DNS Server.

    ![image](https://github.com/user-attachments/assets/0b3bf167-8dfe-4494-9904-713e098c7b67)

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

    ![image](https://github.com/user-attachments/assets/bf28b63e-249d-43e2-8589-1d1cf40221fa)

    5. For OPT3, ensure to enable the interface like below which we will use to setup span port
    
    ![image](https://github.com/user-attachments/assets/8f2ee03c-ca80-4e4b-946f-c44b4eda70aa)


6. **Bridges Configuration**:
    1. In Interfaces Assignment, select Bridges and click Add.

    ![image](https://github.com/user-attachments/assets/4badf7fc-67e5-49a4-95c2-0f06f6698cda)

    2. Select "VictimNetwork" as the Member Interface.
    3. Select Display Advanced and choose "SPANPORT" under Advanced Configuration for Span Port.
  
   ![image](https://github.com/user-attachments/assets/237b167f-4487-4b42-bdea-3b8602c8a6cc)

    5. Scroll down and click Save.

7. **Firewall Rules Configuration**:
    1. Navigate to Firewall > Rules.

    ![image](https://github.com/user-attachments/assets/4f61630b-015c-4102-ae47-d94455f9cb4d)

    2. Click the Add button with the downward arrow.
    3. Under "edit Firewall Rule," select Protocol as ANY.
    4. Scroll down and click Save.

    ![image](https://github.com/user-attachments/assets/d86c3afa-951e-448f-82f6-e5d61d3587e8)


<a id="part5"></a>
### Part 5: Configuring Windows Server as a Domain Controller

1. **Objective**: Set up an Active Directory domain with a Windows 2019 Server as the Domain Controller and 2 Windows 10 machines, using The Cyber Mentor’s YouTube guide.

2. **Downloads**:
    - Download the [Windows 2019 Server Evaluation Copy](https://www.microsoft.com/en-us/evalcenter/evaluate-windows-server-2019).
    - Download the [Windows 10 Evaluation Copy](https://www.microsoft.com/en-us/evalcenter/evaluate-windows-10-enterprise).

3. **Windows Server Installation**:
    1. Install Windows Server in VMware with defaults.
    2. Skip the product key step by clicking Next.
    3. At the end of installation, change the Network Adapter to Vmnet3.
    4. Uncheck “Power on this virtual machine after creation”.
    5. Edit virtual machine settings and remove the Floppy drive.

    ![image](https://github.com/user-attachments/assets/c8afcf7b-eb5b-48b4-8752-47d094f41fc0)

    6. Power on the Virtual Machine and press any key.
    7. Click Next and Install Now.

    ![image](https://github.com/user-attachments/assets/6c3b417c-4d7b-4df9-98c8-3068af19bc57)

    8. Select Windows Server 2019 Standard Evaluation (Desktop Experience).
    9. Accept License Terms and choose Custom Install.

    ![image](https://github.com/user-attachments/assets/149b8ff8-84e5-4c8f-a3c2-a4541fa8c982)

    10. Click New, Apply, OK, and Next.
    11. Create a password after installation.

    ![image](https://github.com/user-attachments/assets/83a89a65-841f-41f9-9a5b-671455e5df4d)

    12. Rename the Domain Controller:
        - Navigate to Settings > Search for "pc name" > Rename PC > Restart Now.

    ![image](https://github.com/user-attachments/assets/77b523db-9d45-48e2-8ac9-3fde337ec6cc)

4. **Active Directory Domain Services Setup**:
    1. On Server Manager Dashboard, click Manage > Add Roles and Features.

    ![image](https://github.com/user-attachments/assets/2a610073-6167-4d12-9230-023bc1e8357c)

    2. Click Next until Server Roles menu.
    3. Select Active Directory Domain Services and Add Features.

    ![image](https://github.com/user-attachments/assets/e8e526d4-a401-4496-ba7c-fa6ecf20c82c)


    4. Continue clicking Next until Confirmation menu, then click Install.
    5. Click Close after installation.
    6. Click the flag with the yellow caution triangle:
        - Select “Promote this server to a domain controller”.
        - Add a new forest and specify a domain name.
        - Set a password and click Next until Prerequisites Check Menu.
        - Click Install and wait for reboot.
    7. Log back in after reboot.
    8. On Server Manager, click Manage > Add Roles & Features.
    9. Click Next until Server Roles.

    ![image](https://github.com/user-attachments/assets/3437ecf0-61e0-4431-a5ea-41e06649d619)

    10. Select Active Directory Certificate Services and Add Features.

    ![image](https://github.com/user-attachments/assets/e991532a-c4bd-4600-8af6-f3365d5899c5)

    11. Click Next until Confirmation menu.
    12. Check “Restart the destination server automatically if required”, select Yes, and Install.
    13. Click Close after installation.
    14. Click the flag with the yellow caution triangle:
        - Select “Configure Active Directory Certificate Services on the destination server”.
        - Click Next on Credentials.
        - Check Certification Authority on Role Services menu.
        - Click Next until Validity period menu and set it to 99 years.
        - Click Next until Confirmation menu, then select Configure.
    15. Manually restart the server for settings to take effect.

5. **User Creation**:
    1. In Server Manager, select Tools > Active Directory Users and Computers.

    ![image](https://github.com/user-attachments/assets/7d257f21-cf61-46bf-aafe-6dd203d9d8fd)

    2. Navigate to your Domain Name (PHOB.local) > Users.
    3. Right-click > New > User:
        - Enter First, Last, and User logon name.

        ![image](https://github.com/user-attachments/assets/4b82a014-8383-47d3-b0f8-c0c49b6086f9)

        - Set a password that never expires and select Finish.
        
        ![image](https://github.com/user-attachments/assets/7f4c778e-17fe-4550-b1f3-36160b6ed71d)

    4. Right-click the created user, select Copy, and create another user:
        - Enter a preferred logon name and set a password that never expires.

6. **Firewall Configuration**:
    1. Search for “Windows Defender Firewall”.
    2. Turn off the firewall for all networks.

    ![image](https://github.com/user-attachments/assets/29944e03-d52a-4b08-a2c4-bb4dee97dc67)


7. **Set pfSense as Default Gateway**:
    1. Navigate to Control Panel > Network and Internet > Network Connections.
    2. Enter the following configuration to use pfSense as the default gateway.

    ![image](https://github.com/user-attachments/assets/6cf13cd7-4498-4b61-96e7-6b7b277686b8)


<a id="part6"></a>
### Part 6: Configuring Windows 10 Desktop & Adding a User to the AD Domain

1. **Objective**: Add 2 Windows 10 desktops to the Domain and complete the Active Directory lab. Note: Having 2 desktops is not mandatory; one desktop is sufficient.

2. **Download**:
    - Download the [Windows 2019 Server Evaluation Copy](https://www.microsoft.com/en-us/evalcenter/evaluate-windows-server-2019) & [Windows 10 Evaluation Copy](https://www.microsoft.com/en-us/evalcenter/evaluate-windows-10-enterprise).

3. **Windows 10 Installation**:
    1. Install Windows 10 in VMware with defaults.
    2. Skip the product key step by clicking Next.
    3. Name the virtual machine after the first user set in your Domain Controller.
    4. Change the Network Adapter to Vmnet3 at the end of the installation.
    5. Uncheck “Power on this virtual machine after creation”.
    6. Edit virtual machine settings and remove the Floppy drive.
    7. Repeat the process for the second user.

4. **Configuration Steps**:
    1. Install Windows 10:
        - Accept license terms.
        - Choose Custom Install.
        - Select New > Apply > OK > Next.
    2. Configure Windows 10:
        - Select “I don’t have internet”.

        ![image](https://github.com/user-attachments/assets/a58f75aa-1618-4556-a869-a8f44e54d30f)

        - Continue with limited setup.
        - Set the first user and password (from the DC configuration).
        - Set security answers.
        - Uncheck all privacy settings and select Accept.

        ![image](https://github.com/user-attachments/assets/e065a50d-40e1-492c-89b5-f0e9574159b6)

        - Choose “Not Now” for Cortana.
    3. Set up the second desktop with the second user account credentials using the same configurations.

5. **Change PC Name**:
    1. Search “pc name” and change the PC Name according to the designated users.
    2. Restart the PC.

![image](https://github.com/user-attachments/assets/b2f63ed2-b916-4cea-aaf3-a8b48da5be7c)


<a id="part7"></a>
### Part 7: Joining the PCs to the Domain

1. **Network Adapter Settings**:
    1. Navigate to Network Adapter settings.
    2. Right-click on Ethernet0 and select properties.
    3. Select IPV4.
    4. Add an IP Address (192.168.2.21) and use 192.168.2.1 as the default gateway.
    5. Use 192.168.2.10 (VictimsNetwork) as the DNS Server.

    ![image](https://github.com/user-attachments/assets/53a6f9f4-423e-4479-bfc1-864eb37cc3a2)

2. **Join the Device to the Domain**:
    1. Search “domain” and select Access work or school.
    2. Select Connect > Join this device to local Active Directory Domain.
    3. Enter your domain name (e.g., PHOB.local).

    ![image](https://github.com/user-attachments/assets/4ad1603e-fa7e-4acf-b4bf-5276d4ba9be9)


3. **pfSense Configuration**:
    1. Navigate to Services > DHCP Server > VICTIMSNETWORK.
    2. Set DNS Server to the IP of your domain controller (192.168.2.10).
    3. Under Other Options, set Domain Name to the domain name (e.g., PHOB.local).

4. **Domain Join Process**:
    1. Enter the Username: Administrator and the password of your DC.
    2. Select Skip.
    3. Restart the PC.
    4. Repeat the process for the second machine.

    ![image](https://github.com/user-attachments/assets/5d0421b4-b374-4312-b8b9-73a39e863ba5)


<a id="part8"></a>
### Part 8: Installing Splunk on a Ubuntu Server

1. **Overview**:
    - Splunk is a widely used SIEM in cybersecurity for aggregating logs and datasets from various sources for easy searching, parsing, and indexing.
    - Recommended resources: [Splunk Fundamentals 1](https://www.splunk.com) and [Splunk Core Certified User Certification](https://www.splunk.com).

2. **Download and Install Ubuntu Server**:
    1. **Download**: [Ubuntu Server](https://ubuntu.com/download/server).
    2. **Create Virtual Machine**:
        - Use default settings.

        ![image](https://github.com/user-attachments/assets/3ee92649-f1be-42a9-b69a-a10d2968e1b7)

        - Before powering on, remove the CD/DVD drive (autoinst.iso) and Floppy drive (autoinst.flp) in Virtual Machine Settings.
    3. **Install Ubuntu Server**:
        - Use default settings.
        - Create a profile.
        - Optional: Install OpenSSH server and any additional services.
        - Remove the CD (ISO) during installation when prompted, then reboot the VM.

![image](https://github.com/user-attachments/assets/01adbbb4-9797-4e28-a9f3-de69038dbc01)


3. **Post-Installation Options**:
    - Access via SSH using AnalystVM.
    - Install a GUI (Ubuntu Desktop) on the Ubuntu Server.

4. **Install GUI on Ubuntu Server**:
    1. **Install tasksel**:
        ```sh
        sudo apt install tasksel
        ```
    2. **Install Ubuntu Desktop GUI**:
        ```sh
        sudo tasksel install ubuntu-desktop
        ```
    3. **Reboot the VM**:
        ```sh
        reboot
        ```

    ![image](https://github.com/user-attachments/assets/eaa7f87b-55bd-41d7-87a6-433c441a3d57)

5. **Installing Splunk**:
    1. **Navigate to Splunk.com**:
        - Click on “Free Splunk”.
        - Create an account or log in.
        - Under “Splunk Core Products” > Splunk Enterprise > Download Free 60-Day Trial.
        - Select the Linux package and download the .tgz file.
    2. **Open Terminal**:
        - Navigate to the downloads directory.
        - Untar the file:
        ```sh
        tar -xvf splunk-<version>.tgz
        ```
    3. **Start Splunk**:
        - Navigate to the `~/splunk/bin` directory.
        - Start Splunk instance:
        ```sh
        ./splunk start
        ```
        - Set an admin username and password.
    4. **Access Splunk**:
        - Open a browser and navigate to `http://splunk:8000`.
        - Log in with the configured username and password.


<a id="part9"></a>
### Part 9: Installing Universal Forwarder on Windows Server

1. **Objective**: Install the Splunk Universal Forwarder on a Windows Server to log activities on endpoints.

2. **Network Setup**:
    - Add the Vmnet6 network adapters to the Splunk adapter.
![image](https://github.com/user-attachments/assets/f1f512e8-9a70-49e9-b874-8eeae79bbfbc)


3. **Configure Receiving on Splunk Server**:
    1. Navigate to `Settings >> Forwarding and Receiving >> New Receiving Port`.
    2. Enter port `9997` and save.

4. **Create Index on Splunk Server**:
    1. Navigate to `Settings >> Indexes >> New index`.
    2. Name the index `wineventlog` and save.

5. **Install Universal Forwarder on Windows Server**:
    1. Download the Universal Forwarder.
    2. Run the installation:
        - Accept the License Agreement and click Next.
        
        ![image](https://github.com/user-attachments/assets/bf8309e7-1484-4674-824c-ba70901c715b)

        - Create a preferred username and password.
        - Enter the IP Address of your Splunk server and the default ports (8089 & 9997).
        - Complete the installation.

6. **Add Data in Splunk**:
    1. Navigate to `Settings >> Add Data`.
    2. Select "Forward".

    ![image](https://github.com/user-attachments/assets/80617b64-2231-460a-9abf-7f59b7368b25)

    3. Select the Domain Controller (Windows Server).
    4. Enter a Server Class Name (e.g., "Domain Controller") and click Next.
    5. Select Local Event Logs and choose your desired event logs.
    6. Select `wineventlog` as the index, review the configuration, and submit.

