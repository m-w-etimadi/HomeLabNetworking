<h1>Home Lab Setup: Configuring Cisco Router, Switches, and Wi-Fi Router for Internet Connectivity</h1>

<!-- ### [YouTube Demonstration](https://youtu.be/7eJexJVCqJo) -->

<h2>Overview</h2>
In this home lab, I successfully configured a network consisting of a Cisco router, two switches (Distribution and Access), and a home Wi-Fi router. The goal was to establish internet connectivity for end users while implementing advanced networking features like EIGRP, NAT/PAT, and DHCP. Below is a detailed breakdown of the lab setup, challenges faced, and the solutions implemented.
<br />


<h2>Lab Objectives</h2>
- <strong>Physical Connectivity</strong>: Established connections between the Cisco router, two switches, and the home Wi-Fi router.<br>
- <strong>IP Address Assignment</strong>: Connected the Cisco router to the Wi-Fi router and obtained an IP address.<br>
- <strong>EIGRP Routing</strong>: Configured EIGRP (AS 1) between the router and the Distribution switch.<br>
- <strong>Default Route Advertisement</strong>: Advertised a static default route to the EIGRP routing domain from the Cisco router.<br>
- <strong>NAT/PAT Configuration</strong>: Set up NAT/PAT to allow end users to access the internet.<br>
- <strong>DHCP Server</strong>: Configured the Distribution switch as a DHCP server for end users.<br>
- <strong>SVIs Configuration</strong>: Set up Switched Virtual Interfaces (SVIs) between the Distribution and Access switches.<br>
- <strong>Connectivity Verification</strong>: Ensured the Distribution and Access switches could ping each other.<br>

<!-- <h2>Environments Used </h2>

- <b>Windows 10</b> (21H2) -->

<h2>Lab Diagram</h2>

<p align="center">
Logical Diagram: <br/>
<img src="https://i.imgur.com/qDD0LYQ.jpg" height="80%" width="80%" alt="Disk Sanitization Steps" />
<br />
<br />

<p align="center">
Physical Diagram: <br/>
<img src="https://i.imgur.com/BwWLBMg.jpeg" height="80%" width="80%" alt="Disk Sanitization Steps" />
<br />
<br />
  
<h2>Challenges and Solutions</h2>

<!-- -<h2>Issue: Default Route Not Installed in Distribution Switch</h2>
 <strong>Issue: Default Route Not Installed in Distribution Switch<br> -->
 <strong>Issue</strong>: Default Route Not Installed in Distribution Switch.<br>
<b>The Distribution switch (LAB-DSW1) was not installing the EIGRP-advertised static default route into its routing table.</b>
<br />
<br />

<div>
  <h3>Verification Steps:</h3>
  <ul>
       <li>The default route (0.0.0.0/0) was not visible in the routing table of LAB-DSW1.</li>
    <li>EIGRP neighbors were established, but the route was missing.</li
  </ul>
</div>
<br />

<p align="center">
Debugged EIGRP neighbor process on LAB-DSW1 <br/>
<img src="https://i.imgur.com/Ck3j8Xj.png" height="80%" width="80%" alt="Disk Sanitization Steps" />
<br />
<br />

<p align="center">
Routing Table on LAB-DSW1 <br/>
<img src="https://i.imgur.com/zdJfeqh.png" height="80%" width="80%" alt="Disk Sanitization Steps" />
<br />
<br />



<div>
  <h3>Troubleshooting Steps:</h3>
  <ul>
    <li><strong>Router Configuration:</strong> Verified the static default route and EIGRP advertisement on the upstream router (LAB-R1).</li>
  </ul>
</div>
<br />

<p align="center">
Config on the upstream router (LAB-R1)<br/>
<img src="https://i.imgur.com/aN6Yuzc.png" height="80%" width="80%" alt="Disk Sanitization Steps" />
<br />
<br />

<ul>
    <li><strong>Solution:</strong> Added the following command on the router's interface facing LAB-DSW1 to summarize the default route:</li>
    <li><strong>ip summary-address eigrp 1 0.0.0.0 0.0.0.0 </strong></li>
  </ul>
<br />

<p align="center">
<img src="https://i.imgur.com/aN6Yuzc.png" height="80%" width="80%" alt="Disk Sanitization Steps" />
<br />
<br />
<div>
  <h3>Outcome:</h3>
  <ul>
    <li>The default route was successfully installed in the Distribution switch's routing table, enabling proper routing for internet traffic.</li>
  </ul>
</div>
<br />
<p align="center">
On the LAB-DSW1: <br/>
<img src="https://i.imgur.com/aN6Yuzc.png" height="80%" width="80%" alt="Disk Sanitization Steps" />
<br />

<div>
  <h3>Key Configurations:</h3>
  <br />
  <h4>PAT/NAT Configuration</h4>
  <ul>
       <li>To allow internal hosts to access the internet, NAT/PAT was configured on the Cisco router:</li>
  </ul>
</div>
<br />
<pre style="background-color: #f5f5f5; padding: 10px; border-radius: 5px; font-family: 'Courier New', monospace; color: #333;">
<code>
<span style="color: #0077cc;">interface</span> GigabitEthernet0/0
 <span style="color: #0077cc;">ip nat outside</span>
!
<span style="color: #0077cc;">interface</span> GigabitEthernet0/1
 <span style="color: #0077cc;">ip nat inside</span>
!
<span style="color: #0077cc;">ip nat inside source list</span> 1 <span style="color: #0077cc;">interface</span> GigabitEthernet0/0 overload
!
<span style="color: #0077cc;">access-list</span> 1 <span style="color: #0077cc;">permit</span> 172.16.0.0 0.0.0.255
</code>
</pre>

 <h4>DHCP Server on Distribution Switch</h4>
  <ul>
       <li>The Distribution switch was configured as a DHCP server to assign IP addresses to end hosts:</li>
  </ul>
  <pre style="background: #f5f5f5; padding: 12px; border-radius: 4px; font-family: 'Courier New', monospace; border-left: 3px solid #0066cc;">
<code>
<span style="color: #0066cc;">ip dhcp pool</span> LAB_POOL
 <span style="color: #0066cc;">network</span> 172.16.0.0 255.255.255.0
 <span style="color: #0066cc;">default-router</span> 172.16.0.2
 <span style="color: #0066cc;">dns-server</span> 8.8.8.8
</code>
</pre>

<div class="cisco-config">
  <div class="device">
    <h3>Distribution Switch:</h3>
    <pre>interface Vlan1
 ip address 172.16.0.2 255.255.255.0
 no shutdown</pre>
  </div>

  <div class="device">
    <h3>Access Switch:</h3>
    <pre>interface Vlan1
 ip address 172.16.0.3 255.255.255.0
 no shutdown</pre>
  </div>
</div>

<div class="verification-section">
  <h3>Verification and Success</h3>
  <ul class="verification-list">
    <li><strong>End-to-End Connectivity:</strong> Confirmed that the Access switch (<code>LAB-ASW1</code>) and Distribution switch (<code>LAB-DSW1</code>) could ping each other.</li>
    <li><strong>Internet Access:</strong> A PC connected to <code>LAB-ASW1</code> successfully obtained an IP via DHCP and accessed the internet.</li>
    <li><strong>NAT Logs:</strong> Verified NAT translations to ensure internal traffic was properly masqueraded.</li>
  </ul>
</div>

<div>
  <h3>Verification on my pc:</h3>
  <ul>
    <li>My PC is connected to switch (LAB-ASW1) port fa0/17</li>
    <li>PC’s Wired NIC is set to get its IP config through DHCP</li>
  </ul>
</div>
<br />

<p align="center">
Tuned off wireless NIC<br/>
<img src="https://i.imgur.com/VWDCdZc.png" height="80%" width="80%" alt="Disk Sanitization Steps" />
<br />
<br />
<p align="center">
  Confirmed! wireless NIC is down<br/>
<img src="https://i.imgur.com/7lehsmg.png" height="80%" width="80%" alt="Disk Sanitization Steps" />
<br />
<br />

<p align="center">
  Wired NIC is taken IP/default gateway/dns <br/>
<img src="https://i.imgur.com/7lehsmg.png" height="80%" width="80%" alt="Disk Sanitization Steps" />
<br />
<br />

<p align="center">
  Confirmed! DNS and layer 3 connectivity is working <br/>
<img src="https://i.imgur.com/DTAsPbd.png" height="80%" width="80%" alt="Disk Sanitization Steps" />
<br />
<br />

<h3>PAT/NAT/Access-list Verification:</h3>

<p align="center">
  PAT verification <br/>
<img src="https://i.imgur.com/BnHJHD3.png" height="80%" width="80%" alt="Disk Sanitization Steps" />
<br />
<br />

<p align="center">
  PAT verification <br/>
<img src="https://i.imgur.com/5tR94tG.png" height="80%" width="80%" alt="Disk Sanitization Steps" />
<br />
<br />

<p align="center">
 Access-list verification<br/>
<img src="https://i.imgur.com/3IYQd4R.png" height="80%" width="80%" alt="Disk Sanitization Steps" />
<br />
<br />

<h2>Conclusion</h2>
This home lab provided hands-on experience with Cisco networking technologies, including routing protocols, NAT/PAT, DHCP, and inter-VLAN routing. By troubleshooting the default route issue and verifying each step, the lab was completed successfully, resulting in a fully functional network with internet access for end users.
<br />
<!-- Select the disk:  <br/>
<img src="https://i.imgur.com/tcTyMUE.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
Enter the number of passes: <br/>
<img src="https://i.imgur.com/nCIbXbg.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
Confirm your selection:  <br/>
<img src="https://i.imgur.com/cdFHBiU.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
Wait for process to complete (may take some time):  <br/>
<img src="https://i.imgur.com/JL945Ga.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
Sanitization complete:  <br/>
<img src="https://i.imgur.com/K71yaM2.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
Observe the wiped disk:  <br/>
<img src="https://i.imgur.com/AeZkvFQ.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p> -->

<!--
 ```diff
- text in red
+ text in green
! text in orange
# text in gray
@@ text in purple (and bold)@@
```
--!>
