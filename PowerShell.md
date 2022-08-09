# POWERSHELL

#Powershell Remoting

**If you want to manage Windows computers remotely with PowerShell, you first have to enable PowerShell remoting on the remote machine. You can then use the Invoke-Command and Enter-PSsession cmdlets to execute PowerShell commands on the remote machine. The commands described in this article also work in PowerShell 6 and PowerShell 7**

# On a local computer with Enable-PSRemoting

To enable PowerShell remoting on a single machine, you can log on to this computer locally or via Remote Desktop and then execute Enable-PSRemoting at a PowerShell prompt with administrator rights.

To avoid the conformation prompts, you can use the -Force parameter:

    Enable-PSRemoting -Force

If the computer's current connection type is set to public, the above command will produce an error message because by default PowerShell remoting is only enabled for private and domain connection types. See this blog post for more details about this issue. To avoid the error message and enable PowerShell remoting on a public network, you can use the ‑SkipNetworkProfileCheck parameter:

    Enable-PSRemoting -Force -SkipNetworkProfileCheck
    
# On workgroup group computers    

PowerShell remoting works best in an Active Directory environment. If you want to enable remoting for workgroup or standalone computers you have to consider a few more settings.

In case your network connection type is set to public, you have to use the ‑SkipNetworkProfileCheck parameter as explained above.

    Enable-PSRemoting -Force -SkipNetworkProfileCheck
    
Authentication in PowerShell remoting relies on Active Directory. By default, only computers that are domain members can connect via PowerShell remoting. In a workgroup environment, you have to add the IP addresses of the computers to the TrustedHosts list manually:

    Set-Item WSMan:\localhost\Client\TrustedHosts -Value "10.0.2.33" -Force
    
You also have to ensure that Windows Firewall is opened for Windows Remote Management on the remote computer. On the remote computer, type "firewall" after clicking **Start**, and click **Advanced settings** in the Control Panel **firewall app**. Right-click Inbound Rules and then select New Rule. In the Predefined field select Windows Remote Management and then follow the wizard.    

To enable PowerShell remoting with PsExec, open a command prompt with admin rights in the folder where you copied PsExec and then execute this command:
    
        psexec.exe \\RemoteComputerName -s powershell Enable-PSRemoting -Force
        
# Via PowerShell Direct        

If you want to enable remoting in virtual machine on a Hyper-V host, you can also use PowerShell Direct if the guest OS is Windows 10, Windows Server 2016 or Windows Server 2019 (see comment below). This is the PowerShell command for the task:

        Invoke-Command -VMName <VM name> -ScriptBlock {Enable-PSRemoting -Force} -Credential Administrator
        
# Testing PowerShell remoting        

To test you have enabled PowerShell remoting correctly, you can enter this command

        Enter-PSSession -ComputerName <hostname>
        
This will open an interactive session with a remote computer where you can then enter PowerShell commands to execute on the remote machine.

If you want to connect with a different account than the one you logged on the local machine with, you can use this command:

        Enter-PSSession -Computername "host" –Credential "host\administrator"
        
We can now start the SSH service (sshd) with this PowerShell command:

        Start-Service sshd
        
To automatically star the OpenSSH service sshd, you need this command

        Set-Service sshd -StartupType Automatic
        
**To connect to the remote host, you have to use the HostName parameter instead of  ComputerName parameter:**        

        Enter-PSsession -HostName <computer name>