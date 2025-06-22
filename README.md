# Introduction to Network Analysis
This repository showcases my approach to network traffic analysis through three PCAP challenges: **PCAP 4**, **PCAP 5**, and **PCAP 3**. For each challenge, I have outlined the questions given, the tools I used (primarily `tcpdump` and Wireshark), the exact commands or GUI steps, and the reasoning behind each technic.

---

## PCAP 4
**Tool Used:** `tcpdump`

### Questions & Answers
1. **How many UDP packets have been captured?**
   - **Command:**
     ```bash
     tcpdump -nnr SBT-PCAP4.pcap udp | wc -l
     ```
   - **Reasoning:**
     - `-nn` This command disables DNS or services lookups for raw IPs and ports.
     - `udp` It filters only UDP packets.
     - `wc -l` Counts one line per packet.
   - **Answer:** `3290`

2. **How many TCP packets have both the SYN and ACK flags set?**
   - **Command:**
     ```bash
     tcpdump -nnr SBT-PCAP4.pcap 'tcp[13]==18' | wc -l
     ```
   - **Reasoning:**
     - `tcp[13]` Examines the TCP flags byte.
     - `==18` Matches the decimal value for SYN (0x02) + ACK (0x10) together.
   - **Answer:** `20`

3. **How many packets have a TTL value of 38?**
   - **Command:**
     ```bash
     tcpdump -nnr SBT-PCAP4.pcap 'ip[8] == 38' | wc -l
     ```
   - **Reasoning:**
     - `ip[8]` Directs to the TTL field in the IPv4 header.
     - `== 38` Chooses the value 38.
   - **Answer:** `710`

4. **Which version of Chrome was used to connect to securityblue.team?**
   - **Tool:** `tshark` (with TLS key logging)
   - **Commands:**
     ```bash
     tshark -o tls.keylog_file:~/sslkeys.log \
       -r SBT-PCAP4.pcap \
       -Y 'http.host == "securityblue.team"' \
       -T fields -e http.user_agent
     ```
   - **Reasoning:**
     - Chrome logs TLS session keys to `sslkeys.log`.
     - `tshark` Decrypts HTTPS and extracts the `User-Agent`.
   - **Answer:**
     ```
     80.0.3987.87 
     ```

---

## PCAP 5
**Tools Used:** `tcpdump`, `tshark`

### Questions & Answers
1. **Name of the PNG file on 192.168.56.111?**
   - **Command:**
     ```bash
     tcpdump -A -r SBT-PCAP5.pcap | grep .png
     ```
   - **Reasoning:**
     - `-A` Shows ASCII payloads.
     - `grep .png` Finds `.png`
   - **Answer:** `proprietary.png`

2. **OpenSSH version on the server?**
   - **Command:**
     ```bash
     tcpdump -vvv -r SBT-PCAP5.pcap | grep SSH-
     ```
   - **Reasoning:**
     - `-vvv` Prints the SSH banner with increased verbosity. 
     - `grep SSH-` Filters the version line.
   - **Answer:** `7.9p1`

3. **On which port is the .zip file being served?**
   - **Commands:**
     ```bash
     tcpdump -A -r SBT-PCAP5.pcap | grep -m1 -B1 "\.zip"
     ```
     **Output snippet:**
     ```
     06:04:58.454287 IP 192.168.56.111.notify_srvr > 192.168.56.1.50159: … ziptest.txtUT …
     ```
     Here `notify_srvr` is the service name.
     ```bash
     grep notify_srvr /etc/services
     ```
     **Output:**
     ```
     notify_srvr     3016/tcp    
     ```
   - **Reasoning:**
     - First tcpdump shows the `.zip` payload and the service label.
     - Looking up `notify_srvr` in `/etc/services` shows port `3016`.
   - **Answer:** `3016`

4. **Timestamp of the packet with TCP checksum 53203?**
   - **Command:**
     ```bash
     tcpdump -x -r SBT-PCAP5.pcap 'tcp[16:2] == 53203'
     ```
   - **Reasoning:**
     - `tcp[16:2]` Reads the 2-byte checksum field.
     - `== 53203` Matches that value.
     - `-x` Shows hex dump + timestamp.
   - **Answer:** `06:04:46.207925`

---

## PCAP 3 (Capstone)
**Tools Used:** `tcpdump`, **Wireshark** (GUI), `tshark`

### Questions & Answers
1. **MAC address of the attacker?**
   - **Wireshark (GUI):**
     ```
     # Step 1. In display field for filter
     arp.code==2
     # Step 2. Statistics > Endpoints > Ethernet > sort by large packets
     ```
   - **Reasoning:**
     - `arp.opcode == 2` shows ARP replies.
     - Attackers poison through unsolicited replies.
   - **Answer:** `08:00:27:3d:27:5d`

2. **Type of attack?**
   - **Observation:** Unsolicited ARP replies mapping gateway IP to attacker MAC.
   - **Answer:** Man-in-the-Middle through ARP poisoning

3. **File downloaded from central server?**
   - **Command (tshark):**
     ```bash
     tshark -r SBT-PCAP3.pcapng \
       -Y 'ftp.request.command == "RETR"' \
       -T fields -e ftp.request.arg | sort -u
     ```
   - **Reasoning:**
     - FTP `RETR`’s argument is the file name.
   - **Answer:** `Alevis_Employee_Information_Chart.csv`

4. **Department of Borden Danilevich?**
   - **Wireshark (GUI):**
     1. Filter: `tcp.stream == 1`
     2. Right-click → Follow → TCP Stream 
     3. Find “Borden Danilevich” in the stream using the search bar on the bottom left. 
   - **Answer:** Sales
   - ![Screenshot](images/img2)

5. **SSH password of Domain Administrator?**
   - **Wireshark (GUI):**
     1. In the same window search for `Domain Admin`
   - **Reasoning:**
     - Same FTP stream includes credentials.
   - **Answer:** `gMR<4eXf]e6W`

---

### Conclusion
This helped me gain experience using `tcpdump`, `tshark`, and Wireshark to extract network information: packet counts, flags, TTLs, decrypted HTTP headers, file downloads, ARP poisoning indicators, and even misplaced credentials—key skills for network forensics.  
