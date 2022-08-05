# POWERSHELL

#Powershell Remoting

**If you want to manage Windows computers remotely with PowerShell, you first have to enable PowerShell remoting on the remote machine. You can then use the Invoke-Command and Enter-PSsession cmdlets to execute PowerShell commands on the remote machine. The commands described in this article also work in PowerShell 6 and PowerShell 7**

# On a local computer with Enable-PSRemoting

To enable PowerShell remoting on a single machine, you can log on to this computer locally or via Remote Desktop and then execute Enable-PSRemoting at a PowerShell prompt with administrator rights.

To avoid the conformation prompts, you can use the -Force parameter:

    Enable-PSRemoting -Force
