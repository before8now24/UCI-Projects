## Automated ELK Stack Deployment

The files in this repository were used to configure the network depicted below.

![image](https://drive.google.com/uc?export=view&id=1hvfKGUgyD1-mbscTAvzpoSd4n12kZHEo)

These files have been tested and used to generate a live ELK deployment on Azure. They can be used to either recreate the entire deployment pictured above. Alternatively, select portions of the playbook file may be used to install only certain pieces of it, such as Filebeat.

  Filebeat: https://github.com/before8now24/UCI-Projects/blob/master/Ansible/filebeat-playbook.yaml
  
  Metricbeat: https://github.com/before8now24/UCI-Projects/blob/master/Ansible/metricbeat-playbook.yaml
  
  Installing Elk: https://github.com/before8now24/UCI-Projects/blob/master/Ansible/install-elk.yaml
  
  Setting up Webservers: https://github.com/before8now24/UCI-Projects/blob/master/Ansible/webservers-playbook.yaml

This document contains the following details:
- Description of the Topology
- Access Policies
- ELK Configuration
  - Beats in Use
  - Machines Being Monitored
- How to Use the Ansible Build

### Description of the Topology

The main purpose of this network is to expose a load-balanced and monitored instance of DVWA, the D*mn Vulnerable Web Application.

Load balancing ensures that the application will be highly responsive, in addition to restricting access to the network.

- Load Balancers are crucial, as they protect against denial of service attacks (DDoS) by analyzing the traffic coming in and then determining what server to send the traffic to. Traffic is distributed evenly among the servers that are connected to it, preventing a singluar server from getting overloaded. It's also verycommon for load balancers to have a health probe that periodically checks a machine is working properly before sending traffic. If a server isn't working properly, the load balancer will divert traffic from the malfunctioning server until the issue is resolved. A jump box limits the access that the public has to your virtual network because in order to access the Jump-Box you must have the private IP of the VW, as well as having access to the "key.pem" used to create the instance. A jumpbox allows greater control over access to a virtual network and its contents.

Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the logs and system traffic

- Filebeat generates and monitors log files on the webservers, looking for changes and being capable of specifying when it occured. These log events are then forwarded to either Elasticsearch or Logstash for indexing.

- Metricbeat records metrics from the operating system and services running on the server. By using Elasticsearch or Logstash, you can visualize the metrics and statistics that Metricbeat generates from the OS and running services.

The configuration details of each machine may be found below.

| Name     | Function                      | IP Address    | Operating System               |
|----------|-------------------------------|---------------|--------------------------------|
| Jump Box | Gateway/Runs Docker w/Ansible | 172.31.42.218 | Linux Ubuntu 20.04 (t2.micro)  |
| WS-1     | Webserver used to run DVWA    | 172.31.41.216 | Linux Ubuntu 20.04 (t2.micro)  |
| WS-2     | Webserver used to run DVWA    | 172.31.31.183 | Linux Ubuntu 20.04 (t2.micro)  |
| Elk      | Run Elk Container and Kabana  | 172.31.5.195  | Linux Ubuntu 20.04 (t2.medium) |

### Access Policies

The machines on the internal network are not exposed to the public Internet. 

Only the Jump-Box machine can accept connections from the Internet via port 22. Access to this machine is only allowed from the following IP addresses:
68.4.189.35/32 (my IP) although you must have the key.pem used when creating this instance.

Webservers within the network can only be accessed from the Jump-Box.
172.31.42.218 (Jump-Box IP)

A summary of the access policies in place can be found in the table below.

| Name          | Publicly Accessible | Allowed IP Addresses |
|---------------|---------------------|----------------------|
| Jump Box      | Yes (via Port 22)   | 68.4.189.35/32       |
| WS-1          | No                  | 172.31.5.195         |
| WS-2          | No                  | 172.31.5.195         |
| Elk           | Yes (via Port 5601) | 68.3.189.35/32       |
| Load Balancer | Yes (via Port 80)   | 68.3.189.35/32       |

Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous because it made the whole process much more streamlined. It also allowed more control over what exactly is being installed or performed on the machine.

The playbook implements the following tasks:
- The first task of the elk playbook installs docker.io on the Elk VM
- Python is then installed on the Elk VM
- Elk requires more virtual memory so this task increases the memory to 262144
- This task tells the Elk machine use the 262144 as the amount of memory defined in the previous task
- Downloads, installs and executes the docker elk container on the Elk vm on restart so the elk container doesn't need to be manually started
- This enables docker on boot so you don't have to manually start docker when you turn your VM back on

The following screenshot displays the result of running `docker ps` after successfully configuring the ELK instance.

![image](https://drive.google.com/uc?export=view&id=1yDlVzG1VBOqg41i4Jz0w1dBhuy1Fozb6)

### Target Machines & Beats
This ELK server is configured to monitor the following machines:
WS-1: 172.31.41.216 ; WS-2: 172.31.31.183

We have installed the following Beats on these machines:
Filebeat and Metricbeat

These Beats allow us to collect the following information from each machine:
Filebeat collects log information about the file system and specifies which files have been changed and when a file was changed to either Elasticsearch or Logstash. If you wanted to see the output from Filebeat, you would connect to Kibana and check the logs for any changes that have been made to the file system over a specific or more general time interval. For example, you would specify the container, the beat and the argument you are looking for. You can further specify by date and time range. Metricbeat shows statistics for the every process running on your system including memory, CPU usage, file system, Network IO and disk IO statics. To view the data collected, you would navigate to Kibana (assuming that your VM is already running), select the system you'd like to review and from there can view the metrics of the system.

### Using the Playbook
In order to use the playbook, you will need to have an Ansible control node already configured. Assuming you have such a control node provisioned: 

SSH into the control node and follow the steps below:
- Copy the filebeat-config.yaml file to /etc/ansible/filebeat-config.yml 
- Update the filebeat-config.yml file to include host "172.31.5.195:9200" with username "elastic" and password "changeme" and setup.Kibana host to "172.31.5.195:5601".
- Run the playbook, and navigate to Kibana (Elk GUI interface) to check that the installation worked as expected.
- Run anisble-playbook filebeat-playbook.yaml --key-file "yourpem.key"


Please make sure to update the filebeat-config.yaml file by adding the Elk IP to the hosts portion of line 1105 & 1805. Also make sure all private IP addresses that need to be accessed need to be added to the hosts file located in /etc/ansible/hosts of the ansible container in order to allow connection. We added 3 IP addresses to this file: 2 were web servers (172.31.41.216 & 172.31.31.183) and an elk server (172.31.5.195). From there, in the playbook file, navigate to "hosts" at the top of the file to specify whether you want the playbook installed on Elk or the Web servers. In addition to this, we also have to specify the host to the ELK server for Filebeat so IP "172.31.5.195" is added to the config file to specify the location for installation.

The following URL was used to check that filebeat and metricbeat are running: http://http://52.88.116.246:5601/app/kibana

As a **Bonus**, provide the specific commands the user will need to run to download the playbook, update the files, etc.

Download ansible docker image: sudo docker pull cyberxsecurity/ansible

Start the ansible docker container with bash prompt: sudo docker run -ti cyberxsecurity/ansible bash

Run "ansible" to verify ansible is installed

View a list of containers that have ran on the instance and find the container name: docker container list -a

Copy the key into the container: sudo docker cp "yourkey.pem" <container name>:/etc/ansible

Start the container: sudo docker start "<container name>"
  
Connect the bash shell of the container: sudo docker attach "<container name>"

Before running the playbook, edit the config file host (located at /etc/ansible/hosts) and add the corresponding IPs for the webservers and/or Elk so it runs, move to the ansible folder:   cd /etc/ansible

Edit the hosts file and add the IP address of the second instance to the file after uncomenting "[webservers]"
nano /etc/hosts
[webservers]
172.31.24.41

Once this is done locate the anisble.cfg file then nano the file: cd etc/ansible/ansible.cfg | nano ansible.cfg

Edit the ansible.cfg file and add the user of the remote machine like the following
remote_user = ec2-user
  
Ping the second instance using the command: ansible -m ping all --key-file "youkey.pem"

Create a playbook file and call it apache-playbook.yaml
 
To run your playbook: ansible-playbook apache-playbook.yaml.yml --key-file "yourkey.pem"
