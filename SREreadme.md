
# What is SRE (site reliability engineering)?

### Overview
Site reliability engineering (SRE) is a software engineering approach to IT operations. SRE teams use software as a tool to manage systems, solve problems, and automate operations tasks.

SRE takes the tasks that have historically been done by operations teams, often manually, and instead gives them to engineers or ops teams who use software and automation to solve problems and manage production systems. 

SRE is a valuable practice when creating scalable and highly reliable software systems. It helps you manage large systems through code, which is more scalable and sustainable for sysadmins managing thousands or hundreds of thousands of machines. 

The concept of site reliability engineering comes from the Google engineering team and is credited to Ben Treynor Sloss. 

SRE helps teams find a balance between releasing new features and making sure that they are reliable for users.

Standardization and automation are 2 important components of the SRE model. Site reliability engineers should always be looking for ways to enhance and automate operations tasks.

In this way, SRE helps to improve the reliability of a system today, while also improving it as it grows over time.

### What does a site reliability engineer do?
A site reliability engineer is a unique role that requires either a background as a software developer with additional operations experience, or as a sysadmin or in an IT operations role that also has software development skills. 

SRE teams are responsible for how code is deployed, configured, and monitored, as well as the availability, latency, change management, emergency response, and capacity management of services in production.

With SRE, 100% reliability is not expected; failure is planned for and accepted. 

Site reliability engineers split their time between operations tasks and project work. According to SRE best practices from Google, a site reliability engineer can only spend a maximum of 50% of their time on operations, which should be monitored to ensure they don’t go over. 

The rest of the time should be spent on development tasks like creating new features, scaling the system, and implementing automation.

Excess operational work and poorly performing services can be redirected back to the dev team to run instead of the site reliability engineer spending too much time on the operations of an application or service. 

Automation is an important part of the site reliability engineer’s role. If they are dealing with a problem repeatedly then they will automate a solution. This also helps ensure that operations work remains at half of their workload. 

Maintaining the balance between operations and development work is a key component of SRE. 

SRE is different because it relies on site reliability engineers within the development team who also have an operations background to remove communication and workflow problems.

The site reliability engineer role itself combines the skillset of dev teams and operations teams by requiring an ov
erlap in responsibilities. 

# SLA: Service Level Agreements

### What is an SLA?
An SLA (service level agreement) is an agreement between provider and client about measurable metrics like uptime, responsiveness, and responsibilities. 

These agreements are typically drawn up by a company’s new business and legal teams and they represent the promises you’re making to customers—and the consequences if you fail to live up to those promises. Typically, consequences include financial penalties, service credits, or license extensions.

### The challenge of SLAs
SLAs are notoriously difficult to measure, report on, and meet. These agreements—generally written by people who aren’t in the tech trenches themselves—often make promises that are difficult for teams to measure, don’t always align with current and ever-evolving business priorities, and don’t account for nuance. 

# SLO: Service Level Objectives

### What is an SLO?
An SLO (service level objective) is an agreement within an SLA about a specific metric like uptime or response time. So, if the SLA is the formal agreement between you and your customer, SLOs are the individual promises you’re making to that customer. SLOs are what set customer expectations and tell IT and DevOps teams what goals they need to hit and measure themselves against.

# SLI: Service Level Indicator

### What is an SLI?
An SLI (service level indicator) measures compliance with an SLO (service level objective). So, for example, if your SLA specifies that your systems will be available 99.95% of the time, your SLO is likely 99.95% uptime and your SLI is the actual measurement of your uptime. Maybe it’s 99.96%. Maybe 99.99%. To stay in compliance with your SLA, the SLI will need to meet or exceed the promises made in that document.
