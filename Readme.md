# IAAS Debian

Configure a clean Debian server.
---

This repository holds the configuration procedure I used to setup a web server in one of my [Virtual Machines](https://en.wikipedia.org/wiki/Virtual_machine) ([IAAS service model](https://en.wikipedia.org/wiki/Cloud_computing#Infrastructure_as_a_service_.28IaaS.29)). Some techniques described bellow can be used on other [VM](https://en.wikipedia.org/wiki/Virtual_machine) running the same distro too, altough the initial state may differ. In my case I received the VM with a clean Debian Server installed on top, among with a plain text password and username to make the first sign in -written in a piece of paper and given to me by hand-.

I tried to use the best practices, but that does not guarantee my configuration settings are suitable for every use. I took the time to document, as much as possible, each step. A basic understanding of Linux systems is good to have, but not required. Moreover, I go beyond than simple system configuration and analyze installation procedures of software packages that you might no need. 

The system is used to connect to virtual machine is Ubuntu 16.04 (LTS).

## Table Of Contents

- [Assumptions](#assumptions)
- [First Sign In](#first-sign-in)

## Assumptions

I don't want to expose my server credentials so lets make the following assumptions regarding to our VM (use the credentials provided to you):

- **USER**: user
- **PASSWORD**: secretPass0022
- **IP**: 88.23.54.32
- **DOMAIN**: example.com
- **vmNAME**: debian-jessie-server

The **USER** and **PASSWORD** info were given just for the first sign in. We'll create our own user later in the procedure and will delete the default credentials. 

You may not have registered a domain name for your server. That's totaly fine, as we only need the IP address to do our work, but if you already have one associated with your IP then even better. I won't cover how to get a domain name. In my case I asked my VM provider to register one.

## First Sign In

Lets open a new terminal (Ctrl+T) and type the following command (do not press enter yet):

```
ssh user@88.23.54.32
```

We make use of ***ssh*** command to connect to our server. *[Secure Shell (SSH)](https://en.wikipedia.org/wiki/Secure_Shell)* is a UNIX-based command and cryptographic network protocol that allow us to remote login and execute commands on a computer securely. 

**Note**: You can also replace the **IP** parameter in the command with the **DOMAIN** name.

We can press enter to execute the command. The first time it will promt a message. 

```
The authenticity of host 'example.com (88.23.54.32)' can't be established.
ECDSA key fingerprint is 12:a7:28:01:25:aa:73:33:d6:20:67:91:52:55:3e:ff.
Are you sure you want to continue connecting (yes/no)?
```

This is a really important message. You've been asked to validate the authenticity of your server. To be said, that means that you have to ask your VM provider to send you the **key fingerprint** of your vm. Then you just check if all letters are match with the ones you see in your screen. If this is not the case, contact your VM provider and ask for additional help.

Type `yes`. 

```
Password:
```

You will now be prompted to enter your **PASSWORD**. Please note that you will NOT see your cursor moving, or any characters, when typing your password. This is a standard Terminal security feature. If you typed the password correctly, you should established a new ssh session successfully.

```
[user@debian-jessie-server ~]# 
```

Congatulations! You just made your first sign in. You can type `exit` any time to disconnect from the server. 
