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

**Figure 1.1: VirtualBox Network Creation**

![image alt](https://github.com/taylor-anthony-smith/Cybersecurity-Projects/blob/03a72a342966e8675ecb96eacf369ba6d6f66c66/Images/DNS/Figure%201.1%20-%20VirtualBox%20Network%20Creation.png)

This means we will be using a private class ‘C’ network address with a classful subnet mask of 255.255.255.0 - giving our network 254 usable hosts to use as our network grows. We will later configure static IPs for each of our hosts on the network. Note: Each virtual machine will need to be attached to a NAT network within the adapter settings which allows us to have an isolated network between multiple virtual machines with internet connectivity. Figure 1.2 shows our established network, outlining each virtual machine with their assigned IP address and subnet mask that will be set up in the later steps.

**Figure 1.2: Network Topology**

![image alt](https://github.com/taylor-anthony-smith/Cybersecurity-Projects/blob/3796e6b76186550859fe0ec830b87bb4bbd27062/Images/DNS/Figure%201.2%20-%20Network%20Topology.png)

# DNS Manager Installation:
Our next step in the process is to set up our Windows 2022 server to run DNS Manager and serve as our recursive resolver. Once loaded into our Windows 2022 virtual machine, we will need to start by assigning our server a static IP address and not use DHCP. This is a critical step to ensure our server’s IP address does not change - allowing client devices to query the server consistently for domain name resolution.

To assign a static IP, we manually enter our IPv4 information within our Ethernet properties as shown in Figure 2.1. Each machine on our network will need their own IP address and be on the same subnet as our server. We also need to set the default gateway as the IP address of our router. Note: It’s important to set the DNS server address to the same address of our DNS server, allowing it to resolve its own queries locally.

**Figure 2.1: Static IP Assignment**
![image alt](https://github.com/taylor-anthony-smith/Cybersecurity-Projects/blob/3796e6b76186550859fe0ec830b87bb4bbd27062/Images/DNS/Figure%202.1%20-%20Static%20IP%20Assignment.png)
