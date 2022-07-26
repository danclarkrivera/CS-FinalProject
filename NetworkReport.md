# Network Analysis

## Table of Contents
- Time Thieves
- Vulnerable Windows Machines
- Illegal Downloads

## Time Thieves
At least two users on the network have been wasting time on YouTube. Usually, IT wouldn't pay much mind to this behavior, but it seems these people have created their own web server on the corporate network. So far, Security knows the following about these time thieves:  
- They have set up an Active Directory network.  
- They are constantly watching videos on YouTube.  
- Their IP addresses are somewhere in the range 10.6.12.0/24.  
  
You must inspect your traffic capture to answer the following questions:
1. What is the domain name of the users' custom site?
  - There is the domain frank-n-ted.com
  - Domain Name IP Address: `10.6.12.12`, this would match the estimated IP Range given  
![image](https://user-images.githubusercontent.com/96210254/180910704-710b98ee-0b9e-4555-9163-42280e95e991.png)
 
2. What is the IP address of the Domain Controller (DC) of the AD network?
  - By utilizing the "samr" filter in WireShark the Domain Controller was found to be the `DESKTOP-86J4BX` machine
  - This machine is located at: `10.6.12.157`  
  ![image](https://user-images.githubusercontent.com/96210254/180912199-02f75108-e9db-4c20-afd8-dfd7b85f964b.png)
 
3. What is the name of the malware downloaded to the 10.6.12.203 machine? Once you have found the file, export it to your Kali machine's desktop.  
  - Using a filter for the IP address 10.6.12.203 and filtering for HTTP GET requests it was found that LAPTOP-5WKHX9YG had requested from `205.185.125.104`
  - The requested file is `june11.dll`  
  ![image](https://user-images.githubusercontent.com/96210254/180912911-44719a9b-e642-4d7d-9376-e0f33d4bcf05.png)

5. Upload the file to VirusTotal.com. What kind of malware is this classified as?  
  - Exported the `june11.dll` file to Kali VM and scanned the file using VirusTotal.com
  - VirusTotal.com indicated that it is a malicious and was detected by 54 of 70 vendors
  - It is a Trojan virus
  ![image](https://user-images.githubusercontent.com/96210254/180915492-e41c215d-126b-490c-80bb-24ec915baa88.png)
 
## Vulnerable Windows Machines
The Security team received reports of an infected Windows host on the network. They know the following:  
- Machines in the network live in the range 172.16.4.0/24.
- The domain mind-hammer.net is associated with the infected computer.
- The DC for this network lives at 172.16.4.4 and is named Mind-Hammer-DC.
- The network has standard gateway and broadcast addresses.  
Inspect your traffic to answer the following questions:
1. Find the following information about the infected Windows machine:
    - Host name: Rotterdam-PC
    - IP address: 172.16.4.205
    - MAC address: 00:59:07:b0:63:a4
    - Using similar filters from the Time Thieves section. A filter was setup for the IP Address range `172.16.4.0/24` and the CName from kerberos.  
    ![image](https://user-images.githubusercontent.com/96210254/180916248-c3ea059b-2e21-4212-9c55-bcbf475f0a43.png)
    ![image](https://user-images.githubusercontent.com/96210254/180916569-65af3ff0-5eb4-4399-aa81-6077efc68ac5.png)

2. What is the username of the Windows user whose computer is infected?
  - matthijs.devries
  ![image](https://user-images.githubusercontent.com/96210254/180917226-a4e96f03-250e-48fb-b185-88a92c1af9bd.png)
 
3. What are the IP addresses used in the actual infection traffic?
  - To find the infection traffic, I choose to look for the largest conversation being had on the Rotterdam-PC.
  - Used a filter to view only items associated with `172.16.4.205` or Rotterdam-PC. By then using the Conversations stats tool there was a huge amount of Bytes between Rotterdam-PC and `185.243.115.83` the traffic was done on Port 80 (HTTP)  
  ![image](https://user-images.githubusercontent.com/96210254/180917962-9f63de77-954c-4b23-a352-b57b940d8d95.png)  
  - There is also a domain name attached to the IP Address, `b5689023.green.mattingsolutions.co`
  ![image](https://user-images.githubusercontent.com/96210254/180918064-c6f37a9e-82f5-4b2a-8689-afd8aa773aef.png)  
 
As a bonus, retrieve the desktop background of the Windows host.
  - Similar to finding the `june11.dll` file earlier utilizintg the WireShark Export tool and filtering for the Domain Name `b5689023.green.mattingsolutions.co` to search the files.
  - The search results indicate 2 much larger files when compared to the rest. These are likely the desktop backgrounds.
  ![image](https://user-images.githubusercontent.com/96210254/180918697-bced09cb-4c2d-4e2e-9b03-0f8f9df69e13.png)  
  - After exporting the files `empty.gif?ss&ss1img` and `empty.gif?ss&ss2img` they are both an image of a seagull.
  ![image](https://user-images.githubusercontent.com/96210254/180918612-3e14ee24-229c-4623-81d9-fbf4f770eeed.png)

## Illegal Downloads
IT was informed that some users are torrenting on the network. The Security team does not forbid the use of torrents for legitimate purposes, such as downloading operating systems. However, they have a strict policy against copyright infringement.  
IT shared the following about the torrent activity:

- The machines using torrents live in the range 10.0.0.0/24 and are clients of an AD domain.
- The DC of this domain lives at 10.0.0.2 and is named DogOfTheYear-DC.
- The DC is associated with the domain dogoftheyear.net.

Your task is to isolate torrent traffic and answer the following questions in your Network Report:

1. Find the following information about the machine with IP address 10.0.0.201:
    - MAC address: 00:16:17:18:66:c8
    - Windows username: elmer.blanco
    - OS version: Windows NT 10.0; Win64; x64
    - Using the same filter pattern a filter was made to for the `10.0.0.201` IP and to find the Kerberos CName. Able to find that this the `BLANCO-DESKTOP` machine.
    ![image](https://user-images.githubusercontent.com/96210254/180919770-c31f9c3a-2d9b-4230-9115-7fb98b228c89.png)  
    - Further searching found that the user's name is Elmer Blanco
    ![image](https://user-images.githubusercontent.com/96210254/180920630-97d9d7cc-b47f-4d01-bebd-b49d6e14d065.png)  
    - Reviewing the HTTP requests at `10.0.0.201`, the Operating System information was found
    ![image](https://user-images.githubusercontent.com/96210254/180920716-b6dc0f3e-4bad-4623-a2a3-76cdbc7b48cf.png)  

2. Which torrent file did the user download?
    - By using the previous filter, one that is set up to view any http requests from `10.0.0.201`.
    - After reviewing the entries there was a download.php file that was done for the file `Betty_Boop_Rhythm_on_the_Reservation.avi.torrent` from the host `www.publicdomaintorrents.com`
    ![image](https://user-images.githubusercontent.com/96210254/180921463-862ebce6-0dc7-4063-a524-76df521ed593.png)
