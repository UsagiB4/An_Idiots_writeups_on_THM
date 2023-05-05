# MR ROBOT
This is one of my favourite shows and also one of my favourite room in **Tryhackme**.
This room is ment for beginner and intermediate level (*Experts* can try it too tho).
Let's jump into it.
<br>
___

## Scanning
We always start with scanning the network using ***nmap***.<br>
I personally us this command in most of my scan :::
```Bash
nmap -sC -sV -T4 -A -v -p- -oN namp.txt <ip>
```
Here's the full namap scan result **[nmap.txt](nmap.txt)**

Scanning the nmap we've found **3** common ports are open

1. port 22
    - Unordered sub-list.
    - version unknown 
2. port 80
    - http
    - Apache httpd
    - Supported Methods: GET HEAD POST OPTIONS
3. port 443
    - ssl/http 
    - Apache httpd
    - Supported Methods: GET HEAD POST OPTIONS
__

## Recon
Now if we look into the *IP*, we'll see that there is a weird terminal like page hosted.
This page doesn't take any normal commands like ```ls, whoami, ifconfig```. But we can use the command `help` to view available commands for us.

Available commands are:
```bash
prepare
fsociety
inform
question
wakeup
join
```
Let's check them

`prepare` command will show you a video.

`fsociety` command will also show you a video.

`inform` will show you a slide about the corrupted organizations.

`question` does the same and shows you some slides.

`wakeup` shows you a video.

`join` this one opens another terminal like page where you have to provide an email address of yours. You can provide a fake email an it will accept it.

On thing that cought my attention is, everytime I was using a command, it was reflecting on the **URL** as a directory. So I decided to brute fourse the directory with **Ffuf**. <br>
In this case I've used the wordlist **directory-list-2.3-medium.txt** from **dirbuster** wordlist.
```bash
ffuf -u https://10.10.214.177/FUZZ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -fc 301
```

###### Result
I've found some directories:
1. robots
2. sitemap
3. dashboard
4. wp-login
5. license
6. readme

I checked ***robots*** directory and found 2 hidden directories called **key-1-of-3.txt** and **fsocity.dic**.<br>
Check the text file and you will get the first key.

I downloaded the **fsocity.dic** file and turns out that it is a wordlist.

The site is made with _Wordpress_
I checked the wordpress version using **wappalizer** and found out that it's using version: `4.3.1`.<br>I searched for some exploit and found a [RCE exploit](https://www.exploit-db.com/exploits/50255) <br>
I tried but this didn't work. So I scanned it with **wpscan** tool.
```bash
wpscan --url IP --api-token API-KEY
```
