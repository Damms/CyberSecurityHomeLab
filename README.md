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


