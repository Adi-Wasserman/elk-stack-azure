Project Elk consists of building out a cloud network for a web appliction and setting up an ELK stack server to monitor its network traffic. A deployed and configured the ELK stack with beats (data collection tools) including Filebeat and Metricbeat.

I initially created a virtual network, deployed a jump box running an Ansible Docker container, and used that container to configure VMs running DVWA containers.
Virtual networks, virtual machines, network security groups, load balancer

Monitoring to detect whatever you want: Log in attempts, file changes, etc/password changes, sensitive changes to files, adding new users, anything unusual on the system, when somebody opens files, changes to cron tabs
______________________________________________

Description of the Topology:

The main purpose of this network is to expose a load-balanced and monitored instance of DVWA, the D*mn Vulnerable Web Application.

Load balancing ensures that the application will be highly redundant, in addition to restricting traffic to the network.
What aspect of security do load balancers protect? What is the advantage of a jump box?_

The Security Group allows access to the network only through the Jump Box or the Load Balancer.  This is a smaller attack surface and protects the Web-1 and Web-2 VMs that have the vital programs / data.  Load balancers prevents direct access to the Web boxes, which hold the sensitive data, and any possible exploits resulting from a direct connection.  Load balancers also distribute traffic across the Web servers / VMs to add redundancy in case of denial of service type attacks or others.  The Jump Box is an access point to connect to the rest of the network and virtual machines, while also preventing direct external access from the virtual network to valuable data on VMs.

Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the log files and system configuration.  Log in attempts, file changes, etc/password changes, sensitive changes to files, adding new users, anything unusual on the system, when somebody opens files


ELK supports special-purpose data collection modules called Beats.  Beats collect specific data of interest from specified machines rather than gathering all log data.
Filebeat collects data about the file system including which files changed and when.  Filebeat logs files including from those such as Apache, Microsoft Azure tools, Nginx web server, and MySQL databases. Filebeat has to be installed on the VMs in order to monitor them.

Metricbeat records machine metrics, such as uptime.  Metricbeat collects data from the operating system and services running on the server.


______________________________________________


A summary of the Security Group Rules for RedTeamSG:

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

