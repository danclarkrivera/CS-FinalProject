# Red Team: Summary of Operations

## Table of Contents
- Exposed Services
- Critical Vulnerabilities
- Exploitation

### Exposed Services
Nmap scan results for each machine reveal the below services and OS details:

$ nmap -sV -O 192.168.1.*  

`Result for 192.168.1.1 (Windows Jumpbox)`  

![image](https://user-images.githubusercontent.com/96210254/180621298-eb05bf46-935c-4381-bfe5-3b89632e5b2a.png)
  
`Result for 192.168.1.100 (ELK - Ubuntu)`  

![image](https://user-images.githubusercontent.com/96210254/180621334-7011b68f-e6fd-4490-a848-21178acd4eba.png)

`Result for 192.168.1.105 (Capstone - Ubuntu)`  

![image](https://user-images.githubusercontent.com/96210254/180621383-b572a2f4-134f-4846-8967-bd8c6e80ab8e.png)

`Result for 192.168.1.110 (Target 1 - Debian)`  

![image](https://user-images.githubusercontent.com/96210254/180621409-16bb57bc-fde6-410e-96f9-64ace703b1a6.png)

`Result for 192.168.1.115 (Target 2 - Debian)`  

![image](https://user-images.githubusercontent.com/96210254/180621427-5379625f-e831-4e0f-8e09-1806e57fe3c5.png)

`Result for 192.168.1.90 (Kali - Debian [Kali])`  

![image](https://user-images.githubusercontent.com/96210254/180621456-56d75fa2-f1b3-42bf-a0e4-44f17d2fc170.png)

This scan identifies the services below as potential points of entry:
- Target 1 (192.168.1.110)
  - Port 22: SSH - OpenSSH 6.7p1 Debian 5+deb8u4 (protocol 2.0)
  - Port 80: HTTP - Apache httpd 2.5.10 ((Debian))
  - Port 111: rpcbind - 2-4 (RPC #100000)
  - Port 139: netbios-ssn - Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
  - Port 445: netbios-ssn - Samba smbd 3.X - 4.X (workgroup: WORKGROUP)

The following vulnerabilities were identified on each target:
- Target 1 (192.168.1.110)
  - [Wordpress XMLRPC GHOST Vulnerability Scanner](https://www.infosecmatter.com/metasploit-module-library/?mm=auxiliary/scanner/http/wordpress_ghost_scanner)
    - CVE-2015-0235
    - Metasploit Module: auxiliary/scanner/http/wordpress_ghost_scanner.rb
    - Target service / protocol: http, https
    - Target Network Port(s): 80, 443, 3000, 8000, 8008, 8080, 8443, 8880, 8888
    - This module is used to determine hosts vulnerable to the GHOST vulnerability via a call to the WordPress XMLRPC interface. If the target is vulnerable, the system will segfault and return a server error. On patched systems, a normal XMLRPC error is returned.
  - [Wordpress XMLRPC DoS](https://www.infosecmatter.com/metasploit-module-library/?mm=auxiliary/dos/http/wordpress_xmlrpc_dos)
    - CVE-2014-5266
    - Metasploit Module: auxiliary/dos/http/wordpress_xmlrpc_dos
    - Target service / protocol: http, https
    - Target network port(s): 80, 443, 3000, 8000, 8008, 8080, 8443, 8880, 8888
    - Wordpress XMLRPC parsing is vulnerable to a XML based denial of service. This vulnerability affects Wordpress 3.5 - 3.9.2 (3.8.4 and 3.7.4 are also patched).
  - [Wordpress XML-RPC Username/Password Login Scanner](https://www.infosecmatter.com/metasploit-module-library/?mm=auxiliary/scanner/http/wordpress_xmlrpc_login)
    - CVE-1999-0502
    - Metasploit Module: auxiliary/scanner/http/wordpress_xmlrpc_login
    - Target service / protocol: http, https
    - Target network port(s): 80, 443, 3000, 8000, 8008, 8080, 8443, 8880, 8888
    - This module attempts to authenticate against a Wordpress-site (via XMLRPC) using username and password combinations indicated by the USER_FILE, PASS_FILE, and USERPASS_FILE options.
  - [Wordpress Pingback Locator](https://www.infosecmatter.com/metasploit-module-library/?mm=auxiliary/scanner/http/wordpress_pingback_access)
    - CVE-2013-0235
    - Metasploit Module: auxiliary/scanner/http/wordpress_pingback_access
    - Target service / protocol: http, https
    - Target network port(s): 80, 443, 3000, 8000, 8008, 8080, 8443, 8880, 8888
    - This module will scan for wordpress sites with the Pingback API enabled. By interfacing with the API an attacker can cause the wordpress site to port scan an external target and return results. Refer to the wordpress_pingback_portscanner module. This issue was fixed in wordpress 3.5.1

![image](https://user-images.githubusercontent.com/96210254/180623258-5f6adc53-b111-4262-9978-0ef19f5ccc7f.png)
![image](https://user-images.githubusercontent.com/96210254/180623323-3925e048-9201-4b2c-a2b5-d23009f8e76c.png)

### Exploitation
The Red Team was able to penetrate `Target 1` and retrieve the following confidential data:
- Target 1
  - `flag1.txt`: b9bbcb33e11b80be759c4e844862482d
    - **Exploit Used**
      - Utilizing WPScan exploit information was found along with user information. Bruteforcing the user 'michael' worked as the user had a very insecure password. Hint given was the use the "most obious possible guess" Attempted password, then michael.
      - `$ wpscan --url http://192.168.1.110 -e u`
      - User: michael Password: michael
    ![image](https://user-images.githubusercontent.com/96210254/180625873-53d67fe6-e7e6-413c-8268-ad8cdabe3509.png)  
    ![image](https://user-images.githubusercontent.com/96210254/180625864-cc3c303a-ec95-4f55-8d38-227c673c04ab.png)  
      - From there using a grep command to find flag1  
    `$ grep -s -ire 'flag1'`
      - Found flag1
    ![image](https://user-images.githubusercontent.com/96210254/180625961-59cb5c8c-2bac-45a2-9b95-e19b0ede3dfe.png)


  - `flag2.txt`: fc3fd58dcdad9ab23faca6e9a36e581c
    - **Exploit Used**
      - Utilizing the same WPScan exploit to gain access to michael's account. Used the grep command to search for flag2.
      `$ grep -s -ire 'flag2'`
      - Found flag2
      - ![image](https://user-images.githubusercontent.com/96210254/180625942-5fd2b659-1e12-45cd-8fa0-c8494bea15a8.png)

  - `flag3.txt`: afc01ab56b50591e7dccf93122770cd2
    - **Exploit Used**
      - Given the hint to search for the `wp-config.php` file. I used a find command to locate the file.  
      `$ find . -name wp-config.php`
      - After using cat to view the file the password to the MySQL database was located.  
      `$ cat wp-config.php`  
      ![image](https://user-images.githubusercontent.com/96210254/180626069-f9ea2294-fdae-4339-abfe-67ed4855e4bb.png)
      - Started MySQL and used the found password `R@v3nSecurity`  
      ![image](https://user-images.githubusercontent.com/96210254/180626502-b09d3388-775a-42cd-a3d8-14f6cffae773.png)
      - Navigated through the databases, while searching I found Flag3 and Flag4 on the Wordpress Database in the wp_posts table. 
      `mysql> use Wordpress` and `mysql> select * from wp_posts`
      ![image](https://user-images.githubusercontent.com/96210254/180626521-8452a067-8108-4690-9a47-8c67e2798ffa.png)  
      ![image](https://user-images.githubusercontent.com/96210254/180626528-6735e365-0e69-440e-a68a-d65cfeb884e8.png)  
      ![image](https://user-images.githubusercontent.com/96210254/180626530-1e9ea5b9-bbbe-44ba-b26f-2abef54e94f5.png)  
      
  - `flag4.txt`: 715dea6c055b9fe3337544932f2941ce
    - **Exploit Used**
      - While within the MySQL database using the password found in the previous step. Navigated to the Wordpress database and wp_posts table to find Flag4, as noted above in the flag3.txt informoation.
      - Flag4.txt could also be found using a different exploit. Within the Wordpress database, the wp_users table contain the hashed passwords for both michael and steven.  
      ![image](https://user-images.githubusercontent.com/96210254/180626633-a09377ff-2b80-4c25-a4cb-a9a144818322.png)  
      - After putting these hashed passwords into a text document name `passwordhashes.txt` and ran it through John the Ripper.
      - John the Ripper was able to find that steven's password is `pink84`, michael's password `michael` was already found previously.  
      ![image](https://user-images.githubusercontent.com/96210254/180626690-cd8983dc-4700-4f21-a957-444650f5945c.png)  
      - Used Steven's credentials to ssh into Target 1 (192.168.1.110)  
      ![image](https://user-images.githubusercontent.com/96210254/180626701-1cad125e-cd44-42c6-aff8-99436b394713.png)  
      - Using `sudo -l` it was noted that steven's account had sudo privileges for python
      - Using the vulnerable SUID binaries a python privilege escalation attack was possible using `sudo python -c 'import os; os.system("/bin/bash")'` this gave root privileges to Target 1.  
      ![image](https://user-images.githubusercontent.com/96210254/180626744-4378bb24-3e09-4ff3-9a25-4a80977bca75.png)  
      - Finally, searching the home directory of root the flag4.txt file was found. 
      ![image](https://user-images.githubusercontent.com/96210254/180626769-46ed88a9-397d-407e-b471-36317c805389.png)
