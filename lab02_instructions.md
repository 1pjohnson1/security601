# Assisted Lab 02: Scanning and Identifying Network Nodes

## Scenario

In this activity, you will use common network tools, such as local commands and the Nmap network mapper, to discover other hosts on the local network. Next, you will conduct a banner grabbing exercise to identify specific services on the hosts. Finally, you will use DNS tools to gather name resolution information.

## Objectives

This activity is designed to test your understanding of and ability to apply content examples in the following CompTIA Security+ objective:

 - 4.1 Given a scenario, use the appropriate tool to assess organizational security

===

## Identify local network configuration

Determine the configuration of the local host and its subnet, using tools such as **ifconfig**, **ip**, **arp**, **netdiscover**, and **pathping**. Run the scans from the KALI Linux VM and the DC1 Windows Server.

1. [] On the @lab.VirtualMachine(PT1-Kali).SelectLink VM, sign in as <tt>root</tt> using <tt>Pa$$w0rd</tt> as the password.

    >[!TIP]If the privacy shade has activated, click-and-drag up with the mouse to show the sign-in box.

1. [] From the top bar, select the Terminal Emulator icon !IMAGE[Gnome Terminal Emulator icon button](icon gnome terminal emulator.png).

1. [] Run the <tt>ifconfig</tt> command to display the interface configuration. Note the IP address assigned to the eth0 interface.

    ```bash-notab-nocopy
    ifconfig
    ```

1. [] Run the <tt>ip a</tt> command to display the same information using the newer <tt>ip a</tt> tool:

    ```bash-notab-nocopy
    ip a
    ```   

    <details>
    <summary> The ifconfig and ip a commands.</summary>
    !IMAGE[ifconfig output](Screen Shot 2020-11-06 at 10.23.15 AM.png)    

    !IMAGE[ip a output](Screen Shot 2020-11-06 at 10.23.30 AM.png)
    </details>


1. [] @lab.Activity(Question1)

    >[!ALERT] The purpose behind Assisted Labs is to confirm your knowledge and guide you through the given configurations. If you get a scored question incorrect, you may repeat the question and achieve the correct answer. You do not need a correct answer to move forward through the lab.

    <details>
    <summary>IP address.</summary>
    !IMAGE[PT1-Kali IP address](Screen Shot 2020-11-06 at 10.23.30 AM copy.png)
    </details>


1. [] Run the following command to identify the default gateway:

    ```bash-notab-nocopy
    ip route show
    ```

    Because the network uses DHCP to provide client addresses, the local machine has been configured with a default gateway address automatically.

1. [] @lab.Activity(Question2)

    <details>
    <summary>Default gateway.</summary>
    !IMAGE[Default gateway IP address](Screen Shot 2020-11-06 at 10.26.53 AM.png)
    </details>

1. [] Run the <tt>arp -a</tt> command to check the ARP cache to display other hosts local to this subnet.

    ```bash-notab-nocopy
    arp -a
    ```

1.  [] @lab.Activity(Question26)

    <details>
    <summary>The arp command.</summary>
    !IMAGE[arp -a output](Screen Shot 2020-11-06 at 10.28.28 AM.png)
    </details>

1. [] Run the <tt>ip neighbor</tt> command to display similar information using the newer <tt>ip</tt> tool.

    ```bash-notab-nocopy
    ip neighbor
    ```

    The ARP cache shows only machines that have communicated with the local host. To verify whether any other hosts are present, you can perform a "sweep" of the local network. One means of doing this is to use **ping** in a for/next loop. You can also use the **netdiscover** tool bundled with Kali.

1. [] Run the following command to scan the network by using **netdiscover**. The results should discover several other hosts connected to the vLOCAL switch.

    ```bash-notab-nocopy
    netdiscover -i eth0 -r 10.1.0.0/24
    ```

    >[!TIP] The <tt>netdiscover</tt> may take up to two minutes to complete *after* it reports as Finished.

1. [] @lab.Activity(Question3)

    <details>
    <summary> The netdiscover command.</summary>
    !IMAGE[netdiscover command output](netdiscover-results.png)
    </details>

1. [] Press <tt>q</tt> to exit the Netdiscover report and return to the command prompt.

    >[!HELP] Run <tt>netdiscover -h</tt> to view the help page. The tool can operate in a passive mode, but you do not need to be stealthy, so you will run an active scan.

1. [] Switch to the @lab.VirtualMachine(DC1).SelectLink VM, send @lab.CtrlAltDelete, and then login as +++@lab.VirtualMachine(DC1).Username+++ using +++@lab.VirtualMachine(DC1).Password+++ as the password.

1. [] Right-click the **Start** menu and select **Command Prompt (Admin)**. When prompted, confirm UAC by selecting **Yes**

1. [] Run the following command to display the IP address configuration for DC1:

    ```cmd-notab
    ipconfig
    ```

1. [] @lab.Activity(Question12)

    <details>
    <summary> The ipconfig command.</summary>
    !IMAGE[ipconfig output](Screen Shot 2020-11-06 at 10.37.19 AM.png)
    </details>

1. [] Run the following command to test the reliability (packet loss) and latency (delay) of the connection between the **DC1** VM and the **PT1-Kali** VM (the test takes 30-45 seconds to run):

    ```cmd-notab
    pathping 10.1.0.192
    ```

1. [] @lab.Activity(Question27)

    <details>
    <summary>The pathping command.</summary>
    !IMAGE[pathping command output](Screen Shot 2020-11-06 at 10.39.42 AM.png)
    </details>
    
    >[!KNOWLEDGE] The two machines are not very far apart on the network—on the same subnet in fact. If you run pathping against network nodes that have several routers between them, the utility displays lost packets along the route, which helps to clarify where network communications might be unreliable. For example, from a Windows computer with Internet access, you could run +++pathping 8.8.8.8+++ to trace the route to the servers running Google's DNS service. Note that the lab virtual machines do *not* have Internet access.

===

## Use nmap to discover hosts

From a penetration tester or threat actor perspective, network reconnaissance will typically aim to discover the following:

 - Default gateway (the router connecting the subnet to other networks).

 - DNS server (used to resolve host names on the network).

 - Whether any network directory/authentication and application servers are present.

 - Whether any host/client access devices are present.

 - Whether any other types of devices (embedded systems or appliances) are present.

Use Nmap from the PT1-Kali VM to report this information for this network.

1. [] Switch back to @lab.VirtualMachine(PT1-Kali).SelectLink. If necessary, log in as <tt>root</tt> using <tt>Pa$$w0rd</tt> as the password.

1. [] Run the following command to scan the Kali VM:

    ```bash-notab-nocopy
    nmap localhost
    ```

1. [] @lab.Activity(Question28)

    <details>
    <summary>Port numbers</summary>
    Review the application layer port numbers. Because this is a Linux machine, you can assume that the SSH service is probably running. That would be a great place to start to identify the open port.
    </details>

1. [] Run the following command to do a basic network scan:

    ```bash-notab-nocopy
    nmap 10.1.0.0/24
    ```

    <details>
    <summary>Use the following chart as a reference for nmap options during this activity.</summary>

    |  Option:      | Explanation: | Example: |
    |:--------------|:---------|:--------|
    | (no option)   | basic scan   |  nmap 10.1.0.0  |
    | -A   | OS, port, service detection   | nmap -A 10.1.1.1  |
    | -sS   | fast scan    |  nmap -sS 10.1.1.1  |
    | -p   | specified port numbers    | nmap -p 21,23,80 or nmap -p 20-100  |
    | --top-ports 20  | scans top 20 common ports (can change the number)  | nmap --top-ports 20 10.1.0.0  |
    | -sn    | scan for up/down hosts  | nmap -sn 10.1.0.0  |

    </details>

1. [] Run the following command and check the output. What services are running and what do they tell you about the host?

    ```bash-notab-nocopy
    nmap -sS 10.1.0.254
    ```
    
    This syntax will scan the default port range (1000 ports) on the target.

1. [] @lab.Activity(Question29)

    <details>
    <summary>Figure: Nmap service discovery scan output.</summary>
    !IMAGE[Service discovery scan output from nmap tool](mwydokcj.png)

     (Screenshot used with permission from nmap.org.)
    </details>


1. [] Run the following command to identify more about the host:

    ```bash-notab-nocopy
    nmap -A 10.1.0.254
    ```

1. [] @lab.Activity(Question30)

    <details>
    <summary>Figure: Nmap OS fingerprinting scan output. (Screenshot used with permission from nmap.org.)</summary>
    !IMAGE[OS fingerprinting scan output from nmap tool](2w9i4iyy.png)
    
    Look at the information obtained from analyzing the open ports:

    - 22—this is an SSH (Secure Shell) port, which would be used to configure the router remotely. The hostkey is the public key used to identify the host and initialize the encryption of communications over the secure channel. Note that Nmap has identified the version of OpenSSH running the service.

    - 53—the router is running a Domain Name Service (DNS), either because it hosts one or more domains or provides forwarding for clients. The software behind this port is not identified ("tcpwrapped" usually indicates that the service is protected by an ACL).
    - MAC Address—Nmap correctly identifies the OUI portion as belonging to Microsoft (the MAC address is assigned by Hyper-V).

    - CPE (Common Platform Enumeration)—Nmap approximates the kernel version and does not identify a specific Linux distribution (VyOS is derived from Debian).

    - The router is not running any sort of dynamic routing protocol on this local interface.

    </details>

1. [] Run the following command to scan for open ports **20-200** on the network:

    ```bash-notab-nocopy
    nmap -p 20-200 10.1.0.0/24
    ```

1. [] @lab.Activity(Question4)

    <details>
    <summary>Port scan results.</summary>
    Port 80 is associated with the HTTP application layer protocol. Identify whether the web server service is listening on the server.
    </details>

1. [] Run the following command to scan for the **twenty** most common ports:

    ```bash-notab-nocopy
    nmap --top-ports 20 10.1.0.0/24
    ```

1. [] Run the following command to quickly scan the network for hosts that are **up** or **down** on the network:

    ```bash-notab-nocopy
    nmap -sn 10.1.0.0/24
    ```

1. [] @lab.Activity(Question15)

    <details>
    In the scan report for MS1 (10.1.0.2), Nmap has identified the hMailServer application listening on SMTP (25/587) and IMAP (143) ports. It is running Microsoft's IIS web server though and Nmap has correctly identified it as version 10.
    </details>


    
===

## Banner grab with curl and Firefox

Banner grabbing is a way of identifying service versions. This information helps attackers select potentially vulnerable machines. The information also helps administrators confirm that all services on the network are of a certain version.

Basic tools can be used to grab banners. In this activity, you will use cURL and Firefox.

1. [] Run the following command in the **Terminal** to connect to the 10.1.0.1 HTTP server by using cURL:
    
    ```bash-notab-nocopy
    curl -s -I 10.1.0.1
    ```

    > [!ALERT] You are disconnected automatically.

1. [] @lab.Activity(Question7)

    <details>
    <summary>The curl command.</summary>
    !IMAGE[Curl command output](Screen Shot 2020-11-06 at 10.42.45 AM.png)
    </details>

    You used port scans in the earlier activities to determine that the 10.1.0.1 virtual machine is running a web service. With this cURL command, you have determined the software type and version for the web server service.

1. [] In Kali, type <tt>firefox http://10.1.0.1</tt> and then select **Enter**.

    ```bash-notab-nocopy
    firefox http://10.1.0.1
    ```

1. [] @lab.Activity(Question9)

    <details>
    <summary>Web server service information.</summary>
    The web server is a Windows Server. The Microsoft web server service is Internet Information Services (IIS). Though this is not a definitive identification of the web server service, it is probably accurate.
    </details>

1. [] Close the Firefox web browser.

===

## Query DNS

DNS provides name resolution to internal networks as well as the Internet. DNS is used any time a user or application refers to a host by name. DNS queries name records to find the IP address associated with a hostname or fully qualified domain name. These name records can also reveal information about how a network is configured. In this task, you will gather DNS information by using the **dig** utility.

1. [] Run the <tt>dig</tt> command in the **Terminal** to perform a reverse lookup on the default gateway.

    ```bash-notab-nocopy
    dig -x 10.1.0.254
    ```

1. [] @lab.Activity(Question10)
    
    <details>
    <summary>The DNS server that answers your query.</summary>
    !IMAGE[Output from dig tool](9qs7q9c7.png)

    Figure: dig reverse lookup query. (Screenshot used with permission from Offensive Security.)
        </details>

1. [] @lab.Activity(Question11)

    <details>
    <summary>The FQDN.</summary>
 
    >[!KNOWLEDGE] The trailing dot represents the root domain. This is not required by client browsers and other user applications, but must be used correctly within name records and configuration files.
    
    >[!HELP] Optionally run <tt>dig -h</tt> to familiarize yourself with the options for the command.

1. [] Run the following command to display the authoritative DNS server for the namespace:

    ```bash-notab-nocopy
    dig soa corp.515support.com
    ```

    <details>
    <summary>The SOA records.</summary>
    !IMAGE[SOA records returned by dig tool](0r37jijo.png)

    Figure: dig query for Start of Authority DNS server record. (Screenshot used with permission from Offensive Security.)
    </details>


    The query returns the FQDN of the DNS server responsible for the domain (DC1.corp.515support.com) and its host record (10.1.0.1). It's also worth noting some of the flags shown:

     - aa indicates that the answer is authoritative. The "AUTHORITY" section of the response is empty. Contents for this section are commonly omitted by name servers to reduce the size of responses.

     - ra indicates that recursion is available; that is, this router will forward queries to other servers.

1. [] Close the terminal window.

===

## Comprehensive questions

Answer the following final comprehensive questions to ensure that you recognize the importance of the activity steps and the uses for the information you have learned.

1. [] @lab.Activity(Question23)

1. [] @lab.Activity(Question24)

1. [] @lab.Activity(Question25)

===

## Grade Lab

That concludes this lab. When you have finished a lab, please ensure you end it properly rather than just closing the browser window.

1. [] Select **Submit** below to grade the lab. Once you select **Submit**, you will not be able to return to the lab environment. 
