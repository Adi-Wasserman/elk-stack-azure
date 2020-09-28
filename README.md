This is a TEST

I've set up an ELK stack server to monitor my cloud network. And deployed and configured the ELK stack with beats (data collection tools) including Filebeat and Metricbeat.

I initially created a virtual network, deployed a jump box running an Ansible Docker container, and used that container to configure VMs running a DVWA container.
Virtual networks, virtual machines, network security groups, load balancer

Monitoring to detect whatever you want: Log in attempts, file changes, etc/password changes, sensitive changes to files, adding new users, anything unusual on the system, when somebody opens files, changes to cron tabs




A summary the Security Group Rules for RedTeamSG:

Initially when setting up a Virtual Network a “Deny all rule” Inbound Security Rule should be put in place until the Project is ready to be launched with the correct security measures.

Source = “Any”
Source Port ranges = “Any”
Destination = “Any”
Action = “Deny”
Priority set to lowest value = 4096

SSH to the Jump Box from my Local Machine
Inbound Security Rule
Source = My Local Machine Public IP Address
Source Port Ranges = *
Destination IP Address = Jump Box Private IP Address 10.0.0.4
Destination Port = 22
Priority = 500

SSH from the Jump Box to the rest of the Virtual Network
Source = Jump Box Private IP Address 10.0.0.4
Source Port ranges = *
Destination = Virtual Networking
Destination Port range = 22
Priority = 501

Web traffic over port 80 through the Load Balancer
Source = My Local Machine Public IP Address
Source Port range = *
Destination = Virtual Networking
Destination Port Range = 80
Priority = 502

Allow TCP traffic over Port 5601 ELK Web server
Source = Any
Source Port range = *
Destination = Virtual Network
Destination Port Range = 5601
Priority = 300

Then delete deny all rule



| Name          | Function                              | Private IP Address   | Operating System |
|---------------|---------------------------------------|----------------------|
| Jump Box      | Yes/No                                | 10.0.0.1 10.0.0.2    |
| Web-1         | DVWA Virtual Machine 1                | 10.0.0.5             |
| Web-2         | DVWA Virtual Machine 1                |                      |
| Elk           | Monitors DVWAs and collects log data  |                      | 

