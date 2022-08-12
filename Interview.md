# What is Virtual Desktop Infrastructure

Virtual desktop infrastructure (VDI) is a desktop virtualization technology wherein a desktop operating system, typically Microsoft Windows, runs and is managed in a data center. The virtual desktop image is delivered over a network to an endpoint device, which allows the user to interact with the operating system and its applications as if they were running locally. The endpoint may be a traditional PC, thin client device or a mobile device.

The concept of presenting virtualized applications and desktops to users falls under the umbrella of end-user computing (EUC). The term VDI was originally coined by VMware and has since become a de facto technology acronym. While Windows-based VDI is the most common workload, Linux virtual desktops are also an option

How the user accesses VDI depends on the organization's configuration, ranging from automatic presentation of the virtual desktop at logon to requiring the user to select the virtual desktop and then launching it. Once the user accesses the virtual desktop, it takes primary focus, and the look and feel are that of a local workstation. The user selects the appropriate applications and can perform their work.

# Benefits of VDI

VDI as a platform has many benefits, including:

**Device flexibility**. Because little actual computing takes place at the endpoint, IT departments may be able to extend the life span of otherwise obsolete PCs by repurposing them as VDI endpoints. And when the time does come to purchase new devices, organizations can buy less powerful -- and less expensive -- end-user computing devices, including thin clients.

**Increased security**. Because all data lives in the data center, not on the endpoint, VDI provides significant security benefits. A thief who steals a laptop from a VDI user can't take any data from the endpoint device because no data is stored on it.
User experience. VDI provides a centralized, standardized desktop, and users grow accustomed to a consistent workspace. Whether that user is accessing VDI from a home computer, thin client, kiosk, roving workstation or mobile device, the user interface is the same, with no need to acclimate for any physical platform.
The VDI user experience is equal to or better than the physical workstation due to the centralized system resources assigned to the virtual desktop, as well as the desktop image's close proximity to back-end databases, storage repositories and other resources. Further, remote display protocols compress and optimize network traffic considerably, which enables screen paints, keyboard and mouse data, and other interactions to simulate the responsiveness of a local desktop.

**Scalability**. When an organization expands temporarily, such as seasonal call center agent contractors, it can quickly expand the VDI environment. By enabling these workers to access an enterprise virtual desktop workload and its respective apps, these contractors can be fully functional within minutes, compared with days or weeks to procure endpoint devices and configure apps.

**Mobility**. Other benefits of VDI include the ability to more easily support remote and mobile workers. Mobile workers comprise a significant percentage of the workforce, and remote workers are becoming more common. Whether these individuals are field engineers, sales representatives, onsite project teams or executives, they all need remote access to their apps while traveling. By presenting a virtual desktop to these remote users, they cab work as efficiently as if they were in the office.

**No IT staff is required**
**No maintenance cost**
**No software upgrade cost**
**Pay as per the requirements**


# Drawbacks of VDI

Reliance on internet connectivity. No network, no VDI session. VDI's reliance on network connectivity presents another challenge.

# Components of VDI

**Virtualization** - Virtualization is creation of virtual version of desktop,os,server,storage.

**Hypervisor** -  Hypervisor is a software which seprate operating system with the underlying operating system to create a virtual environment.

**Connection Broker** - Connection Broker is a software which help end user to connect to the remote virtual desktop.

**Desktop Pools** -  Desktop Pool is a group of virtual desktops with the indentical configuration such as OS,Softwares,Applications,Storage.

**Application Virtulization** - Application Virtulization is the technology used to create a virtulized application image and replicate to all the virtual desktop in desktop pools.

# How VDI works.

> User sends login request to their endpoint device
> 
> Connection Broker accepts the request
> 
> Now the user can use the desktop according to their process


# Packer in Azure
Packer is an open source tool for creating identical machine images for multiple platforms from a single source configuration. Packer is lightweight, runs on every major operating system, and is highly performant, creating machine images for multiple platforms in parallel.

GitHub repo: https://github.com/MohammedHousa/Packer-in-Azure

Tutorials:
1. How to create a custom image in Azure using Packer
2. Create VM from packer image with Azure CLI
3. Create VM from packer image with Terraform

# Linux VM Main File

Finally, create the file vm-nginx-main.tf. This file will create the virtual machines and Network Security Group (NSG). This is the code compatible with AzureRM v2.x:

      # Bootstrapping Template File
      data "template_file" "nginx-vm-cloud-init" {
        template = file("install-nginx.sh")
      }
      # Create Network Security Group
      resource "azurerm_network_security_group" "nginx-vm-nsg" {
        depends_on=[azurerm_resource_group.network-rg]
        name = "nginx-nsg"
        location = azurerm_resource_group.network-rg.location
        resource_group_name = azurerm_resource_group.network-rg.name
        security_rule {
          name = "Allow-SSH"
          description = "Allow SSH"
          priority = 100
          direction = "Inbound"
          access = "Allow"
          protocol = "Tcp"
          source_port_range = "*"
          destination_port_range = "22"
          source_address_prefix = "Internet"
          destination_address_prefix = "*"
        }
        security_rule {
          name = "Allow-HTTP"
          description = "Allow HTTP"
          priority = 110
          direction = "Inbound"
          access = "Allow"
          protocol = "Tcp"
          source_port_range = "*"
          destination_port_range = "80"
          source_address_prefix = "Internet"
          destination_address_prefix = "*"
        }
      }
      # Associate the web NSG with the subnet
      resource "azurerm_subnet_network_security_group_association" "nginx-vm-nsg-association" {
        depends_on=[azurerm_resource_group.network-rg]
        subnet_id = azurerm_subnet.vm-subnet.id
        network_security_group_id = azurerm_network_security_group.nginx-vm-nsg.id
      }
      # Get a Static Public IP
      resource "azurerm_public_ip" "nginx-vm-ip" {
        depends_on=[azurerm_resource_group.network-rg]
        name = "nginx-ip"
        location = azurerm_resource_group.network-rg.location
        resource_group_name = azurerm_resource_group.network-rg.name
        allocation_method = "Static"
      }
      # Create Network Card for the VM
      resource "azurerm_network_interface" "nginx-nic" {
        depends_on=[azurerm_resource_group.network-rg]
        name = "nginx-nic"
        location = azurerm_resource_group.network-rg.location
        resource_group_name = azurerm_resource_group.network-rg.name

        ip_configuration {
          name = "internal"
          subnet_id = azurerm_subnet.vm-subnet.id
          private_ip_address_allocation = "Dynamic"
          public_ip_address_id = azurerm_public_ip.nginx-vm-ip.id
        }
      }
      # Create Nginx VM
      resource "azurerm_linux_virtual_machine" "nginx-vm" {
        depends_on=[azurerm_network_interface.nginx-nic]
        name = "nginx-vm"
        location = azurerm_resource_group.network-rg.location
        resource_group_name = azurerm_resource_group.network-rg.name
        network_interface_ids = [azurerm_network_interface.nginx-nic.id]
        size = var.nginx_vm_size
        source_image_reference {
          publisher = var.ubuntu-publisher
          offer = var.ubuntu-offer
          sku = "18.04-LTS"
          version = "latest"
        }
        os_disk {
          name = "nginx-osdisk"
          caching = "ReadWrite"
          storage_account_type = "Standard_LRS"
        }
        computer_name = "nginx-vm"
        admin_username = var.nginx_admin_username
        admin_password = random_password.nginx-vm-password.result
        disable_password_authentication = false
        custom_data = base64encode(data.template_file.nginx-vm-cloud-init.rendered)
      }


# HCL (Hushicorp Configuration Language) Packer

# What is Packer?

Packer is an open source tool for creating identical machine images for multiple platforms from a single source configuration. Packer is lightweight, runs on every major operating system, and is highly performant, creating machine images for multiple platforms in parallel. Packer does not replace configuration management like Chef or Puppet. In fact, when building images, Packer is able to use tools like Chef or Puppet to install software onto the image.


A machine image is a single static unit that contains a pre-configured operating system and installed software which is used to quickly create new running machines. Machine image formats change for each platform. Some examples include AMIs for EC2, VMDK/VMX files for VMware, OVF exports for VirtualBox, etc.

# Packer Templates

**.pkr.hcl or .pkr.json**

Packer's behavior is determined by the Packer template, which consists of a series of declarations and commands for Packer to follow. This template tells Packer what plugins (builders, provisioners, post-processors) to use, how to configure each of those plugins, and what order to run them in.

The template contains flexible variable injection tools, as well as built-in functions to help you customize your builds.

Historically, Packer has used a JSON template for its configuration, but Packer is transitioning to a new template configuration format that uses HCL2 -- the same configuration language used by Terraform and HashiCorp's other products. This format is more flexible, more modular, and more concise than the original JSON template format. While the JSON format is still supported, certain new features in the Packer core will only be implemented for the newer HCL format. Please use the side bar to find documentation for the different template formats.


# Provisioners

Provisioners use builtin and third-party software to install and configure the machine image after booting. Provisioners prepare the system for use, so common use cases for provisioners include:

      installing packages

      patching the kernel

      creating users

      downloading application code

Packer uses the HashiCorp Configuration Language - HCL - designed to allow concise descriptions of the required steps to get to a build file. This page describes the features of HCL2 exhaustively, if you would like to give a quick try to HCL2

# Breakpoint Provisioner

Type: breakpoint

The breakpoint provisioner will pause until the user presses "enter" to resume the build. This is intended for debugging purposes, and allows you to halt at a particular part of the provisioning process.

This is independent of the -debug flag, which will instead halt at every step and between every provisioner.

»Basic Example

      {
        "builders": [
          {
            "type": "null",
            "communicator": "none"
          }
        ],
        "provisioners": [
          {
            "type": "shell-local",
            "inline": "echo hi"
          },
          {
            "type": "breakpoint",
            "disable": false,
            "note": "this is a breakpoint"
          },
          {
            "type": "shell-local",
            "inline": "echo hi 2"
          }
        ]
      }

# Create a Packer Image Template JSON file – Example

Now let us write a Packer Image Template file. For this post, we will install Tomcat8 and OpenJDK8 on Ubuntu 16 Xenial image

Here is Packer Template file with my configuration

      {
          "variables": {
              "aws_access_key": "{{env `AWS_ACCESS_KEY_ID`}}",
              "aws_secret_key": "{{env `AWS_SECRET_ACCESS_KEY`}}"
          },
          "builders": [
              {
                  "access_key": "{{user `aws_access_key`}}",
                  "ami_name": "Tomcat8_OJDK8_AMI",
                  "instance_type": "t3.micro",
                  "region": "ap-south-1",
                  "secret_key": "{{user `aws_secret_key`}}",
                  "security_group_id":"sg-04cce0d3e58f065a0",
                  "ssh_keypair_name":"SaravMacKeyPair",
                  "ssh_private_key_file":"SaravMacKeyPair.pem",
                  "associate_public_ip_address":"true",
                  "source_ami_filter": {
                    "filters": {
                    "virtualization-type": "hvm",
                    "name": "ubuntu/images/*ubuntu-xenial-16.04-amd64-server-*",
                    "root-device-type": "ebs"
                    },
                    "owners": ["099720109477"],
                    "most_recent": true
                  },
                  "ssh_username": "ubuntu",
                  "type": "amazon-ebs"
              }
          ],
          "provisioners": [
              {
                  "type": "shell",
                  "inline":[
                      "sudo add-apt-repository ppa:openjdk-r/ppa",
                      "sudo apt-get update",
                      "sudo apt-get install -y openjdk-8-jdk",
                      "java -version",
                      "sudo apt-get install -y tomcat8"
                  ]
              }
          ]
      }
