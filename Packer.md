# Sample Video
https://www.youtube.com/watch?v=yZa03MYHsag

https://github.com/listentolearn/aws-ami-builder-packer

# HashiCorp Packer

Packer is an open source tool for creating identical machine images for multiple platforms from a single source configuration. It helps you automate for machine image building — whether you want your image on AWS, GCP, Docker, a private cloud or QEMU. For example, if you have to build a base server image from an Ubuntu server ISO, it should have your security and telemetry settings. You could use Packer to start an instance, apply your security and telemetry provisioning steps, and then save it. That is going to work for many environments.




# Provisioners

    Overview
    Breakpoint
    File
    PowerShell
    Shell
    Shell (Local)
    Windows Shell
    Windows Restart
    Custom
    Community-Supported
  
   
# Builder  


    "builders": {
      "type": "amazon-ebs",
      "access_key": "AKIAIOSFODNN7EXAMPLE",
      "secret_key": "wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY",
      "region": "us-east-1",
    }
  
  
# Sample Packer json

## template 

    {
      "variables": {
        "ami_id": "ami-01e78c5619c5e68b4",
        "app_name": "httpd"
      },

    "builders": [{
      "type": "amazon-ebs",
      "region": "eu-west-2",
      "source_ami": "{{user `ami_id`}}",
      "instance_type": "t2.micro",
      "ssh_username": "ec2-user",
      "ami_name": "PACKER-DEMO-{{user `app_name` }}",
      "tags": {
          "Name": "PACKER-DEMO-{{user `app_name` }}",
          "Env": "DEMO"

        }
    }],

    "provisioners": [
      {
        "type": "shell",
        "script": "demo-script.sh"
      }
    ]

  }
  
## demo-script.sh

    #!/bin/bash
    sudo yum -y update
    sudo yum install -y httpd
