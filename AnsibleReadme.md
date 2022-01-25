### Ansible

Ansible is a software tool that provides simple but powerful automation for cross-platform computer support. It is primarily intended for IT professionals, who use it for application deployment, updates on workstations and servers, cloud provisioning, configuration management, intra-service orchestration, and nearly anything a systems administrator does on a weekly or daily basis. Ansible doesn't depend on agent software and has no additional security infrastructure, so it's easy to deploy.

Ansible is an open-source software provisioning, configuration management, and application-deployment tool enabling infrastructure as code.

### How Ansible works


In Ansible, there are two categories of computers: the control node and managed nodes. The control node is a computer that runs Ansible. There must be at least one control node, although a backup control node may also exist. A managed node is any device being managed by the control node.

Ansible works by connecting to nodes (clients, servers, or whatever you're configuring) on a network, and then sending a small program called an Ansible module to that node. Ansible executes these modules over SSH and removes them when finished. The only requirement for this interaction is that your Ansible control node has login access to the managed nodes. SSH keys are the most common way to provide access, but other forms of authentication are also supported.



Every YAML file optionally starts with “---” and ends with “...”.

login users

	awk -F":" '{print "Login:" $1 "\tName:" $5 "\tHome:" $6}' /etc/passwd
	
==================================================================

    i-0f96027db0b7cf336: ec2-13-127-142-204.ap-south-1.compute.amazonaws.com		172.31.30.107	ansible-dbserver.server
    
    i-0022e78ca75778b38: ec2-13-233-28-197.ap-south-1.compute.amazonaws.com			172.31.19.54	ansible-controller.server
    
    i-0a5a0a09be9d1ac75: ec2-13-233-225-84.ap-south-1.compute.amazonaws.com			172.31.27.160	ansible-appserver.server
	
	
[dbservers]
ansible-dbserver.server

[appservers]
ansible-appserver.server


==================================================================	

https://www.youtube.com/watch?v=ChSCEXQHfI4&index=2&list=PLLsor6GJ_BEEC9jUSc760iqaOx6u5lqRA

https://www.youtube.com/watch?v=lRwGkO3PtB8&list=PLLsor6GJ_BEEC9jUSc760iqaOx6u5lqRA&index=3

https://www.slideshare.net/PJishnuJaykumar/ansible-overview-system-administration-and-maintenance


Ansible-control
dbserver
appserver
username -  ansadm
=========================================================================
Ansible is part of the Extra Packages for Enterprise Linux (EPEL) repository so you need to install epel-release package first

	$ sudo yum install epel-release

The repo should now be visible in the repo list

	$ sudo yum repolist

So now you can go ahead and install ansible

	$ sudo yum install ansible

=========================================================================
On Ansible-control
	yum repolist
	yum install ansible
	rpm -qa | grep ansible (return ansible name with version)
	rpm -ql name with version |more ,will display where ansible installed and all the files related to ansible
	/etc/ansible
	/etc/ansible/ansible.config
	/etc/ansible/hosts
	/etc/ansible/roles
	/usr/bin/ansible
	/usr/bin/ansible-console
	/usr/bin/ansible-doc
	/usr/bin/ansible-galaxy
	/usr/bin/ansible-playbook
	/usr/bin/ansible-pull
	/usr/bin/ansible-vault
	etc....

Now, add ansadm user
	>>useradd -d /home/ansadm  -m ansadm
	>>passwd ansadm
set new password

change password to never expire

	>>passwd -x -1 ansadm		(-1 is ONE)
------------------------------------------------------------------------------------------------------------------------------------------------------
Now generate ssh keys on Ansible-control
	>>ssh-keygen -t rsa		(/home/ansadm/.ssh/id_rsa, /home/ansadm/.ssh/id_rsa.pub)
	>>cat /home/ansadm/.ssh/id_rsa.pub			(LONG_SSH_KEY)

	Now change user login to new user - ansadm
	>>su - ansadm

--------------------------------------------------
Now on dbserver
	> su - ansadm
	>>mkdir .ssh directory(mkdir .ssh)
	>>chmod 777 .ssh
	>>chown ansadm:ansadm .ssh/
	>>cd .ssh
	>> vi authorized_keys (and whole LONG_SSH_KEY in it, save and quit)
	chown ansadm:ansadm authorized_keys
	chmod 600 authorized_keys


ssh -i id_rsa ansadm@172.31.30.107		(from /home/ansadm/.ssh)
------------------------------------------------

[root@ip-172-31-19-54 ec2-user]# ansible --version

ansible 2.6.8

  config file = /etc/ansible/ansible.cfg
  
  configured module search path = [u'/root/.ansible/plugins/modules', u'/usr/share/ansible/plugins/modules']
  
  ansible python module location = /usr/lib/python2.6/site-packages/ansible
  
  executable location = /usr/bin/ansible
  python version = 2.6.9 (unknown, Nov  2 2017, 19:21:21) [GCC 4.8.5 20150623 (Red Hat 4.8.5-11)]

[root@ip-172-31-19-54 ec2-user]# which ansible
/usr/bin/ansible
[root@ip-172-31-19-54 ec2-user]#

====================================================
add ips and name of the servers in /etc/hosts on Ansible-control

Create an entry for all servers in etc/hosts file as shown below

vim etc/hosts


cat /etc/hosts
90.10.10.11	Ansible-control
90.10.10.12	dbserver
90.10.10.13	appserver

----------------
check dbserver connecting from Ansible-control
	>>ssh dbserver		(login should happen,without asking password)
exit
----------------------

change user and group on /etc/ansible (currently set to root root)
	chown -R ansadm:ansadm /etc/ansible
ls -lrt
------------------------

now add servers names in ansible hosts file (on Ansible-control)
>>vi /etc/ansible/hosts		(everything will be commented #), now add your server groups,and server names
	[db_servers] <---This is group name in []-->
	dbserver1
	dbserver2
	dbserver3
	[app_servers]	<---This is group name in []-->
	webserver1
	webserver2
	webserver3
------------------------
now test something, link ping all servers from 'Ansible-control'
	>>ansible db_servers -m ping 		(-m means Module Name ,   ansible-doc -l |more will list all module, ansible-doc -s yum )
	>>ansible all -m ping -0			-o for output in single line)

	>>ansible all -m shell "uname -a;df -k"

-----------------

add user into sudo 
>>visudo -f /etc/sudoers
in the end, add below 
## ANSIBLE ADMIN user
ansadm ALL=NOPASSWD: ALL


===========
[root@ansible ec2-user]# hostname

ansible.controller.server

[root@ansible ec2-user]# useradd -d /home/ansadm  -m ansadm

[root@ansible ec2-user]# passwd ansadm

Changing password for user ansadm.

New password:

BAD PASSWORD: The password is shorter than 8 characters

Retype new password:

passwd: all authentication tokens updated successfully.

[root@ansible ec2-user]#
=====================


------------
[ansadm@ansible .ssh]$ ansible all -m ping

The authenticity of host 'ansible-dbserver.server (172.31.30.107)' can't be established.

ECDSA key fingerprint is SHA256:vNFqISbSJoefHXbofuLuXJfwRubFSZxD5hqOEVbFczw.

ECDSA key fingerprint is MD5:17:6f:9b:92:48:56:85:7c:94:ca:09:79:4f:14:f2:2d.

Are you sure you want to continue connecting (yes/no)? ansible-appserver.server | SUCCESS => {
    "changed": false,
    "ping": "pong"
}

ansible-dbserver.server | UNREACHABLE! => {
    "changed": false,
    "msg": "Failed to connect to the host via ssh: Host key verification failed.\r\n",
    "unreachable": true
}
[ansadm@ansible .ssh]$ ansible all -m ping

The authenticity of host 'ansible-dbserver.server (172.31.30.107)' can't be established.

ECDSA key fingerprint is SHA256:vNFqISbSJoefHXbofuLuXJfwRubFSZxD5hqOEVbFczw.

ECDSA key fingerprint is MD5:17:6f:9b:92:48:56:85:7c:94:ca:09:79:4f:14:f2:2d.

Are you sure you want to continue connecting (yes/no)? ansible-appserver.server | SUCCESS => {
    "changed": false,
    "ping": "pong"
}

yes

ansible-dbserver.server | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
[ansadm@ansible .ssh]$ ansible all -m ping
ansible-appserver.server | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
ansible-dbserver.server | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
[ansadm@ansible .ssh]$

========================================================================

cat /etc/ansible/hosts
[dbservers]
ansible-dbserver.server

[appservers]
ansible-appserver.server

-------------
[ansadm@ansible .ssh]$ ansible dbservers -m ping
ansible-dbserver.server | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
[ansadm@ansible .ssh]$

[ansadm@ansible .ssh]$ ansible all -m ping -o
ansible-appserver.server | SUCCESS => {"changed": false, "ping": "pong"}
ansible-dbserver.server | SUCCESS => {"changed": false, "ping": "pong"}
[ansadm@ansible .ssh]$

----------------
check os name and disk space on all servers

[ansadm@ansible .ssh]$ ansible all -m shell -a  "uname -a;df -k"

ansible-appserver.server | SUCCESS | rc=0 >>
Linux ansible-appserver.server 4.14.77-70.59.amzn1.x86_64 #1 SMP Mon Nov 12 22:02:45 UTC 2018 x86_64 x86_64 x86_64 GNU/Linux
Filesystem     1K-blocks    Used Available Use% Mounted on
devtmpfs          494152      60    494092   1% /dev
tmpfs             504716       0    504716   0% /dev/shm
/dev/xvda1      10188068 1106608   8981212  11% /

ansible-dbserver.server | SUCCESS | rc=0 >>
Linux ansible-dbserver.server 4.14.88-72.76.amzn1.x86_64 #1 SMP Mon Jan 7 19:47:07 UTC 2019 x86_64 x86_64 x86_64 GNU/Linux
Filesystem     1K-blocks    Used Available Use% Mounted on
devtmpfs          494132      60    494072   1% /dev
tmpfs             504716       0    504716   0% /dev/shm
/dev/xvda1      10188068 2341848   7745972  24% /

[ansadm@ansible .ssh]$
----------------------------------
[ansadm@ansible tmp]$ ansible all -m copy -a "src=/tmp/ansibleTestFile dest=/tmp/ansibleTestFile"
ansible-appserver.server | SUCCESS => {
    "changed": true,
    "checksum": "76f330712870c9575eaabe92afc6a441767a73a4",
    "dest": "/tmp/ansibleTestFile",
    "gid": 501,
    "group": "ansadm",
    "md5sum": "08a4593ca191c68e00b817b7f0ec91a2",
    "mode": "0664",
    "owner": "ansadm",
    "size": 59,
    "src": "/home/ansadm/.ansible/tmp/ansible-tmp-1547356497.82-96353921393191/source",
    "state": "file",
    "uid": 501
}
ansible-dbserver.server | SUCCESS => {
    "changed": true,
    "checksum": "76f330712870c9575eaabe92afc6a441767a73a4",
    "dest": "/tmp/ansibleTestFile",
    "gid": 501,
    "group": "ansadm",
    "md5sum": "08a4593ca191c68e00b817b7f0ec91a2",
    "mode": "0664",
    "owner": "ansadm",
    "size": 59,
    "src": "/home/ansadm/.ansible/tmp/ansible-tmp-1547356497.83-270049117418124/source",
    "state": "file",
    "uid": 501
}
[ansadm@ansible tmp]$



========================================================================
RPM Commands in Linux with Examples. RPM(Redhat Package Manager) is a command line package management utility used for installing, uninstalling, updating, querying and verifying software packages

rpm {-i|--install} [install-options] PACKAGE_FILE

How to query all installed package		-		sudo rpm -qa
How to query particular package			-		sudo rpm -qa | grep GeoIP


======================
Commands

ansible localhost -m debug -a 'var=groups'			(lists groups)

=========================================================================
Let’s create a simple playbook that will install Nginx and a MySQL server on the managed hosts that we had already defined in the inventory file. To be more precise, we want Nginx installed on hosts in the webservers group and a MySQL server installed on hosts in the dbservers group.

$ vi playbook.yml

	---
	- hosts: webservers
	  gather_facts: yes
	  become_user: root
	  tasks:
	  - name: Install Nginx
	    apt: pkg=nginx state=present
	    notify:
	    - restart nginx
	  - name: Enable Nginx during boot
	    service: name=nginx state=started enabled=yes
	  handlers:
	    - name: restart nginx
	      service: name=nginx state=restarted

	- hosts: dbservers
	  become_user: root
	  tasks:
	  - name: Install mysql
	    apt: pkg=mysql-server state=present
    

The hosts tells ansible on which hosts to run the tasks. The above playbook includes two host groups from the inventory file. The tasks for webservers group are to install Nginx and enable Nginx during boot, and the dbservers group includes a single task to install MySQL.

The become_user in both the host section tells ansible to use sudo to run the tasks.

The gather_facts option gathers information about managed hosts such as distribution, OS family, and more. In ansible terminology, this information is known as FACTS.

The handlers section restarts Nginx when Ansible gets notified that Nginx has been installed.

A handler is the same as a task, but it will be executed when called by another task. It is like an event-driven system. A handler will run a task only when it is called by an event it listens for.

Now run the above playbook using ansible-playbook. Append the name of the user from a remote managed host in the command using -u switch.

$ ansible-playbook playbook.yml -u USER

======================================

	---
	- hosts: appservers
	  user: ansadm
	  become: yes
	  become_method: sudo
	  tasks:
	   - name: 1.Install Latest Version of HTTP/Apache RPMs
	     yum: name=httpd state=present
	   - name: 2.Start httpd service
	     command: name=httpd state=started enabled=yes
	   - name: 3.Copy the standard index.html file
	     copy: src=/tmp/index.html dest=/var/www/html/index.html mode=0664
	   - name: 4.Add apache iptable rule
	     command: /sbin/iptables -I INPUT 1 -p tcp --dport http -j ACCEPT -m comment --comment "Apache on Port 80"
	   - name: Save iptable rule
	     command: iptable-save
	 
=====================================
[ansadm@ansible ec2-user]$ ansible --help
Usage: ansible <host-pattern> [options]

Define and run a single task 'playbook' against a set of hosts

Options:
	  -a MODULE_ARGS, --args=MODULE_ARGS
				module arguments
	  --ask-vault-pass      ask for vault password
	  -B SECONDS, --background=SECONDS
				run asynchronously, failing after X seconds
				(default=N/A)
	  -C, --check           don't make any changes; instead, try to predict some
				of the changes that may occur
	  -D, --diff            when changing (small) files and templates, show the
				differences in those files; works great with --check
	  -e EXTRA_VARS, --extra-vars=EXTRA_VARS
				set additional variables as key=value or YAML/JSON, if
				filename prepend with @
	  -f FORKS, --forks=FORKS
				specify number of parallel processes to use
				(default=5)
	  -h, --help            show this help message and exit
	  -i INVENTORY, --inventory=INVENTORY, --inventory-file=INVENTORY
				specify inventory host path or comma separated host
				list. --inventory-file is deprecated
	  -l SUBSET, --limit=SUBSET
				further limit selected hosts to an additional pattern
	  --list-hosts          outputs a list of matching hosts; does not execute
				anything else
	  -m MODULE_NAME, --module-name=MODULE_NAME
				module name to execute (default=command)
	  -M MODULE_PATH, --module-path=MODULE_PATH
				prepend colon-separated path(s) to module library
				(default=[u'/home/ansadm/.ansible/plugins/modules',
				u'/usr/share/ansible/plugins/modules'])
	  -o, --one-line        condense output
	  --playbook-dir=BASEDIR
				Since this tool does not use playbooks, use this as a
				subsitute playbook directory.This sets the relative
				path for many features including roles/ group_vars/
				etc.
	  -P POLL_INTERVAL, --poll=POLL_INTERVAL
				set the poll interval if using -B (default=15)
	  --syntax-check        perform a syntax check on the playbook, but do not
				execute it
	  -t TREE, --tree=TREE  log output to this directory
	  --vault-id=VAULT_IDS  the vault identity to use
	  --vault-password-file=VAULT_PASSWORD_FILES
				vault password file
	  -v, --verbose         verbose mode (-vvv for more, -vvvv to enable
				connection debugging)
	  --version             show program's version number and exit

  Connection Options:
    control as whom and how to connect to hosts

	    -k, --ask-pass      ask for connection password
	    --private-key=PRIVATE_KEY_FILE, --key-file=PRIVATE_KEY_FILE
				use this file to authenticate the connection
	    -u REMOTE_USER, --user=REMOTE_USER
				connect as this user (default=None)
	    -c CONNECTION, --connection=CONNECTION
				connection type to use (default=smart)
	    -T TIMEOUT, --timeout=TIMEOUT
				override the connection timeout in seconds
				(default=10)
	    --ssh-common-args=SSH_COMMON_ARGS
				specify common arguments to pass to sftp/scp/ssh (e.g.
				ProxyCommand)
	    --sftp-extra-args=SFTP_EXTRA_ARGS
				specify extra arguments to pass to sftp only (e.g. -f,
				-l)
	    --scp-extra-args=SCP_EXTRA_ARGS
				specify extra arguments to pass to scp only (e.g. -l)
	    --ssh-extra-args=SSH_EXTRA_ARGS
				specify extra arguments to pass to ssh only (e.g. -R)

  Privilege Escalation Options:
    control how and which user you become as on target hosts

	    -s, --sudo          run operations with sudo (nopasswd) (deprecated, use
				become)
	    -U SUDO_USER, --sudo-user=SUDO_USER
				desired sudo user (default=root) (deprecated, use
				become)
	    -S, --su            run operations with su (deprecated, use become)
	    -R SU_USER, --su-user=SU_USER
				run operations with su as this user (default=None)
				(deprecated, use become)
	    -b, --become        run operations with become (does not imply password
				prompting)
	    --become-method=BECOME_METHOD
				privilege escalation method to use (default=sudo),
				valid choices: [ sudo | su | pbrun | pfexec | doas |
				dzdo | ksu | runas | pmrun | enable | machinectl ]
	    --become-user=BECOME_USER
				run operations as this user (default=root)
	    --ask-sudo-pass     ask for sudo password (deprecated, use become)
	    --ask-su-pass       ask for su password (deprecated, use become)
	    -K, --ask-become-pass
				ask for privilege escalation password

Some modules do not make sense in Ad-Hoc (include, meta, etc)
[ansadm@ansible ec2-user]$

======================	 


Creating a User in Ansible

	---
	 - name: Create a login user
	     user:
	      name: fideloper
	      password: '???'
	      groups: # Empty by default, here we give it some groups
	       - docker
	       - sudo
	      state: present
	      shell: /bin/bash       # Defaults to /bin/bash
	      system: no             # Defaults to no
	      createhome: yes        # Defaults to yes
	      home: /home/fideloper  # Defaults to /home/<username>


Generating Password Strings

The one thing I left off here is how to make a valid password string. You can't put a plaintext password into Ansible. Instead you need to generate one!

The easiest way to do this is with the mkpasswd command:

mkpasswd --method=sha-512

This will prompt you for the plaintext password and will give you a hashed password string you can paste into the task definition. For password secret, I get hash 
	
	$6$F4NWXRFtSdCi8$DsB5vvMJYusQhSbvGXrYDXL6Xj37MUuqFCd4dGXdKd6NyxT3lpdELN07/Kpo7EjjWnm9zusFg/LLFv6oc.ynu/
--------------------------------------------------------------------------------------------------------------------------
	---
	 - name: Create a login user
	     user:
	      name: fideloper
	      password: '$6$F4NWXRFtSdCi8$DsB5vvMJYusQhSbvGXrYDXL6Xj37MUuqFCd4dGXdKd6NyxT3lpdELN07/Kpo7EjjWnm9zusFg/LLFv6oc.ynu/'
	      groups: docker, sudo   # Empty by default.
	      state: present
	      shell: /bin/bash       # Defaults to /bin/bash
	      system: no             # Defaults to no
	      createhome: yes        # Defaults to yes
	      home: /home/fideloper  # Defaults to /home/<username>
======================================================================================================================================================
https://docs.ansible.com/ansible/latest/user_guide/playbooks_intro.html

listing users and group in linux

	awk -F: '{ print $1}' /etc/passwd
	awk -F: '{ print $1}' /etc/group
	echo "groups:" ; for i in $(id -Gn);do echo "  - $i" ;done


	 awk -F":" '{print "Login:" $1 "\tName:" $5 "\tHome:" $6}' /etc/passwd
 
 
Login:ansadm    Name:   Home:/home/ansadm
Login:ansanoo   Name:   Home:/home/ansanoo
[root@ansible-dbserver ec2-user]#


	  working playbook
[ansadm@ansible tmp]$ ansible-playbook createuser.yml --syntax-check

playbook: createuser.yml
[ansadm@ansible tmp]$ cat createuser.yml
	---
	- hosts: dbservers
	  remote_user: root
	  tasks:
	    - name: test connection
	      ping:
	      remote_user: yourname
[ansadm@ansible tmp]$

================================================================

playbook: createuser.yml
	[ansadm@ansible tmp]$ cat createuser.yml
		---
		- hosts: dbservers
		  become: yes
		  become_user: root
		  become_method: sudo
		  tasks:
		    - name: create user account
		      ping:
		      remote_user: root
[ansadm@ansible tmp]$

==========

working ansible playbook

[ansadm@ansible tmp]$ cat a.yml
	---
	- hosts: dbservers
	  become: yes
	  become_method: sudo
	  tasks:
	    - name: Creating 'ansanoo' user on dbservers.
	      user:
		name: ansanoo
		password: 'sdsdeRsFF2ym'
		group: ansadm
		system: no
		createhome: yes
		home: /home/ansanoo
		state: present
		shell: /bin/bash

[ansadm@ansible tmp]$
[ansadm@ansible tmp]$ ansible-playbook a.yml

PLAY [dbservers] ********************************************************************************************************************************************

TASK [Gathering Facts] **************************************************************************************************************************************
ok: [ansible-dbserver.server]

TASK [Creating 'ansanoo' user on dbservers.] ****************************************************************************************************************
changed: [ansible-dbserver.server]

PLAY RECAP **************************************************************************************************************************************************
ansible-dbserver.server    : ok=2    changed=1    unreachable=0    failed=0

-------------------------------
To see what hosts would be affected by a playbook before you run it, you can do this:

ansible-playbook playbook.yml --list-hosts

while installing httpd, you can copy and index.html file from local to server at /var/www/html path

[ansadm@ansible tmp]$ vi InstallApache.yml
[ansadm@ansible tmp]$ ansible-playbook InstallApache.yml --syntax-check

playbook: InstallApache.yml
[ansadm@ansible tmp]$ ansible-playbook InstallApache.yml --syntax-checkca^C
[ansadm@ansible tmp]$ cat InstallApache.yml
	---
	- hosts: dbservers
	  gather_facts: yes
	  remote_user: root
	  become: yes
	  become_method: sudo
	  vars:
	    http_port: 80
	    max_clients: 200
	  tasks:
	    - name: Install Apache on DB Servers.
	      yum:
		name: httpd
		state: latest
	      notify:
	      - restart apache
	    - name: Ensure apache is running.
	      service:
		name: httpd
		state: started
	  handlers:
	    - name: Restart Apache.
	      service:
		 name: httpd
		 state: restarted

[ansadm@ansible tmp]$ ansible-playbook InstallApache.yml --syntax-check

playbook: InstallApache.yml
[ansadm@ansible tmp]$
---------------------------------
Limit to one or more hosts

This is required when one wants to run a playbook against a host group, but only against one or more members of that group.

Limit to one host

ansible-playbook playbooks/PLAYBOOK_NAME.yml --limit "host1"

Limit to multiple hosts

ansible-playbook playbooks/PLAYBOOK_NAME.yml --limit "host1,host2"

### Working with Ansible Register Variables	
	
Ansible registers are used when you want to capture the output of a task to a variable. You can then use the value of these registers for different scenarios like a conditional statement, logging etc.

The variables will contain the value returned by the task. The common return values are documented in Ansible docs. Some of the modules like shell, command etc. have module specific return values.	

Each registered variables will be valid on the remote host where the task was run for the rest of the playbook execution.
	
		- hosts: all
		  tasks:
		  - name: Ansible register variable basic example
		    shell: "find *.txt"
		    args:
		      chdir: "/Users/mdtutorials2/Documents/Ansible"
		    register: find_output

		  - debug:
		      var: find_output

		output
		======

		ok: [localhost] => {
		    "find_output": {
			"changed": true, 
			"cmd": "find *.txt", 
			"delta": "0:00:00.008597", 
			"end": "2017-09-30 15:07:15.940235", 
			"rc": 0, 
			"start": "2017-09-30 15:07:15.931638", 
			"stderr": "", 
			"stderr_lines": [], 
			"stdout": "check.txt\ncheck2.txt", 
			"stdout_lines": [
			    "check.txt", 
			    "check2.txt"
			]
		    }
		}

You can select the individual parameter from the above output by appending the name with the return value. For example, if you need only the file names then you can use find_output.stdout.
	
		- debug:
		    var: find_output.stdout

		output
		======
		"find_output.stdout": "check.txt\ncheck2.txt"
===============================================================================================


http://ec2-13-233-109-212.ap-south-1.compute.amazonaws.com
[ansadm@ansible tmp]$ cat InstallApache.yml
---
	- hosts: all
	  gather_facts: yes
	  #remote_user: root
	  become: yes
	  become_method: sudo
	  become_user: root
	  vars:
	    http_port: 80
	    max_clients: 200
	  tasks:
	    - name: Install Apache on DB Servers.
	      yum:
		name: httpd
		state: latest
	      notify:
	      - restart apache
	    - name: Ensure apache is running.
	      service:
		name: httpd
		state: started
	  handlers:
	    - name: restart apache.
	      service:
		 name: httpd
		 state: restarted
[ansadm@ansible tmp]$

Handlers

Sometimes you want a task to run only when a change is made on a machine. For example, you may want to restart a service if a task updates the configuration of that service, but not if the configuration is unchanged. Ansible uses handlers to address this use case. Handlers are tasks that only run when notified

By default, handlers run after all the tasks in a particular play have been completed. This approach is efficient, because the handler only runs once, regardless of how many tasks notify it. For example, if multiple tasks update a configuration file and notify a handler to restart Apache, Ansible only bounces Apache once to avoid unnecessary restarts.	
	
	
---------------------------------------------------------------------


[ansadm@ansible tmp]$ ansible-playbook InstallApache.yml

PLAY [all] **************************************************************************************************************************************************

TASK [Gathering Facts] **************************************************************************************************************************************
ok: [ansible-dbserver.server]
fatal: [ansible-appserver.server]: UNREACHABLE! => {"changed": false, "msg": "Failed to connect to the host via ssh: ssh: connect to host ansible-appserver.server port 22: No route to host\r\n", "unreachable": true}

TASK [Install Apache on DB Servers.] ************************************************************************************************************************
ERROR! The requested handler 'restart apache' was not found in either the main handlers list nor in the listening handlers list
[ansadm@ansible tmp]$ vi InstallApache.yml
[ansadm@ansible tmp]$ vi InstallApache.yml
[ansadm@ansible tmp]$ ansible-playbook InstallApache.yml

PLAY [all] **************************************************************************************************************************************************

TASK [Gathering Facts] **************************************************************************************************************************************
ok: [ansible-dbserver.server]
fatal: [ansible-appserver.server]: UNREACHABLE! => {"changed": false, "msg": "Failed to connect to the host via ssh: ssh: connect to host ansible-appserver.server port 22: No route to host\r\n", "unreachable": true}

TASK [Install Apache on DB Servers.] ************************************************************************************************************************
ok: [ansible-dbserver.server]

TASK [Ensure apache is running.] ****************************************************************************************************************************
changed: [ansible-dbserver.server]
        to retry, use: --limit @/tmp/InstallApache.retry

PLAY RECAP **************************************************************************************************************************************************
ansible-appserver.server   : ok=0    changed=0    unreachable=1    failed=0
ansible-dbserver.server    : ok=3    changed=1    unreachable=0    failed=0


Testing :   curl http://public_name_of_db_server:80/

ftp> lcd Desktop
Local directory now C:\Documents and Settings\olga\Desktop.
ftp> lcd Web
Local directory now C:\Documents and Settings\olga\Desktop\Web.
ftp> open cunix
Connected to cunix.cc.columbia.edu.
220 Cunix FTP server (Version 5.60) ready.
User (cunix.cc.columbia.edu:(none)): olga
331 Password required for olga.
Password: (type your password here)
230 User olga logged in.
ftp> cd public_html   (That's "public_html" in case you can't see the underscore)
ftp> binary
ftp> put index.html
200 PORT command successful.
150 Opening ASCII mode data connection for index.html.
226 Transfer complete.
ftp: 285 bytes sent in 0.00Seconds 285000.00Kbytes/sec.
ftp> site chmod 664 index.html
200 CHMOD command successful
ftp> bye

https://www.youtube.com/watch?v=SgxtxzQJM4w

uninstall apache


yum remove httpd* alone cant remove httpd completely. Follow the steps as shown below.

    Use yum list installed "httpd*" to list all the httpd packages
    Now use yum list installed "mod_" to list all modules that helps httpd.
    Remove all the listed packages with yum remove command.
    Once its removed, delete the user and group called "apache" .
    Followed by apache path rm -rf /var/www /etc/httpd /usr/lib/httpd

Thus we have completely removed httpd.
-----------------------------------------------------------------------
Roles

under roles apache is created 

/etc/ansible/roles/apache
[ansadm@ansible apache]$ ls -lrt
total 36
drwxrwxr-x 2 ansadm ansadm 4096 Jan 17 10:42 templates
-rw-rw-r-- 1 ansadm ansadm 1328 Jan 17 10:42 README.md
drwxrwxr-x 2 ansadm ansadm 4096 Jan 17 10:42 files
drwxrwxr-x 2 ansadm ansadm 4096 Jan 17 10:42 tests
drwxrwxr-x 2 ansadm ansadm 4096 Jan 17 10:42 vars
drwxrwxr-x 2 ansadm ansadm 4096 Jan 17 10:42 handlers
drwxrwxr-x 2 ansadm ansadm 4096 Jan 17 10:42 defaults
drwxrwxr-x 2 ansadm ansadm 4096 Jan 17 10:42 tasks
drwxrwxr-x 2 ansadm ansadm 4096 Jan 17 10:42 meta
[ansadm@ansible apache]$

[ansadm@ansible roles]$ tree
.
└── apache
    ├── defaults
    │   └── main.yml
    ├── files
    ├── handlers
    │   └── main.yml
    ├── meta
    │   └── main.yml
    ├── README.md
    ├── tasks
    │   └── main.yml
    ├── templates
    ├── tests
    │   ├── inventory
    │   └── test.yml
    └── vars
        └── main.yml

9 directories, 8 files
========================================================================
zip folder in unix
zip -r roles .
-rw-rw-r-- 1 ansadm ansadm 15153 Jan 17 14:16 roles.zip
copying this zip file to local from aws linux server

scp -i GoluAwsKeyPair.pem ec2-user@ec2-13-233-231-216.ap-south-1.compute.amazonaws.com:/etc/ansible/roles.zip C:\guddu\Devops\Ansible


		ansible-galaxy init --force --offline vivekrole 
		
		site.yml
			---
			- hosts: wordpress_hosts
			  roles:
			    - nginx
			    - php
			    - mysql
			    - wordpress

		ansible-playbook site.yml

https://www.digitalocean.com/community/search?q=ansible


To add user

	$ ansible webservers -m user -a 'name=user1 password=user1' --become
To delete user

	$ ansible webservers -m user -a 'name=user1 state=absent' --become
Example 1: Create a file

	$ ansible webservers -m file -a 'dest=/home/ansible/niranjan.txt state=touch mode=600 owner=ansible group=ansible'
Example 2: Create a directory

To create a directory using the file module, you need to set two parameters.

    Path(alias – name, dest) – This is the absolute path of the directory to be created.
    State – You should enter the value as ‘directory.’ By default, the value is ‘file’.

	$ ansible webservers -m file -a "dest=/home/ansible/vndir state=directory mode=755"

Example 3: Delete a file

	$ ansible webservers -m file -a "dest=/home/ansible/niranjan.txt state=absent"


Example 4: Delete a directory

You can delete a directory by setting the state parameter value to absent. The directory and all its contents will be deleted.

	$ ansible webservers -m file -a "dest=/home/ansible/vndir state=absent"

#6) Copy Module

It is used for copying files to multiple target machines.

	$ ansible webservers -m copy -a "src=sample.txt dest=/home/ansible/sample.txt

#7) Managing Software Packages

If you need to install software packages through ‘yum’ or ‘apt’ you can use the below commands.

Example 1: Install GIT

	$ ansible webservers –m yum -a “name=git state=present” --become

In this command, state=present will check if the package is installed or not and if not installed it will install the latest version.

Example 2: Check if the package is installed & update it to the latest version.

	$ ansible webservers -m yum -a “name=git state=latest”
Example 3: Install Apache Webserver

	$ ansible webservers -m yum -a "name=httpd state=present" –become

Example 4: Check if Maven is installed or not.

	$ ansible webservers -m yum -a "name=maven state=absent" –become

#8) Managing Services Module

To manage services with ansible, we use a module ‘service’.

Starting a service

	$ ansible webservers -m service -a “name=httpd state=started” --become

Stopping a service

	$ ansible webservers -m service -a “name=httpd state=stopped” --become

Restarting a service

	$ ansible webservers -m service -a “name=httpd state=restarted --become

=========================================================================

ANSIBLE VAULT

		[ec2-user@ansible ansible]$ cat hello-world.yml
			---
			- name: This is a hello world program for ansible
			  hosts: all
			  tasks:
			  - name: Create a temp file and write hello world in it
			    copy:
			      content: "hello world\n"
			      dest: /tmp/HelloWorld.txt
		[ec2-user@ansible ansible]$ ls -lrt
		total 64
	
		-rw-r--r-- 1 ansadm ansadm 19557 Nov 24 18:13 ansible.cfg
		-rw-r--r-- 1 ansadm ansadm  1092 Jan 12 17:28 hosts
		drwxr-xr-x 3 ansadm ansadm  4096 Jan 17 10:42 roles
		-rw-rw-r-- 1 ansadm ansadm    24 Jan 17 13:50 goapache.retry
		-rw-rw-r-- 1 ansadm ansadm 15153 Jan 17 14:16 roles.zip
		-rw-rw-r-- 1 ansadm ansadm    95 Jan 30 07:27 goapache.yml
		-rw-r--r-- 1 root   root     206 Feb  5 05:56 hello-world.yml
		-rw-rw-r-- 1 ansadm ansadm    25 Feb  5 05:57 hello-world.retry
		-rw------- 1 ansadm ansadm  1197 Feb  5 06:00 demo-hello-world.yml
		[ec2-user@ansible ansible]$ ansible-vault decrypt demo-hello-world.yml
		Vault password:
		ERROR! [Errno 13] Permission denied: u'/etc/ansible/demo-hello-world.yml'
		[ec2-user@ansible ansible]$ sudo ansible-vault decrypt demo-hello-world.yml
		Vault password:
		Decryption successful
		[ec2-user@ansible ansible]$ cat demo-hello-world.yml
		cat: demo-hello-world.yml: Permission denied
		[ec2-user@ansible ansible]$ sudo cat demo-hello-world.yml
			---
			- name: Anooruddh - This is a hello world program for ansible
			  hosts: all
			  tasks:
			  - name: Create a temp file and write hello world in it
			    copy:
			      content: "hello world\n"
			      dest: /tmp/HelloWorld.txt
		[ec2-user@ansible ansible]$ ansible-vault encrypt demo-hello-world.yml
		New Vault password:
		Confirm New Vault password:
		 [WARNING]: Error in vault password prompt (default): Passwords do not match

		ERROR! Passwords do not match
		[ec2-user@ansible ansible]$ ansible-vault encrypt demo-hello-world.yml
		New Vault password:
		Confirm New Vault password:
		ERROR! [Errno 13] Permission denied: u'/etc/ansible/demo-hello-world.yml'
		[ec2-user@ansible ansible]$ sudo ansible-vault encrypt demo-hello-world.yml
		New Vault password:
		Confirm New Vault password:
		Encryption successful
		[ec2-user@ansible ansible]$ cat demo-hello-world.yml
		cat: demo-hello-world.yml: Permission denied
		[ec2-user@ansible ansible]$ sudo cat demo-hello-world.yml
		$ANSIBLE_VAULT;1.1;AES256
		34343730306164393131353961366238623464333065663533613636636366356161383230343139
		3534313634326230656138326439396337393039343666630a326262336235653231333636613761
		65353932393736663239393130666235656631356263376234366461313138393738366364316638
		3638626133636363340a326434363230663931336134393631626636656334346630643165333037
		30653663313339663431343466316238666266363039626533636130326666646161326135346462
		64343562353438363961656361306331613261613932643433313630313738303561303232636532
		30313635316665383039643533623638613530643339643235386163636230326237656566323737
		64383961323565306333336462383234643636346438633633383536306431303765336130633439
		32393931316335613265323237646331666633353666363231313434383030666666633762383639
		61393566373837623332313937383565653231303937393866303834373739376266666563306435
		35303937346265346339373163626139313664353530323430353136396138316231613639663164
		66376565666338623466393633633637663331353834356231666663666665363837643437643561
		38376461383838326663313838326636643763333962353039313836626537623362363434636636
		38613536666638303338353737396436626462376563333861376262373136376166353963663833
		653036306536303461626538356562303334
		[ec2-user@ansible ansible]$


=========================

TAGS

vim example.yml

			tasks:

			    - yum: name={{ item }} state=installed
			      with_items:
				 - httpd
				 - memcached
			      tags:
				 - packages

			    - template: src=templates/src.j2 dest=/etc/foo.conf
			      tags:
				 - configuration


RUN ansible with tag
	
			ansible-playbook example.yml --tags "configuration,packages"

( will run both  tasks configuration,packages, if you want to run only one task specify that tag only mentioned under task)
=========================

	Common core modules include:

	    command - Executes a command on a remote node
	    script - Runs a local script on a remote node after transferring it
	    shell - Execute commands in nodes
	    mysql_db - Add or remove MySQL databases from a remote host
	    mysql_user - Adds or removes a user from a MySQL database
	    postgresql_db - Add or remove PostgreSQL databases from a remote host
	    postgresql_user - Adds or removes a users (roles) from a PostgreSQL database
	    fetch - Fetches a file from remote nodes
	    template - Templates a file out to a remote server
	    yum - Manages packages with the yum package manager
	    apt - Manages apt-packages
	    git - Deploy software (or files) from git checkouts
	    service - Manage services

=======================================================================
	
	Use ansible-vault when you want to store sensitive information

If one of your tasks requires sensitive information (let’s say the database user and password), it’s a good practice to keep this information encrypted, instead of storing it in plain text.

Ansible ships with a command line tool called ansible-vault, that allows you to create and manage encrypted files. This way you can commit the encrypted file to your source control and only users with the decryption password will be able to read it.

# Encrypt an existing file. You'll need to create an encryption password.
		ansible-vault encrypt secrets.yml

# Creates a new, encrypted file. You'll need to create an encryption password.
		ansible-vault create secrets.yml

# Decrypt a file. You'll have to enter password used for encryption.
# Use it with caution! Don't leave your files unecrypted.
		ansible-vault decrypt secrets.yml

# Edit an encrypted file (uses vim by default, can be overriden by the environment variable $EDITOR)
		ansible-vault edit secrets.yml

# Print the contents of the encrypted file
		ansible-vault edit secrets.yml

If you import the vars_file secrets.yml in your playbook, Ansible will fail, as it will not know how to read the encrypted file. You’ll have to specify the command line argument --ask-vault-pass, which will make Ansible prompt you the password of the encrypted file.

		ansible-playbook playbook.yml -i hosts --ask-vault-password

Another way is to store the password in a file (which should not be commited) and specify the path to the file using the --vault-password-file argument. If this file is marked as executable, Ansible will run it and use the output as the password.
	
	
	================================
	cat copy-file.yml
	---
	 - hosts: localhost
	   tasks:
	   - name: Copy file to temp
	     copy: src=/etc/ansible/demo.txt dest=/tmp
	   - name: Make directory inside temp
	     file: state=directory name=/tmp/ag2
------------------------------------------------------------------
	you can use the run_once parameter to tell Ansible to run the command only one time:

	- name: run the database migrations
	  command: bundle exec rake db:migrate
	  run_once: true
-----------------------------------------------------------------
	
Handlers are special types of tasks

Handlers are tasks with unique names that will only be executed if notified by another task. They are really useful for restarting a service or rebooting the system.

Handlers that were notified will be executed one time at the end of the playbook and, regardless of how many times they were notified. You can declare them with the handler clause and trigger them using notify.

Here is an example of how restart two services when the contents of a file change, but only if the file changes (extracted from Ansible docs):

		- name: template configuration file
		  template: src=template.j2 dest=/etc/foo.conf
		  notify:
		     - restart memcached
		     - restart apache

The handlers should be declared somewhere else in your playbook:

		handlers:
		    - name: restart memcached
		      # The service module was used, but you could use whatever module you wanted
		      service: name=memcached state=restarted
		    - name: restart apache
		      service: name=apache state=restarted
---------------------------------------------------------------------
check what tasks yaml file is going to perform

		ansible-playbook --check playbook.yml	
---------------------------------------------------------------------	
AS yes or no performing existing a task/action from ansible playbook
	
[ansadm@ansible ansible]$ ansible-playbook copy-file.yml -i hosts --step

PLAY [localhost] ********************************************************************************************************************************************
Perform task: TASK: Gathering Facts (N)o/(y)es/(c)ontinue: y

Perform task: TASK: Gathering Facts (N)o/(y)es/(c)ontinue: **************************************************************************************************

TASK [Gathering Facts] **************************************************************************************************************************************
ok: [localhost]
Perform task: TASK: Copy file to temp (N)o/(y)es/(c)ontinue: y

Perform task: TASK: Copy file to temp (N)o/(y)es/(c)ontinue: ************************************************************************************************

TASK [Copy file to temp] ************************************************************************************************************************************
ok: [localhost]
Perform task: TASK: Make directory inside temp (N)o/(y)es/(c)ontinue: y

Perform task: TASK: Make directory inside temp (N)o/(y)es/(c)ontinue: ***************************************************************************************

TASK [Make directory inside temp] ***************************************************************************************************************************
ok: [localhost]

PLAY RECAP **************************************************************************************************************************************************
localhost                  : ok=3    changed=0    unreachable=0    failed=0

[ansadm@ansible ansible]$
-------------------------------------------------------------------------------------
	
Tasks can be run based on their tags

You can add one or more tags to a task or a play. To do so, simply mark what you want to tag with the tags attribute:
# playbook.yml
		- hosts: servername
		  tags:
		    - server
		  tasks:
		    - name: Download optional files
		      tags:
			- download
			- optional
		    - name: Install dependencies
		      tags:
			- dependencies

-----
	Later on you can decide which tags to run or skip using the flags --tags <tagname> (or simply -t) and --skip-tags <tagnames>:

# will run only tasks with the tag 'dependencies'
		$ ansible-playbook --tags=dependencies playbook.yml

# will run all tasks except the ones that contain the tag 'optional'
		$ ansible-playbook --skip-tags=optional playbook.yml

You can specify more than one tag by separating them with commas.	

----------------------------------------
		- name: install git
		  yum: name=git state=present
		when: ansible_os_family == 'RedHat'

INTERVIEW CONTENT
====================================================================================================================================================================
	
few modules - ping,user,service,shell,debug,command,copy,yumc,uri,twilio,telnet,snmp,setup(gather facts of remote server)

	ansible_facts
	gather_facts
	become: true
	become_user: root
	remote_user: root
	register
	debug
	notify
	handler
	ansible-vault ( encrypt,decrypt,view,edit,rekey)
	when (when ansible_facts["os_name"] == "RedHat")
	with_items:
	  - 1
	  - [2,3]
	  - 4
	loop:
	     - testuser1
	     - testuser2

	ansible-playbook example.yml --tags "configuration,packages"	

	ansible appservers -m setup -i ansible_hosts -a 	'filter=ansible_distribution,ansible_distribution_version,ansible_memfree_mb,ansible_memtotal_mb,ansible_processor_cores*,ansible_architecture'


	stage: restarted,present,absent,installed,Latest

	--ask-vault-password		ansible-playbook playbook.yml -i hosts --ask-vault-password
	ansible all --list-hosts
	tasks can be run based on their tags
	ansible-playbook --tags=dependencies playbook.yml

/etc/ansible
/etc/ansible/ansible.config
/etc/ansible/hosts

cat /etc/ansible/hosts
		--------------------------------------------------------------------------------------------------------------------------
		[dbservers]
		ansible-dbserver.server

		[appservers]
		ansible-appserver.server
		--------------------------------------------------------------------------------------------------------------------------

		ANSIBLE.config
		--------------------------------------------------------------------------------------------------------------------------
		# SSH timeout
		#timeout = 10

		#remote_user = root
		#vault_password_file = /path/to/vault_password_file

		#retry_files_enabled = False
		#retry_files_save_path = ~/.ansible-retry

		#retries = 3
		#connect_timeout = 30

		--------------------------------------------------------------------------------------------------------------------------



Azure Devops
	Azure Repo
	Azure Board
	Azure Pipeline
	Azure Test Plan
	Azure Artifacts

Azure DevOps includes:

	Azure Pipelines: CI/CD that works with any language, platform, and cloud. Connect to GitHub or any Git repository and deploy continuously. Azure Pipelines is also now available in the GitHub Marketplace.
	Azure Boards: Powerful work tracking with Kanban boards, backlogs, team dashboards, and custom reporting.
	Azure Artifacts: Maven, npm, and NuGet package feeds from public and private sources.
	Azure Repos: Unlimited cloud-hosted private Git repos for your project. Collaborative pull requests, advanced file management, and more.
	Azure Test Plans: All in one planned and exploratory testing solution.	



Standard loops

	- name: Add several users
	  ansible.builtin.user:
	    name: "{{ item }}"
	    state: present
	    groups: "wheel"
	  loop:
	     - testuser1
	     - testuser2


	with_list
	with_items
	with_indexed_items
	with_flattened
	with_together
	with_dict
	with_sequence
	with_subelements
	with_nested/with_cartesian
	with_random_choice

============================================================================================================================

Kubernetes

to delete label user - after lable name k label service lblsvc-

	kubectl create is imperative management ( we need to specify what and how to perform a task), whereas apply is declarative approach( we only specify what to perform)
	apply perform create or update, However Create, It creates resources from the file prov
	ided. It shows an error if the resource has already been created.

	apply works on all files under a directory and its subdirectory ( yml files), however create works a single file within a folder.

Deployment - recreate,rolling update,blue-green,canary

What is an Ingress?
	In Kubernetes, an Ingress is an object that allows access to your Kubernetes services from outside the Kubernetes cluster. You configure access by creating a 	collection of rules that define which inbound connections reach which services.

	Ingress. This service allows the routing of HTTP(S) traffic according to defined rules like path-based routings. This can be associated with one or more service objects where these services are further associated with Pods. The ingress controller creates HTTP(S) load balancer Layer 7 load balancer which are configured automatically using the definition in the Ingress object.


Deployment vs Service	- Deployment makes pods available, Where service makes pods accessible

Services - ClusterIP, Node Port, Load Balancer


Terraform

	azurerm_subnet
	azurerm_virtual_network
	azurerm_public_ip
	azuread_application
	azuread_service_principal
	azurerm_application_gateway
	azurerm_network_profile
	azurerm_network_security_group
	azurerm_subnet_network_security_group_association
	azurerm_network_security_rule
	azurerm_subnet_network_security_group_association
	azurerm_key_vault_secret
	azurerm_container_group



{{terraform.workspace}}	

	
### Run a specific part of the configuration without running the whole playbook	

Both plays and tasks support a “tags:” attribute for this reason

Example:	
	
	tasks:

	    - yum: name={{ item }} state=installed
	      with_items:
		 - httpd
		 - memcached
	      tags:
		 - packages

	    - template: src=templates/src.j2 dest=/etc/foo.conf
	      tags:
		 - configuration

If you wanted to just run the “configuration” and “packages” part of a very long playbook, you could do this:

		ansible-playbook example.yml --tags "configuration,packages"
On the other hand, if you want to run a playbook without certain tasks, you could do this:

		ansible-playbook example.yml --skip-tags "notification"
You may also apply tags to roles:

		roles:
		  - { role: webserver, port: 5000, tags: [ 'web', 'foo' ] }
And you may also tag basic include statements:

		- include: foo.yml tags=web,foo	

***ANSIBLE ROLES***
	

1. Ansible roles are consists of many playbooks, which is similar to modules in puppet and cook books in chef. We term the same in ansible as roles.

2. Roles are a way to group multiple tasks together into one container to do the automation in very effective manner with clean directory structures.

3. Roles are set of tasks and additional files for a certain role which allow you to break up the configurations.

4. It can be easily reuse the codes by anyone if the role is suitable to someone.	
	
5. It can be easily modify and will reduce the syntax errors.	
	

To create a Ansible roles, use ansible-galaxy command which has the templates to create it. This will create it under the default directory /etc/ansible/roles and do the modifications else we need to create each directories and files manually.
	

	[root@learnitguide ~]# ansible-galaxy init /etc/ansible/roles/apache --offline
	- apache was created successfully
	[root@learnitguide ~]# 

init is to initiliaze the role.
apache is the name of role,	
offline - create offline mode rather than getting from online repository.


List out the directory created under /etc/ansible/roles.
	
	[root@learnitguide ~]# tree /etc/ansible/roles/apache/
	[root@learnitguide ~]# tree /etc/ansible/roles/apache/
	/etc/ansible/roles/apache/
	|-- README.md
	|-- defaults
	|   `-- main.yml
	|-- files
	|-- handlers
	|   `-- main.yml
	|-- meta
	|   `-- main.yml
	|-- tasks
	|   `-- main.yml
	|-- templates
	|-- tests
	|   |-- inventory
	|   `-- test.yml
	`-- vars
	    `-- main.yml
	8 directories, 8 files
	[root@learnitguide ~]#

	/etc/ansible/roles/apache/	


We have got the clean directory structure with the ansible-galaxy command. Each directory must contain a main.yml file, which contains the relevant content.
Directory Structure
	
	tasks - contains the main list of tasks to be executed by the role.

	handlers - contains handlers, which may be used by this role or even anywhere outside this role.

	defaults - default variables for the role.

	vars - other variables for the role. Vars has the higher priority than defaults.

	files - contains files required to transfer or deployed to the target machines via this role.

	templates - contains templates which can be deployed via this role.

	meta - defines some data / information about this role (author, dependency, versions, examples, etc,.)

	
Lets take an example to create a role for Apache Web server.
		
Below is a sample playbook codes to deploy Apache web server. Lets convert this playbook codes into Ansible roles.
	
	---
	- hosts: all
	  tasks:
	  - name: Install httpd Package
	    yum: name=httpd state=latest
	  - name: Copy httpd configuration file
	    copy: src=/data/httpd.original dest=/etc/httpd/conf/httpd.conf
	  - name: Copy index.html file
	    copy: src=/data/index.html dest=/var/www/html
	    notify:
	    - restart apache
	  - name: Start and Enable httpd service
	    service: name=httpd state=restarted enabled=yes
	  handlers:
	  - name: restart apache
	    service: name=httpd state=restarted


Edit main.yml available in the tasks folder to define the tasks to be executed.

		[root@learnitguide apache]# vi tasks/main.yml
		---
		- name: Install httpd Package
		  yum: name=httpd state=latest
		- name: Copy httpd configuration file
		  copy: src=/data/httpd.original dest=/etc/httpd/conf/httpd.conf
		- name: Copy index.html file
		  copy: src=/data/index.html dest=/var/www/html
		  notify:
		  - restart apache
		- name: Start and Enable httpd service
		  service: name=httpd state=restarted enabled=yes

Altogether, you can add all your tasks in this file or just break the codes even more as below using "import_tasks" statements.

		[root@learnitguide apache]# cat tasks/main.yml
		---
		# tasks file for /etc/ansible/roles/apache
		- import_tasks: install.yml
		- import_tasks: configure.yml
		- import_tasks: service.yml
Lets create install.yml, confgure.yml, service.yml included in the main.yml with actions in the same directory.	
	
***install.yml***

	
		[root@learnitguide apache]# cat tasks/install.yml
		---
		- name: Install httpd Package
		  yum: name=httpd state=latest

***configure.yml***

	
		[root@learnitguide apache]# cat tasks/configure.yml
		---
		- name: Copy httpd configuration file
		  copy: src=files/httpd.conf dest=/etc/httpd/conf/httpd.conf
		- name: Copy index.html file
		  copy: src=files/index.html dest=/var/www/html
		  notify:
		  - restart apache

***service.yml***

	
		[root@learnitguide apache]# cat tasks/service.yml
		---
		- name: Start and Enable httpd service
		service: name=httpd state=restarted enabled=yes

***Files***

Copy the required files to the files directory
	
		[root@learnitguide apache]# ll files/*
		-rw-r--r-- 1 root root 11753 Feb  4 10:01 files/httpd.conf
		-rw-r--r-- 1 root root    66 Feb  4 10:02 files/index.html
		[root@learnitguide apache]# cat files/index.html
		This is a homepage created by learnitguide.net for ansible roles.
		[root@learnitguide apache]#
***Handlers***

		[root@learnitguide apache]# cat handlers/main.yml
		---
		# handlers file for /etc/ansible/roles/apache
		- name: restart apache
		  service: name=httpd state=restarted	

List directory structure
	
	[root@learnitguide apache]# tree
	.
	|-- README.md
	|-- defaults
	|   `-- main.yml
	|-- files
	|   |-- httpd.conf
	|   `-- index.html
	|-- handlers
	|   `-- main.yml
	|-- meta
	|   `-- main.yml
	|-- tasks
	|   |-- configure.yml
	|   |-- install.yml
	|   |-- main.yml
	|   `-- service.yml
	|-- templates
	|-- tests
	|   |-- inventory
	|   `-- test.yml
	`-- vars
	    `-- main.yml
	8 directories, 13 files
	[root@learnitguide apache]#

Create a playbook, which will use/run these roles
	
	[root@learnitguide apache]# cat /etc/ansible/runsetup.yml
	---
	 - hosts: node2
	   roles:
	   - apache
	[root@learnitguide apache]#

Verify the syntax
	
	[root@learnitguide apache]# ansible-playbook /etc/ansible/runsetup.yml --syntax-check
	playbook: /etc/ansible/runsetup.yml
	[root@learnitguide apache]#
