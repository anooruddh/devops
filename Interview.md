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


