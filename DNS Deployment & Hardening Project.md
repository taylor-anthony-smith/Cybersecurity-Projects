# OBJECTIVE
The objective of this project is to design, configure, and secure a Domain Name System (DNS) server within a virtual network environment to provide reliable and secure name resolution services. This hands-on experience was designed to deepen understanding of DNS queries and defensive strategies to secure the requests being made to the server.

# SKILLS LEARNED
-Installing and configuring DNS services in a Windows environment.\
-Creating forward and reverse lookup zones.\
-Managing A, AAAA, CNAME, MX, PTR, and TXT records.\
-Testing DNS lookup using Wireshark and nslookup.\
-Configuring Domain Name System Security Extensions (DNSSEC).\
-Implementing DNS logging and monitoring.

# TOOLS USED
-VirtualBox\
-Windows Server Manager\
-Windows DNS Manager\
-Wireshark

# Network Setup & Topology:
For this lab, we will create a small virtual network within a tool called VirtualBox where we will host our DNS server and user endpoints. After building each machine using their ISO images, we are able to create our network within the ‘Tools’ tab of the application. In our case, we have created a virtual network called ‘Specter Network’ with an assigned IP range of 192.168.0.0/24 (Figure 1.1).

***Figure 1.1: VirtualBox Network Creation***

![image alt](https://github.com/taylor-anthony-smith/Cybersecurity-Projects/blob/03a72a342966e8675ecb96eacf369ba6d6f66c66/Images/DNS/Figure%201.1%20-%20VirtualBox%20Network%20Creation.png)

This means we will be using a private class ‘C’ network address with a classful subnet mask of 255.255.255.0 - giving our network 254 usable hosts to use as our network grows. We will later configure static IPs for each of our hosts on the network. Note: Each virtual machine will need to be attached to a NAT network within the adapter settings which allows us to have an isolated network between multiple virtual machines with internet connectivity. Figure 1.2 shows our established network, outlining each virtual machine with their assigned IP address and subnet mask that will be set up in the later steps.

***Figure 1.2: Network Topology***

![image alt](https://github.com/taylor-anthony-smith/Cybersecurity-Projects/blob/3796e6b76186550859fe0ec830b87bb4bbd27062/Images/DNS/Figure%201.2%20-%20Network%20Topology.png)

# DNS Manager Installation:
Our next step in the process is to set up our Windows 2022 server to run DNS Manager and serve as our recursive resolver. Once loaded into our Windows 2022 virtual machine, we will need to start by assigning our server a static IP address and not use DHCP. This is a critical step to ensure our server’s IP address does not change - allowing client devices to query the server consistently for domain name resolution.

To assign a static IP, we manually enter our IPv4 information within our Ethernet properties as shown in Figure 2.1. Each machine on our network will need their own IP address and be on the same subnet as our server. We also need to set the default gateway as the IP address of our router. Note: It’s important to set the DNS server address to the same address of our DNS server, allowing it to resolve its own queries locally.

***Figure 2.1: Static IP Assignment***

![image alt](https://github.com/taylor-anthony-smith/Cybersecurity-Projects/blob/3796e6b76186550859fe0ec830b87bb4bbd27062/Images/DNS/Figure%202.1%20-%20Static%20IP%20Assignment.png)

Next, we can proceed with setting up DNS on our server. To do so, we need to navigate to ‘Add Roles and Features’ within Server Manager and select the ‘DNS Server’ role to be added. Below is a breakdown of the selections we need to make as we progress through the installation process (Figure 2.2).
1. Select role-based or featured based installation on the ‘Installation Type’ page.
2. Select our Windows 2022 server in the ‘Server Selection’ page.
3. Under ‘Server Roles’, select ‘DNS Server’ and select ‘Add Features’.

***Figure 2.2: Adding DNS Server Role***

![image alt](https://github.com/taylor-anthony-smith/Cybersecurity-Projects/blob/6ede5d24f0b432d13a28d945fe6494aca87179fd/Images/DNS/Figure%202.2%20-%20Adding%20DNS%20Server%20Role.png)

Once the installation is complete, we observe that this process has installed remote installation, role administration, and DNS server tools within our server manager. On the left hand side, we now have DNS installed as an option to configure (Figure 2.3).

***Figure 2.3: Successful Installation***

![image alt](https://github.com/taylor-anthony-smith/Cybersecurity-Projects/blob/6ede5d24f0b432d13a28d945fe6494aca87179fd/Images/DNS/Figure%202.3%20-%20Successful%20Installation.png)

# Configuring Endpoints:
Now that we have installed DNS Manager on our server, we will need to configure our endpoints to point their DNS queries to our Windows server (Figure 3.1). To allow this, we enter our network adapter settings within our Windows 10 endpoint and set the ‘Preferred DNS’ field as the IP address of our Windows server. The same process can be followed for other endpoints within our network, such as our Kali Linux workstation.

***Figure 3.1: Setting Preferred DNS on Endpoint***

![image alt](https://github.com/taylor-anthony-smith/Cybersecurity-Projects/blob/6ede5d24f0b432d13a28d945fe6494aca87179fd/Images/DNS/Figure%203.1%20-%20Setting%20Preferred%20DNS%20on%20Endpoint.png)

By setting the preferred DNS on our Windows 10 endpoint, we are now ready to start using our recursive server for DNS queries. To ensure everything is working properly and that our Windows server is being sent the requests, we can utilize a packet tracer such as Wireshark to check our DNS traffic. Within WireShark, we can start a packet capture and then navigate to www.google.com or a different website using our web browser. WireShark will capture all traffic going across our network card. In this example, we can see that our DNS traffic is being sent to our DNS server as expected - with 192.168.0.75 being our source host and 192.168.0.25 as our destination which is the IP address of our DNS server (Figure 3.2).

***Figure 3.2: Using WireShark***

![image alt](https://github.com/taylor-anthony-smith/Cybersecurity-Projects/blob/6ede5d24f0b432d13a28d945fe6494aca87179fd/Images/DNS/Figure%203.2%20-%20Using%20Wireshark.png)

Alternatively, we can use a command line tool called nslookup, a network administration tool, to confirm that queries are now being sent to our DNS server (Figure 3.3). This tool can also find IP addresses, associated domain names, and other DNS records for websites.

**Figure 3.3: nslookup**

![image alt](https://github.com/taylor-anthony-smith/Cybersecurity-Projects/blob/6ede5d24f0b432d13a28d945fe6494aca87179fd/Images/DNS/Figure%203.3%20-%20nslookup.png)

# DNS Records Explained:
Our server is authoritative for the DNS names of local resources, but forwards all other queries to other DNS servers such as root nameservers, TLD nameservers, and authoritative nameservers as needed if the domain is not saved within our local cache. Within our DNS Manager, we are now able to create forward lookup zones and reverse lookup zones for our domain (Figure 4.1). Forward DNS lookups translate domain names such as www.amazon.com into IP addresses using ‘A’ and ‘AAAA’ records (whether it’s IPv4 or IPv6), while reverse lookups do the opposite where PTR records map IP addresses back into domain names.

***Figure 4.1: Creating DNS Records***

![image alt](https://github.com/taylor-anthony-smith/Cybersecurity-Projects/blob/6ede5d24f0b432d13a28d945fe6494aca87179fd/Images/DNS/Figure%204.1%20-%20Creating%20DNS%20Records.png)

There are many DNS records we need to be aware of including:\
**A:** The record that contains the IPv4 address of a domain.\
**AAAA:** The record that contains the IPv6 address of a domain.\
**CNAME:** Forwards one domain or subdomain to another domain.\
**MX:** Directs mail to an email server.\
**TXT:** Lets an admin store text notes in the records.\
**NS:** Stores the name server server for a DNS entry.\
**SOA:** Stores admin information about a domain.\
**SRV:** Specifies a port for specific services.\
**PTR:** Provides a domain name in reverse lookups.

# DNS Logging & Audit Records:
By default, logging of DNS queries is turned off. However, we can enable the debug logging feature to record the packets sent and received by our DNS server to a log file of our choosing (Figure 5.1). In our case, we are using the default file path of %systemroot%\system32\dns\nds.log as our location to store the queries being requested by our server (Figure 5.2).

***Figure 5.1: Enabling Debug Logging***

![image alt](https://github.com/taylor-anthony-smith/Cybersecurity-Projects/blob/7e54e6fc1dd8a5e97e0503b2e70a5bcc004495cf/Images/DNS/Figure%205.1%20-%20Enabling%20Debug%20Logging.png)

***Figure 5.2: DNS File Path***

![image alt](https://github.com/taylor-anthony-smith/Cybersecurity-Projects/blob/7e54e6fc1dd8a5e97e0503b2e70a5bcc004495cf/Images/DNS/Figure%205.2%20-%20DNS%20File%20Path.png)

With debug logging enabled, all DNS resolutions will be updated within our log file with the path specified (Figure 5.3). Note: When selecting the data types when first enabling debug logging, it is important to only capture the information needed and regularly monitor the file size to avoid disk space issues. For our example, we searched for the website www.amazon.com on our Windows 10 endpoint which has an IP address of 192.168.0.75. This query was captured by our DNS server and documented within our log file.

***Figure 5.3: Audit Trail***

![image alt](https://github.com/taylor-anthony-smith/Cybersecurity-Projects/blob/7e54e6fc1dd8a5e97e0503b2e70a5bcc004495cf/Images/DNS/Figure%205.3%20-%20Audit%20Trail.png)

# Securing DNS:
By default, DNS lacks security and is prone to a number of attacks including DNS cache poisoning/spoofing, hijacking, and DDoS amplification. DNSSEC is a set of extensions that add security to the DNS protocol including origin authority, data integrity, and authenticated denial of existence. This allows for DNS servers and resolvers to trust DNS responses by using digital signatures. However, DNSSEC does not encrypt DNS traffic. For data encryption, mechanisms like DNS over HTTPS (DoH) and DNS over TLS (DoT) can be utilized.

To install DNSSEC on our server, we will need to enter the properties screen for our domain and enable it. Once enabled, we can now see that we are using RSA/SHA-256 with a key length of 2048 to create digital signatures (Figure 6.1).

***Figure 6.1: DNSSEC Properties***

![image alt](https://github.com/taylor-anthony-smith/Cybersecurity-Projects/blob/7e54e6fc1dd8a5e97e0503b2e70a5bcc004495cf/Images/DNS/Figure%206.1%20-%20DNSSEC%20Properties.png)

With DNSSEC now configured, digital signatures are now being used to ensure data integrity and authenticity of records to prevent possible alterations of the data during transit. This also helps prevent common attack vectors such as hijacking where attackers misdirect user traffic bound for legitimate websites to malicious websites that may be controlled by the attacker.






