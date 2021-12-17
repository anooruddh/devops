Ansible

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

This will prompt you for the plaintext password and will give you a hashed password string you can paste into the task definition. For password secret, I get hash $6$F4NWXRFtSdCi8$DsB5vvMJYusQhSbvGXrYDXL6Xj37MUuqFCd4dGXdKd6NyxT3lpdELN07/Kpo7EjjWnm9zusFg/LLFv6oc.ynu/
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
ANSIBLE ALL MODULES

	[ansadm@ansible tmp]$ ansible-doc -l > AnsibleAllModules.log
	[ansadm@ansible tmp]$ vi AnsibleAllModules.log
	[ansadm@ansible tmp]$ cat AnsibleAllModules.log
	a10_server                                           Manage A10 Networks AX/SoftAX/Thunder/vThunder devices' server object.
	a10_server_axapi3                                    Manage A10 Networks AX/SoftAX/Thunder/vThunder devices
	a10_service_group                                    Manage A10 Networks AX/SoftAX/Thunder/vThunder devices' service groups.
	a10_virtual_server                                   Manage A10 Networks AX/SoftAX/Thunder/vThunder devices' virtual servers.
	accelerate                                           Enable accelerated mode on remote node
	aci_aaa_user                                         Manage AAA users (aaa:User)
	aci_aaa_user_certificate                             Manage AAA user certificates (aaa:UserCert)
	aci_access_port_to_interface_policy_leaf_profile     Manage Fabric interface policy leaf profile interface selectors (infra:HPortS, infra:RsAccBaseGrp, ...
	aci_aep                                              Manage attachable Access Entity Profile (AEP) objects (infra:AttEntityP, infra:ProvAcc)
	aci_aep_to_domain                                    Bind AEPs to Physical or Virtual Domains (infra:RsDomP)
	aci_ap                                               Manage top level Application Profile (AP) objects (fv:Ap)
	aci_bd                                               Manage Bridge Domains (BD) objects (fv:BD)
	aci_bd_subnet                                        Manage Subnets (fv:Subnet)
	aci_bd_to_l3out                                      Bind Bridge Domain to L3 Out (fv:RsBDToOut)
	aci_config_rollback                                  Provides rollback and rollback preview functionality (config:ImportP)
	aci_config_snapshot                                  Manage Config Snapshots (config:Snapshot, config:ExportP)
	aci_contract                                         Manage contract resources (vz:BrCP)
	aci_contract_subject                                 Manage initial Contract Subjects (vz:Subj)
	aci_contract_subject_to_filter                       Bind Contract Subjects to Filters (vz:RsSubjFiltAtt)
	aci_domain                                           Manage physical, virtual, bridged, routed or FC domain profiles (phys:DomP, vmm:DomP, l2ext:DomP, l...
	aci_domain_to_encap_pool                             Bind Domain to Encap Pools (infra:RsVlanNs)
	aci_domain_to_vlan_pool                              Bind Domain to VLAN Pools (infra:RsVlanNs)
	aci_encap_pool                                       Manage encap pools (fvns:VlanInstP, fvns:VxlanInstP, fvns:VsanInstP)
	aci_encap_pool_range                                 Manage encap ranges assigned to pools (fvns:EncapBlk, fvns:VsanEncapBlk)
	aci_epg                                              Manage End Point Groups (EPG) objects (fv:AEPg)
	aci_epg_monitoring_policy                            Manage monitoring policies (mon:EPGPol)
	aci_epg_to_contract                                  Bind EPGs to Contracts (fv:RsCons, fv:RsProv)
	aci_epg_to_domain                                    Bind EPGs to Domains (fv:RsDomAtt)
	aci_fabric_node                                      Manage Fabric Node Members (fabric:NodeIdentP)
	aci_filter                                           Manages top level filter objects (vz:Filter)
	aci_filter_entry                                     Manage filter entries (vz:Entry)
	aci_firmware_source                                  Manage firmware image sources (firmware:OSource)
	aci_interface_policy_fc                              Manage Fibre Channel interface policies (fc:IfPol)
	aci_interface_policy_l2                              Manage Layer 2 interface policies (l2:IfPol)
	aci_interface_policy_leaf_policy_group               Manage fabric interface policy leaf policy groups (infra:AccBndlGrp, infra:AccPortGrp)
	aci_interface_policy_leaf_profile                    Manage fabric interface policy leaf profiles (infra:AccPortP)
	aci_interface_policy_lldp                            Manage LLDP interface policies (lldp:IfPol)
	aci_interface_policy_mcp                             Manage MCP interface policies (mcp:IfPol)
	aci_interface_policy_port_channel                    Manage port channel interface policies (lacp:LagPol)
	aci_interface_policy_port_security                   Manage port security (l2:PortSecurityPol)
	aci_interface_selector_to_switch_policy_leaf_profile Bind interface selector profiles to switch policy leaf profiles (infra:RsAccPortP)
	aci_l3out                                            Manage Layer 3 Outside (L3Out) objects (l3ext:Out)
	aci_l3out_route_tag_policy                           Manage route tag policies (l3ext:RouteTagPol)
	aci_rest                                             Direct access to the Cisco APIC REST API
	aci_static_binding_to_epg                            Bind static paths to EPGs (fv:RsPathAtt)
	aci_switch_leaf_selector                             Bind leaf selectors to switch policy leaf profiles (infra:LeafS, infra:NodeBlk, infra:RsAccNodePGre...
	aci_switch_policy_leaf_profile                       Manage switch policy leaf profiles (infra:NodeP)
	aci_switch_policy_vpc_protection_group               Manage switch policy explicit vPC protection groups (fabric:ExplicitGEp, fabric:NodePEp).
	aci_taboo_contract                                   Manage taboo contracts (vz:BrCP)
	aci_tenant                                           Manage tenants (fv:Tenant)
	aci_tenant_action_rule_profile                       Manage action rule profiles (rtctrl:AttrP)
	aci_tenant_ep_retention_policy                       Manage End Point (EP) retention protocol policies (fv:EpRetPol)
	aci_tenant_span_dst_group                            Manage SPAN destination groups (span:DestGrp)
	aci_tenant_span_src_group                            Manage SPAN source groups (span:SrcGrp)
	aci_tenant_span_src_group_to_dst_group               Bind SPAN source groups to destination groups (span:SpanLbl)
	aci_vlan_pool                                        Manage VLAN pools (fvns:VlanInstP)
	aci_vlan_pool_encap_block                            Manage encap blocks assigned to VLAN pools (fvns:EncapBlk)
	aci_vrf                                              Manage contexts or VRFs (fv:Ctx)
	acl                                                  Sets and retrieves file ACL information.
	acme_account                                         Create, modify or delete accounts with Let's Encrypt
	acme_certificate                                     Create SSL certificates with an ACME protocol endpoint
	add_host                                             add a host (and alternatively a group) to the ansible-playbook in-memory inventory
	airbrake_deployment                                  Notify airbrake about app deployments
	aireos_command                                       Run commands on remote devices running Cisco WLC
	aireos_config                                        Manage Cisco WLC configurations
	aix_inittab                                          Manages the inittab on AIX
	aix_lvol                                             Configure AIX LVM logical volumes
	alternatives                                         Manages alternative programs for common commands
	aos_asn_pool                                         Manage AOS ASN Pool
	aos_blueprint                                        Manage AOS blueprint instance
	aos_blueprint_param                                  Manage AOS blueprint parameter values
	aos_blueprint_virtnet                                Manage AOS blueprint parameter values
	aos_device                                           Manage Devices on AOS Server
	aos_external_router                                  Manage AOS External Router
	aos_ip_pool                                          Manage AOS IP Pool
	aos_logical_device                                   Manage AOS Logical Device
	aos_logical_device_map                               Manage AOS Logical Device Map
	aos_login                                            Login to AOS server for session token
	aos_rack_type                                        Manage AOS Rack Type
	aos_template                                         Manage AOS Template
	apache2_mod_proxy                                    Set and/or get members' attributes of an Apache httpd 2.4 mod_proxy balancer pool
	apache2_module                                       Enables/disables a module of the Apache2 webserver.
	apk                                                  Manages apk packages
	apt                                                  Manages apt-packages
	apt_key                                              Add or remove an apt key
	apt_repository                                       Add and remove APT repositories
	apt_rpm                                              apt_rpm package manager
	archive                                              Creates a compressed archive of one or more files or trees
	aruba_command                                        Run commands on remote devices running Aruba Mobility Controller
	aruba_config                                         Manage Aruba configuration sections
	asa_acl                                              Manage access-lists on a Cisco ASA
	asa_command                                          Run arbitrary commands on Cisco ASA devices
	asa_config                                           Manage configuration sections on Cisco ASA devices
	assemble                                             Assembles a configuration file from fragments
	assert                                               Asserts given expressions are true
	async_status                                         Obtain status of asynchronous task
	at                                                   Schedule the execution of a command or script file via the at command
	atomic_container                                     Manage the containers on the atomic host platform
	atomic_host                                          Manage the atomic host platform
	atomic_image                                         Manage the container images on the atomic host platform
	authorized_key                                       Adds or removes an SSH authorized key
	avi_actiongroupconfig                                Module for setup of ActionGroupConfig Avi RESTful Object
	avi_alertconfig                                      Module for setup of AlertConfig Avi RESTful Object
	avi_alertemailconfig                                 Module for setup of AlertEmailConfig Avi RESTful Object
	avi_alertscriptconfig                                Module for setup of AlertScriptConfig Avi RESTful Object
	avi_alertsyslogconfig                                Module for setup of AlertSyslogConfig Avi RESTful Object
	avi_analyticsprofile                                 Module for setup of AnalyticsProfile Avi RESTful Object
	avi_api_session                                      Avi API Module
	avi_api_version                                      Avi API Version Module
	avi_applicationpersistenceprofile                    Module for setup of ApplicationPersistenceProfile Avi RESTful Object
	avi_applicationprofile                               Module for setup of ApplicationProfile Avi RESTful Object
	avi_authprofile                                      Module for setup of AuthProfile Avi RESTful Object
	avi_autoscalelaunchconfig                            Module for setup of AutoScaleLaunchConfig Avi RESTful Object
	avi_backup                                           Module for setup of Backup Avi RESTful Object
	avi_backupconfiguration                              Module for setup of BackupConfiguration Avi RESTful Object
	avi_certificatemanagementprofile                     Module for setup of CertificateManagementProfile Avi RESTful Object
	avi_cloud                                            Module for setup of Cloud Avi RESTful Object
	avi_cloudconnectoruser                               Module for setup of CloudConnectorUser Avi RESTful Object
	avi_cloudproperties                                  Module for setup of CloudProperties Avi RESTful Object
	avi_cluster                                          Module for setup of Cluster Avi RESTful Object
	avi_clusterclouddetails                              Module for setup of ClusterCloudDetails Avi RESTful Object
	avi_controllerproperties                             Module for setup of ControllerProperties Avi RESTful Object
	avi_customipamdnsprofile                             Module for setup of CustomIpamDnsProfile Avi RESTful Object
	avi_dnspolicy                                        Module for setup of DnsPolicy Avi RESTful Object
	avi_errorpagebody                                    Module for setup of ErrorPageBody Avi RESTful Object
	avi_errorpageprofile                                 Module for setup of ErrorPageProfile Avi RESTful Object
	avi_gslb                                             Module for setup of Gslb Avi RESTful Object
	avi_gslbapplicationpersistenceprofile                Module for setup of GslbApplicationPersistenceProfile Avi RESTful Object
	avi_gslbgeodbprofile                                 Module for setup of GslbGeoDbProfile Avi RESTful Object
	avi_gslbhealthmonitor                                Module for setup of GslbHealthMonitor Avi RESTful Object
	avi_gslbservice                                      Module for setup of GslbService Avi RESTful Object
	avi_gslbservice_patch_member                         Avi API Module
	avi_hardwaresecuritymodulegroup                      Module for setup of HardwareSecurityModuleGroup Avi RESTful Object
	avi_healthmonitor                                    Module for setup of HealthMonitor Avi RESTful Object
	avi_httppolicyset                                    Module for setup of HTTPPolicySet Avi RESTful Object
	avi_ipaddrgroup                                      Module for setup of IpAddrGroup Avi RESTful Object
	avi_ipamdnsproviderprofile                           Module for setup of IpamDnsProviderProfile Avi RESTful Object
	avi_l4policyset                                      Module for setup of L4PolicySet Avi RESTful Object
	avi_microservicegroup                                Module for setup of MicroServiceGroup Avi RESTful Object
	avi_network                                          Module for setup of Network Avi RESTful Object
	avi_networkprofile                                   Module for setup of NetworkProfile Avi RESTful Object
	avi_networksecuritypolicy                            Module for setup of NetworkSecurityPolicy Avi RESTful Object
	avi_pkiprofile                                       Module for setup of PKIProfile Avi RESTful Object
	avi_pool                                             Module for setup of Pool Avi RESTful Object
	avi_poolgroup                                        Module for setup of PoolGroup Avi RESTful Object
	avi_poolgroupdeploymentpolicy                        Module for setup of PoolGroupDeploymentPolicy Avi RESTful Object
	avi_prioritylabels                                   Module for setup of PriorityLabels Avi RESTful Object
	avi_role                                             Module for setup of Role Avi RESTful Object
	avi_scheduler                                        Module for setup of Scheduler Avi RESTful Object
	avi_seproperties                                     Module for setup of SeProperties Avi RESTful Object
	avi_serverautoscalepolicy                            Module for setup of ServerAutoScalePolicy Avi RESTful Object
	avi_serviceengine                                    Module for setup of ServiceEngine Avi RESTful Object
	avi_serviceenginegroup                               Module for setup of ServiceEngineGroup Avi RESTful Object
	avi_snmptrapprofile                                  Module for setup of SnmpTrapProfile Avi RESTful Object
	avi_sslkeyandcertificate                             Module for setup of SSLKeyAndCertificate Avi RESTful Object
	avi_sslprofile                                       Module for setup of SSLProfile Avi RESTful Object
	avi_stringgroup                                      Module for setup of StringGroup Avi RESTful Object
	avi_systemconfiguration                              Module for setup of SystemConfiguration Avi RESTful Object
	avi_tenant                                           Module for setup of Tenant Avi RESTful Object
	avi_trafficcloneprofile                              Module for setup of TrafficCloneProfile Avi RESTful Object
	avi_useraccount                                      Avi UserAccount Module
	avi_useraccountprofile                               Module for setup of UserAccountProfile Avi RESTful Object
	avi_virtualservice                                   Module for setup of VirtualService Avi RESTful Object
	avi_vrfcontext                                       Module for setup of VrfContext Avi RESTful Object
	avi_vsdatascriptset                                  Module for setup of VSDataScriptSet Avi RESTful Object
	avi_vsvip                                            Module for setup of VsVip Avi RESTful Object
	avi_wafpolicy                                        Module for setup of WafPolicy Avi RESTful Object
	avi_wafprofile                                       Module for setup of WafProfile Avi RESTful Object
	avi_webhook                                          Module for setup of Webhook Avi RESTful Object
	awall                                                Manage awall policies
	aws_acm_facts                                        Retrieve certificate facts from AWS Certificate Manager service
	aws_api_gateway                                      Manage AWS API Gateway APIs
	aws_application_scaling_policy                       Manage Application Auto Scaling Scaling Policies
	aws_az_facts                                         Gather facts about availability zones in AWS.
	aws_batch_compute_environment                        Manage AWS Batch Compute Environments
	aws_batch_job_definition                             Manage AWS Batch Job Definitions
	aws_batch_job_queue                                  Manage AWS Batch Job Queues
	aws_caller_facts                                     Get facts about the user and account being used to make AWS calls.
	aws_config_aggregation_authorization                 Manage cross-account AWS Config authorizations
	aws_config_aggregator                                Manage AWS Config aggregations across multiple accounts
	aws_config_delivery_channel                          Manage AWS Config delivery channels
	aws_config_recorder                                  Manage AWS Config Recorders
	aws_config_rule                                      Manage AWS Config resources
	aws_direct_connect_connection                        Creates, deletes, modifies a DirectConnect connection
	aws_direct_connect_gateway                           Manage AWS Direct Connect Gateway.
	aws_direct_connect_link_aggregation_group            Manage Direct Connect LAG bundles.
	aws_direct_connect_virtual_interface                 Manage Direct Connect virtual interfaces.
	aws_elasticbeanstalk_app                             create, update, and delete an elastic beanstalk application
	aws_glue_connection                                  Manage an AWS Glue connection
	aws_glue_job                                         Manage an AWS Glue job
	aws_inspector_target                                 Create, Update and Delete Amazon Inspector Assessment Targets
	aws_kms                                              Perform various KMS management tasks.
	aws_kms_facts                                        Gather facts about AWS KMS keys
	aws_region_facts                                     Gather facts about AWS regions.
	aws_s3                                               manage objects in S3.
	aws_s3_bucket_facts                                  Lists S3 buckets in AWS
	aws_s3_cors                                          Manage CORS for S3 buckets in AWS
	aws_ses_identity                                     Manages SES email and domain identity
	aws_ses_identity_policy                              Manages SES sending authorization policies
	aws_sgw_facts                                        Fetch AWS Storage Gateway facts
	aws_ssm_parameter_store                              Manage key-value pairs in aws parameter store.
	aws_waf_condition                                    create and delete WAF Conditions
	aws_waf_facts                                        Retrieve facts for WAF ACLs, Rule , Conditions and Filters.
	aws_waf_rule                                         create and delete WAF Rules
	aws_waf_web_acl                                      create and delete WAF Web ACLs
	azure                                                create or terminate a virtual machine in azure
	azure_rm_acs                                         Manage an Azure Container Service Instance (ACS).
	azure_rm_aks                                         Manage a managed Azure Container Service (AKS) Instance.
	azure_rm_aks_facts                                   Get Azure Kubernetes Service facts.
	azure_rm_availabilityset                             Manage Azure availability set.
	azure_rm_availabilityset_facts                       Get availability set facts.
	azure_rm_containerinstance                           Manage an Azure Container Instance.
	azure_rm_containerregistry                           Manage an Azure Container Registry.
	azure_rm_deployment                                  Create or destroy Azure Resource Manager template deployments
	azure_rm_dnsrecordset                                Create, delete and update DNS record sets and records.
	azure_rm_dnsrecordset_facts                          Get DNS Record Set facts.
	azure_rm_dnszone                                     Manage Azure DNS zones.
	azure_rm_dnszone_facts                               Get DNS zone facts.
	azure_rm_functionapp                                 Manage Azure Function Apps
	azure_rm_functionapp_facts                           Get Azure Function App facts
	azure_rm_image                                       Manage Azure image.
	azure_rm_keyvault                                    Manage Key Vault instance.
	azure_rm_keyvaultkey                                 Use Azure KeyVault keys.
	azure_rm_keyvaultsecret                              Use Azure KeyVault Secrets.
	azure_rm_loadbalancer                                Manage Azure load balancers.
	azure_rm_loadbalancer_facts                          Get load balancer facts.
	azure_rm_managed_disk                                Manage Azure Manage Disks
	azure_rm_managed_disk_facts                          Get managed disk facts.
	azure_rm_mysqldatabase                               Manage MySQL Database instance.
	azure_rm_mysqlserver                                 Manage MySQL Server instance.
	azure_rm_networkinterface                            Manage Azure network interfaces.
	azure_rm_networkinterface_facts                      Get network interface facts.
	azure_rm_postgresqldatabase                          Manage PostgreSQL Database instance.
	azure_rm_postgresqlserver                            Manage PostgreSQL Server instance.
	azure_rm_publicipaddress                             Manage Azure Public IP Addresses.
	azure_rm_publicipaddress_facts                       Get public IP facts.
	azure_rm_resource                                    Create any Azure resource.
	azure_rm_resource_facts                              Generic facts of Azure resources.
	azure_rm_resourcegroup                               Manage Azure resource groups.
	azure_rm_resourcegroup_facts                         Get resource group facts.
	azure_rm_securitygroup                               Manage Azure network security groups.
	azure_rm_securitygroup_facts                         Get security group facts.
	azure_rm_sqldatabase                                 Manage SQL Database instance.
	azure_rm_sqlserver                                   Manage SQL Server instance
	azure_rm_sqlserver_facts                             Get SQL Server facts.
	azure_rm_storageaccount                              Manage Azure storage accounts.
	azure_rm_storageaccount_facts                        Get storage account facts.
	azure_rm_storageblob                                 Manage blob containers and blob objects.
	azure_rm_subnet                                      Manage Azure subnets.
	azure_rm_virtualmachine                              Manage Azure virtual machines.
	azure_rm_virtualmachine_extension                    Managed Azure Virtual Machine extension
	azure_rm_virtualmachine_scaleset                     Manage Azure virtual machine scale sets.
	azure_rm_virtualmachine_scaleset_facts               Get Virtual Machine Scale Set facts
	azure_rm_virtualmachineimage_facts                   Get virtual machine image facts.
	azure_rm_virtualnetwork                              Manage Azure virtual networks.
	azure_rm_virtualnetwork_facts                        Get virtual network facts.
	bcf_switch                                           Create and remove a bcf switch.
	beadm                                                Manage ZFS boot environments on FreeBSD/Solaris/illumos systems.
	bearychat                                            Send BearyChat notifications
	bigip_asm_policy                                     Manage BIG-IP ASM policies
	bigip_command                                        Run arbitrary command on F5 devices
	bigip_config                                         Manage BIG-IP configuration sections
	bigip_configsync_action                              Perform different actions related to config-sync
	bigip_data_group                                     Manage data groups on a BIG-IP
	bigip_device_connectivity                            Manages device IP configuration settings for HA on a BIG-IP
	bigip_device_dns                                     Manage BIG-IP device DNS settings
	bigip_device_group                                   Manage device groups on a BIG-IP
	bigip_device_group_member                            Manages members in a device group
	bigip_device_httpd                                   Manage HTTPD related settings on BIG-IP
	bigip_device_license                                 Manage license installation and activation on BIG-IP devices
	bigip_device_ntp                                     Manage NTP servers on a BIG-IP
	bigip_device_sshd                                    Manage the SSHD settings of a BIG-IP
	bigip_device_trust                                   Manage the trust relationships between BIG-IPs
	bigip_facts                                          Collect facts from F5 BIG-IP devices
	bigip_gtm_datacenter                                 Manage Datacenter configuration in BIG-IP
	bigip_gtm_facts                                      Collect facts from F5 BIG-IP GTM devices
	bigip_gtm_global                                     Manages global GTM settings
	bigip_gtm_monitor_bigip                              Manages F5 BIG-IP GTM BIG-IP monitors
	bigip_gtm_monitor_external                           Manages external GTM monitors on a BIG-IP
	bigip_gtm_monitor_firepass                           Manages F5 BIG-IP GTM FirePass monitors
	bigip_gtm_monitor_http                               Manages F5 BIG-IP GTM http monitors
	bigip_gtm_monitor_https                              Manages F5 BIG-IP GTM https monitors
	bigip_gtm_monitor_tcp                                Manages F5 BIG-IP GTM tcp monitors
	bigip_gtm_monitor_tcp_half_open                      Manages F5 BIG-IP GTM tcp half-open monitors
	bigip_gtm_pool                                       Manages F5 BIG-IP GTM pools
	bigip_gtm_pool_member                                Manage GTM pool member settings
	bigip_gtm_server                                     Manages F5 BIG-IP GTM servers
	bigip_gtm_virtual_server                             Manages F5 BIG-IP GTM virtual servers
	bigip_gtm_wide_ip                                    Manages F5 BIG-IP GTM wide ip
	bigip_hostname                                       Manage the hostname of a BIG-IP
	bigip_iapp_service                                   Manages TCL iApp services on a BIG-IP
	bigip_iapp_template                                  Manages TCL iApp templates on a BIG-IP
	bigip_iapplx_package                                 Manages Javascript iApp packages on a BIG-IP
	bigip_irule                                          Manage iRules across different modules on a BIG-IP
	bigip_log_destination                                Manages log destinations on a BIG-IP.
	bigip_log_publisher                                  Manages log publishers on a BIG-IP
	bigip_management_route                               Manage system management routes on a BIG-IP
	bigip_monitor_external                               Manages external LTM monitors on a BIG-IP
	bigip_monitor_http                                   Manages F5 BIG-IP LTM http monitors
	bigip_monitor_https                                  Manages F5 BIG-IP LTM https monitors
	bigip_monitor_snmp_dca                               Manages BIG-IP SNMP data collecting agent (DCA) monitors
	bigip_monitor_tcp                                    Manages F5 BIG-IP LTM tcp monitors
	bigip_monitor_tcp_echo                               Manages F5 BIG-IP LTM tcp echo monitors
	bigip_monitor_tcp_half_open                          Manages F5 BIG-IP LTM tcp half-open monitors
	bigip_monitor_udp                                    Manages F5 BIG-IP LTM udp monitors
	bigip_node                                           Manages F5 BIG-IP LTM nodes
	bigip_partition                                      Manage BIG-IP partitions
	bigip_policy                                         Manage general policy configuration on a BIG-IP
	bigip_policy_rule                                    Manage LTM policy rules on a BIG-IP
	bigip_pool                                           Manages F5 BIG-IP LTM pools
	bigip_pool_member                                    Manages F5 BIG-IP LTM pool members
	bigip_profile_client_ssl                             Manages client SSL profiles on a BIG-IP
	bigip_profile_dns                                    Manage DNS profiles on a BIG-IP
	bigip_profile_tcp                                    Manage TCP profiles on a BIG-IP
	bigip_profile_udp                                    Manage UDP profiles on a BIG-IP
	bigip_provision                                      Manage BIG-IP module provisioning
	bigip_qkview                                         Manage qkviews on the device
	bigip_remote_syslog                                  Manipulate remote syslog settings on a BIG-IP
	bigip_routedomain                                    Manage route domains on a BIG-IP
	bigip_security_address_list                          Manage address lists on BIG-IP AFM
	bigip_security_port_list                             Manage port lists on BIG-IP AFM
	bigip_selfip                                         Manage Self-IPs on a BIG-IP system
	bigip_service_policy                                 Manages service policies on a BIG-IP.
	bigip_smtp                                           Manages SMTP settings on the BIG-IP
	bigip_snat_pool                                      Manage SNAT pools on a BIG-IP
	bigip_snmp                                           Manipulate general SNMP settings on a BIG-IP
	bigip_snmp_community                                 Manages SNMP communities on a BIG-IP.
	bigip_snmp_trap                                      Manipulate SNMP trap information on a BIG-IP
	bigip_software_update                                Manage the software update settings of a BIG-IP
	bigip_ssl_certificate                                Import/Delete certificates from BIG-IP
	bigip_ssl_key                                        Import/Delete SSL keys from BIG-IP
	bigip_static_route                                   Manipulate static routes on a BIG-IP
	bigip_sys_db                                         Manage BIG-IP system database variables
	bigip_sys_global                                     Manage BIG-IP global settings
	bigip_timer_policy                                   Manage timer policies on a BIG-IP
	bigip_traffic_group                                  Manages traffic groups on BIG-IP
	bigip_trunk                                          Manage trunks on a BIG-IP
	bigip_ucs                                            Manage upload, installation and removal of UCS files
	bigip_ucs_fetch                                      Fetches a UCS file from remote nodes
	bigip_user                                           Manage user accounts and user attributes on a BIG-IP
	bigip_vcmp_guest                                     Manages vCMP guests on a BIG-IP
	bigip_virtual_address                                Manage LTM virtual addresses on a BIG-IP
	bigip_virtual_server                                 Manage LTM virtual servers on a BIG-IP
	bigip_vlan                                           Manage VLANs on a BIG-IP system
	bigip_wait                                           Wait for a BIG-IP condition before continuing
	bigiq_application_fasthttp                           Manages BIG-IQ FastHTTP applications
	bigiq_application_fastl4_tcp                         Manages BIG-IQ FastL4 TCP applications
	bigiq_application_fastl4_udp                         Manages BIG-IQ FastL4 UDP applications
	bigiq_application_http                               Manages BIG-IQ HTTP applications
	bigiq_application_https_offload                      Manages BIG-IQ HTTPS offload applications
	bigiq_application_https_waf                          Manages BIG-IQ HTTPS WAF applications
	bigiq_regkey_license                                 Manages licenses in a BIG-IQ registration key pool
	bigiq_regkey_license_assignment                      Manage regkey license assignment on BIG-IPs from a BIG-IQ.
	bigiq_regkey_pool                                    Manages registration key pools on BIG-IQ
	bigiq_utility_license                                Manage utility licenses on a BIG-IQ
	bigmon_chain                                         Create and remove a bigmon inline service chain.
	bigmon_policy                                        Create and remove a bigmon out-of-band policy.
	bigpanda                                             Notify BigPanda about deployments
	blockinfile                                          Insert/update/remove a text block surrounded by marker lines
	bower                                                Manage bower packages with bower
	bundler                                              Manage Ruby Gem dependencies with Bundler
	bzr                                                  Deploy software (or files) from bzr branches
	campfire                                             Send a message to Campfire
	capabilities                                         Manage Linux capabilities
	catapult                                             Send a sms / mms using the catapult bandwidth api
	ce_aaa_server                                        Manages AAA server global configuration on HUAWEI CloudEngine switches.
	ce_aaa_server_host                                   Manages AAA server host configuration on HUAWEI CloudEngine switches.
	ce_acl                                               Manages base ACL configuration on HUAWEI CloudEngine switches.
	ce_acl_advance                                       Manages advanced ACL configuration on HUAWEI CloudEngine switches.
	ce_acl_interface                                     Manages applying ACLs to interfaces on HUAWEI CloudEngine switches.
	ce_bfd_global                                        Manages BFD global configuration on HUAWEI CloudEngine devices.
	ce_bfd_session                                       Manages BFD session configuration on HUAWEI CloudEngine devices.
	ce_bfd_view                                          Manages BFD session view configuration on HUAWEI CloudEngine devices.
	ce_bgp                                               Manages BGP configuration on HUAWEI CloudEngine switches.
	ce_bgp_af                                            Manages BGP Address-family configuration on HUAWEI CloudEngine switches.
	ce_bgp_neighbor                                      Manages BGP peer configuration on HUAWEI CloudEngine switches.
	ce_bgp_neighbor_af                                   Manages BGP neighbor Address-family configuration on HUAWEI CloudEngine switches.
	ce_command                                           Run arbitrary command on HUAWEI CloudEngine devices.
	ce_config                                            Manage Huawei CloudEngine configuration sections.
	ce_dldp                                              Manages global DLDP configuration on HUAWEI CloudEngine switches.
	ce_dldp_interface                                    Manages interface DLDP configuration on HUAWEI CloudEngine switches.
	ce_eth_trunk                                         Manages Eth-Trunk interfaces on HUAWEI CloudEngine switches.
	ce_evpn_bd_vni                                       Manages EVPN VXLAN Network Identifier (VNI) on HUAWEI CloudEngine switches.
	ce_evpn_bgp                                          Manages BGP EVPN configuration on HUAWEI CloudEngine switches.
	ce_evpn_bgp_rr                                       Manages RR for the VXLAN Network on HUAWEI CloudEngine switches.
	ce_evpn_global                                       Manages global configuration of EVPN on HUAWEI CloudEngine switches.
	ce_facts                                             Gets facts about HUAWEI CloudEngine switches.
	ce_file_copy                                         Copy a file to a remote cloudengine device over SCP on HUAWEI CloudEngine switches.
	ce_info_center_debug                                 Manages information center debug configuration on HUAWEI CloudEngine switches.
	ce_info_center_global                                Manages outputting logs on HUAWEI CloudEngine switches.
	ce_info_center_log                                   Manages information center log configuration on HUAWEI CloudEngine switches.
	ce_info_center_trap                                  Manages information center trap configuration on HUAWEI CloudEngine switches.
	ce_interface                                         Manages physical attributes of interfaces on HUAWEI CloudEngine switches.
	ce_interface_ospf                                    Manages configuration of an OSPF interface instanceon HUAWEI CloudEngine switches.
	ce_ip_interface                                      Manages L3 attributes for IPv4 and IPv6 interfaces on HUAWEI CloudEngine switches.
	ce_link_status                                       Get interface link status on HUAWEI CloudEngine switches.
	ce_mlag_config                                       Manages MLAG configuration on HUAWEI CloudEngine switches.
	ce_mlag_interface                                    Manages MLAG interfaces on HUAWEI CloudEngine switches.
	ce_mtu                                               Manages MTU settings on HUAWEI CloudEngine switches.
	ce_netconf                                           Run an arbitrary netconf command on HUAWEI CloudEngine switches.
	ce_netstream_aging                                   Manages timeout mode of NetStream on HUAWEI CloudEngine switches.
	ce_netstream_export                                  Manages netstream export on HUAWEI CloudEngine switches.
	ce_netstream_global                                  Manages global parameters of NetStream on HUAWEI CloudEngine switches.
	ce_netstream_template                                Manages NetStream template configuration on HUAWEI CloudEngine switches.
	ce_ntp                                               Manages core NTP configuration on HUAWEI CloudEngine switches.
	ce_ntp_auth                                          Manages NTP authentication configuration on HUAWEI CloudEngine switches.
	ce_ospf                                              Manages configuration of an OSPF instance on HUAWEI CloudEngine switches.
	ce_ospf_vrf                                          Manages configuration of an OSPF VPN instance on HUAWEI CloudEngine switches.
	ce_reboot                                            Reboot a HUAWEI CloudEngine switches.
	ce_rollback                                          Set a checkpoint or rollback to a checkpoint on HUAWEI CloudEngine switches.
	ce_sflow                                             Manages sFlow configuration on HUAWEI CloudEngine switches.
	ce_snmp_community                                    Manages SNMP community configuration on HUAWEI CloudEngine switches.
	ce_snmp_contact                                      Manages SNMP contact configuration on HUAWEI CloudEngine switches.
	ce_snmp_location                                     Manages SNMP location configuration on HUAWEI CloudEngine switches.
	ce_snmp_target_host                                  Manages SNMP target host configuration on HUAWEI CloudEngine switches.
	ce_snmp_traps                                        Manages SNMP traps configuration on HUAWEI CloudEngine switches.
	ce_snmp_user                                         Manages SNMP user configuration on HUAWEI CloudEngine switches.
	ce_startup                                           Manages a system startup information on HUAWEI CloudEngine switches.
	ce_static_route                                      Manages static route configuration on HUAWEI CloudEngine switches.
	ce_stp                                               Manages STP configuration on HUAWEI CloudEngine switches.
	ce_switchport                                        Manages Layer 2 switchport interfaces on HUAWEI CloudEngine switches.
	ce_vlan                                              Manages VLAN resources and attributes on Huawei CloudEngine switches.
	ce_vrf                                               Manages VPN instance on HUAWEI CloudEngine switches.
	ce_vrf_af                                            Manages VPN instance address family on HUAWEI CloudEngine switches.
	ce_vrf_interface                                     Manages interface specific VPN configuration on HUAWEI CloudEngine switches.
	ce_vrrp                                              Manages VRRP interfaces on HUAWEI CloudEngine devices.
	ce_vxlan_arp                                         Manages ARP attributes of VXLAN on HUAWEI CloudEngine devices.
	ce_vxlan_gateway                                     Manages gateway for the VXLAN network on HUAWEI CloudEngine devices.
	ce_vxlan_global                                      Manages global attributes of VXLAN and bridge domain on HUAWEI CloudEngine devices.
	ce_vxlan_tunnel                                      Manages VXLAN tunnel configuration on HUAWEI CloudEngine devices.
	ce_vxlan_vap                                         Manages VXLAN virtual access point on HUAWEI CloudEngine Devices.
	circonus_annotation                                  create an annotation in circonus
	cisco_spark                                          Send a message to a Cisco Spark Room or Individual.
	cl_bond                                              Configures a bond port on Cumulus Linux
	cl_bridge                                            Configures a bridge port on Cumulus Linux
	cl_img_install                                       Install a different Cumulus Linux version.
	cl_interface                                         Configures a front panel port, loopback or management port on Cumulus Linux.
	cl_interface_policy                                  Configure interface enforcement policy on Cumulus Linux
	cl_license                                           Install licenses for Cumulus Linux
	cl_ports                                             Configure Cumulus Switch port attributes (ports.conf)
	clc_aa_policy                                        Create or Delete Anti Affinity Policies at CenturyLink Cloud.
	clc_alert_policy                                     Create or Delete Alert Policies at CenturyLink Cloud.
	clc_blueprint_package                                deploys a blue print package on a set of servers in CenturyLink Cloud.
	clc_firewall_policy                                  Create/delete/update firewall policies
	clc_group                                            Create/delete Server Groups at Centurylink Cloud
	clc_loadbalancer                                     Create, Delete shared loadbalancers in CenturyLink Cloud.
	clc_modify_server                                    modify servers in CenturyLink Cloud.
	clc_publicip                                         Add and Delete public ips on servers in CenturyLink Cloud.
	clc_server                                           Create, Delete, Start and Stop servers in CenturyLink Cloud.
	clc_server_snapshot                                  Create, Delete and Restore server snapshots in CenturyLink Cloud.
	cloud_init_data_facts                                Retrieve facts of cloud-init.
	cloudflare_dns                                       manage Cloudflare DNS records
	cloudformation                                       Create or delete an AWS CloudFormation stack
	cloudformation_facts                                 Obtain facts about an AWS CloudFormation stack
	cloudfront_distribution                              create, update and delete aws cloudfront distributions.
	cloudfront_facts                                     Obtain facts about an AWS CloudFront distribution
	cloudfront_invalidation                              create invalidations for aws cloudfront distributions
	cloudfront_origin_access_identity                    create, update and delete origin access identities for a cloudfront distribution.
	cloudscale_floating_ip                               Manages floating IPs on the cloudscale.ch IaaS service
	cloudscale_server                                    Manages servers on the cloudscale.ch IaaS service
	cloudtrail                                           manage CloudTrail create, delete, update
	cloudwatchevent_rule                                 Manage CloudWatch Event rules and targets
	cloudwatchlogs_log_group                             create or delete log_group in CloudWatchLogs
	cloudwatchlogs_log_group_facts                       get facts about log_group in CloudWatchLogs
	cnos_backup                                          Backup the current running or startup configuration to a remote server on devices running Lenovo CN...
	cnos_bgp                                             Manage BGP resources and attributes on devices running Lenovo CNOS
	cnos_command                                         Run arbitrary commands on Lenovo CNOS devices
	cnos_conditional_command                             Execute a single command based on condition on devices running Lenovo CNOS
	cnos_conditional_template                            Manage switch configuration using templates based on condition on devices running Lenovo CNOS
	cnos_config                                          Manage Lenovo CNOS configuration sections
	cnos_factory                                         Reset the switch's startup configuration to default (factory) on devices running Lenovo CNOS
	cnos_facts                                           Collect facts from remote devices running Lenovo CNOS
	cnos_image                                           Perform firmware upgrade/download from a remote server on devices running Lenovo CNOS
	cnos_interface                                       Manage interface configuration on devices running Lenovo CNOS
	cnos_portchannel                                     Manage portchannel (port aggregation) configuration on devices running Lenovo CNOS
	cnos_reload                                          Perform switch restart on devices running Lenovo CNOS
	cnos_rollback                                        Roll back the running or startup configuration from a remote server on devices running Lenovo CNOS
	cnos_save                                            Save the running configuration as the startup configuration on devices running Lenovo CNOS
	cnos_showrun                                         Collect the current running configuration on devices running Lenovo CNOS
	cnos_template                                        Manage switch configuration using templates on devices running Lenovo CNOS
	cnos_vlag                                            Manage VLAG resources and attributes on devices running Lenovo CNOS
	cnos_vlan                                            Manage VLAN resources and attributes on devices running Lenovo CNOS
	command                                              Executes a command on a remote node
	composer                                             Dependency Manager for PHP
	consul                                               Add, modify & delete services within a consul cluster.
	consul_acl                                           Manipulate Consul ACL keys and rules
	consul_kv                                            Manipulate entries in the key/value store of a consul cluster
	consul_session                                       Manipulate consul sessions
	copy                                                 Copies files to remote locations
	cpanm                                                Manages Perl library dependencies.
	cron                                                 Manage cron.d and crontab entries
	cronvar                                              Manage variables in crontabs
	crypttab                                             Encrypted Linux block devices
	cs_account                                           Manages accounts on Apache CloudStack based clouds.
	cs_affinitygroup                                     Manages affinity groups on Apache CloudStack based clouds.
	cs_cluster                                           Manages host clusters on Apache CloudStack based clouds.
	cs_configuration                                     Manages configuration on Apache CloudStack based clouds.
	cs_domain                                            Manages domains on Apache CloudStack based clouds.
	cs_facts                                             Gather facts on instances of Apache CloudStack based clouds.
	cs_firewall                                          Manages firewall rules on Apache CloudStack based clouds.
	cs_host                                              Manages hosts on Apache CloudStack based clouds.
	cs_instance                                          Manages instances and virtual machines on Apache CloudStack based clouds.
	cs_instance_facts                                    Gathering facts from the API of instances from Apache CloudStack based clouds.
	cs_instance_nic                                      Manages NICs of an instance on Apache CloudStack based clouds.
	cs_instance_nic_secondaryip                          Manages secondary IPs of an instance on Apache CloudStack based clouds.
	cs_instancegroup                                     Manages instance groups on Apache CloudStack based clouds.
	cs_ip_address                                        Manages public IP address associations on Apache CloudStack based clouds.
	cs_iso                                               Manages ISO images on Apache CloudStack based clouds.
	cs_loadbalancer_rule                                 Manages load balancer rules on Apache CloudStack based clouds.
	cs_loadbalancer_rule_member                          Manages load balancer rule members on Apache CloudStack based clouds.
	cs_network                                           Manages networks on Apache CloudStack based clouds.
	cs_network_acl                                       Manages network access control lists (ACL) on Apache CloudStack based clouds.
	cs_network_acl_rule                                  Manages network access control list (ACL) rules on Apache CloudStack based clouds.
	cs_network_offering                                  Manages network offerings on Apache CloudStack based clouds.
	cs_nic                                               Manages NICs and secondary IPs of an instance on Apache CloudStack based clouds
	cs_pod                                               Manages pods on Apache CloudStack based clouds.
	cs_portforward                                       Manages port forwarding rules on Apache CloudStack based clouds.
	cs_project                                           Manages projects on Apache CloudStack based clouds.
	cs_region                                            Manages regions on Apache CloudStack based clouds.
	cs_resourcelimit                                     Manages resource limits on Apache CloudStack based clouds.
	cs_role                                              Manages user roles on Apache CloudStack based clouds.
	cs_role_permission                                   Manages role permissions on Apache CloudStack based clouds.
	cs_router                                            Manages routers on Apache CloudStack based clouds.
	cs_securitygroup                                     Manages security groups on Apache CloudStack based clouds.
	cs_securitygroup_rule                                Manages security group rules on Apache CloudStack based clouds.
	cs_service_offering                                  Manages service offerings on Apache CloudStack based clouds.
	cs_snapshot_policy                                   Manages volume snapshot policies on Apache CloudStack based clouds.
	cs_sshkeypair                                        Manages SSH keys on Apache CloudStack based clouds.
	cs_staticnat                                         Manages static NATs on Apache CloudStack based clouds.
	cs_storage_pool                                      Manages Primary Storage Pools on Apache CloudStack based clouds.
	cs_template                                          Manages templates on Apache CloudStack based clouds.
	cs_user                                              Manages users on Apache CloudStack based clouds.
	cs_vmsnapshot                                        Manages VM snapshots on Apache CloudStack based clouds.
	cs_volume                                            Manages volumes on Apache CloudStack based clouds.
	cs_vpc                                               Manages VPCs on Apache CloudStack based clouds.
	cs_vpc_offering                                      Manages vpc offerings on Apache CloudStack based clouds.
	cs_vpn_connection                                    Manages site-to-site VPN connections on Apache CloudStack based clouds.
	cs_vpn_customer_gateway                              Manages site-to-site VPN customer gateway configurations on Apache CloudStack based clouds.
	cs_vpn_gateway                                       Manages site-to-site VPN gateways on Apache CloudStack based clouds.
	cs_zone                                              Manages zones on Apache CloudStack based clouds.
	cs_zone_facts                                        Gathering facts of zones from Apache CloudStack based clouds.
	cv_server_provision                                  Provision server port by applying or removing template configuration to an Arista CloudVision Porta...
	cyberark_authentication                              Module for CyberArk Vault Authentication using PAS Web Services SDK
	cyberark_user                                        Module for CyberArk User Management using PAS Web Services SDK
	data_pipeline                                        Create and manage AWS Datapipelines
	datadog_event                                        Posts events to DataDog  service
	datadog_monitor                                      Manages Datadog monitors
	dconf                                                Modify and read dconf database
	debconf                                              Configure a .deb package
	debug                                                Print statements during execution
	dellos10_command                                     Run commands on remote devices running Dell OS10
	dellos10_config                                      Manage Dell EMC Networking OS10 configuration sections
	dellos10_facts                                       Collect facts from remote devices running Dell EMC Networking OS10
	dellos6_command                                      Run commands on remote devices running Dell OS6
	dellos6_config                                       Manage Dell EMC Networking OS6 configuration sections
	dellos6_facts                                        Collect facts from remote devices running Dell EMC Networking OS6
	dellos9_command                                      Run commands on remote devices running Dell OS9
	dellos9_config                                       Manage Dell EMC Networking OS9 configuration sections
	dellos9_facts                                        Collect facts from remote devices running Dell EMC Networking OS9
	deploy_helper                                        Manages some of the steps common in deploying projects.
	digital_ocean                                        Create/delete a droplet/SSH_key in DigitalOcean
	digital_ocean_account_facts                          Gather facts about DigitalOcean User account
	digital_ocean_block_storage                          Create/destroy or attach/detach Block Storage volumes in DigitalOcean
	digital_ocean_certificate                            Manage certificates in DigitalOcean.
	digital_ocean_certificate_facts                      Gather facts about DigitalOcean certificates
	digital_ocean_domain                                 Create/delete a DNS record in DigitalOcean
	digital_ocean_domain_facts                           Gather facts about DigitalOcean Domains
	digital_ocean_floating_ip                            Manage DigitalOcean Floating IPs
	digital_ocean_floating_ip_facts                      DigitalOcean Floating IPs facts
	digital_ocean_image_facts                            Gather facts about DigitalOcean images
	digital_ocean_load_balancer_facts                    Gather facts about DigitalOcean load balancers
	digital_ocean_region_facts                           Gather facts about DigitalOcean regions
	digital_ocean_size_facts                             Gather facts about DigitalOcean Droplet sizes
	digital_ocean_snapshot_facts                         Gather facts about DigitalOcean Snapshot
	digital_ocean_sshkey                                 Manage DigitalOcean SSH keys
	digital_ocean_sshkey_facts                           DigitalOcean SSH keys facts
	digital_ocean_tag                                    Create and remove tag(s) to DigitalOcean resource.
	digital_ocean_tag_facts                              Gather facts about DigitalOcean tags
	digital_ocean_volume_facts                           Gather facts about DigitalOcean volumes
	dimensiondata_network                                Create, update, and delete MCP 1.0 & 2.0 networks
	dimensiondata_vlan                                   Manage a VLAN in a Cloud Control network domain.
	django_manage                                        Manages a Django application.
	dladm_etherstub                                      Manage etherstubs on Solaris/illumos systems.
	dladm_iptun                                          Manage IP tunnel interfaces on Solaris/illumos systems.
	dladm_linkprop                                       Manage link properties on Solaris/illumos systems.
	dladm_vlan                                           Manage VLAN interfaces on Solaris/illumos systems.
	dladm_vnic                                           Manage VNICs on Solaris/illumos systems.
	dnf                                                  Manages packages with the `dnf' package manager
	dnsimple                                             Interface with dnsimple.com (a DNS hosting service).
	dnsmadeeasy                                          Interface with dnsmadeeasy.com (a DNS hosting service).
	docker                                               manage docker containers
	docker_container                                     manage docker containers
	docker_image                                         Manage docker images.
	docker_image_facts                                   Inspect docker images
	docker_login                                         Log into a Docker registry.
	docker_network                                       Manage Docker networks
	docker_secret                                        Manage docker secrets.
	docker_service                                       Manage docker services and containers.
	docker_volume                                        Manage Docker volumes
	dpkg_selections                                      Dpkg package selection selections
	dynamodb_table                                       Create, update or delete AWS Dynamo DB tables.
	dynamodb_ttl                                         set TTL for a given DynamoDB table.
	easy_install                                         Installs Python libraries
	ec2                                                  create, terminate, start or stop an instance in ec2
	ec2_ami                                              create or destroy an image in ec2
	ec2_ami_copy                                         copies AMI between AWS regions, return new image id
	ec2_ami_facts                                        Gather facts about ec2 AMIs
	ec2_ami_find                                         Searches for AMIs to obtain the AMI ID and other information
	ec2_ami_search                                       Retrieve AWS AMI information for a given operating system.
	ec2_asg                                              Create or delete AWS Autoscaling Groups
	ec2_asg_facts                                        Gather facts about ec2 Auto Scaling Groups (ASGs) in AWS
	ec2_asg_lifecycle_hook                               Create, delete or update AWS ASG Lifecycle Hooks.
	ec2_customer_gateway                                 Manage an AWS customer gateway
	ec2_customer_gateway_facts                           Gather facts about customer gateways in AWS
	ec2_eip                                              manages EC2 elastic IP (EIP) addresses.
	ec2_eip_facts                                        List EC2 EIP details
	ec2_elb                                              De-registers or registers instances from EC2 ELBs
	ec2_elb_facts                                        Gather facts about EC2 Elastic Load Balancers in AWS
	ec2_elb_lb                                           Creates or destroys Amazon ELB.
	ec2_eni                                              Create and optionally attach an Elastic Network Interface (ENI) to an instance
	ec2_eni_facts                                        Gather facts about ec2 ENI interfaces in AWS
	ec2_group                                            maintain an ec2 VPC security group.
	ec2_group_facts                                      Gather facts about ec2 security groups in AWS.
	ec2_instance                                         Create & manage EC2 instances
	ec2_instance_facts                                   Gather facts about ec2 instances in AWS
	ec2_key                                              create or delete an ec2 key pair
	ec2_lc                                               Create or delete AWS Autoscaling Launch Configurations
	ec2_lc_facts                                         Gather facts about AWS Autoscaling Launch Configurations
	ec2_lc_find                                          Find AWS Autoscaling Launch Configurations
	ec2_metadata_facts                                   Gathers facts (instance metadata) about remote hosts within ec2
	ec2_metric_alarm                                     Create/update or delete AWS Cloudwatch 'metric alarms'
	ec2_placement_group                                  Create or delete an EC2 Placement Group
	ec2_placement_group_facts                            List EC2 Placement Group(s) details
	ec2_remote_facts                                     Gather facts about ec2 instances in AWS
	ec2_scaling_policy                                   Create or delete AWS scaling policies for Autoscaling groups
	ec2_snapshot                                         creates a snapshot from an existing volume
	ec2_snapshot_copy                                    copies an EC2 snapshot and returns the new Snapshot ID.
	ec2_snapshot_facts                                   Gather facts about ec2 volume snapshots in AWS
	ec2_tag                                              create and remove tag(s) to ec2 resources.
	ec2_vol                                              create and attach a volume, return volume id and device map
	ec2_vol_facts                                        Gather facts about ec2 volumes in AWS
	ec2_vpc                                              configure AWS virtual private clouds
	ec2_vpc_dhcp_option                                  Manages DHCP Options, and can ensure the DHCP options for the given VPC match what's requested
	ec2_vpc_dhcp_option_facts                            Gather facts about dhcp options sets in AWS
	ec2_vpc_egress_igw                                   Manage an AWS VPC Egress Only Internet gateway
	ec2_vpc_endpoint                                     Create and delete AWS VPC Endpoints.
	ec2_vpc_endpoint_facts                               Retrieves AWS VPC endpoints details using AWS methods.
	ec2_vpc_igw                                          Manage an AWS VPC Internet gateway
	ec2_vpc_igw_facts                                    Gather facts about internet gateways in AWS
	ec2_vpc_nacl                                         create and delete Network ACLs.
	ec2_vpc_nacl_facts                                   Gather facts about Network ACLs in an AWS VPC
	ec2_vpc_nat_gateway                                  Manage AWS VPC NAT Gateways.
	ec2_vpc_nat_gateway_facts                            Retrieves AWS VPC Managed Nat Gateway details using AWS methods.
	ec2_vpc_net                                          Configure AWS virtual private clouds
	ec2_vpc_net_facts                                    Gather facts about ec2 VPCs in AWS
	ec2_vpc_peer                                         create, delete, accept, and reject VPC peering connections between two VPCs.
	ec2_vpc_peering_facts                                Retrieves AWS VPC Peering details using AWS methods.
	ec2_vpc_route_table                                  Manage route tables for AWS virtual private clouds
	ec2_vpc_route_table_facts                            Gather facts about ec2 VPC route tables in AWS
	ec2_vpc_subnet                                       Manage subnets in AWS virtual private clouds
	ec2_vpc_subnet_facts                                 Gather facts about ec2 VPC subnets in AWS
	ec2_vpc_vgw                                          Create and delete AWS VPN Virtual Gateways.
	ec2_vpc_vgw_facts                                    Gather facts about virtual gateways in AWS
	ec2_vpc_vpn                                          Create, modify, and delete EC2 VPN connections.
	ec2_vpc_vpn_facts                                    Gather facts about VPN Connections in AWS.
	ec2_win_password                                     gets the default administrator password for ec2 windows instances
	ecs_attribute                                        manage ecs attributes
	ecs_cluster                                          create or terminate ecs clusters
	ecs_ecr                                              Manage Elastic Container Registry repositories
	ecs_service                                          create, terminate, start or stop a service in ecs
	ecs_service_facts                                    list or describe services in ecs
	ecs_task                                             run, start or stop a task in ecs
	ecs_taskdefinition                                   register a task definition in ecs
	ecs_taskdefinition_facts                             describe a task definition in ecs
	edgeos_command                                       Run one or more commands on EdgeOS devices
	edgeos_config                                        Manage EdgeOS configuration on remote device
	edgeos_facts                                         Collect facts from remote devices running EdgeOS
	efs                                                  create and maintain EFS file systems
	efs_facts                                            Get information about Amazon EFS file systems
	ejabberd_user                                        Manages users for ejabberd servers
	elasticache                                          Manage cache clusters in Amazon Elasticache.
	elasticache_facts                                    Retrieve facts for AWS Elasticache clusters
	elasticache_parameter_group                          Manage cache security groups in Amazon Elasticache.
	elasticache_snapshot                                 Manage cache snapshots in Amazon Elasticache.
	elasticache_subnet_group                             manage Elasticache subnet groups
	elasticsearch_plugin                                 Manage Elasticsearch plugins
	elb_application_lb                                   Manage an Application load balancer
	elb_application_lb_facts                             Gather facts about application ELBs in AWS
	elb_classic_lb                                       Creates or destroys Amazon ELB.
	elb_classic_lb_facts                                 Gather facts about EC2 Elastic Load Balancers in AWS
	elb_instance                                         De-registers or registers instances from EC2 ELBs
	elb_network_lb                                       Manage a Network Load Balancer
	elb_target                                           Manage a target in a target group
	elb_target_group                                     Manage a target group for an Application or Network load balancer
	elb_target_group_facts                               Gather facts about ELB target groups in AWS
	enos_command                                         Run arbitrary commands on Lenovo ENOS devices
	enos_config                                          Manage Lenovo ENOS configuration sections
	enos_facts                                           Collect facts from remote devices running Lenovo ENOS
	eos_banner                                           Manage multiline banners on Arista EOS devices
	eos_command                                          Run arbitrary commands on an Arista EOS device
	eos_config                                           Manage Arista EOS configuration sections
	eos_eapi                                             Manage and configure Arista EOS eAPI.
	eos_facts                                            Collect facts from remote devices running Arista EOS
	eos_interface                                        Manage Interface on Arista EOS network devices
	eos_l2_interface                                     Manage L2 interfaces on Arista EOS network devices.
	eos_l3_interface                                     Manage L3 interfaces on Arista EOS network devices.
	eos_linkagg                                          Manage link aggregation groups on Arista EOS network devices
	eos_lldp                                             Manage LLDP configuration on Arista EOS network devices
	eos_logging                                          Manage logging on network devices
	eos_static_route                                     Manage static IP routes on Arista EOS network devices
	eos_system                                           Manage the system attributes on Arista EOS devices
	eos_user                                             Manage the collection of local users on EOS devices
	eos_vlan                                             Manage VLANs on Arista EOS network devices
	eos_vrf                                              Manage VRFs on Arista EOS network devices
	etcd3                                                Set or delete key value pairs from an etcd3 cluster
	execute_lambda                                       Execute an AWS Lambda function
	exo_dns_domain                                       Manages domain records on Exoscale DNS API.
	exo_dns_record                                       Manages DNS records on Exoscale DNS.
	exos_command                                         Run commands on remote devices running Extreme EXOS
	expect                                               Executes a command and responds to prompts.
	facter                                               Runs the discovery program `facter' on the remote system
	fail                                                 Fail with custom message
	fetch                                                Fetches a file from remote nodes
	file                                                 Sets attributes of files
	filesystem                                           Makes a filesystem
	find                                                 Return a list of files based on specific criteria
	firewalld                                            Manage arbitrary ports/services with firewalld
	flatpak                                              Manage flatpaks
	flatpak_remote                                       Manage flatpak repository remotes
	flowadm                                              Manage bandwidth resource control and priority for protocols, services and zones on Solaris/illumos...
	flowdock                                             Send a message to a flowdock
	fmgr_script                                          Add/Edit/Delete and execute scripts
	foreman                                              Manage Foreman Resources
	fortios_address                                      Manage fortios firewall address objects
	fortios_config                                       Manage config on Fortinet FortiOS firewall devices
	fortios_ipv4_policy                                  Manage IPv4 policy objects on Fortinet FortiOS firewall devices
	fortios_webfilter                                    Configure webfilter capabilities of FortiGate and FortiOS.
	gc_storage                                           This module manages objects/buckets in Google Cloud Storage.
	gcdns_record                                         Creates or removes resource records in Google Cloud DNS
	gcdns_zone                                           Creates or removes zones in Google Cloud DNS
	gce                                                  create or terminate GCE instances
	gce_eip                                              Create or Destroy Global or Regional External IP addresses.
	gce_img                                              utilize GCE image resources
	gce_instance_template                                create or destroy instance templates of Compute Engine of GCP.
	gce_labels                                           Create, Update or Destroy GCE Labels.
	gce_lb                                               create/destroy GCE load-balancer resources
	gce_mig                                              Create, Update or Destroy a Managed Instance Group (MIG).
	gce_net                                              create/destroy GCE networks and firewall rules
	gce_pd                                               utilize GCE persistent disk resources
	gce_snapshot                                         Create or destroy snapshots for GCE storage volumes
	gce_tag                                              add or remove tag(s) to/from GCE instances
	gconftool2                                           Edit GNOME Configurations
	gcp_backend_service                                  Create or Destroy a Backend Service.
	gcp_compute_address                                  Creates a GCP Address
	gcp_compute_backend_bucket                           Creates a GCP BackendBucket
	gcp_compute_backend_service                          Creates a GCP BackendService
	gcp_compute_disk                                     Creates a GCP Disk
	gcp_compute_firewall                                 Creates a GCP Firewall
	gcp_compute_forwarding_rule                          Creates a GCP ForwardingRule
	gcp_compute_global_address                           Creates a GCP GlobalAddress
	gcp_compute_global_forwarding_rule                   Creates a GCP GlobalForwardingRule
	gcp_compute_health_check                             Creates a GCP HealthCheck
	gcp_compute_http_health_check                        Creates a GCP HttpHealthCheck
	gcp_compute_https_health_check                       Creates a GCP HttpsHealthCheck
	gcp_compute_image                                    Creates a GCP Image
	gcp_compute_instance                                 Creates a GCP Instance
	gcp_compute_instance_group                           Creates a GCP InstanceGroup
	gcp_compute_instance_group_manager                   Creates a GCP InstanceGroupManager
	gcp_compute_instance_template                        Creates a GCP InstanceTemplate
	gcp_compute_network                                  Creates a GCP Network
	gcp_compute_route                                    Creates a GCP Route
	gcp_compute_ssl_certificate                          Creates a GCP SslCertificate
	gcp_compute_subnetwork                               Creates a GCP Subnetwork
	gcp_compute_target_http_proxy                        Creates a GCP TargetHttpProxy
	gcp_compute_target_https_proxy                       Creates a GCP TargetHttpsProxy
	gcp_compute_target_pool                              Creates a GCP TargetPool
	gcp_compute_target_ssl_proxy                         Creates a GCP TargetSslProxy
	gcp_compute_target_tcp_proxy                         Creates a GCP TargetTcpProxy
	gcp_compute_url_map                                  Creates a GCP UrlMap
	gcp_container_cluster                                Creates a GCP Cluster
	gcp_container_node_pool                              Creates a GCP NodePool
	gcp_dns_managed_zone                                 Creates a GCP ManagedZone
	gcp_dns_resource_record_set                          Creates a GCP ResourceRecordSet
	gcp_forwarding_rule                                  Create, Update or Destroy a Forwarding_Rule.
	gcp_healthcheck                                      Create, Update or Destroy a Healthcheck.
	gcp_pubsub_subscription                              Creates a GCP Subscription
	gcp_pubsub_topic                                     Creates a GCP Topic
	gcp_storage_bucket                                   Creates a GCP Bucket
	gcp_storage_bucket_access_control                    Creates a GCP BucketAccessControl
	gcp_target_proxy                                     Create, Update or Destroy a Target_Proxy.
	gcp_url_map                                          Create, Update or Destory a Url_Map.
	gcpubsub                                             Create and Delete Topics/Subscriptions, Publish and pull messages on PubSub
	gcpubsub_facts                                       List Topics/Subscriptions and Messages from Google PubSub.
	gcspanner                                            Create and Delete Instances/Databases on Spanner
	gem                                                  Manage Ruby gems
	get_url                                              Downloads files from HTTP, HTTPS, or FTP to node
	getent                                               A wrapper to the unix getent utility
	git                                                  Deploy software (or files) from git checkouts
	git_config                                           Read and write git configuration
	github_deploy_key                                    Manages deploy keys for GitHub repositories.
	github_hooks                                         Manages GitHub service hooks.
	github_issue                                         View GitHub issue.
	github_key                                           Manage GitHub access keys.
	github_release                                       Interact with GitHub Releases
	gitlab_deploy_key                                    Manages GitLab project deploy keys.
	gitlab_group                                         Creates/updates/deletes Gitlab Groups
	gitlab_hooks                                         Manages GitLab project hooks.
	gitlab_project                                       Creates/updates/deletes Gitlab Projects
	gitlab_user                                          Creates/updates/deletes Gitlab Users
	gluster_peer                                         Attach/Detach peers to/from the cluster
	gluster_volume                                       Manage GlusterFS volumes
	grafana_dashboard                                    Manage Grafana dashboards
	grafana_datasource                                   Manage Grafana datasources
	grafana_plugin                                       Manage Grafana plugins via grafana-cli
	group                                                Add or remove groups
	group_by                                             Create Ansible groups based on facts
	grove                                                Sends a notification to a grove.io channel
	gunicorn                                             Run gunicorn with various settings.
	hall                                                 Send notification to Hall
	haproxy                                              Enable, disable, and set weights for HAProxy backend servers using socket commands.
	helm                                                 Manages Kubernetes packages with the Helm package manager
	heroku_collaborator                                  Add or delete app collaborators on Heroku
	hg                                                   Manages Mercurial (hg) repositories
	hipchat                                              Send a message to Hipchat.
	homebrew                                             Package manager for Homebrew
	homebrew_cask                                        Install/uninstall homebrew casks.
	homebrew_tap                                         Tap a Homebrew repository.
	honeybadger_deployment                               Notify Honeybadger.io about app deployments
	hostname                                             Manage hostname
	hpilo_boot                                           Boot system using specific media through HP iLO interface
	hpilo_facts                                          Gather facts through an HP iLO interface
	hponcfg                                              Configure HP iLO interface using hponcfg
	htpasswd                                             manage user files for basic authentication
	iam                                                  Manage IAM users, groups, roles and keys
	iam_cert                                             Manage server certificates for use on ELBs and CloudFront
	iam_group                                            Manage AWS IAM groups
	iam_managed_policy                                   Manage User Managed IAM policies
	iam_mfa_device_facts                                 List the MFA (Multi-Factor Authentication) devices registered for a user
	iam_policy                                           Manage IAM policies for users, groups, and roles
	iam_role                                             Manage AWS IAM roles
	iam_role_facts                                       Gather information on IAM roles
	iam_server_certificate_facts                         Retrieve the facts of a server certificate
	iam_user                                             Manage AWS IAM users
	icinga2_feature                                      Manage Icinga2 feature
	icinga2_host                                         Manage a host in Icinga2
	imc_rest                                             Manage Cisco IMC hardware through its REST API
	imgadm                                               Manage SmartOS images
	import_playbook                                      Import a playbook
	import_role                                          Import a role into a play
	import_tasks                                         Import a task list
	include                                              Include a play or task list
	include_role                                         Load and execute a role
	include_tasks                                        Dynamically include a task list
	include_vars                                         Load variables from files, dynamically within a task
	infini_export                                        Create, Delete or Modify NFS Exports on Infinibox
	infini_export_client                                 Create, Delete or Modify NFS Client(s) for existing exports on Infinibox
	infini_fs                                            Create, Delete or Modify filesystems on Infinibox
	infini_host                                          Create, Delete and Modify Hosts on Infinibox
	infini_pool                                          Create, Delete and Modify Pools on Infinibox
	infini_vol                                           Create, Delete or Modify volumes on Infinibox
	infinity                                             manage Infinity IPAM using Rest API
	influxdb_database                                    Manage InfluxDB databases
	influxdb_query                                       Query data points from InfluxDB.
	influxdb_retention_policy                            Manage InfluxDB retention policies
	influxdb_user                                        Manage InfluxDB users
	influxdb_write                                       Write data points into InfluxDB.
	ini_file                                             Tweak settings in INI files
	interfaces_file                                      Tweak settings in /etc/network/interfaces files
	ios_banner                                           Manage multiline banners on Cisco IOS devices
	ios_command                                          Run commands on remote devices running Cisco IOS
	ios_config                                           Manage Cisco IOS configuration sections
	ios_facts                                            Collect facts from remote devices running Cisco IOS
	ios_interface                                        Manage Interface on Cisco IOS network devices
	ios_l2_interface                                     Manage Layer-2 interface on Cisco IOS devices.
	ios_l3_interface                                     Manage L3 interfaces on Cisco IOS network devices.
	ios_linkagg                                          Manage link aggregation groups on Cisco IOS network devices
	ios_lldp                                             Manage LLDP configuration on Cisco IOS network devices.
	ios_logging                                          Manage logging on network devices
	ios_ping                                             Tests reachability using ping from Cisco IOS network devices
	ios_static_route                                     Manage static IP routes on Cisco IOS network devices
	ios_system                                           Manage the system attributes on Cisco IOS devices
	ios_user                                             Manage the aggregate of local users on Cisco IOS device
	ios_vlan                                             Manage VLANs on IOS network devices
	ios_vrf                                              Manage the collection of VRF definitions on Cisco IOS devices
	iosxr_banner                                         Manage multiline banners on Cisco IOS XR devices
	iosxr_command                                        Run commands on remote devices running Cisco IOS XR
	iosxr_config                                         Manage Cisco IOS XR configuration sections
	iosxr_facts                                          Collect facts from remote devices running IOS XR
	iosxr_interface                                      Manage Interface on Cisco IOS XR network devices
	iosxr_logging                                        Configuration management of system logging services on network devices
	iosxr_netconf                                        Configures NetConf sub-system service on Cisco IOS-XR devices
	iosxr_system                                         Manage the system attributes on Cisco IOS XR devices
	iosxr_user                                           Manage the aggregate of local users on Cisco IOS XR device
	ip_netns                                             Manage network namespaces
	ipa_dnsrecord                                        Manage FreeIPA DNS records
	ipa_dnszone                                          Manage FreeIPA DNS Zones
	ipa_group                                            Manage FreeIPA group
	ipa_hbacrule                                         Manage FreeIPA HBAC rule
	ipa_host                                             Manage FreeIPA host
	ipa_hostgroup                                        Manage FreeIPA host-group
	ipa_role                                             Manage FreeIPA role
	ipa_service                                          Manage FreeIPA service
	ipa_subca                                            Manage FreeIPA Lightweight Sub Certificate Authorities.
	ipa_sudocmd                                          Manage FreeIPA sudo command
	ipa_sudocmdgroup                                     Manage FreeIPA sudo command group
	ipa_sudorule                                         Manage FreeIPA sudo rule
	ipa_user                                             Manage FreeIPA users
	ipadm_addr                                           Manage IP addresses on an interface on Solaris/illumos systems
	ipadm_addrprop                                       Manage IP address properties on Solaris/illumos systems.
	ipadm_if                                             Manage IP interfaces  on Solaris/illumos systems.
	ipadm_ifprop                                         Manage IP interface properties on Solaris/illumos systems.
	ipadm_prop                                           Manage protocol properties on Solaris/illumos systems.
	ipify_facts                                          Retrieve the public IP of your internet gateway.
	ipinfoio_facts                                       Retrieve IP geolocation facts of a host's IP address
	ipmi_boot                                            Management of order of boot devices
	ipmi_power                                           Power management for machine
	iptables                                             Modify the systems iptables
	irc                                                  Send a message to an IRC channel
	ironware_command                                     Run arbitrary commands on Extreme IronWare devices
	ironware_config                                      Manage configuration sections on Extreme Ironware devices
	ironware_facts                                       Collect facts from devices running Extreme Ironware
	iso_extract                                          Extract files from an ISO image
	jabber                                               Send a message to jabber user or chat room
	java_cert                                            Uses keytool to import/remove key from java keystore(cacerts)
	jboss                                                deploy applications to JBoss
	jenkins_job                                          Manage jenkins jobs
	jenkins_job_facts                                    Get facts about Jenkins jobs
	jenkins_plugin                                       Add or remove Jenkins plugin
	jenkins_script                                       Executes a groovy script in the jenkins instance
	jira                                                 create and modify issues in a JIRA instance
	junos_banner                                         Manage multiline banners on Juniper JUNOS devices
	junos_command                                        Run arbitrary commands on an Juniper JUNOS device
	junos_config                                         Manage configuration on devices running Juniper JUNOS
	junos_facts                                          Collect facts from remote devices running Juniper Junos
	junos_interface                                      Manage Interface on Juniper JUNOS network devices
	junos_l2_interface                                   Manage Layer-2 interface on Juniper JUNOS network devices
	junos_l3_interface                                   Manage L3 interfaces on Juniper JUNOS network devices
	junos_linkagg                                        Manage link aggregation groups on Juniper JUNOS network devices
	junos_lldp                                           Manage LLDP configuration on Juniper JUNOS network devices
	junos_lldp_interface                                 Manage LLDP interfaces configuration on Juniper JUNOS network devices
	junos_logging                                        Manage logging on network devices
	junos_netconf                                        Configures the Junos Netconf system service
	junos_package                                        Installs packages on remote devices running Junos
	junos_rpc                                            Runs an arbitrary RPC over NetConf on an Juniper JUNOS device
	junos_scp                                            Transfer files from or to remote devices running Junos
	junos_static_route                                   Manage static IP routes on Juniper JUNOS network devices
	junos_system                                         Manage the system attributes on Juniper JUNOS devices
	junos_user                                           Manage local user accounts on Juniper JUNOS devices
	junos_vlan                                           Manage VLANs on Juniper JUNOS network devices
	junos_vrf                                            Manage the VRF definitions on Juniper JUNOS devices
	k8s                                                  Manage Kubernetes (K8s) objects
	k8s_scale                                            Set a new size for a Deployment, ReplicaSet, Replication Controller, or Job.
	katello                                              Manage Katello Resources
	kernel_blacklist                                     Blacklist kernel modules
	keycloak_client                                      Allows administration of Keycloak clients via Keycloak API
	keycloak_clienttemplate                              Allows administration of Keycloak client templates via Keycloak API
	kibana_plugin                                        Manage Kibana plugins
	kinesis_stream                                       Manage a Kinesis Stream.
	known_hosts                                          Add or remove a host from the `known_hosts' file
	kubernetes                                           Manage Kubernetes resources
	lambda                                               Manage AWS Lambda functions
	lambda_alias                                         Creates, updates or deletes AWS Lambda function aliases.
	lambda_event                                         Creates, updates or deletes AWS Lambda function event mappings.
	lambda_facts                                         Gathers AWS Lambda function details as Ansible facts
	lambda_policy                                        Creates, updates or deletes AWS Lambda policy statements.
	layman                                               Manage Gentoo overlays
	ldap_attr                                            Add or remove LDAP attribute values.
	ldap_entry                                           Add or remove LDAP entries.
	ldap_passwd                                          Set passwords in LDAP.
	librato_annotation                                   create an annotation in librato
	lightsail                                            Create or delete a virtual machine instance in AWS Lightsail
	lineinfile                                           Manage lines in text files
	linode                                               Manage instances on the Linode Public Cloud
	lldp                                                 get details reported by lldp
	locale_gen                                           Creates or removes locales
	logentries                                           Module for tracking logs via logentries.com
	logentries_msg                                       Send a message to logentries.
	logicmonitor                                         Manage your LogicMonitor account through Ansible Playbooks
	logicmonitor_facts                                   Collect facts about LogicMonitor objects
	logstash_plugin                                      Manage Logstash plugins
	lvg                                                  Configure LVM volume groups
	lvol                                                 Configure LVM logical volumes
	lxc_container                                        Manage LXC Containers
	lxd_container                                        Manage LXD Containers
	lxd_profile                                          Manage LXD profiles
	macports                                             Package manager for MacPorts
	mail                                                 Send an email
	make                                                 Run targets in a Makefile
	manageiq_alert_profiles                              Configuration of alert profiles for ManageIQ
	manageiq_alerts                                      Configuration of alerts in ManageIQ
	manageiq_policies                                    Management of resource policy_profiles in ManageIQ.
	manageiq_provider                                    Management of provider in ManageIQ.
	manageiq_tags                                        Management of resource tags in ManageIQ.
	manageiq_user                                        Management of users in ManageIQ.
	mattermost                                           Send Mattermost notifications
	maven_artifact                                       Downloads an Artifact from a Maven Repository
	memset_dns_reload                                    Request reload of Memset's DNS infrastructure,
	memset_zone                                          Creates and deletes Memset DNS zones.
	memset_zone_domain                                   Create and delete domains in Memset DNS zones.
	memset_zone_record                                   Create and delete records in Memset DNS zones.
	meraki_admin                                         Manage administrators in the Meraki cloud
	meraki_network                                       Manage networks in the Meraki cloud
	meraki_organization                                  Manage organizations in the Meraki cloud
	meraki_snmp                                          Manage organizations in the Meraki cloud
	meta                                                 Execute Ansible 'actions'
	mksysb                                               Generates AIX mksysb rootvg backups.
	modprobe                                             Load or unload kernel modules
	mongodb_parameter                                    Change an administrative parameter on a MongoDB server.
	mongodb_user                                         Adds or removes a user from a MongoDB database.
	monit                                                Manage the state of a program monitored via Monit
	mount                                                Control active and configured mount points
	mqtt                                                 Publish a message on an MQTT topic for the IoT
	mssql_db                                             Add or remove MSSQL databases from a remote host.
	mysql_db                                             Add or remove MySQL databases from a remote host.
	mysql_replication                                    Manage MySQL replication
	mysql_user                                           Adds or removes a user from a MySQL database.
	mysql_variables                                      Manage MySQL global variables
	na_cdot_aggregate                                    Manage NetApp cDOT aggregates.
	na_cdot_license                                      Manage NetApp cDOT protocol and feature licenses
	na_cdot_lun                                          Manage  NetApp cDOT luns
	na_cdot_qtree                                        Manage qtrees
	na_cdot_svm                                          Manage NetApp cDOT svm
	na_cdot_user                                         useradmin configuration and management
	na_cdot_user_role                                    useradmin configuration and management
	na_cdot_volume                                       Manage NetApp cDOT volumes
	na_ontap_aggregate                                   Manage NetApp ONTAP aggregates.
	na_ontap_broadcast_domain                            Manage NetApp ONTAP broadcast domains.
	na_ontap_broadcast_domain_ports                      Manage NetApp Ontap broadcast domain ports
	na_ontap_cifs                                        Manage NetApp cifs-share
	na_ontap_cifs_acl                                    Manage NetApp cifs-share-access-control
	na_ontap_cifs_server                                 cifs server configuration
	na_ontap_cluster                                     Create/Join ONTAP cluster. Apply license to cluster
	na_ontap_cluster_ha                                  Manage HA status for cluster
	na_ontap_export_policy                               Manage NetApp ONTAP export-policy
	na_ontap_export_policy_rule                          Manage ONTAP Export rules
	na_ontap_igroup                                      ONTAP iSCSI igroup configuration
	na_ontap_interface                                   ONTAP LIF configuration
	na_ontap_iscsi                                       Manage NetApp Ontap iscsi service
	na_ontap_job_schedule                                Manage NetApp Ontap Job Schedule
	na_ontap_license                                     Manage NetApp ONTAP protocol and feature licenses
	na_ontap_lun                                         Manage  NetApp Ontap luns
	na_ontap_lun_map                                     Manage NetApp Ontap lun maps
	na_ontap_net_ifgrp                                   Create, modify, destroy the network interface group
	na_ontap_net_port                                    Manage NetApp Ontap network ports.
	na_ontap_net_routes                                  Manage NetApp Ontap network routes
	na_ontap_net_vlan                                    Manage NetApp Ontap network vlan
	na_ontap_nfs                                         Manage Ontap NFS status
	na_ontap_ntp                                         Create/Delete/modify_version ONTAP NTP server
	na_ontap_qtree                                       Manage qtrees
	na_ontap_service_processor_network                   Manage NetApp Ontap service processor network
	na_ontap_snapshot                                    Manage NetApp Sanpshots
	na_ontap_snmp                                        Manage NetApp SNMP community
	na_ontap_svm                                         Manage NetApp Ontap svm
	na_ontap_ucadapter                                   ONTAP UC adapter configuration
	na_ontap_user                                        useradmin configuration and management
	na_ontap_user_role                                   useradmin configuration and management
	na_ontap_volume                                      Manage NetApp ONTAP volumes.
	na_ontap_volume_clone                                Manage NetApp Ontap volume clones.
	nagios                                               Perform common tasks in Nagios related to downtime and notifications.
	nclu                                                 Configure network interfaces using NCLU
	net_banner                                           Manage multiline banners on network devices
	net_get                                              Copy a file from a network device to Ansible Controller
	net_interface                                        Manage Interface on network devices
	net_l2_interface                                     Manage Layer-2 interface on network devices
	net_l3_interface                                     Manage L3 interfaces on network devices
	net_linkagg                                          Manage link aggregation groups on network devices
	net_lldp                                             Manage LLDP service configuration on network devices
	net_lldp_interface                                   Manage LLDP interfaces configuration on network devices
	net_logging                                          Manage logging on network devices
	net_ping                                             Tests reachability using ping from a network device
	net_put                                              Copy a file from Ansible Controller to a network device
	net_static_route                                     Manage static IP routes on network appliances (routers, switches et. al.)
	net_system                                           Manage the system attributes on network devices
	net_user                                             Manage the aggregate of local users on network device
	net_vlan                                             Manage VLANs on network devices
	net_vrf                                              Manage VRFs on network devices
	netact_cm_command                                    Manage network configuration data in Nokia Core and Radio networks
	netapp_e_amg                                         Create, Remove, and Update Asynchronous Mirror Groups
	netapp_e_amg_role                                    Update the role of a storage array within an Asynchronous Mirror Group (AMG).
	netapp_e_amg_sync                                    Conduct synchronization actions on asynchronous mirror groups.
	netapp_e_auth                                        Sets or updates the password for a storage array.
	netapp_e_facts                                       Get facts about NetApp E-Series arrays
	netapp_e_flashcache                                  Manage NetApp SSD caches
	netapp_e_host                                        manage eseries hosts
	netapp_e_hostgroup                                   Manage NetApp Storage Array Host Groups
	netapp_e_lun_mapping                                 Create or Remove LUN Mappings
	netapp_e_snapshot_group                              Manage snapshot groups
	netapp_e_snapshot_images                             Create and delete snapshot images
	netapp_e_snapshot_volume                             Manage E/EF-Series snapshot volumes.
	netapp_e_storage_system                              Add/remove arrays from the Web Services Proxy
	netapp_e_storagepool                                 Manage disk groups and disk pools
	netapp_e_volume                                      Manage storage volumes (standard and thin)
	netapp_e_volume_copy                                 Create volume copy pairs
	netconf_config                                       netconf device configuration
	netconf_get                                          Fetch configuration/state data from NETCONF enabled network devices.
	netconf_rpc                                          Execute operations on NETCONF enabled network devices.
	netscaler                                            Manages Citrix NetScaler entities
	netscaler_cs_action                                  Manage content switching actions
	netscaler_cs_policy                                  Manage content switching policy
	netscaler_cs_vserver                                 Manage content switching vserver
	netscaler_gslb_service                               Manage gslb service entities in Netscaler.
	netscaler_gslb_site                                  Manage gslb site entities in Netscaler.
	netscaler_gslb_vserver                               Configure gslb vserver entities in Netscaler.
	netscaler_lb_monitor                                 Manage load balancing monitors
	netscaler_lb_vserver                                 Manage load balancing vserver configuration
	netscaler_nitro_request                              Issue Nitro API requests to a Netscaler instance.
	netscaler_save_config                                Save Netscaler configuration.
	netscaler_server                                     Manage server configuration
	netscaler_service                                    Manage service configuration in Netscaler
	netscaler_servicegroup                               Manage service group configuration in Netscaler
	netscaler_ssl_certkey                                Manage ssl cerificate keys.
	newrelic_deployment                                  Notify newrelic about app deployments
	nexmo                                                Send a SMS via nexmo
	nginx_status_facts                                   Retrieve nginx status facts.
	nios_dns_view                                        Configure Infoblox NIOS DNS views
	nios_host_record                                     Configure Infoblox NIOS host records
	nios_network                                         Configure Infoblox NIOS network object
	nios_network_view                                    Configure Infoblox NIOS network views
	nios_zone                                            Configure Infoblox NIOS DNS zones
	nmcli                                                Manage Networking
	nosh                                                 Manage services with nosh
	npm                                                  Manage node.js packages with npm
	nso_action                                           Executes Cisco NSO actions and verifies output.
	nso_config                                           Manage Cisco NSO configuration and service synchronization.
	nso_query                                            Query data from Cisco NSO.
	nso_show                                             Displays data from Cisco NSO.
	nso_verify                                           Verifies Cisco NSO configuration.
	nsupdate                                             Manage DNS records.
	nuage_vspk                                           Manage Nuage VSP environments
	nxos_aaa_server                                      Manages AAA server global configuration.
	nxos_aaa_server_host                                 Manages AAA server host-specific configuration.
	nxos_acl                                             Manages access list entries for ACLs.
	nxos_acl_interface                                   Manages applying ACLs to interfaces.
	nxos_banner                                          Manage multiline banners on Cisco NXOS devices
	nxos_bgp                                             Manages BGP configuration.
	nxos_bgp_af                                          Manages BGP Address-family configuration.
	nxos_bgp_neighbor                                    Manages BGP neighbors configurations.
	nxos_bgp_neighbor_af                                 Manages BGP address-family's neighbors configuration.
	nxos_command                                         Run arbitrary command on Cisco NXOS devices
	nxos_config                                          Manage Cisco NXOS configuration sections
	nxos_evpn_global                                     Handles the EVPN control plane for VXLAN.
	nxos_evpn_vni                                        Manages Cisco EVPN VXLAN Network Identifier (VNI).
	nxos_facts                                           Gets facts about NX-OS switches
	nxos_feature                                         Manage features in NX-OS switches.
	nxos_file_copy                                       Copy a file to a remote NXOS device over SCP.
	nxos_gir                                             Trigger a graceful removal or insertion (GIR) of the switch.
	nxos_gir_profile_management                          Create a maintenance-mode or normal-mode profile for GIR.
	nxos_hsrp                                            Manages HSRP configuration on NX-OS switches.
	nxos_igmp                                            Manages IGMP global configuration.
	nxos_igmp_interface                                  Manages IGMP interface configuration.
	nxos_igmp_snooping                                   Manages IGMP snooping global configuration.
	nxos_install_os                                      Set boot options like boot, kickstart image and issu.
	nxos_interface                                       Manages physical attributes of interfaces.
	nxos_interface_ospf                                  Manages configuration of an OSPF interface instance.
	nxos_ip_interface                                    Manages L3 attributes for IPv4 and IPv6 interfaces.
	nxos_l2_interface                                    Manage Layer-2 interface on Cisco NXOS devices.
	nxos_l3_interface                                    Manage L3 interfaces on Cisco NXOS network devices
	nxos_linkagg                                         Manage link aggregation groups on Cisco NXOS devices.
	nxos_lldp                                            Manage LLDP configuration on Cisco NXOS network devices.
	nxos_logging                                         Manage logging on network devices
	nxos_mtu                                             Manages MTU settings on Nexus switch.
	nxos_ntp                                             Manages core NTP configuration.
	nxos_ntp_auth                                        Manages NTP authentication.
	nxos_ntp_options                                     Manages NTP options.
	nxos_nxapi                                           Manage NXAPI configuration on an NXOS device.
	nxos_ospf                                            Manages configuration of an ospf instance.
	nxos_ospf_vrf                                        Manages a VRF for an OSPF router.
	nxos_overlay_global                                  Configures anycast gateway MAC of the switch.
	nxos_pim                                             Manages configuration of a PIM instance.
	nxos_pim_interface                                   Manages PIM interface configuration.
	nxos_pim_rp_address                                  Manages configuration of an PIM static RP address instance.
	nxos_ping                                            Tests reachability using ping from Nexus switch.
	nxos_portchannel                                     Manages port-channel interfaces.
	nxos_reboot                                          Reboot a network device.
	nxos_rollback                                        Set a checkpoint or rollback to a checkpoint.
	nxos_smu                                             Perform SMUs on Cisco NX-OS devices.
	nxos_snapshot                                        Manage snapshots of the running states of selected features.
	nxos_snmp_community                                  Manages SNMP community configs.
	nxos_snmp_contact                                    Manages SNMP contact info.
	nxos_snmp_host                                       Manages SNMP host configuration.
	nxos_snmp_location                                   Manages SNMP location information.
	nxos_snmp_traps                                      Manages SNMP traps.
	nxos_snmp_user                                       Manages SNMP users for monitoring.
	nxos_static_route                                    Manages static route configuration
	nxos_switchport                                      Manages Layer 2 switchport interfaces.
	nxos_system                                          Manage the system attributes on Cisco NXOS devices
	nxos_udld                                            Manages UDLD global configuration params.
	nxos_udld_interface                                  Manages UDLD interface configuration params.
	nxos_user                                            Manage the collection of local users on Nexus devices
	nxos_vlan                                            Manages VLAN resources and attributes.
	nxos_vpc                                             Manages global VPC configuration
	nxos_vpc_interface                                   Manages interface VPC configuration
	nxos_vrf                                             Manages global VRF configuration.
	nxos_vrf_af                                          Manages VRF AF.
	nxos_vrf_interface                                   Manages interface specific VRF configuration.
	nxos_vrrp                                            Manages VRRP configuration on NX-OS switches.
	nxos_vtp_domain                                      Manages VTP domain configuration.
	nxos_vtp_password                                    Manages VTP password configuration.
	nxos_vtp_version                                     Manages VTP version configuration.
	nxos_vxlan_vtep                                      Manages VXLAN Network Virtualization Endpoint (NVE).
	nxos_vxlan_vtep_vni                                  Creates a Virtual Network Identifier member (VNI)
	oc                                                   Manage OpenShift Resources
	office_365_connector_card                            Use webhooks to create Connector Card messages within an Office 365 group
	ohai                                                 Returns inventory data from `Ohai'
	omapi_host                                           Setup OMAPI hosts.
	one_host                                             Manages OpenNebula Hosts
	one_image                                            Manages OpenNebula images
	one_image_facts                                      Gather facts about OpenNebula images
	one_service                                          Deploy and manage OpenNebula services
	one_vm                                               Creates or terminates OpenNebula instances
	oneandone_firewall_policy                            Configure 1&1 firewall policy.
	oneandone_load_balancer                              Configure 1&1 load balancer.
	oneandone_monitoring_policy                          Configure 1&1 monitoring policy.
	oneandone_private_network                            Configure 1&1 private networking.
	oneandone_public_ip                                  Configure 1&1 public IPs.
	oneandone_server                                     Create, destroy, start, stop, and reboot a 1&1 Host server.
	oneview_datacenter_facts                             Retrieve facts about the OneView Data Centers
	oneview_enclosure_facts                              Retrieve facts about one or more Enclosures
	oneview_ethernet_network                             Manage OneView Ethernet Network resources
	oneview_ethernet_network_facts                       Retrieve the facts about one or more of the OneView Ethernet Networks
	oneview_fc_network                                   Manage OneView Fibre Channel Network resources.
	oneview_fc_network_facts                             Retrieve the facts about one or more of the OneView Fibre Channel Networks
	oneview_fcoe_network                                 Manage OneView FCoE Network resources
	oneview_fcoe_network_facts                           Retrieve the facts about one or more of the OneView FCoE Networks
	oneview_logical_interconnect_group                   Manage OneView Logical Interconnect Group resources
	oneview_logical_interconnect_group_facts             Retrieve facts about one or more of the OneView Logical Interconnect Groups
	oneview_network_set                                  Manage HPE OneView Network Set resources
	oneview_network_set_facts                            Retrieve facts about the OneView Network Sets
	oneview_san_manager                                  Manage OneView SAN Manager resources
	oneview_san_manager_facts                            Retrieve facts about one or more of the OneView SAN Managers
	onyx_bgp                                             Configures BGP on Mellanox ONYX network devices
	onyx_command                                         Run commands on remote devices running Mellanox ONYX
	onyx_config                                          Manage Mellanox ONYX configuration sections
	onyx_facts                                           Collect facts from Mellanox ONYX network devices
	onyx_interface                                       Manage Interfaces on Mellanox ONYX network devices
	onyx_l2_interface                                    Manage Layer-2 interface on Mellanox ONYX network devices
	onyx_l3_interface                                    Manage L3 interfaces on Mellanox ONYX network devices
	onyx_linkagg                                         Manage link aggregation groups on Mellanox ONYX network devices
	onyx_lldp                                            Manage LLDP configuration on Mellanox ONYX network devices
	onyx_lldp_interface                                  Manage LLDP interfaces configuration on Mellanox ONYX network devices
	onyx_magp                                            Manage MAGP protocol on Mellanox ONYX network devices
	onyx_mlag_ipl                                        Manage IPL (inter-peer link) on Mellanox ONYX network devices
	onyx_mlag_vip                                        Configures MLAG VIP on Mellanox ONYX network devices
	onyx_ospf                                            Manage OSPF protocol on Mellanox ONYX network devices
	onyx_pfc_interface                                   Manage priority flow control on ONYX network devices
	onyx_protocol                                        Enables/Disables protocols on Mellanox ONYX network devices
	onyx_vlan                                            Manage VLANs on Mellanox ONYX network devices
	open_iscsi                                           Manage iscsi targets with open-iscsi
	openbsd_pkg                                          Manage packages on OpenBSD
	opendj_backendprop                                   Will update the backend configuration of OpenDJ via the dsconfig set-backend-prop command.
	openshift_raw                                        Manage Kubernetes (K8s) objects
	openshift_scale                                      Set a new size for a Deployment, ReplicaSet, Replication Controller, or Job.
	openssl_certificate                                  Generate and/or check OpenSSL certificates
	openssl_csr                                          Generate OpenSSL Certificate Signing Request (CSR)
	openssl_dhparam                                      Generate OpenSSL Diffie-Hellman Parameters
	openssl_privatekey                                   Generate OpenSSL private keys.
	openssl_publickey                                    Generate an OpenSSL public key from its private key.
	openvswitch_bridge                                   Manage Open vSwitch bridges
	openvswitch_db                                       Configure open vswitch database.
	openvswitch_port                                     Manage Open vSwitch ports
	openwrt_init                                         Manage services on OpenWrt.
	opkg                                                 Package manager for OpenWrt
	ordnance_config                                      Manage Ordnance configuration sections
	ordnance_facts                                       Collect facts from Ordnance Virtual Routers over SSH
	os_auth                                              Retrieve an auth token
	os_client_config                                     Get OpenStack Client config
	os_flavor_facts                                      Retrieve facts about one or more flavors
	os_floating_ip                                       Add/Remove floating IP from an instance
	os_group                                             Manage OpenStack Identity Groups
	os_image                                             Add/Delete images from OpenStack Cloud
	os_image_facts                                       Retrieve facts about an image within OpenStack.
	os_ironic                                            Create/Delete Bare Metal Resources from OpenStack
	os_ironic_inspect                                    Explicitly triggers baremetal node introspection in ironic.
	os_ironic_node                                       Activate/Deactivate Bare Metal Resources from OpenStack
	os_keypair                                           Add/Delete a keypair from OpenStack
	os_keystone_domain                                   Manage OpenStack Identity Domains
	os_keystone_domain_facts                             Retrieve facts about one or more OpenStack domains
	os_keystone_endpoint                                 Manage OpenStack Identity service endpoints
	os_keystone_role                                     Manage OpenStack Identity Roles
	os_keystone_service                                  Manage OpenStack Identity services
	os_network                                           Creates/removes networks from OpenStack
	os_networks_facts                                    Retrieve facts about one or more OpenStack networks.
	os_nova_flavor                                       Manage OpenStack compute flavors
	os_nova_host_aggregate                               Manage OpenStack host aggregates
	os_object                                            Create or Delete objects and containers from OpenStack
	os_port                                              Add/Update/Delete ports from an OpenStack cloud.
	os_port_facts                                        Retrieve facts about ports within OpenStack.
	os_project                                           Manage OpenStack Projects
	os_project_access                                    Manage OpenStack compute flavors acceess
	os_project_facts                                     Retrieve facts about one or more OpenStack projects
	os_quota                                             Manage OpenStack Quotas
	os_recordset                                         Manage OpenStack DNS recordsets
	os_router                                            Create or delete routers from OpenStack
	os_security_group                                    Add/Delete security groups from an OpenStack cloud.
	os_security_group_rule                               Add/Delete rule from an existing security group
	os_server                                            Create/Delete Compute Instances from OpenStack
	os_server_action                                     Perform actions on Compute Instances from OpenStack
	os_server_facts                                      Retrieve facts about one or more compute instances
	os_server_group                                      Manage OpenStack server groups
	os_server_metadata                                   Add/Update/Delete Metadata in Compute Instances from OpenStack
	os_server_volume                                     Attach/Detach Volumes from OpenStack VM's
	os_stack                                             Add/Remove Heat Stack
	os_subnet                                            Add/Remove subnet to an OpenStack network
	os_subnets_facts                                     Retrieve facts about one or more OpenStack subnets.
	os_user                                              Manage OpenStack Identity Users
	os_user_facts                                        Retrieve facts about one or more OpenStack users
	os_user_group                                        Associate OpenStack Identity users and groups
	os_user_role                                         Associate OpenStack Identity users and roles
	os_volume                                            Create/Delete Cinder Volumes
	os_volume_snapshot                                   Create/Delete Cinder Volume Snapshots
	os_zone                                              Manage OpenStack DNS zones
	osx_defaults                                         osx_defaults allows users to read, write, and delete Mac OS X user defaults from Ansible
	ovh_ip_loadbalancing_backend                         Manage OVH IP LoadBalancing backends
	ovirt                                                oVirt/RHEV platform management
	ovirt_affinity_group                                 Module to manage affinity groups in oVirt/RHV
	ovirt_affinity_label                                 Module to manage affinity labels in oVirt/RHV
	ovirt_affinity_label_facts                           Retrieve facts about one or more oVirt/RHV affinity labels
	ovirt_api_facts                                      Retrieve facts about the oVirt/RHV API
	ovirt_auth                                           Module to manage authentication to oVirt/RHV
	ovirt_cluster                                        Module to manage clusters in oVirt/RHV
	ovirt_cluster_facts                                  Retrieve facts about one or more oVirt/RHV clusters
	ovirt_datacenter                                     Module to manage data centers in oVirt/RHV
	ovirt_datacenter_facts                               Retrieve facts about one or more oVirt/RHV datacenters
	ovirt_disk                                           Module to manage Virtual Machine and floating disks in oVirt/RHV
	ovirt_disk_facts                                     Retrieve facts about one or more oVirt/RHV disks
	ovirt_external_provider                              Module to manage external providers in oVirt/RHV
	ovirt_external_provider_facts                        Retrieve facts about one or more oVirt/RHV external providers
	ovirt_group                                          Module to manage groups in oVirt/RHV
	ovirt_group_facts                                    Retrieve facts about one or more oVirt/RHV groups
	ovirt_host_networks                                  Module to manage host networks in oVirt/RHV
	ovirt_host_pm                                        Module to manage power management of hosts in oVirt/RHV
	ovirt_host_storage_facts                             Retrieve facts about one or more oVirt/RHV HostStorages (applicable only for block storage)
	ovirt_hosts                                          Module to manage hosts in oVirt/RHV
	ovirt_hosts_facts                                    Retrieve facts about one or more oVirt/RHV hosts
	ovirt_mac_pools                                      Module to manage MAC pools in oVirt/RHV
	ovirt_networks                                       Module to manage logical networks in oVirt/RHV
	ovirt_networks_facts                                 Retrieve facts about one or more oVirt/RHV networks
	ovirt_nics                                           Module to manage network interfaces of Virtual Machines in oVirt/RHV
	ovirt_nics_facts                                     Retrieve facts about one or more oVirt/RHV virtual machine network interfaces
	ovirt_permissions                                    Module to manage permissions of users/groups in oVirt/RHV
	ovirt_permissions_facts                              Retrieve facts about one or more oVirt/RHV permissions
	ovirt_quotas                                         Module to manage datacenter quotas in oVirt/RHV
	ovirt_quotas_facts                                   Retrieve facts about one or more oVirt/RHV quotas
	ovirt_scheduling_policies_facts                      Retrieve facts about one or more oVirt scheduling policies
	ovirt_snapshots                                      Module to manage Virtual Machine Snapshots in oVirt/RHV
	ovirt_snapshots_facts                                Retrieve facts about one or more oVirt/RHV virtual machine snapshots
	ovirt_storage_connections                            Module to manage storage connections in oVirt
	ovirt_storage_domains                                Module to manage storage domains in oVirt/RHV
	ovirt_storage_domains_facts                          Retrieve facts about one or more oVirt/RHV storage domains
	ovirt_storage_templates_facts                        Retrieve facts about one or more oVirt/RHV templates relate to a storage domain.
	ovirt_storage_vms_facts                              Retrieve facts about one or more oVirt/RHV virtual machines relate to a storage domain.
	ovirt_tags                                           Module to manage tags in oVirt/RHV
	ovirt_tags_facts                                     Retrieve facts about one or more oVirt/RHV tags
	ovirt_templates                                      Module to manage virtual machine templates in oVirt/RHV
	ovirt_templates_facts                                Retrieve facts about one or more oVirt/RHV templates
	ovirt_users                                          Module to manage users in oVirt/RHV
	ovirt_users_facts                                    Retrieve facts about one or more oVirt/RHV users
	ovirt_vmpools                                        Module to manage VM pools in oVirt/RHV
	ovirt_vmpools_facts                                  Retrieve facts about one or more oVirt/RHV vmpools
	ovirt_vms                                            Module to manage Virtual Machines in oVirt/RHV
	ovirt_vms_facts                                      Retrieve facts about one or more oVirt/RHV virtual machines
	pacemaker_cluster                                    Manage pacemaker clusters
	package                                              Generic OS package manager
	package_facts                                        package information as facts
	packet_device                                        Manage a bare metal server in the Packet Host.
	packet_sshkey                                        Create/delete an SSH key in Packet host.
	pacman                                               Manage packages with `pacman'
	pagerduty                                            Create PagerDuty maintenance windows
	pagerduty_alert                                      Trigger, acknowledge or resolve PagerDuty incidents
	pam_limits                                           Modify Linux PAM limits
	pamd                                                 Manage PAM Modules
	panos_admin                                          Add or modify PAN-OS user accounts password.
	panos_admpwd                                         change admin password of PAN-OS device using SSH with SSH key
	panos_cert_gen_ssh                                   generates a self-signed certificate using SSH protocol with SSH key
	panos_check                                          check if PAN-OS device is ready for configuration
	panos_commit                                         commit firewall's candidate configuration
	panos_dag                                            create a dynamic address group
	panos_dag_tags                                       Create tags for DAG's on PAN-OS devices.
	panos_import                                         import file on PAN-OS devices
	panos_interface                                      configure data-port network interface for DHCP
	panos_lic                                            apply authcode to a device/instance
	panos_loadcfg                                        load configuration on PAN-OS device
	panos_match_rule                                     Test for match against a security rule on PAN-OS devices or Panorama management console.
	panos_mgtconfig                                      configure management settings of device
	panos_nat_policy                                     create a policy NAT rule
	panos_nat_rule                                       create a policy NAT rule
	panos_object                                         create/read/update/delete object in PAN-OS or Panorama
	panos_op                                             execute arbitrary OP commands on PANW devices (e.g. show interface all)
	panos_pg                                             create a security profiles group
	panos_query_rules                                    PANOS module that allows search for security rules in PANW NGFW devices.
	panos_restart                                        restart a device
	panos_sag                                            Create a static address group.
	panos_security_policy                                Create security rule policy on PanOS devices.
	panos_security_rule                                  Create security rule policy on PAN-OS devices or Panorama management console.
	parted                                               Configure block device partitions
	patch                                                Apply patch files using the GNU patch tool
	pause                                                Pause playbook execution
	pear                                                 Manage pear/pecl packages
	ping                                                 Try to connect to host, verify a usable python and return `pong' on success
	pingdom                                              Pause/unpause Pingdom alerts
	pip                                                  Manages Python library dependencies
	pkg5                                                 Manages packages with the Solaris 11 Image Packaging System
	pkg5_publisher                                       Manages Solaris 11 Image Packaging System publishers
	pkgin                                                Package manager for SmartOS, NetBSD, et al.
	pkgng                                                Package manager for FreeBSD >= 9.0
	pkgutil                                              Manage CSW-Packages on Solaris
	pn_cluster                                           CLI command to create/delete a cluster.
	pn_ospf                                              CLI command to add/remove ospf protocol to a vRouter.
	pn_ospfarea                                          CLI command to add/remove ospf area to/from a vrouter.
	pn_show                                              Run show commands on nvOS device.
	pn_trunk                                             CLI command to create/delete/modify a trunk.
	pn_vlag                                              CLI command to create/delete/modify vlag.
	pn_vlan                                              CLI command to create/delete a VLAN.
	pn_vrouter                                           CLI command to create/delete/modify a vrouter.
	pn_vrouterbgp                                        CLI command to add/remove/modify vrouter-bgp.
	pn_vrouterif                                         CLI command to add/remove/modify vrouter-interface.
	pn_vrouterlbif                                       CLI command to add/remove vrouter-loopback-interface.
	portage                                              Package manager for Gentoo
	portinstall                                          Installing packages from FreeBSD's ports system
	postgresql_db                                        Add or remove PostgreSQL databases from a remote host.
	postgresql_ext                                       Add or remove PostgreSQL extensions from a database.
	postgresql_lang                                      Adds, removes or changes procedural languages with a PostgreSQL database.
	postgresql_privs                                     Grant or revoke privileges on PostgreSQL database objects.
	postgresql_schema                                    Add or remove PostgreSQL schema from a remote host
	postgresql_user                                      Adds or removes a users (roles) from a PostgreSQL database.
	profitbricks                                         Create, destroy, start, stop, and reboot a ProfitBricks virtual machine.
	profitbricks_datacenter                              Create or destroy a ProfitBricks Virtual Datacenter.
	profitbricks_nic                                     Create or Remove a NIC.
	profitbricks_volume                                  Create or destroy a volume.
	profitbricks_volume_attachments                      Attach or detach a volume.
	proxmox                                              management of instances in Proxmox VE cluster
	proxmox_kvm                                          Management of Qemu(KVM) Virtual Machines in Proxmox VE cluster.
	proxmox_template                                     management of OS templates in Proxmox VE cluster
	proxysql_backend_servers                             Adds or removes mysql hosts from proxysql admin interface.
	proxysql_global_variables                            Gets or sets the proxysql global variables.
	proxysql_manage_config                               Writes the proxysql configuration settings between layers.
	proxysql_mysql_users                                 Adds or removes mysql users from proxysql admin interface.
	proxysql_query_rules                                 Modifies query rules using the proxysql admin interface.
	proxysql_replication_hostgroups                      Manages replication hostgroups using the proxysql admin interface.
	proxysql_scheduler                                   Adds or removes schedules from proxysql admin interface.
	psexec                                               Runs commands on a remote Windows host based on the PsExec model
	pubnub_blocks                                        PubNub blocks management module.
	pulp_repo                                            Add or remove Pulp repos from a remote host.
	puppet                                               Runs puppet
	purefa_ds                                            Configure FlashArray Directory Service
	purefa_facts                                         Collect facts from Pure Storage FlashArray
	purefa_hg                                            Manage hostgroups on Pure Storage FlashArrays
	purefa_host                                          Manage hosts on Pure Storage FlashArrays
	purefa_pg                                            Manage protection groups on Pure Storage FlashArrays
	purefa_pgsnap                                        Manage protection group snapshots on Pure Storage FlashArrays
	purefa_snap                                          Manage volume snapshots on Pure Storage FlashArrays
	purefa_volume                                        Manage volumes on Pure Storage FlashArrays
	purefb_fs                                            Manage filesystemon Pure Storage FlashBlade`
	purefb_snap                                          Manage filesystem snapshots on Pure Storage FlashBlades
	pushbullet                                           Sends notifications to Pushbullet
	pushover                                             Send notifications via https://pushover.net
	rabbitmq_binding                                     This module manages rabbitMQ bindings
	rabbitmq_exchange                                    This module manages rabbitMQ exchanges
	rabbitmq_parameter                                   Adds or removes parameters to RabbitMQ
	rabbitmq_plugin                                      Manage RabbitMQ plugins
	rabbitmq_policy                                      Manage the state of policies in RabbitMQ.
	rabbitmq_queue                                       This module manages rabbitMQ queues
	rabbitmq_user                                        Adds or removes users to RabbitMQ
	rabbitmq_vhost                                       Manage the state of a virtual host in RabbitMQ
	raw                                                  Executes a low-down and dirty SSH command
	rax                                                  create / delete an instance in Rackspace Public Cloud
	rax_cbs                                              Manipulate Rackspace Cloud Block Storage Volumes
	rax_cbs_attachments                                  Manipulate Rackspace Cloud Block Storage Volume Attachments
	rax_cdb                                              create/delete or resize a Rackspace Cloud Databases instance
	rax_cdb_database                                     create / delete a database in the Cloud Databases
	rax_cdb_user                                         create / delete a Rackspace Cloud Database
	rax_clb                                              create / delete a load balancer in Rackspace Public Cloud
	rax_clb_nodes                                        add, modify and remove nodes from a Rackspace Cloud Load Balancer
	rax_clb_ssl                                          Manage SSL termination for a Rackspace Cloud Load Balancer.
	rax_dns                                              Manage domains on Rackspace Cloud DNS
	rax_dns_record                                       Manage DNS records on Rackspace Cloud DNS
	rax_facts                                            Gather facts for Rackspace Cloud Servers
	rax_files                                            Manipulate Rackspace Cloud Files Containers
	rax_files_objects                                    Upload, download, and delete objects in Rackspace Cloud Files
	rax_identity                                         Load Rackspace Cloud Identity
	rax_keypair                                          Create a keypair for use with Rackspace Cloud Servers
	rax_meta                                             Manipulate metadata for Rackspace Cloud Servers
	rax_mon_alarm                                        Create or delete a Rackspace Cloud Monitoring alarm.
	rax_mon_check                                        Create or delete a Rackspace Cloud Monitoring check for an existing entity.
	rax_mon_entity                                       Create or delete a Rackspace Cloud Monitoring entity
	rax_mon_notification                                 Create or delete a Rackspace Cloud Monitoring notification.
	rax_mon_notification_plan                            Create or delete a Rackspace Cloud Monitoring notification plan.
	rax_network                                          create / delete an isolated network in Rackspace Public Cloud
	rax_queue                                            create / delete a queue in Rackspace Public Cloud
	rax_scaling_group                                    Manipulate Rackspace Cloud Autoscale Groups
	rax_scaling_policy                                   Manipulate Rackspace Cloud Autoscale Scaling Policy
	rds                                                  create, delete, or modify an Amazon rds instance
	rds_instance_facts                                   obtain facts about one or more RDS instances
	rds_param_group                                      manage RDS parameter groups
	rds_snapshot_facts                                   obtain facts about one or more RDS snapshots
	rds_subnet_group                                     manage RDS database subnet groups
	redhat_subscription                                  Manage registration and subscriptions to RHSM using the `subscription-manager' command
	redis                                                Various redis commands, slave and flush
	redshift                                             create, delete, or modify an Amazon Redshift instance
	redshift_facts                                       Gather facts about Redshift cluster(s)
	redshift_subnet_group                                manage Redshift cluster subnet groups
	replace                                              Replace all instances of a particular string in a file using a back-referenced regular expression.
	rhevm                                                RHEV/oVirt automation
	rhn_channel                                          Adds or removes Red Hat software channels
	rhn_register                                         Manage Red Hat Network registration using the `rhnreg_ks' command
	rhsm_repository                                      Manage RHSM repositories using the subscription-manager command
	riak                                                 This module handles some common Riak operations
	rocketchat                                           Send notifications to Rocket Chat
	rollbar_deployment                                   Notify Rollbar about app deployments
	route53                                              add or delete entries in Amazons Route53 DNS service
	route53_facts                                        Retrieves route53 details using AWS methods
	route53_health_check                                 add or delete health-checks in Amazons Route53 DNS service
	route53_zone                                         add or delete Route53 zones
	rpm_key                                              Adds or removes a gpg key from the rpm db
	rundeck_acl_policy                                   Manage Rundeck ACL policies.
	rundeck_project                                      Manage Rundeck projects.
	runit                                                Manage runit services
	s3_bucket                                            Manage S3 buckets in AWS, Ceph, Walrus and FakeS3
	s3_lifecycle                                         Manage s3 bucket lifecycle rules in AWS
	s3_logging                                           Manage logging facility of an s3 bucket in AWS
	s3_sync                                              Efficiently upload multiple files to S3
	s3_website                                           Configure an s3 bucket as a website
	say                                                  Makes a computer to speak.
	scaleway_compute                                     Scaleway compute management module
	scaleway_sshkey                                      Scaleway SSH keys management module
	script                                               Runs a local script on a remote node after transferring it
	seboolean                                            Toggles SELinux booleans
	sefcontext                                           Manages SELinux file context mapping definitions
	selinux                                              Change policy and state of SELinux
	selinux_permissive                                   Change permissive domain in SELinux policy
	sendgrid                                             Sends an email with the SendGrid API
	sensu_check                                          Manage Sensu checks
	sensu_client                                         Manages Sensu client configuration
	sensu_handler                                        Manages Sensu handler configuration
	sensu_silence                                        Manage Sensu silence entries
	sensu_subscription                                   Manage Sensu subscriptions
	seport                                               Manages SELinux network port type definitions
	serverless                                           Manages a Serverless Framework project
	service                                              Manage services
	service_facts                                        Return service state information as fact data
	set_fact                                             Set host facts from a task
	set_stats                                            Set stats for the current ansible run
	setup                                                Gathers facts about remote hosts
	sf_account_manager                                   Manage SolidFire accounts
	sf_check_connections                                 Check connectivity to MVIP and SVIP.
	sf_snapshot_schedule_manager                         Manage SolidFire snapshot schedules
	sf_volume_access_group_manager                       Manage SolidFire Volume Access Groups
	sf_volume_manager                                    Manage SolidFire volumes
	shell                                                Execute commands in nodes.
	sl_vm                                                create or cancel a virtual instance in SoftLayer
	slack                                                Send Slack notifications
	slackpkg                                             Package manager for Slackware >= 12.2
	slurp                                                Slurps a file from remote nodes
	slxos_command                                        Run commands on remote devices running Extreme Networks SLX-OS
	slxos_config                                         Manage Extreme Networks SLX-OS configuration sections
	slxos_facts                                          Collect facts from devices running Extreme SLX-OS
	slxos_interface                                      Manage Interfaces on Extreme SLX-OS network devices
	slxos_l2_interface                                   Manage Layer-2 interface on Extreme Networks SLXOS devices.
	slxos_l3_interface                                   Manage L3 interfaces on Extreme Networks SLXOS network devices.
	slxos_linkagg                                        Manage link aggregation groups on Extreme Networks SLXOS network devices
	slxos_vlan                                           Manage VLANs on Extreme Networks SLX-OS network devices
	smartos_image_facts                                  Get SmartOS image details.
	snmp_facts                                           Retrieve facts for a device using SNMP.
	snow_record                                          Create/Delete/Update records in ServiceNow
	sns                                                  Send Amazon Simple Notification Service (SNS) messages
	sns_topic                                            Manages AWS SNS topics and subscriptions
	solaris_zone                                         Manage Solaris zones
	sorcery                                              Package manager for Source Mage GNU/Linux
	spectrum_device                                      Creates/deletes devices in CA Spectrum.
	spotinst_aws_elastigroup                             Create, update or delete Spotinst AWS Elastigroups
	sqs_queue                                            Creates or deletes AWS SQS queues.
	sros_command                                         Run commands on remote devices running Nokia SR OS
	sros_config                                          Manage Nokia SR OS device configuration
	sros_rollback                                        Configure Nokia SR OS rollback
	stackdriver                                          Send code deploy and annotation events to stackdriver
	stacki_host                                          Add or remove host to stacki front-end
	stat                                                 Retrieve file or file system status
	statusio_maintenance                                 Create maintenance windows for your status.io dashboard
	sts_assume_role                                      Assume a role using AWS Security Token Service and obtain temporary credentials
	sts_session_token                                    Obtain a session token from the AWS Security Token Service
	subversion                                           Deploys a subversion repository
	supervisorctl                                        Manage the state of a program or group of programs running via supervisord
	svc                                                  Manage daemontools services
	svr4pkg                                              Manage Solaris SVR4 packages
	swdepot                                              Manage packages with swdepot package manager (HP-UX)
	swupd                                                Manages updates and bundles in ClearLinux systems.
	synchronize                                          A wrapper around rsync to make common tasks in your playbooks quick and easy.
	sysctl                                               Manage entries in sysctl.conf.
	syslogger                                            Log messages in the syslog
	systemd                                              Manage services
	sysvinit                                             Manage SysV services.
	taiga_issue                                          Creates/deletes an issue in a Taiga Project Management Platform
	telegram                                             module for sending notifications via telegram
	telnet                                               Executes a low-down and dirty telnet command
	tempfile                                             Creates temporary files and directories.
	template                                             Templates a file out to a remote server
	terraform                                            Manages a Terraform deployment (and plans)
	timezone                                             Configure timezone setting
	tower_credential                                     create, update, or destroy Ansible Tower credential.
	tower_group                                          create, update, or destroy Ansible Tower group.
	tower_host                                           create, update, or destroy Ansible Tower host.
	tower_inventory                                      create, update, or destroy Ansible Tower inventory.
	tower_job_cancel                                     Cancel an Ansible Tower Job.
	tower_job_launch                                     Launch an Ansible Job.
	tower_job_list                                       List Ansible Tower jobs.
	tower_job_template                                   create, update, or destroy Ansible Tower job template.
	tower_job_wait                                       Wait for Ansible Tower job to finish.
	tower_label                                          create, update, or destroy Ansible Tower label.
	tower_organization                                   create, update, or destroy Ansible Tower organizations
	tower_project                                        create, update, or destroy Ansible Tower projects
	tower_role                                           create, update, or destroy Ansible Tower role.
	tower_team                                           create, update, or destroy Ansible Tower team.
	tower_user                                           create, update, or destroy Ansible Tower user.
	twilio                                               Sends a text message to a mobile phone through Twilio.
	typetalk                                             Send a message to typetalk
	ucs_ip_pool                                          Configures IP address pools on Cisco UCS Manager
	ucs_lan_connectivity                                 Configures LAN Connectivity Policies on Cisco UCS Manager
	ucs_mac_pool                                         Configures MAC address pools on Cisco UCS Manager
	ucs_san_connectivity                                 Configures SAN Connectivity Policies on Cisco UCS Manager
	ucs_vhba_template                                    Configures vHBA templates on Cisco UCS Manager
	ucs_vlans                                            Configures VLANs on Cisco UCS Manager
	ucs_vnic_template                                    Configures vNIC templates on Cisco UCS Manager
	ucs_vsans                                            Configures VSANs on Cisco UCS Manager
	ucs_wwn_pool                                         Configures WWNN or WWPN pools on Cisco UCS Manager
	udm_dns_record                                       Manage dns entries on a univention corporate server
	udm_dns_zone                                         Manage dns zones on a univention corporate server
	udm_group                                            Manage of the posix group
	udm_share                                            Manage samba shares on a univention corporate server
	udm_user                                             Manage posix users on a univention corporate server
	ufw                                                  Manage firewall with UFW
	unarchive                                            Unpacks an archive after (optionally) copying it from the local machine.
	uptimerobot                                          Pause and start Uptime Robot monitoring
	uri                                                  Interacts with webservices
	urpmi                                                Urpmi manager
	user                                                 Manage user accounts
	vca_fw                                               add remove firewall rules in a gateway  in a vca
	vca_nat                                              add remove nat rules in a gateway  in a vca
	vca_vapp                                             Manages vCloud Air vApp instances.
	vcenter_folder                                       Manage folders on given datacenter
	vcenter_license                                      Manage VMware vCenter license keys
	vdirect_commit                                       Commits pending configuration changes on Radware devices
	vdirect_file                                         Uploads a new or updates an existing runnable file into Radware vDirect server
	vdirect_runnable                                     Runs templates and workflow actions in Radware vDirect server
	vdo                                                  Module to control VDO
	vertica_configuration                                Updates Vertica configuration parameters.
	vertica_facts                                        Gathers Vertica database facts.
	vertica_role                                         Adds or removes Vertica database roles and assigns roles to them.
	vertica_schema                                       Adds or removes Vertica database schema and roles.
	vertica_user                                         Adds or removes Vertica database users and assigns roles.
	virt                                                 Manages virtual machines supported by libvirt
	virt_net                                             Manage libvirt network configuration
	virt_pool                                            Manage libvirt storage pools
	vmadm                                                Manage SmartOS virtual machines and zones.
	vmware_cfg_backup                                    Backup / Restore / Reset ESXi host configuration
	vmware_cluster                                       Manage VMware vSphere clusters
	vmware_cluster_facts                                 Gather facts about clusters available in given vCenter
	vmware_datacenter                                    Manage VMware vSphere Datacenters
	vmware_datastore_cluster                             Manage VMware vSphere datastore clusters
	vmware_datastore_facts                               Gather facts about datastores available in given vCenter
	vmware_datastore_maintenancemode                     Place a datastore into maintenance mode
	vmware_dns_config                                    Manage VMware ESXi DNS Configuration
	vmware_drs_rule_facts                                Gathers facts about DRS rule on the given cluster
	vmware_dvs_host                                      Add or remove a host from distributed virtual switch
	vmware_dvs_portgroup                                 Create or remove a Distributed vSwitch portgroup.
	vmware_dvswitch                                      Create or remove a distributed vSwitch
	vmware_guest                                         Manages virtual machines in vCenter
	vmware_guest_disk_facts                              Gather facts about disks of given virtual machine
	vmware_guest_facts                                   Gather facts about a single VM
	vmware_guest_file_operation                          Files operation in a VMware guest operating system without network
	vmware_guest_find                                    Find the folder path(s) for a virtual machine by name or UUID
	vmware_guest_powerstate                              Manages power states of virtual machines in vCenter
	vmware_guest_snapshot                                Manages virtual machines snapshots in vCenter
	vmware_guest_snapshot_facts                          Gather facts about virtual machine's snapshots in vCenter
	vmware_guest_tools_wait                              Wait for VMware tools to become available
	vmware_host                                          Add / Remove ESXi host to / from vCenter
	vmware_host_acceptance                               Manage acceptance level of ESXi host
	vmware_host_capability_facts                         Gathers facts about an ESXi host's capability information
	vmware_host_config_facts                             Gathers facts about an ESXi host's advance configuration information
	vmware_host_config_manager                           Manage advance configurations about an ESXi host
	vmware_host_datastore                                Manage a datastore on ESXi host
	vmware_host_dns_facts                                Gathers facts about an ESXi host's DNS configuration information
	vmware_host_facts                                    Gathers facts about remote ESXi hostsystem
	vmware_host_firewall_facts                           Gathers facts about an ESXi host's firewall configuration information
	vmware_host_firewall_manager                         Manage firewall configurations about an ESXi host
	vmware_host_lockdown                                 Manage administrator permission for the local administrative account for the ESXi host
	vmware_host_ntp                                      Manage NTP configurations about an ESXi host
	vmware_host_package_facts                            Gathers facts about available packages on an ESXi host
	vmware_host_powerstate                               Manages power states of host systems in vCenter
	vmware_host_service_facts                            Gathers facts about an ESXi host's services
	vmware_host_service_manager                          Manage services on a given ESXi host
	vmware_host_vmnic_facts                              Gathers facts about vmnics available on the given ESXi host
	vmware_local_role_manager                            Manage local roles on an ESXi host
	vmware_local_user_facts                              Gather facts about users on the given ESXi host
	vmware_local_user_manager                            Manage local users on an ESXi host
	vmware_maintenancemode                               Place a host into maintenance mode
	vmware_migrate_vmk                                   Migrate a VMK interface from VSS to VDS
	vmware_portgroup                                     Create a VMware portgroup
	vmware_portgroup_facts                               Gathers facts about an ESXi host's portgroup configuration
	vmware_resource_pool                                 Add/remove resource pools to/from vCenter
	vmware_resource_pool_facts                           Gathers facts about resource pool information
	vmware_tag                                           Manage VMware tags
	vmware_tag_facts                                     Manage VMware tag facts
	vmware_target_canonical_facts                        Return canonical (NAA) from an ESXi host system
	vmware_vm_facts                                      Return basic facts pertaining to a vSphere virtual machine guest
	vmware_vm_shell                                      Run commands in a VMware guest operating system
	vmware_vm_vm_drs_rule                                Configure VMware DRS Affinity rule for virtual machine in given cluster
	vmware_vm_vss_dvs_migrate                            Migrates a virtual machine from a standard vswitch to distributed
	vmware_vmkernel                                      Manage a VMware VMkernel Interface aka. Virtual NICs of host system.
	vmware_vmkernel_facts                                Gathers VMKernel facts about an ESXi host
	vmware_vmkernel_ip_config                            Configure the VMkernel IP Address
	vmware_vmotion                                       Move a virtual machine using vMotion
	vmware_vsan_cluster                                  Configure VSAN clustering on an ESXi host
	vmware_vswitch                                       Manage a VMware Standard Switch to an ESXi host.
	vmware_vswitch_facts                                 Gathers facts about an ESXi host's vswitch configurations
	vr_account_facts                                     Gather facts about the Vultr account.
	vr_dns_domain                                        Manages DNS domains on Vultr.
	vr_dns_record                                        Manages DNS records on Vultr.
	vr_firewall_group                                    Manages firewall groups on Vultr.
	vr_firewall_rule                                     Manages firewall rules on Vultr.
	vr_server                                            Manages virtual servers on Vultr.
	vr_ssh_key                                           Manages ssh keys on Vultr.
	vr_startup_script                                    Manages startup scripts on Vultr.
	vr_user                                              Manages users on Vultr.
	vsphere_copy                                         Copy a file to a vCenter datastore
	vsphere_guest                                        Create/delete/manage a guest VM through VMware vSphere.
	vyos_banner                                          Manage multiline banners on VyOS devices
	vyos_command                                         Run one or more commands on VyOS devices
	vyos_config                                          Manage VyOS configuration on remote device
	vyos_facts                                           Collect facts from remote devices running VyOS
	vyos_interface                                       Manage Interface on VyOS network devices
	vyos_l3_interface                                    Manage L3 interfaces on VyOS network devices
	vyos_linkagg                                         Manage link aggregation groups on VyOS network devices
	vyos_lldp                                            Manage LLDP configuration on VyOS network devices
	vyos_lldp_interface                                  Manage LLDP interfaces configuration on VyOS network devices
	vyos_logging                                         Manage logging on network devices
	vyos_static_route                                    Manage static IP routes on Vyatta VyOS network devices
	vyos_system                                          Run `set system` commands on VyOS devices
	vyos_user                                            Manage the collection of local users on VyOS device
	vyos_vlan                                            Manage VLANs on VyOS network devices
	wait_for                                             Waits for a condition before continuing
	wait_for_connection                                  Waits until remote system is reachable/usable
	wakeonlan                                            Send a magic Wake-on-LAN (WoL) broadcast packet
	webfaction_app                                       Add or remove applications on a Webfaction host
	webfaction_db                                        Add or remove a database on Webfaction
	webfaction_domain                                    Add or remove domains and subdomains on Webfaction
	webfaction_mailbox                                   Add or remove mailboxes on Webfaction
	webfaction_site                                      Add or remove a website on a Webfaction host
	win_acl                                              Set file/directory/registry permissions for a system user or group
	win_acl_inheritance                                  Change ACL inheritance
	win_audit_policy_system                              Used to make changes to the system wide Audit Policy
	win_audit_rule                                       Adds an audit rule to files, folders, or registry keys
	win_certificate_store                                Manages the certificate store
	win_chocolatey                                       Manage packages using chocolatey
	win_command                                          Executes a command on a remote Windows node
	win_copy                                             Copies files to remote locations on windows hosts
	win_defrag                                           Consolidate fragmented files on local volumes
	win_disk_facts                                       Show the attached disks and disk information of the target host
	win_disk_image                                       Manage ISO/VHD/VHDX mounts on Windows hosts
	win_dns_client                                       Configures DNS lookup on Windows hosts
	win_domain                                           Ensures the existence of a Windows domain
	win_domain_computer                                  Manage computers in Active Directory
	win_domain_controller                                Manage domain controller/member server state for a Windows host
	win_domain_group                                     Creates, modifies or removes domain groups
	win_domain_membership                                Manage domain/workgroup membership for a Windows host
	win_domain_user                                      Manages Windows Active Directory user accounts
	win_dotnet_ngen                                      Runs ngen to recompile DLLs after .NET  updates
	win_dsc                                              Invokes a PowerShell DSC configuration
	win_environment                                      Modify environment variables on windows hosts
	win_eventlog                                         Manage Windows event logs
	win_eventlog_entry                                   Write entries to Windows event logs
	win_feature                                          Installs and uninstalls Windows Features on Windows Server
	win_file                                             Creates, touches or removes files or directories
	win_file_version                                     Get DLL or EXE file build version
	win_find                                             Return a list of files based on specific criteria
	win_firewall                                         Enable or disable the Windows Firewall
	win_firewall_rule                                    Windows firewall automation
	win_get_url                                          Downloads file from HTTP, HTTPS, or FTP to node
	win_group                                            Add and remove local groups
	win_group_membership                                 Manage Windows local group membership
	win_hostname                                         Manages local Windows computer name.
	win_hotfix                                           Install and uninstalls Windows hotfixes
	win_iis_virtualdirectory                             Configures a virtual directory in IIS
	win_iis_webapplication                               Configures IIS web applications
	win_iis_webapppool                                   Configure IIS Web Application Pools
	win_iis_webbinding                                   Configures a IIS Web site binding
	win_iis_website                                      Configures a IIS Web site
	win_lineinfile                                       Ensure a particular line is in a file, or replace an existing line using a back-referenced regular ...
	win_mapped_drive                                     Map network drives for users
	win_msg                                              Sends a message to logged in users on Windows hosts
	win_msi                                              Installs and uninstalls Windows MSI files
	win_nssm                                             NSSM - the Non-Sucking Service Manager
	win_owner                                            Set owner
	win_package                                          Installs/uninstalls an installable package
	win_pagefile                                         Query or change pagefile configuration
	win_path                                             Manage Windows path environment variables
	win_pester                                           Run Pester tests on Windows hosts
	win_ping                                             A windows version of the classic ping module
	win_power_plan                                       Changes the power plan of a Windows system
	win_product_facts                                    Provides Windows product information (product id, product key)
	win_psexec                                           Runs commands (remotely) as another (privileged) user
	win_psmodule                                         Adds or removes a Powershell Module
	win_rabbitmq_plugin                                  Manage RabbitMQ plugins
	win_reboot                                           Reboot a windows machine
	win_reg_stat                                         Get information about Windows registry keys
	win_regedit                                          Add, change, or remove registry keys and values
	win_region                                           Set the region and format settings
	win_regmerge                                         Merges the contents of a registry file into the windows registry
	win_robocopy                                         Synchronizes the contents of two directories using Robocopy
	win_route                                            Add or remove a static route
	win_say                                              Text to speech module for Windows to speak messages and optionally play sounds
	win_scheduled_task                                   Manage scheduled tasks
	win_scheduled_task_stat                              Get information about Windows Scheduled Tasks
	win_security_policy                                  Change local security policy settings
	win_service                                          Manage and query Windows services
	win_share                                            Manage Windows shares
	win_shell                                            Execute shell commands on target hosts
	win_shortcut                                         Manage shortcuts on Windows
	win_stat                                             Get information about Windows files
	win_tempfile                                         Creates temporary files and directories
	win_template                                         Templates a file out to a remote server
	win_timezone                                         Sets Windows machine timezone
	win_toast                                            Sends Toast windows notification to logged in users on Windows 10 or later hosts
	win_unzip                                            Unzips compressed files and archives on the Windows node
	win_updates                                          Download and install Windows updates
	win_uri                                              Interacts with webservices
	win_user                                             Manages local Windows user accounts
	win_user_right                                       Manage Windows User Rights
	win_wait_for                                         Waits for a condition before continuing
	win_wakeonlan                                        Send a magic Wake-on-LAN (WoL) broadcast packet
	win_webpicmd                                         Installs packages using Web Platform Installer command-line
	win_whoami                                           Get information about the current user and process
	xattr                                                Manage user defined extended attributes
	xbps                                                 Manage packages with XBPS
	xenserver_facts                                      get facts reported on xenserver
	xml                                                  Manage bits and pieces of XML files or strings
	yarn                                                 Manage node.js packages with Yarn
	yum                                                  Manages packages with the `yum' package manager
	yum_repository                                       Add or remove YUM repositories
	zabbix_group                                         Zabbix host groups creates/deletes
	zabbix_group_facts                                   Gather facts about Zabbix hostgroup
	zabbix_host                                          Zabbix host creates/updates/deletes
	zabbix_hostmacro                                     Zabbix host macro creates/updates/deletes
	zabbix_maintenance                                   Create Zabbix maintenance windows
	zabbix_proxy                                         Zabbix proxy creates/deletes/gets/updates
	zabbix_screen                                        Zabbix screen creates/updates/deletes
	zabbix_template                                      create/delete/dump zabbix template
	zfs                                                  Manage zfs
	zfs_facts                                            Gather facts about ZFS datasets.
	znode                                                Create, delete, retrieve, and update znodes using ZooKeeper
	zpool_facts                                          Gather facts about ZFS pools.
	zypper                                               Manage packages on SUSE and openSUSE
	zypper_repository                                    Add and remove Zypper repositories
	[ansadm@ansible tmp]$


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

----------------------------------------------
Testing :   curl http://public_name_of_db_server:80/
----------------------------------------------


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
========================
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
=======================================================
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
ansible-playbook example.yml --tags "configuration,packages"		 will run both  tasks configuration,packages, if you want to run only one task specify that tag only mentioned under task
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

ansible-playbook example.yml --tags "configuration,packages"	

ansible appservers -m setup -i ansible_hosts -a 'filter=ansible_distribution,ansible_distribution_version,ansible_memfree_mb,ansible_memtotal_mb,ansible_processor_cores*,ansible_architecture'


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

Kubernetest

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
