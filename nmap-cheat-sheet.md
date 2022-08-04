# Nmap Cheat Sheet

- [Nmap-Cheat-Sheet](#nmap-cheat-sheet)
  * [Target Specification](#target-specification)
  * [Scanning Techniques](#scanning-techniques)
  * [Host Discovery](#host-discovery)
  * [Port Specification](#port-specification)
  * [Timing and Performance](#timing-and-performance)
  * [Timing and Performance Continued](#timing-and-performance-continued)
  * [Firewall / IDS Evasion and Spoofing](#firewall---ids-evasion-and-spoofing)
  * [Output](#output)
  * [Helpful Nmap Output examples](#helpful-nmap-output-examples)
  * [More Useful Commands](#more-useful-commands)
  * [Inspired by StationX nmap CheatSheet](#inspired-by-stationx-nmap-cheatsheet)

## Target Specification

```bash
<Switch>        <Example>                           <Desc>
                nmap 192.168.1.1                    Scan a single IP
                nmap 192.168.1.1 192.168.2.1        Scan specific IPs
                nmap 192.168.1.1-254                Scan a range
                nmap scanme.nmap.org                Scan a domain
                nmap 192.168.1.0/24                 Scan using CIDR notation
-iL             nmap -iL targets.txt                Scan targets from a file
-iR             nmap -iR 100                        Scan 100 random hosts
--exclude       nmap --exclude 192.168.1.1          Exclude listed hosts
```
## Scanning Techniques

```bash
<Switch>        <Example>                           <Desc>
-sS             nmap 192.168.1.1 -sS                TCP SYN port scan (Default)
-sT             nmap 192.168.1.1 -sT                TCP connect port scan
(Default without root priv)
-sU             nmap 192.168.1.1 -sU                UDP port scan
-sA             nmap 192.168.1.1 -sA                TCP ACK port scan
-sW             nmap 192.168.1.1 -sW                TCP Window port scan
-sM             nmap 192.168.1.1 -sM                TCP Maimon port scan
```
## Host Discovery

```bash
<Switch>        <Example>                           <Desc>
-sL             nmap 192.168.1.1-3 -sL              No Scan. List targets only
-sn             nmap 192.168.1.1/24 -sn             Disable port scanning
-Pn             nmap 192.168.1.1-5 -Pn              Disable host discovery. Port scan only
-PS             nmap 192.168.1.1-5 -PS22-25,80      TCP SYN discovery on port x. Port 80 by default
-PA             nmap 192.168.1.1-5 -PA22-25,80      TCP ACK discovery on port x. Port 80 by default
-PU             nmap 192.168.1.1-5 -PU53            UDP discovery on port x. Port 40125 by default
-PR             nmap 192.168.1.1-1/24 -PR           ARP discovery on local network
-n              nmap 192.168.1.1 -n                 Never do DNS resolution
```
## Port Specification
```bash
<Switch>        <Example>                           <Desc>
p               nmap 192.168.1.1 -p 21              Port scan for port x
-p              nmap 192.168.1.1 -p 21-100          Port range
-p              nmap 192.168.1.1 -p U:53,T:21-25,80 Port scan multiple TCP and UDP ports
-p-             nmap 192.168.1.1 -p-                Port scan all ports
-p              nmap 192.168.1.1 -p http,https      Port scan from service name
-F              nmap 192.168.1.1 -F                 Fast port scan (100 ports)
--top-ports     nmap 192.168.1.1 --top-ports 2000   Port scan the top x ports
-p-65535        nmap 192.168.1.1 -p-65535           Leaving off initial port in range makes the scan start at port 1
-p0-            nmap 192.168.1.1 -p0-               Leaving off end port in range makes the scan go through to port 65535
```
## Timing and Performance
```bash
<Switch>        <Example>                           <Desc>
-T0             nmap 192.168.1.1 -T0                Paranoid (0) Intrusion Detection System evasion
-T1             nmap 192.168.1.1 -T1                Sneaky (1) Intrusion Detection System evasion
-T2             nmap 192.168.1.1 -T2                Polite (2) slows down the scan to use less bandwidth and use less target machine resources
-T3             nmap 192.168.1.1 -T3                Normal (3) which is default speed
-T4             nmap 192.168.1.1 -T4                Aggressive (4) speeds scans; assumes you are on a reasonably fast and reliable network
-T5             nmap 192.168.1.1 -T5                Insane (5) speeds scan; assumes you are on an extraordinarily fast network
```
## Timing and Performance Continued
```bash
<Switch>                                                      <Example Inp>                           <Desc>

--host-timeout <time>                                         1s; 4m; 2h                              Give up on target aer this long
--min-rtt-timeout/max-rtt-timeout/initial-rtt-timeout <time>  1s; 4m; 2h                              Specifies probe round trip time
 --min-hostgroup/max-hostgroup <size>                         50; 1024                                Parallel host scan group sizes
--min-parallelism/max-parallelism <numprobes>                 10; 1                                   Probe parallelization
--scan-delay/--max-scan-delay <time>                          20ms; 2s; 4m; 5h                        Adjust delay between probes
--max-retries <tries>                                         3                                       Specify the maximum number of port scan probe retransmissions
--min-rate <number>                                           100                                     Send packets no slower than <number> per second
--max-rate <number>                                           100                                     Send packets no faster than <number> per second
```
## Firewall / IDS Evasion and Spoofing
```bash
<Switch>                                                      <Example Inp>                                                               <Desc>
-f                                                            nmap 192.168.1.1 -f                                                         Requested scan (including ping scans) use tiny fragmented IP packets. Harder for packet filters
--mtu                                                         nmap 192.168.1.1 --mtu 32                                                   Set your own offset size
-D                                                            nmap -D 192.168.1.101,192.168.1.102                                         Send scans from spoofed IPs
-D                                                            nmap -D decoy-ip1,decoy-ip2,your-own-ip,decoy-ip3,decoy-ip4 remote-host-ip  Above example explained
-S                                                            nmap -S www.microso.com www.facebook.com                                    Scan Facebook from Microso (-e eth0 -Pn may be required)
-g                                                            nmap -g 53 192.168.1.1                                                      Use given source port number
--proxies                                                     nmap --proxies http://192.168.1.1:8080, http://192.168.1.2:8080 192.168.1   Relay connections through HTTP/SOCKS4 proxies
--data-length                                                 nmap --data-length 200 192.168.1.1                                          Appends random data to sent packets
Example IDS Evasion command
nmap -f -t 0 -n -Pn --data-length 200 -D 192.168.1.101,192.168.1.102,192.168.1.103,192.168.1.23 192.168.1.1
```
## Output
```bash
<Switch>                                                       <Example Inp>                                       <Desc>
-oN                                                            nmap 192.168.1.1 -oN normal.file                    Normal output to the file normal.file
-oX                                                            nmap 192.168.1.1 -oX xml.file                       XML output to the file xml.file
-oG                                                            nmap 192.168.1.1 -oG grep.file                      Grepable output to the file grep.file
-oA                                                            nmap 192.168.1.1 -oA results                        Output in the three major formats at once
-oG -                                                          nmap 192.168.1.1 -oG -                              Grepable output to screen. -oN -, -oX - also usable
--append-output                                                nmap 192.168.1.1 -oN file.file --append-output      Append a scan to a previous scan file
-v                                                             nmap 192.168.1.1 -v                                 Increase the verbosity level (use -vv or more for greater effect)
-d                                                             nmap 192.168.1.1 -d                                 Increase debugging level (use -dd or more for greater effect)
--reason                                                       nmap 192.168.1.1 --reason                           Display the reason a port is in a particular state, same output as -vv
--open                                                         nmap 192.168.1.1 --open                             Only show open (or possibly open) ports
--packet-trace                                                 nmap 192.168.1.1 -T4 --packet-trace                 Show all packets sent and received
--iflist                                                       nmap --iflist                                       Shows the host interfaces and routes
--resume                                                       nmap --resume results.file                          Resumes a scan
-6                                                             nmap -6 2607:f0d0:1002:51::4                        Enable IPv6 scanning
-h                                                             nmap -h                                             nmap help screen
```

## Helpful Nmap Output examples
```bash
<Command>                                                                                              <Desc>
nmap -p80 -sV -oG - --open 192.168.1.1/24 | grep open                                                  Scan for web servers and grep to show which IPs are running web servers
nmap -iR 10 -n -oX out.xml | grep "Nmap" | cut -d " " -f5 > live-hosts.txt                             Generate a list of the IPs of live hosts
nmap -iR 10 -n -oX out2.xml | grep "Nmap" | cut -d " " -f5 >> live-hosts.txt                           Append IP to the list of live hosts
ndiff scanl.xml scan2.xml                                                                              Compare output from nmap using the ndiff
xsltproc nmap.xml -o nmap.html                                                                         Convert nmap xml files to html files
grep " open " results.nmap | sed -r 's/ +/ /g' | sort | uniq -c | sort -rn | less                      Reverse sorted list of how oen ports turn up
```
## More Useful Commands
```bash
<Command>                                                                                              <Desc>
nmap -iR 10 -PS22-25,80,113,1050,35000 -v -s                                                           Discovery only on ports x, no port scan
nmap 192.168.1.1-1/24 -PR -sn -vv                                                                      Arp discovery only on local network, no port scan
nmap -iR 10 -sn -traceroute                                                                            Traceroute to random targets, no port scan
nmap 192.168.1.1-50 -sL --dns-server 192.168.1.1                                                       Query the Internal DNS for hosts, list targets only

```

### Inspired by StationX nmap CheatSheet 
