## Prerequisites

* Ansible must be installed ( only in control machine, remember this is agentless)
* some remote Virtual machines to test, you can use vagrant to build them ( this article would help you get started)
* Make sure that the control machine and VM has SSH connectivity, recommend to enable SSH key-based authentication for efficiency  ( refer this article to enable SSH key based auth)

## What are Ansible ad hoc commands

> ad hoc is latin words refers to something done for a very precise and particular purpose.  As the word suggests, ansible ad hoc commands are written for a very particular task. Some of the ad hoc scenarios are given below

1. Just validate the uptime of 1 to 200 remote servers
2. Just get the disk space of remote hosts
3. Ping and validate if the server is alive and responds
4. shutdown multiple remote hosts at a single command

<p align="center"> <img src="https://github.com/lerndevops/ansible/blob/master/static/ad-hoc-syntax.PNG"> </p>

## ***below are real-time examples of ansible ad hoc commands*** 

#### ***Ansible Ad Hoc Command Examples***

#### ***We presume that you have set up SSH key based auth between the control machine and the hosts. If yes then there is no need to enter the credentials and command would be simple***


#### ***1) Validate the connection between Ansible control machine and host using ansible ping module***

```
ansible all -m ping -i ansible_hosts --user=devops   -- when SSH Keys Configured 
```
```
ansible all -m ping -i ansible_hosts --user=devops --ask-pass  -- when NO SSH keys Configured 
```

#### ***2) Get the uptime of remote hosts using ansible ad hoc command***

* Ansible provides two major modules to run the command over the host group or on the remote server.
* Which one to pick is not a big confusion if you know what are they and their capabilities

```
ansible all -m command -a uptime

ansible all -m shell -a uptime

ansible all -a uptime 
```

#### ***3) How to check the free memory or memory usage of  hosts using ansible ad hoc command***

```
ansible all -a "free -m" -i ansible_hosts
```

#### ***4) ansible ad hoc command to get physical memory allocated to the host***

```
ansible all -m shell -a "cat /proc/meminfo|head -2" 
```
 
#### ***5) ansible ad hoc command Execute a command as root user (sudo) on host***
 
```
ansible multi -m shell -a "cat /etc/passwd|grep -i devops" -b --ask-sudo-pass
```

#### ***6) ansible ad hoc command to Execute a command as a different user  (sudo su)***

```
ansible app -m file -a "path=/opt/oracle/binaries state=directory mode=0755" -i ansible_hosts -b --become-user=weblogic
```
 
#### ***7) Create a unix user group with ansible ad hoc command***

```
ansible app -b -m group -a "name=weblogic state=present" 
```
 
#### ***8) Create a unix user with ansible ad hoc command***

```
ansible app -m user -a "name=weblogic group=weblogic createhome=yes" -b
```
 
#### ***9) Create a Directory with 755 permission using ansible ad hoc command***

```
ansible app -m file -a "path=/opt/oracle state=directory mode=0755" -b
```

#### ***10) Create a file with 755 permission using ansible ad hoc commands***

```
ansible app -m file -a "path=/tmp/testfile state=touch mode=0755"
```
 
#### ***11) Change ownership of a file using ansible ad hoc command***

```
ansible app -m file -a "path=/opt/oracle group=weblogic owner=weblogic" -i ansible_hosts -b
```

#### ***12) how to check free disk space of hosts using ansible ad hoc commands***

```
ansible all -a "df -h"
```
 
#### ***13) ad hoc command to Install a package using yum command***

```
ansible web -b -m yum -a "name=httpd state=installed"
```
 
#### ***14) ad hoc command to Start or stop the service***

```
# To Start
ansible multi -b -m service -a "name=httod state=started enabled=yes"

# To Stop
ansible multi -b -m service -a "name=httpd state=stop enabled=yes"
```

#### ***15) Install and configure python Django application server with ansible ad hoc commands***

* These are set of commands you have to execute to install the Django application server and Mysql libraries. Here we are using easy_install which is an ansible module it helps to find the easy installation option from ansible galaxy

```
ansible app -b -m yum -a "name=MySQL-python state=present"
ansible app -b -m yum -a "name=python-setuptools state=present"
ansible app -b -m easy_install -a "name=django"
```

#### ***16) Managing Cron Job and Scheduling with Ansible ad hoc***

```
##Run the job every 15 minutes

ansible multi -b -m cron -a "name='daily-cron-all-servers' minute=*/15 job='/path/to/minute-script.sh'"

##Run the job every four hours

ansible multi -b -m cron -a "name='daily-cron-all-servers' hour=4 job='/path/to/hour-script.sh'"

##Enabling a Job to run at system reboot

ansible multi -b -m cron -a "name='daily-cron-all-servers' special_time=reboot job='/path/to/startup-script.sh'"

##Scheduling a Daily job

ansible multi -b -m cron -a "name='daily-cron-all-servers' special_time=daily job='/path/to/daily-script.sh'"

##Scheduling a Weekly job

ansible multi -b -m cron -a "name='daily-cron-all-servers' special_time=weekly job='/path/to/daily-script.sh'"
```
 
#### ***17) Running operations in the background asynchronous with Polling ansible***

* You can use AD-HOC commands to run the operations background using -B and poll the job in the frequent interval -P
* ansible ad-hoc command  to perform yum update and frequently poll and check the status of the job

```
ansible all -b -B 3600 -a "yum -y update"
ansible all -m async_status -a "jid=763350539037"
```

* By Default the polling interval is 10 seconds, but can modify it using -P option
* If you set -P as 0 it called as fire and forget, The job id would not be given and you cannot track the job using async_status as shown above.
* Rebooting the host in the background is the best example for fire and forget

```
ansible all -i inventory -b -B 1 -P 0 -m shell -a "sleep 5 && reboot"
```
