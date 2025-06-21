The  usage of Nmap (Network Mapper), a powerful open-source tool for network exploration and security auditing, within a Kali Linux environment.I executed several Nmap commands to perform different types of network scans on a target network/IP address, likely a virtual machine or a local network.

Here's a detailed breakdown focusing on each Nmap command demonstrated:

 Analysis:



Command: nmap
Action:  typed nmap and pressed Enter.
Output/Explanation: Nmap responds by displaying its basic usage information, including the version, available options (like -sS, -sT, -sU, -p), target specifications, and other general syntax guidelines. This is a common way to get help or remind oneself of Nmap's capabilities.
nmap 10.0.2.15/24 

Command: nmap 10.0.2.15/24
Action: I specified a target using CIDR notation. 10.0.2.15 is an IP address, and /24 indicates a subnet mask of 255.255.255.0, meaning Nmap will scan all 256 possible IP addresses in the 10.0.2.0 to 10.0.2.255 range.
Output/Explanation: Nmap performs a default scan on the specified network. This typically includes:
Host Discovery (Ping Scan): It first identifies which hosts are live within the /24 subnet.
Port Scan: For each live host, it performs a default port scan (usually the 1000 most common TCP ports) to determine which ports are open, closed, or filtered.
The output shows Nmap scan report for various IP addresses (e.g., 10.0.2.1, 10.0.2.2, 10.0.2.15). It lists the state of scanned ports (e.g., 22/tcp filtered ssh, 23/tcp filtered telnet, 139/tcp filtered netbios-ssn). The term "filtered" often suggests that a firewall is blocking the traffic to those ports. It also summarizes 256 IP addresses (3 hosts up) meaning it found 3 active hosts in the range.
nmap -sP 10.0.2.15/24 

Command: nmap -sP 10.0.2.15/24
Action: I used  the -sP option.
Output/Explanation:
-sP: This is the "Ping Scan" option. It instructs Nmap to only perform host discovery (determine if a host is online) and not to perform any port scanning. This is much faster than a full scan.
The output confirms this: "Nmap done: 256 IP addresses (3 hosts up) scanned in X seconds." It shows only the "Host is up" messages for 10.0.2.1, 10.0.2.2, and 10.0.2.15, without listing any open ports, indicating that only the host discovery phase was executed.
nmap -sT 10.0.2.15/24 

Command: nmap -sT 10.0.2.15/24
Action: I used the -sT option.
Output/Explanation:
-sT: This performs a "TCP Connect Scan." Unlike the default SYN scan (which is often done by default if not specified and privileged), a TCP Connect Scan completes the full TCP 3-way handshake (SYN, SYN-ACK, ACK) with the target port. This type of scan is less stealthy as it generates full connection entries in the target's logs, but it can be reliable when SYN scans are blocked by firewalls.
The output again shows scan reports for the live hosts (10.0.2.1, 10.0.2.2, 10.0.2.15). For 10.0.2.15, it specifically shows 22/tcp filtered ssh, 23/tcp filtered telnet, 139/tcp filtered netbios-ssn. The detailed packet trace-like output around 0:32-0:35 indicates the TCP handshake attempts (e.g., connect_info, connect_read). It indicates that ports are filtered, meaning a firewall is likely dropping connection attempts.
nmap -p 22,23,139 10.0.2.15 

Command: nmap -p 22,23,139 10.0.2.15
Action: I used specifier particular ports to scan using -p.
Output/Explanation:
-p 22,23,139: This option tells Nmap to scan only ports 22 (SSH), 23 (Telnet), and 139 (NetBIOS Session Service) on the target 10.0.2.15.
The output shows Nmap scan report for 10.0.2.15 and lists the state of these three specific ports as filtered. This is a more targeted scan, useful for checking specific services without scanning all possible ports.
nmap -p 22 23 139 10.0.2.15 

Command: nmap -p 22 23 139 10.0.2.15
Action: I used repeated the port-specific scan but separated the port numbers with spaces instead of commas.
Output/Explanation: Nmap correctly interprets this syntax as well, scanning the same three ports (22, 23, 139) on 10.0.2.15. The output is identical to the previous command, showing them as filtered. This demonstrates flexibility in Nmap's command-line parsing for port specification.
nmap --packet-trace 10.0.2.15 

Command: nmap --packet-trace 10.0.2.15
Action: I  used the --packet-trace option.
Output/Explanation:
--packet-trace: This highly verbose option tells Nmap to display every packet sent and received during the scan. This is a powerful debugging and learning tool.
The output is flooded with low-level packet information, including details like:
SENT (0.0006s) TCP 10.0.2.15:22 > 10.0.2.15:100 (SYN): Nmap sending a SYN packet to port 22 on the target from its own ephemeral port 100.
RECV (0.0001s) ICMP 10.0.2.15:0 > 10.0.2.15:0 type 3 code 13 (unreachable): The target or an intermediary device is responding with an ICMP "Destination Unreachable" message, specifically "Communication Administratively Filtered" (code 13), which confirms that a firewall is blocking the connection. This directly explains why the ports were reported as "filtered" in previous scans.
This command is invaluable for understanding exactly how Nmap is interacting with the network and for diagnosing network or firewall issues.

Summary of  Learning Points:

The several key aspects of Nmap:

Basic Usage: How to start Nmap and get help.
Target Specification: Scanning single IPs and entire subnets using CIDR notation.
Host Discovery (-sP): Quickly finding live hosts without port scanning.
TCP Connect Scan (-sT): Performing a full TCP handshake scan, useful in certain scenarios but less stealthy.
Port Specification (-p): Targeting specific ports to save time and resources.
Debugging and Deep Dive (--packet-trace): Understanding the low-level network interactions and diagnosing scan results (like "filtered" ports due to firewalls).
The filtered state observed for ports 22, 23, and 139 consistently across multiple scans (especially evident with --packet-trace showing ICMP type 3 code 13) strongly suggests that a firewall is actively blocking access to these ports on the target 10.0.2.15.
