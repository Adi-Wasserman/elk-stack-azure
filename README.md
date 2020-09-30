Project Elk consists of building out a cloud network for a web appliction and setting up an ELK stack server to monitor its network traffic. A deployed and configured ELK stack includes the beats (data collection tools) Filebeat and Metricbeat.

Initially created a virtual network, deployed a jump box running an Ansible Docker container, and used that container to configure VMs running DVWA containers.
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

______________________________________

ELK Configuration:

Ansible was used to automate configuration of the ELK machine.  Ansible allows for easy deployment and management of containers through infrastructure as code. Playbooks can be developed and deployed, so that individual virtual machines do not have to be shut down and updated one at a time.  Many virtual machines’ configurations may be updated concurrently and deployed at once with Ansible.

- Create a Virtual Network for ELK
	-Add peering between the ELK network and the RedTeam network
- Create an ELK Virtual Machine on Azure with an SSH key through the Jump Box Ansible Container
- Download and configure the ELK Virtual Machine by adding the ELK Virtual Machine to the Ansible Host file and creating a YML ELK playbook file.
- Add an incoming rule on the Elk Server Security Group for incoming traffic to flow through port 5601

ELK yml file:

- name: Config elk VM with Docker
  hosts: elk
  remote_user: sysadmin
  become: true
  tasks:


- name: Use more memory
  sysctl:
    name: vm.max_map_count
    value: '262144'
    state: present
    reload: yes


The playbook should then install the following services:
•	docker.io
•	python3-pip
•	docker, which is the Docker Python pip module.
- name: Configure Elk VM with Docker
	  hosts: elkservers
	  remote_user: elk
	  become: true
	  tasks:
	    # Use apt module
	    - name: Install docker.io
	      apt:
	        update_cache: yes
	        name: docker.io
	        state: present
	

	      # Use apt module
	    - name: Install pip3
	      apt:
	        force_apt_get: yes
	        name: python3-pip
	        state: present
	

	      # Use pip module
	    - name: Install Docker python module
	      pip:
	        name: docker
	        state: present
	

	      # Use command module
	    - name: Increase virtual memory
	      command: sysctl -w vm.max_map_count=262144
	

	      # Use sysctl module
	    - name: Use more memory
	      sysctl:
	        name: vm.max_map_count
	        value: "262144"
	        state: present
	        reload: yes
	

	      # Use docker_container module
	    - name: download and launch a docker elk container
	      docker_container:
	        name: elk
	        image: sebp/elk:761
	        state: started
	        restart_policy: always
	        published_ports:
	          - 5601:5601
	          - 9200:9200
	          - 5044:5044

1. Run "sudo docker pull cyberxsecurity/ansible"
1. installs Trilogy created containers
2. Run "sudo docker run -ti cyberxsecurity/ansible:latest bash"
1. Creates container
3. Run "sudo docker list -a"
1. Note the name of the container
4. Run "sudo docker start [container name]"
5. Run "sudo docker attach [container name]"

____________________________________

Using the Playbook:

In order to use the playbook, the Ansible control node must be considered configured. After the Ansible control node is provisioned: 

SSH into the Ansible Container control node and follow the steps below:
- Copy the filebeat-playbook.yml file to /etc/ansible/files/.
- Update the filebeat-playbook.yml file to include the IP address of my ELK machine.
- Run the playbook ansible-playbook filebeat-playbook.yml, and navigate to the Filebeat installation page on the ELK server GUI to check that the installation worked as expected.
Run "curl localhost/setup.php" on both Web-1 and Web-2 to check installation worked as expected.


Editing Ansible files for access
1. In the Ansible container
1. Go the "/etc/ansible" directory
2. Open ansible.cfg file
1. Add "remote-user=[username]"
3. Open hosts file
1. Add "[webservers]" <-- actually type that exact string in the quotations. with the
brackets included
2. Add "[Private IP of Web-1]" (should be 10.0.0.5 if everything else went correctly)
3. Add "[Private IP of Web-2]" (should be 10.0.0.6 if everything else went correctly)
4. After each IP address, put a space and append "ansible_python_interpreter=/usr/bin/python3" to the end

15. Creating and Running YAML file
2. In the ansible container
1. Create "playbook.yml" and paste the .yaml file from Github 
2. Run "ansible-playbook /etc/ansible/playbook.yml"
1. May take a while for this command to finish running
2. Should be no red or error notifications. Yellow, or "changed", is fine.

In the /etc/ansible directory open and edit the ansible.cfg hosts file to add additional hosts and run playbooks on specific machines.  Add [ELK] in the ansible.cnfg file and specify each DVWA virtual machines’ private IP address.


_____________________________________

Navigate to URL below to check that the ELK server is running:

http://104.42.170.179:5601/app/kibana

http://[my.VM.IP]:5601/app/kibana

