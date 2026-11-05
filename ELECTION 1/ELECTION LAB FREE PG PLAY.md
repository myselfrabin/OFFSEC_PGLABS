**Leverage enumeration and web enumeration techniques to uncover system weaknesses. Apply privilege escalation strategies to gain elevated access. This lab is designed to harness your skills in vulnerability discovery and exploitation.**
## Summary

In this lab, you will exploit a web application to discover SSH credentials and escalate privileges by leveraging a vulnerable SUID binary to gain root access. This exercise simulates a real-world scenario involving directory enumeration, SSH exploitation, and privilege escalation using an available exploit.

Learning Objectives

**After completion of this lab, learners will be able to:**

- Discover open services and perform enumeration using Nmap.
- Use Gobuster to identify hidden directories on the web application.
- Locate credentials in the logs directory and use them to log in via SSH.
- Identify SUID binaries on the target and leverage an exploit for privilege escalation.
- Transfer, compile, and execute an exploit to gain root access.

## STEPS HOW WE SOLVE THIS LAB: 

## STEP- 1st: 
- Connect with the lab openvpn
```bash
sudo openvpn <filename.ovpn>
```
- Check  it's ip and connect with the lab
- When seeing the lab ip I am seeing a default apache page.
- Let's add that ip with name in the host file

### USING RUSTSCAN
- Using the rustscan I can found out that the **port 22 and port 80** is being opened.
![](Attachments/Pasted%20image%2020261105125640.png)

## FUZZING WITH FFUF
- I fuzz with the ffuf and got the key infos
![](Attachments/Pasted%20image%2020261105130308.png)
- robots.txt shows me some wordlist infos they are:
![](Attachments/Pasted%20image%2020261105130348.png)
- Now I can check for each word which one is giving us the result.
- So the path: **/election/** gives me a result every other were 404 not found
![](Attachments/Pasted%20image%2020261105130847.png)

- So what I got after /election is /admin that's working there
- And after /admin/'found some pages here' but Let's fuzz for more hopefully If we found some more then it's benefical for us.


## FOUND LOG FILE AND INSIDE THERE SYSTEM LOG
![](Attachments/Pasted%20image%2020261105131853.png)

- I've got a link of `system.log` when viewing that we got a password for user.
![](Attachments/Pasted%20image%2020261105132015.png)
- Got a password for the user love creds is: **love:  P@\$\$w0rd\@123**



## PORT 22 SSH IS BEING OPENED
- So I got the usrname and password for love I can try loggedin through it ssh
```bash
ssh love@election1.offsec
The authenticity of host 'election1.offsec (192.168.194.211)' can't be established.
ED25519 key fingerprint is: SHA256:z1Xg/pSBrK8rLIMLyeb0L7CS1YL4g7BgCK95moiAYhQ
This host key is known by the following other names/addresses:
    ~/.ssh/known_hosts:3: [hashed name]
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added 'election1.offsec' (ED25519) to the list of known hosts.
** WARNING: connection is not using a post-quantum key exchange algorithm.
** This session may be vulnerable to "store now, decrypt later" attacks.
** The server may need to be upgraded. See https://openssh.com/pq.html
love@election1.offsec's password: 
Welcome to Ubuntu 18.04.4 LTS (GNU/Linux 5.4.0-120-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage


 * Canonical Livepatch is available for installation.
   - Reduce system reboots and improve kernel security. Activate at:
     https://ubuntu.com/livepatch

471 packages can be updated.
358 updates are security updates.

Your Hardware Enablement Stack (HWE) is supported until April 2023.
Last login: Thu Apr  9 23:19:28 2020 from 192.168.1.5
love@election:~$ ls

```
- I got a local.txt file there reading it gives me the first flag.

![](Attachments/Pasted%20image%2020261105132950.png)



- Found a know vulnerability based on **Serv-U** file protocol.
![](Attachments/Pasted%20image%2020261105133304.png)

- Let's download that C file and use it in the love user **/tmp** directory.
- Most exploits target the **/tmp** directory because it is **world-writable** by default, allowing any user or compromised process to create files without restriction.

## NOW SEND THAT C FILE INTO THE USER /TMP DIRECTORY FROM OUR OWN.

![](Attachments/Pasted%20image%2020261105135304.png)

- Now I have the C file in my user love user /tmp directory.
![](Attachments/Pasted%20image%2020261105135441.png)
- Got a shell as a user root.

## GOT ANOTHER ROOT FLAG IN /ROOT/proff.txt
![](Attachments/Pasted%20image%2020261105135641.png)



Finally we solved this lab.