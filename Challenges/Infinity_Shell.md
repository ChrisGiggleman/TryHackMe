TryHackMe: Infinity Shell Write-Up

Objective: Investigate a compromised web application and trace the attacker’s web shell activity.

Step 1: Access the Target Machine

Start the target machine on TryHackMe and find its IP address:

ip a


Example IP in our environment:

10.201.93.211


Confirm connectivity:

ping 10.201.93.211

Step 2: Identify the Web Application

Navigate to the web root on the target machine:

cd /var/www/html
ls


Discovered directory:

CMSsite-master/


Observation: This indicates a CMS-based website, a common target for attackers.

Step 3: Explore the CMS Directory

Move into the CMS project folder:

cd /var/www/html/CMSsite-master
ls


Output:

LICENSE  category.php  img  js  register.php
README.md  css  includes  php_cms.sql  search.php
admin  fonts  index.php  post.php


Observation: Common vulnerable directories include includes and img.

Step 4: Discover the Web Shell

Inside the img folder, we found a suspicious PHP file:

cd img
ls
cat images.php


Finding: images.php is the attacker’s entry point, likely used to execute commands via a web shell.

Step 5: Inspect Apache Logs for Web Shell Usage

Check Apache logs for requests to images.php:

cd /var/log/apache2/
cat other_vhosts_access.log.1 | grep -r 'images.php?'


Observation: HTTP GET requests interacting with the web shell often contained Base64-encoded commands in the query string.

Step 6: Extract and Decode Commands

Sample Base64 strings from the logs:

d2hvYW1pCg==
bHMK
ZWNobyAnVEhNe3N1cDNyXzM0c3lfdzNic2gzbGx9Jwo=
aWZjb25maWcK
Y2F0IC9ldGMvcGFzc3dkCg==
aWQK


Decode these strings using CyberChef
 or any Base64 decoder.

Example:

ZWNobyAnVEhNe3N1cDNyXzM0c3lfdzNic2gzbGx9Jwo=


Decodes to:

THM{sup3r_34sy_w3bsh3ll}

Step 7: Capture the Flag

Flag:

THM{sup3r_34sy_w3bsh3ll}

Summary

By inspecting the CMS folder, identifying suspicious files (images.php), and analyzing Apache logs for web shell activity, we successfully extracted the attacker’s commands and retrieved the flag.

Web Shell Attack Flow
┌───────────────┐
│   Attacker    │
└───────┬───────┘
        │ Exploit known CMS vulnerability
        │
        ▼
┌───────────────┐
│ Vulnerable    │
│ CMS Website   │
│ (/var/www/html│
│ CMSsite-master│
│ /img/images.php)
└───────┬───────┘
        │ Web shell deployed
        │ via images.php
        ▼
┌───────────────┐
│ Base64-encoded│
│ commands sent │
│ via HTTP GET  │
└───────┬───────┘
        │ Logged in Apache logs
        ▼
┌───────────────┐
│ Log Analysis  │
│ (other_vhosts │
│ _access.log.1)│
└───────┬───────┘
        │ Decode Base64
        ▼
┌───────────────┐
│ Retrieve Flag │
│ THM{sup3r_34sy│
│ _w3bsh3ll}    │
└───────────────┘
