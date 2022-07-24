# Blue Team: Summary of Operations

## Table of Contents
- Network Topology
- Description of Targets
- Monitoring the Targets
- Patterns of Traffic & Behavior
- Suggestions for Going Further

### Network Topology
The following machines were identified on the network:
- ELK
  - **Operating System**: Linux
  - **Purpose**: ELK server to collect logs of FileBeat, MetricBeat, and PacketBeat from the webservers
  - **IP Address**: 192.168.1.100
- Kali
  - **Operating System**: Linux
  - **Purpose**: Cybersecurity and Pentration focused operating system to test the webservers
  - **IP Address**: 192.168.1.90
- Capstone
  - **Operating System**: Linux
  - **Purpose**: Alert testing for ELK Server
  - **IP Address**: 192.168.1.105
- Target 1
  - **Operating System**: Linux
  - **Purpose**: Vulnerable WordPress Server (Basic)
  - **IP Address**: 192.168.1.110
- Target 2
  - **Operating System**: Linux
  - **Purpose**: Vulnerable WordPress Server (Advanced)
  - **IP Address**: 192.168.1.115

### Description of Targets
The target of this attack was: `Target 1` (192.168.1.110).

Target 1 is an Apache web server and has SSH enabled, so ports 80 and 22 are possible ports of entry for attackers. As such, the following alerts have been implemented:

#### SSH Login Alert
- Monitors Port 22
- Monitors SSH port for unauthorized access
- Alert should trigger when a user attempts to access system over Port 22

#### Privilege Escalation Alert
- Monitor unauthorized root access attempts
- Alert should trigger when an unauthorized user uses sudo or attempts to access privileged directories/files

### Monitoring the Targets

Traffic to these services should be carefully monitored. To this end, we have implemented the alerts below:

#### Excessive HTTP Errors
`Excessive HTTP Errors` is implemented as follows:
  - **Metric**: packetbeat
  - **Threshold**: `http.response.status_code` is above `400` for the last `5` minutes
  - **Vulnerability Mitigated**: Bruteforce, Denial of Service, Enumeration
  - **Reliability**: High Reliability, this alert will generate primarily True Positives. The status codes above 400 will indicate errors through HTTP and alert when they occur
 ![image](https://user-images.githubusercontent.com/96210254/180620664-59ebe9b1-6726-45c7-95d0-af5d0e85221c.png)


#### HTTP Request Size Monitor
`HTTP Request Size Monitor` is implemented as follows:
  - **Metric**: packetbeat
  - **Threshold**: `http.request.bytes` is above `3500` for the last `1` minute
  - **Vulnerability Mitigated**: Large HTTP requests, HTTP Request Smuggling
  - **Reliability**: Medium Reliabilty, this alert will indicate any large HTTP requests. There will be False Positives as there will be viable and confirmable requests that alert the monitor but are still legitimate requests.
![image](https://user-images.githubusercontent.com/96210254/180620767-611dd637-d6d1-4a1b-9d85-fb72d40c6cac.png)


#### CPU Usage Monitor
`CPU Usage Monitor` is implemented as follows:
  - **Metric**: metricbeat
  - **Threshold**: Max of `system.process.cpu.total.pct` above `0.5` for the last `5` minutes
  - **Vulnerability Mitigated**: Denial of Service, CPU Overusage
  - **Reliability**: Low Reliability, this alert will indicate when the CPU goes above 50% utilization. There will be many False Positives as there are many reasons a machine may go above 50% CPU utilization for legitimate system processes and workload.  
![image](https://user-images.githubusercontent.com/96210254/180627474-724e66ed-a984-49dc-a437-9d53571d1cef.png)

### Suggestions for Going Further
The logs and alerts generated during the assessment suggest that this network is susceptible to several active threats, identified by the alerts above. In addition to watching for occurrences of such threats, the network should be hardened against them. The Blue Team suggests that IT implement the fixes below to protect the network:
- Open SSH Port (Port 22)
  - **Patch**: Remove SSH Port access or utilize a whitelist of known and approved ip addresses via Port 22
  - **Why It Works**: Using a whitelist will ensure that only ip addresses that have been permitted to use the port will be able to.
- Weak Passwords
  - **Patch**: Update password complexity policies and user training.
  - **Why It Works**: As bruteforce attempts get quicker a more complex and difficult password is needed. Updating the password complexity policies for the company and training the user on proper password usage will mitigate bruteforcing and guessing of passwords.
- Wordpress Enumeration Vulnerability
  - **Patch**: Utilize the "[WP Hardening](https://wordpress.org/plugins/wp-security-hardening/)" plugin in WordPress.
  - **Why It Works**: This is a plugin designed for WordPress to stop user enumeration.
- Wordpress and SQL Database Vulnerability
  - **Patch**: Encrypt the `wp-config.php` file.
  - **Why It Works**: As the `wp-config.php` contains plaintext sensitive data about the SQL database encrypting the file will hash the plaintext data and protect it from attackers.
- Wordpress XML-RPC Vulnerabilites
  - **Patch**: Utilize the "[WP Hardening](https://wordpress.org/plugins/wp-security-hardening/)" plugin in WordPress.
  - **Why It Works**: The same plugin as earlier, the plugin also comes built with a module to disable XMLRPC. If the plugin is not used a filter can disable XMLRPC as well. Use `add_filter( 'xmlrpc_enabled', '_return_false' );`
