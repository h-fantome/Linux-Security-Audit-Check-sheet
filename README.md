#Linux Security Audit Commands:

------------------------------------------------------------
--                 Remote Network Commands                --
------------------------------------------------------------

### Useful commands to be used over network for Linux system

### traceroute
```
traceroute 8.8.8.8
```
### traceroute using ICMP
```
traceroute -I 8.8.8.8
```
### nmap TCP syn scan, all TCP ports with scripts to all nmap output formats
```
nmap -sS -sV -sC -v -p- -oA all-tcp-127.0.0.1 127.0.0.1
nmap -sS -sV -A -v -p- -oA all-tcp-127.0.0.1 127.0.0.1
```
### nmap reverse DNS resolution
```
nmap -Pn -sn -R -oA dns-10.1.0.0_16 10.1.0.0/16
```
### update the nmap scripts
```
nmap --script-updatedb
```
### list nmap scripts
```
ls -la /usr/share/nmap/scripts/
```
### nmap brute force scripts
```
nmap -vvv --script http-brute --script-args userdb=users.txt,passdb=pass.txt -p <port> <host>
nmap --script vmauthd-brute -p <port> <host>
nmap --script ftp-brute -p <port> <host>
```
### help for script
```
nmap --script-help=ssl-heartbleed
```
### scan using script
```
nmap -sV –script=ssl-heartbleed.nse -p <port> <host>
```
### scan using set of scripts
```
nmap -sV --script=smb* -p <port> <host>
```
### nmap used as vulnerability scanner
```
mkdir /usr/share/nmap/scripts/vulscan
cd /usr/share/nmap/scripts/vulscan
git clone https://github.com/scipag/vulscan.git
nmap -sV --script=vulscan/vulscan.nse 127.0.0.1
```
### ncrack
```
ncrack -vv --user root <host>:<port>
```
### ncrack RDP
```
ncrack -vv -U username.txt -P password.txt <host>:3389
```
### ncrack SSH
```
ncrack -vv --user root <host>:22
```
### crack password used in encrypted zip archive
```
fcrackzip -b -l 1-4 -u  ./archive.zip
```
### hydra
### Bruteforce SSH
```
hydra -L <user-list.txt> -P <password-list.txt> ssh://<host>
```
### Bruteforce IP router over HTTP
```
hydra -V -l admin -P passwords.txt -t 36 -f -s 80 192.168.1.1 http-get /
hydra -V -l admin -P passwords.txt -t 36 -f -s 80 http-get://192.168.1.1:8080
```
### Bruteforce FTP
```
hydra -V -l admin -P passwords.txt -e ns -f -s 21 192.168.1.1 ftp
```
### Bruteforce RDP
```
hydra -t 1 -V -f -l username -P password.lst rdp://192.168.1.1
```
### skipfish
### basic scan
```
skipfish -o out_dir https://www.host.com
```
### using cookies to access authenticated pages
```
skipfish -o out_dir -I urls_to_scan -X urls_not_to_scan -C cookie1=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX -C cookie2=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX  https://www.host.com
```
### wfuzz
### URL brute forcing
```
wfuzz -c -z file,Directories_Common.wordlist --hc 404 http://<host>/FUZZ.php
```
### GET params brute forcing
```
wfuzz -c -z file,users.txt -z file,pass.txt --hc 404 http://<host>/index.php?user=FUZZ&pass=FUZ2Z
```
### sqlmap
```
sqlmap -u "http://host.com/vulnerable.php?param=12345"
sqlmap -u "http://host.com/vulnerable.php?param=12345" --dbms "Microsoft SQL Server" --sql-query="select name,master.sys.fn_sqlvarbasetostr(password_hash) from master.sys.sql_logins
sqlmap -u "http://host.com/vulnerable.php?param=12345" --dbms "Microsoft SQL Server" --dbs
sqlmap -u "http://host.com/vulnerable.php?param=12345" --dbms "Microsoft SQL Server" --dump -D database -T table
sqlmap -u "http://host.com/vulnerable.php?param=12345" --cookie "cookie1=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
sqlmap -r POST.txt -p field
```
### MySQL
```
mysql -u <username> -p --port <port> -h <host>
mysqldump -h <host> -u <username> -p -f --port <port> --events --routines --triggers --all-databases > MySQLData.sql
```
### Oracle
```
sqlplus "username/password@(DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=hostname)(PORT=port))(CONNECT_DATA=(SERVER=dedicated)(SERVICE_NAME=servicename)))"
```
### --------- sqlplus commands -----------
### improve sqlplus console output 
```
SET PAGESIZE 50000;
```
### list tablespaces
```
SELECT TABLESPACE_NAME FROM USER_TABLESPACES;
```
### list all tables 
```
SELECT owner, table_name FROM dba_tables;
```
### find table with given column name
```
SELECT owner, table_name, column_name FROM all_tab_columns WHERE UPPER(column_name) = UPPER('PASSWORD');
SELECT owner, table_name, column_name FROM all_tab_columns WHERE UPPER(column_name) LIKE '%PASS%';
```
### find table and count rows for given column name
```
SET SERVEROUTPUT ON
DECLARE
val NUMBER;
BEGIN
FOR I IN (SELECT DISTINCT owner, table_name FROM all_tab_columns WHERE UPPER(column_name) LIKE '%PASS%') LOOP
EXECUTE IMMEDIATE 'SELECT count(*) FROM ' || i.owner || '.' || i.table_name INTO val;
DBMS_OUTPUT.PUT_LINE(i.owner || '.' || i.table_name || ' ==> ' || val );
END LOOP;
END;
/
```
### find string in whole database for columns type NVARCHAR2
```
SET SERVEROUTPUT ON SIZE 100000

DECLARE
match_count INTEGER;
BEGIN
FOR t IN (SELECT owner, table_name, column_name
          FROM all_tab_columns
          WHERE owner <> 'SYS' and data_type LIKE 'NVARCHAR2') LOOP

EXECUTE IMMEDIATE
  'SELECT COUNT(*) FROM ' || t.owner || '.' || t.table_name ||
  ' WHERE '||t.column_name||' = :1'
  INTO match_count
  USING 'SEARCH_TEXT';

IF match_count > 0 THEN
  dbms_output.put_line( t.table_name ||' '||t.column_name||' '||match_count );
END IF;

END LOOP;

END;
/### --------------------------------------
```

### Postgres
```
psql -h 127.0.0.1 db_name username
```
### SNMP
### SNMPv1
```
snmpwalk -mALL -v1 -cpublic <host>
snmpwalk -mALL -v1 -cprivate <host>
snmpget -mALL -v1 -cpublic <host> sysName.0
```
### SNMPv2
```
snmpwalk -v2c -cprivate <host>:<port>
snmpget -v2c -cprivate -mALL <host> sysName.0 sysObjectID.0 ilomCtrlDateAndTime.0
snmpset -mALL -v2c -cprivate <host> ilomCtrlHttpEnabled.0 i 1
SUN-ILOM-CONTROL-MIB::ilomCtrlHttpEnabled.0 = INTEGER: true(1)
```
### SNMPv3
```
snmpwalk -v3  -l authPriv -u snmpadmin -a MD5 -A PaSSword  -x DES -X PRIvPassWord <host>:<port> system
```
### LDAP
```
ldapsearch -x -b "dc=company,dc=com" -s base -h <host>
LDAPTLS_REQCERT=never ldapsearch -x -D "uid=Name.Surname,OU=People,DC=Company,DC=com" -W -H ldaps://<host> -b "uid=Name.Surname,OU=People,DC=Company,DC=com" -s sub
ldapsearch -x -p 389 -h "127.0.0.1" -b "ou=people,dc=company,dc=com" -s sub "objectClass=*"
ldapsearch -x -p 1389 -h "127.0.0.1" -b "dc=company,dc=com" -s one "objectClass=*"
ldapmodify -a -h "127.0.0.1" -p 389 -D "cn=Directory Manager" -w 'password' -f modify.ldif
dn: ou=people,dc=company,dc=com
objectClass: top
objectClass: organizationalunit
ou: people
...

ldap delete -x -D "cn=Directory Manager" -w 'password' -p 1389 -h "127.0.0.1" "uid=identifier,ou=people,dc=company,dc=com"
```
### Redis
```
redis-cli dbsize
```
### get all keys from DB0
```
redis-cli -n 0 keys "*"
```
### NFS
```
showmount -e 127.0.0.1
mount -o ro 127.0.0.1:/ /mnt/nfs
```
### SIP
### svmap, send SIP OPTIONS
```
svmap -p5060,5061,5080-5090 10.0.0.1
```
### svcrack
```
svcrack -u100 -d dictionary.txt 10.0.0.1
```
### SMB
```
smbclient -L <host> -N
smbclient //<host>/<dir> -N
```
### SSHFS
### mount
```
sshfs user@<host>:/remote/path /mnt/tmp -C -p 22
```
### unmount
```
fusermount -u /mnt/tmp
```
### redir
```
redir --laddr=<listen_address> --lport=<listen_port> --caddr=<connect_address> --cport=<connect_port>
```
### sending HTTP post request
```
curl --data "param1=value1&param2=value2" https://host.com/index.php
```
### sending SOAP request by nc
###!/bin/sh
```
HOST=host.com
PORT=8888

nc $HOST $PORT << __EOF__
POST /services/ HTTP/1.1
Host: host.com:8888
Content-Type: text/xml;charset=UTF-8
SOAPAction: ""

<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" xmlns:web="http://host.com/">
   <soapenv:Header/>
   <soapenv:Body>
      <web:soapRequest>
      </web:soapRequest>
   </soapenv:Body>
</soapenv:Envelope>
__EOF__
```
### sending SOAP request by curl
```
$ proxychains curl --header "Content-Type: text/xml;charset=UTF-8" --header "SOAPAction:" --data @data.xml http://127.0.0.1:8888/
$ cat data.xml 
<?xml version="1.0" encoding="UTF-8"?>
<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" xmlns:web="http://host.com/">
   <soapenv:Header/>
   <soapenv:Body>
      <web:soapRequest>
      </web:soapRequest>
   </soapenv:Body>
</soapenv:Envelope>
```
### send payload using nping, spoof source IP
```
sudo nping -c 1 --data hexstring --udp -p dest_port -S source_ip -g source_port dest_ip
```
### Bash like SQL group by
```
cat test.txt | sort | uniq -c | sort -n
```
### Wireshark over SSH
```
ssh root@192.168.1.1 "sudo tcpdump -U -s0 -i lo -w - 'not port 22'" | wireshark -k -i -
wireshark -k -i <(ssh root@192.168.1.1 tcpdump -U -s0 -i any -w - not port 22)
```
### HEX to PCAP
```
xxd -r -p test.hex | od -Ax -tx1 | text2pcap - test.pcap
```
### Parse JSON
```
grep -Po '"field" : .*?[^\\]",' test.json
```
### tshark to Elasticsearch output
```
./tshark -i any -f tcp -T ek -e "ip.addr" -e "tcp.port"
```
### tshark display filter to files
```
tshark -r input.pcap -Y "ip.src == 10.1.1.1" -w output.pcap -F pcap
```
### john the ripper over GPU, OpenCL formats, start session
```
john --session=session_name --format=opencl ~/hash.txt
```
### john list the GPU OpenCL formats
```
john --list=formats --format=opencl
```
### john the ripper, continue session
```
john --restore=session_name
```
### john the ripper, show the cracked passwords
```
john ~/hash.txt --show
```
### dynamic formats
### edit john/JohnTheRipper/run/dynamic.conf
```
john --fork=16 --session=session_dynamic --format=dynamic_xxxx hash.txt
```
------------------------------------------------------------
--                     Local Commands                     --
------------------------------------------------------------

### Useful commands running locally on the Linux system
### To quickly analyze the system and possibly help to escalate privileges.

### Before connecting to remote system, enable logging record the interactive session. Only after perform e.g. ssh connect.
```
script <filename>
```
### login
```
ssh username@hostname
```
### check current shell
```
echo $0
```
### check current user
```
whoami
```
### current folder
```
pwd
```
### list history of the user
```
history
```
### check system
```
uname -a
```
### check uptime
```
uptime
```
### check system variables
```
export
```
### processes
```
ps -ef
ps auxf
ps auxfww
```
### find in files
```
find . -name "*.java" -type f -exec fgrep -iHn "textToFind" {} \;
find . -regex ".*\.\(c\|java\)" -type f -exec fgrep -iHn "textToFind" {} \;
find / -maxdepth 4 -name *.conf -type f -exec grep -Hn "textToFind" {} \; 2>/dev/null
```
### SUID files owned by root
```
find / -uid 0 -perm -4000 -type f 2>/dev/null
```
### SUID files owned by root and world readable
```
find / -uid 0 -perm -u=s,o=r -type f -exec ls -la {} \; 2> /dev/null
```
### SUID files
```
find / -perm -4000 -type f 2>/dev/null
```
### world writable directories
```
find / -perm -2 -type d 2>/dev/null
```
### find passwords in files and ignore errors and filter out the proc and other folders
```
find . ! -path "*/proc/*" -type f -name "*" -exec fgrep -iHn password {} \;
find . -type f \( -iname \*.conf -o -iname \*.cfg -o -iname \*.xml -o -iname \*.ini -o -iname \*.json -o -iname \*.sh -o -iname \*.pl -o -iname \*.py \) -exec fgrep -iHn password {} \; 2> /dev/null
```
### find using several patterns read from file (patterns are delimited by new line)
```
find . -type f -exec grep -iHFf patterns.txt {} \;
```
### find password keyword in small files
```
find . -type f -size -512k -exec fgrep -iHn password {} \;
```
### reverse java jar files and find passwords there
```
find . -name "*.jar" -type f -exec ~/jd-cli/jd-cli -oc -l -n -st {} \; | egrep -i -e "Location:" -e "password" | uniq
```
### check open ports and services listening
```
netstat -anp
```
### check defined hosts
```
cat /etc/hosts
```
### check local IP addresses and interfaces
```
ifconfig -a
```
### check route
```
route -v
```
### check filesystem
```
df
```
### check sudo privileges
```
sudo -l
```
### check crontab
```
crontab -l
```
### check inittab
```
cat /etc/inittab
```
### try to sniff traffic
```
tcpdump
tcpdump -s0 not port 22 -w trace.pcap
```
### check known hosts
```
cat ~/.ssh/known_hosts
```
### try access mails
```
head /var/mail/root
```
### list groups, users
```
cat /etc/group
cat /etc/passwd
```
### with root privileges
```
cat /etc/shadow
```
### check shared memory
```
ipcs -mp
```
### logout
```
logout
```
### close script session
```
Ctrl + D
```

------------------------------------------------------------
--                SSH tunneling and chaining              --
------------------------------------------------------------

### Useful to jump or tunnel traffic over several machines

### SSH proxy command
```
ssh config (~/.ssh/config)
Host _first_server
Hostname XXX.XXX.XXX.XX 
Port 22 
User root 
Host _second_server 
Hostname 127.0.0.1 
Port 22 
User root ### password is  "XXXXXXX" 
ProxyCommand ssh -v -W 127.0.0.1:22 _first_server
Host _third_server
Hostname XXX.XXX.XXX.XXX 
Port 22 
User XXXXX 
ProxyCommand ssh _second_server -W %h:%p
Host _host_over_sshpass and ssh key
    Hostname XXX.XXX.XXX.XXX
    IdentityFile id_rsa
    User XXXXX
    ProxyCommand sshpass -pXXXXXXX ssh -Fssh_config _host_previous_in_chain -W %h:%p
```
### connect
```
ssh root@_second_server 
```
### Proxychains
### run ssh on background and without executing commands
```
ssh -f -N -D 9050 user@host
proxychains telnet hosts
```
### using local SSH tunneling to access web server on remote server
```
sudo ssh -F ~/.ssh/ssh_config _host_definition -L 127.0.0.1:8080:127.0.0.1:8080 -L 127.0.0.1:8443:127.0.0.1:8443
```
### torsocks
```
torsocks sshpass -p '********' ssh -C admin@XXX.XXX.XXX.XXX "sudo tcpdump -i any -U -s0 -w - 'not port 22'" | wireshark -k -i -
```
### allow internet access on remote server over client SSH (reverse SSH tunnel)
```
sudo apt install tinyproxy
sudo systemctl disable tinyproxy
sudo systemctl stop tinyproxy
sudo vi /etc/tinyproxy/tinyproxy.conf
```
### configure listen port (e.g. Port 3128)
```
sudo systemctl start tinyproxy
ssh -R 3128:127.0.0.1:3128 host
$ export http_proxy=http://127.0.0.1:3128
$ export https_proxy=http://127.0.0.1:3128
```

------------------------------------------------------------
--                       Decompilers                      --
------------------------------------------------------------

### jd-gui (Java decompiler)
```
cd ~/Decompilers/Java/
java -jar jd-gui-1.6.2.jar
```
### ghidra (C decompiler)
```
~/Decompilers/ghidra_9.0.4/ghidraRun
```
### ffdec (Flash decompiler)
```
sudo update-alternatives --config java
There are 2 choices for the alternative java (providing /usr/bin/java).

  Selection    Path                                 Priority Status
------------------------------------------------------------
  0            /usr/lib/jvm/java-11-openjdk-amd64/bin/java   1111 auto mode
* 1            /usr/lib/jvm/java-10-openjdk-amd64/bin/java   1 manual mode
  2            /usr/lib/jvm/java-11-openjdk-amd64/bin/java   1111 manual mode

Press <enter> to keep the current choice[*], or type selection number:

~/Decompilers/Flash/ffdec_11.2.0_nightly1721
./ffdec.sh

```
------------------------------------------------------------
--                       Auxiliary                        --
------------------------------------------------------------

### compress & copy over ssh by rsync
```
rsync --append-verify -avhzpP -e ssh user@host:/source/* dest
```
### copy locally by rsync, full sync, delete removed files
```
rsync --append-verify -avhepP --delete /source/* dest
```
### Grep in csv
```
cat some.csv | awk -F, '$3 == value {print}'
```
### Find files in exact date
```
find . -type f -newermt "YYYY-MM-D1" ! -newermt "YYYY-MM-D2"
```
