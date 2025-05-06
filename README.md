<h1>Bruteforce Monitoring Dashboard- Splunk</h1>

<h2>Description</h2>

This dashboard is designed to detect failed authentication attempts that may indicate brute-force attacks on systems. Users can filter data by time, source IP address, and username.

The dashboard includes two main panels:

<b>Failed Authentication Table:</b> Displays the number of successful and failed login attempts, grouped by computer, user, and source IP. A failure rate is calculated, and systems with a rate exceeding 20% are highlighted.

<b>Attack Graph:</b> Provides a visual representation of successful and failed login attempts by user and source IP, emphasizing accounts and IPs with more than 20 failed login attempts. country.


<h2>Languages and Utilities Used</h2>

- <b>SPL-Search Processing Language</b>
- <b>BotsV2-[Dataset](https://github.com/splunk/botsv2)</b>
  
<h2>Environments Used </h2>

- <b>SIEM-Splunk</b>
- <b>OS-Kali Linux</b>

<h2>Dashboard Setup</h2>

<h3><b><p align="center">
Failed Authentication Table Panel <br/> 
</b></h3>

<b><p align="center">
SPL Query: <br/> 
</b>
```
index=botsv2 source IN ("WinEventlog:Security") EventCode IN (4624, 4625) src_ip=$ip_token$ user=$user_token$
| stats count(eval(EventCode=4624)) as successes, count(eval(EventCode=4625)) as failures by ComputerName, user, src_ip
| eval total_attempts = failures + successes
| eval failure_rate = round((failures / total_attempts) *100, 2)
| where failure_rate >20
```
<b>This dashboard uses EventCode 4624, which indicates a successful authentication on a Windows system, and EventCode 4625, which indicates a failed authentication attempt.
Monitoring these event codes is essential for detecting brute-force attack attempts.</b>

<br /><p align="center">
Failed Auth Panel <br/>
<img src="https://i.imgur.com/vmnaDnv.png" height="80%" width="80%" alt="Panel"/>
<br />

<h3><b><p align="center">
Attack Graph <br/> 
</b></h3>

<b><p align="center">
SPL Query: <br/> 
</b>
```
index=botsv2 source IN ("WinEventlog:Security") EventCode IN (4624, 4625) src_ip=$ip_token$ user=$user_token$
| stats count(eval(EventCode=4624)) as successful_logins, count(eval(EventCode=4625)) as failed_logins by user src_ip
| where failed_logins >20
| table user successful_logins failed_logins
```
<b>Provides a visual representation of successful and failed login attempts by user and source IP, emphasizing accounts and IPs with more than 20 failed login attempts.</b>

<br /><p align="center">
Graph <br/>
<img src="https://i.imgur.com/TqCfZaf.png" height="80%" width="80%" alt="Panel"/>
<br />

<h3><b><p align="center">
Final Dashboard <br/> 
</b></h3>

<br />
 <p align="center"><br/>
<img src="https://i.imgur.com/Up0L6al.png" height="80%" width="80%" alt="Panel"/>
<br />
<b>This allows for better organization and provides a clearer overall view of the connections.</b>
