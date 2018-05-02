
Watchog is an integration of open source security tools aimed to provide a holistic security view for a given domain/IP. The way Watchdog is built it can be used by product security teams, red teams and also by bug bounty hunters to get a 360 degree view of any Internet property it scans. Given a list of domains/IP it has the capability to perform a network scan, feed the output to open source web app scanners like Google's skip-fish and wapiti, perform tech stack analysis and determine if the stack has any known CVE’s.
Watchdog is designed considering the use case where it is necessary to know all open services and its corresponding technologies used by the endpoints you own exposed over the Internet. As a company grows it’s foot-prints on the Internet for a product security team it becomes really difficult to maintain an inventory of all the services and technologies which it exposes over the Internet, and on an event of a zero-day outbreak on a particular protocol or a third party product it becomes immensely crucial to know which all endpoints might be affected. WatchDog has the ability to scan all endpoints and perform technology version analysis on the services it detects and also map this information with it's rich [CVE](https://github.com/cve-search/cve-search) database which it maintains and updates locally.

Eg: Consider a test domain www.scanthis.com 
Watchdog will perform following task on this domain:
a. Scan the domain to find visible open ports. 
{e.g. output}
* 80  [Apache httpd 2.2.22 ((Debian))]
* 443 [Apache httpd 2.2.22 ((Debian))]
* 22  [OpenSSH 5.8p1_hpn13v10 (FreeBSD 20110102; protocol 2.0)]
* 21  [ProFTPD 1.3.3e]
* 993 [Plesk Courier imapd]

b. Perform tech-stack fingerprinting and identify all front-end and service level technologies running.
* jquery [1.2]
* twitter bootstrap [2.3]
* font awesome [**]
* google analytics [**]
* piwik []

c. Map the tech-stack versions with known vulnerabilities found in the master CVE database.{e.g. jquery 1.2 version has multiple CVE’s }

d. If step 1 detects any http services running (80/443) it will go ahead and perform a web application security scanning with wapiti and Skipfish.

f. Once the scan is complete the data will get populated on Watchdog’s UI which can be found at http://localhost/index.php

Installing watchdog
* prerequisites & softwares
* Ubuntu 16.04+
* Apache2 + PHP5.6 + Mongo
* PyV8
* clone watchdog repository
$ git clone https://github.fkinternal.com/flipkart-incubator/watchdog.git
$ cd watchdog
* Install PyV8
    * incase, if you are facing any issues in installing, follow below steps (workaround, works for Ubuntu 16+)
* $ export LC_ALL=C
$ cd tmp
$ pip install -e git://github.com/brokenseal/PyV8-OS-X#egg=pyv8
$ git clone https://github.com/emmetio/pyv8-binaries.git
$ unzip pyv8-binaries/pyv8-linux64.zip (or unzip appropriate zip file based on kernal version)
$ mv *PyV8* src/pyv8/pyv8/.
* Update your subdomains.txt file with your target subdomains
    * ex:
             scanme.nmap.org
            testphp.vulnweb.com
* Run installation script
$ sudo chmod +x install.sh
$ sudo ./install.sh
* During installation, installation script prompts for web root directory. Default directory /var/www/html will be taken automatically if not provided explicitly with-in 10 secs
Scanning with Watchdog

* watchdog can be run by using following command
$ sudo python run.py

root@projectWatchdog:/watchdog# python run.py
usage: run.py [-h] [-c] [-iA INVENTORY_APPEND] [-iR INVENTORY_REPLACE]
              [-u {install,map,update}] [-s]

optional arguments:
  -h, --help            show this help message and exit
  -c, --config          to configure db structure
  -iA INVENTORY_APPEND, --inventory-append INVENTORY_APPEND
                        to append target to IP Inventory
  -iR INVENTORY_REPLACE, --inventory-replace INVENTORY_REPLACE
                        to replace targets in IP Inventory
  -u {install,map,update}, --updateCVEs {install,map,update}
                        to configure or update CVE database
  -s, --start           to start scaning engine

Configuring CVE-DB

* install cve db using below command (Required to run atleast once)
$sudo python run.py -u install
* map cves with cpes using below command (Required to run at-least once and the first run generally takes around 30~45 mins for the entire db to get populated. Recommended time minimum `30mins`)
$sudo python run.py -u map
* you can update the DB by using below command (optional/ can run this once a month)
$sudo python run.py -u update
Need to add new domains for scanning?

* update subdomains.txt file with new (sub)domains and run below commands
$ sudo python run.py -iA subdomains.txt (for appending targets to existing inventory)
$ sudo python run.py -iR subdomains.txt (for replacing targets in existing inventory)
* Frontend can be accessed from http://localhost/index.php (or replace localhost with your web server address)

Lead Developer

* Mohan Krushna K (mohan.kk@flipkart.com)

Project Lead

* Prajal Kulkarni (https://twitter.com/prajalkulkarni

Project Team:

* [Shubham Bansal] (https://github.com/shubham643)
* [Prabhav Adhikari] (https://github.com/prabhavad)
* [Rohit Agrawal] (https://github.com/iirohit)

Credits

* Flipkart Security Team
* NMAP
* Wapiti
* Skipfish
* Phantalyser
* CVE Search