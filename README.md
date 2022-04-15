# CTF_skynet
this is Poc writeup CTF skynet from TryHackMe.com, link https://tryhackme.com/room/skynet

## tools
1. nmap (looking port that open)
2. dirb (brute listing directory)
3. sqlmap (sql injection database)
4. smbmap (mapping smb server)
5. smbclient (log in smb server)
6. burp (community edition)
7. nc or Ncat (listener reverse shell)
8. python3 (for http.server)
9. nano (text editor)
10. hydra (brute force by wordlist from login form)
11. gobuster (brute listing director)
12. searchsploit (search exploit that match with targeted system)

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
  
