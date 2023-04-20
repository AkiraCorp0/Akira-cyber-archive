

## CTF THM TEAM 

First step im adding target ip to the /etc/hosts at target.thm

![](https://i.imgur.com/7rRHDuG.png)


## PORT ENUM

>$ nmap -sV -p- target.thm

![](https://i.imgur.com/DsMh1Ts.png)


seem like we have a ftp server, ssh server and a webserver

## WEBSITE 

The web site have a default Apache2 ubuntu page

## website enum

I gona brute-force page with gobuster to enum web directory 

> $ gobuster dir -u http://target.thm -w wordlists/dirb/common.txt 

![](https://i.imgur.com/Dt59UV7.png)

Nothing doesn't look really useful.

I find this in the web site source code.  

![](https://i.imgur.com/lJc5p7c.png)

So i added team.thm in my /etc/hosts

![](https://i.imgur.com/QXrt0Rm.png)

We got  a website ! http://team.thm 

![](https://i.imgur.com/FfsPuZc.png)

I try to enum page with gobuster

> $ gobuster dir -u team.thm -w wordlists/dirb/common.txt 

![](https://i.imgur.com/kZAilar.png)

I dont find usefull thing
I try to enum subdomain with ffuf

>$ ffuf -w wordlists/Subdomain.txt -H "host: FUZZ.team.thm" -u http://team.thm      

![](https://i.imgur.com/yNjpTnG.png)

Im filtering all http response with size 11366 
> $ ffuf -w wordlists/Subdomain.txt -H "host: FUZZ.team.thm" -u http://team.thm -fs 11366 

![](https://i.imgur.com/UK0YPWi.png)

Look like we find the sebdomain DEV 
Im adding dev.team.thm to my /etc/hosts

![](https://i.imgur.com/0UIMsQT.png)


I find a lfi on http://dev.team.thm/script.php?page=


![](https://i.imgur.com/hOcmE34.png)

Seem we have 2 user :
gyles
dale

I gona fuzz usefull lfi payload with burp intruder

![](https://i.imgur.com/cv7h5Ln.png)

![](https://i.imgur.com/MzrOpt5.png)

I found dale ssh private key 

![](https://i.imgur.com/BoeQ798.png)

I remove all # 
and send it to ssh2john 

![](https://i.imgur.com/pVkgo26.png)
look like the key dont have a pass phrase 

I perm 600 to the key 
and try to log in with the key 

![](https://i.imgur.com/Vbpd8bt.png)

Bingo we are in ! 

## PRIVESC

I take the flag in the dale home

I do a sudo -l to list wich commande i can use with other user 

![](https://i.imgur.com/c9nVZwc.png)

I check what admin_checks do 

![](https://i.imgur.com/ptkcXwa.png)

i can try to write /bin/bash in place of the $error input

![](https://i.imgur.com/0yKzlGo.png)

We are gyles ! 

i spawn a tty shell 
>$ python3 -c 'import pty;pty.spawn("/bin/bash")'

I look in which group gyles are

![](https://i.imgur.com/2zNAHWN.png)

Searching wich i can get in the group admin 

>$ find / -group admin 2>/dev/null

![](https://i.imgur.com/FDa7jsC.png)



It seem we have a a script in /opt/admin_stuff 

The script is own by root 

Let's look the script 

![](https://i.imgur.com/cbLgKwX.png)

The script is set in a cronjob :) 

We are autorized to write in main_backup.sh

lets make a reverse tcp shell ! 

![](https://i.imgur.com/ZMCx0PS.png)

I launch a netcat listner 

>nc -nlvp 1337

I wait the cron 

![](https://i.imgur.com/xJnTXyG.png)

### PWNED


