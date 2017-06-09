# IAAS Debian

Configure a clean Debian server.
---

This repository holds the configuration procedure I used to setup a web server in one of my [Virtual Machines](https://en.wikipedia.org/wiki/Virtual_machine) ([IAAS service model](https://en.wikipedia.org/wiki/Cloud_computing#Infrastructure_as_a_service_.28IaaS.29)). Some techniques described bellow can be used on other [VM](https://en.wikipedia.org/wiki/Virtual_machine) running the same distro too, although the initial state may differ. In my case I received the VM with a clean Debian Server installed on top, among with a plain text password and username to make the first sign in -written in a piece of paper and given to me by hand-.

I tried to use the best practices, but that does not guarantee my configuration settings are suitable for every use. I took the time to document, as much as possible, each step. A basic understanding of Linux systems is good to have, but not required. Moreover, I go beyond than simple system configuration and analyze installation procedures of software packages that you might no need. 

The system is used to connect to virtual machine is Ubuntu 16.04 (LTS).

## Table Of Contents

- [Assumptions](#assumptions)
- [First Sign In](#first-sign-in)
- [Change root Password](#change-root-password)
- [Updating The System](#updating-the-system)
- [Adding New User](#adding-new-user)
- [Delete a User](#delete-a-user)
- [Generating ssh key-pair](#generating-ssh-key-pair)
- [Using ssh key-pairs](#using-ssh-key-pairs)

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

This is a really important message. You've been asked to validate the authenticity of your server. To be said, that means that you have to ask your VM provider to send you the **key fingerprint** of your VM. Then you just check if all letters are match with the ones you see in your screen. If this is not the case, contact your VM provider and ask for additional help.

Type `yes`. 

```
Password:
```

You will now be prompted to enter your **PASSWORD**. Please note that you will NOT see your cursor moving, or any characters, when typing your password. This is a standard Terminal security feature. If you typed the password correctly, you should established a new ssh session successfully.

```
[user@debian-jessie-server ~]# 
```

Congratulations! You just made your first sign in. You can type `exit` any time to disconnect from the server.

## Change root Password

Before we continue our configurations we have to change the root password. There is only one acount named root. Is created by default in every Unix-like operating system (like ours). By default it has access to all commands available in the system. We also refer to root user as superuser. 

We access the superuser account by executing the command bellow:

```
su
```

We are prompt to enter the root password. For me, the root password was the same as my user's one. You might be given a different root password.

```
Password:
```

If the password was entered successfully you gained access to root's account.

```
root@debian-jessie-server:/home/user#
```

We won't use the root account very much. The only reason we might do so, is to change security settings. The bellow command change the password of any user in the system. In this case it'll change the password of our superuser. 

```
passwd
```
                                                                               
A new line will be appeared.
                                                                               
```
Enter new UNIX password:
```
                                                                               
Enter the new password you want to use, press enter and retype it. Choose a long and difficult password (eg 20-characters long). 
                                                                               
```
Retype new UNIX password:
```
                                                                               
If the two passwords matches, you expect to see a confirmation message. 
                                                                               
```
passwd: password updated successfully
```
                                                                               
Else you'll have to repeat the last procedure again.

## Updating our system

Next we are going to handle our system updates. The first thing you have to do is to sign in as root.

```
root@debian-jessie-server:/home/user#
```

The command that check for new updates is

```
apt-get update
```

and goes along with

```
apt-get upgrade
```

The second command do the actual installation. Every time you want to update your system you have to type those two commands. I advice you to do it often. If your system gets outdated, you can have potential security risks and that's something we don't want to.

If you haven't execute the above commands until now it's time to do so. 

Note that when you execute `apt-get upgrade` command you are prompt with a question.

```
Do you want to continue? [Y/n]
```

Just type `y` or hit enter.

In this stage lets consider something. What will happen if you forget to connect to your server for a long time? For a month or two? Remember the security issues we might encounter if we don't update our system often.

To bypass this problem we are going to install a package that will automate the procedure of checking and installing **security** updates. That means, you still have to do a manual update when you connect to your VM, but now you know that the critical updates will be installed on your system. 

The package is named `unattended-upgrades`. Install it using the bellow command.

```
apt-get install unattended-upgrades
```

**Note**: You can install any package you like using the `apt-get install` command. 

When you promt for confirmation, type `y` and hit enter. 

Now we have to configure the package. Fortunately, `unattended-upgrades` comes with default configs. To enable them type:

```
dpkg-reconfigure unattended-upgrades
```

Select `yes` when you prompt for confirmation.

## Adding New User

Next we're going to create a new user. Right now I use the one called `user`. We'd like to create a new one for ourselves to manage the tasks in our server.

First we have to be already signed into the root's account. For this example, we're going to create a new user named `georgegkas` (pick your own username).

We start by executing the bellow command *-Be sure to replace georgegkas with the user that you want to create.-*:

```
useradd georgegkas
```

Every user in Unix-based distros have it's home directory. Let's create one for our new user.

```
mkdir /home/georgegkas
```

Next we have to set home folder permissions.

```
chown georgegkas:georgegkas /home/georgegkas -R
```

The command `chown` is used to change the ownership of any file and folder in the system. We can also combine the above command with `chmod` to allow only our user to modify the contents of he's home folder.

```
chmod 700 -R /home/georgegkas
```

Next, we're going to set a password. Pick a strong one. 

```
passwd georgegkas
```

Recall the command `passwd`. You can pass a parameter to indicate which user's password want to change.

Right now our new user has limited privileges on the system. Most of the time we have to run commands available only by root (such as `apt-get update`). To sign in as root every time is not a good practice and can expose our server's security. 

To bypass this signin-as-root restriction we are going to add our new user to `sudo` group. Sudo stands for either "substitute user do" or "super user do". Sudo allow us to run commands as any other users (typically as root), we just have to add the `sudo` keyword at the beginning of any command.

By default, we have to install sudo on the system. 

```
apt-get install sudo
```

Now add the new user to the sudo group:

```
usermod -a -G sudo georgegkas
```

Lets make a try. Sign out and sign in using the new user. In our case this is what we're going to execute:

```
ssh georgegkas@88.23.54.32
```

Type the password you set for your user and run the system update command. Typically you're going to get the following messages:

```
E: Could not open lock file /var/lib/apt/lists/lock - open (13: Permission denied)
E: Unable to lock directory /var/lib/apt/lists/
E: Could not open lock file /var/lib/dpkg/lock - open (13: Permission denied)
E: Unable to lock the administration directory (/var/lib/dpkg/), are you root?
```

As we already have said, `apt-get update` can be executed only by root or a sudoer (a sudo user). Try to run the update again using the `sudo` command like so:

```
sudo apt-get update
```

We can now update our system without the need to sign in as root. Moreover, we can run any other command we'd like as a root user, just by inserting `sudo` at the beginning. 

From now on, every time we want to create a new user and allow him to execute commands as root, we have to run the `usermod` command we introduced above.

## Delete a User

Now that we have set up our primary user, we're going to delete the user that was provided to us in the beginning (named `user` in my case).

```
deluser –remove-all-files user
```

The above command remove `user` from the system, deletes his home directory and all the files owned by this user. 

## Generating ssh key-pair

SSH keys provide a more secure way of connecting to a VM. Right now, we have to type our user's password every time we want to sign in to the server. Passwords can be cracked with brute-force methods and eventually anyone who has access to a computer can connect to our server. SSH key pair provides us with two different text files containing the public and the private key respectively. We place the public key to our server and keep the private key in our machine. With this method, the attacker cannot connect to our server without the private key. We also do not have to enter the user's password every time we connect to the server. Finally, we can use the same ssh public-private key pair for as many VMs as we like.

Let's start by creating a ssh key-pair in our machine.

```
ssh-keygen -t ed25519 -a 100
```

I'm not going to analyze what the above command do. A nice post can be found on [Security stackexchange forum](https://security.stackexchange.com/questions/143442/what-are-ssh-keygen-best-practices).

```
Enter file in which to save the key (/home/georgegkas/.ssh/id_ed25519):
```

First prompt allow us to change the filename of the new generated key-pair. We'll use the default location, so hit enter to continue. By default, all ssh keys are saved under `/home/user/.ssh` folder, in this example `user` is `georgegkas`.

```
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
```

Next, we've been asked to enter a passphrase for the private key. Even if we can skip this option, securing our private key with a password is highly recommended. We're going to enter that password every time we want to connect to the server that using this private key. Choose a strong one.

```
Your identification has been saved in /home/georgegkas/.ssh/id_ed25519.
Your public key has been saved in /home/georgegkas/.ssh/id_ed25519.pub.
The key fingerprint is:
SHA256:/qWwgLHsmcQBao8TuL8zlfE8j1omb0+Q2C6JzP8Yqfw me@mymachine
The key's randomart image is:
+--[ED25519 256]--+
|                 |
|                 |
|  .              |
|.. ..o .         |
|oo  +=+ S        |
|.+.+oO+O         |
|.o%o% =+.   .    |
| o+= @oo.+ o     |
|  ==E+ooo o      |
+----[SHA256]-----+
```

We see now that we have successfully generated a new key pair. The private key have been saved as `id_ed25519` and the public key as `id_ed25519.pub`. The **key fingerprint** is used to identify the authenticity of the key pair and `me@mymachine` is the our localhost user who is able to use this key pair to connect into a server.

## Using ssh key-pairs

In this section we're going to add our ssh public key to our VM. First we're going to connect to our server as root.

```
root@debian-jessie-server:/home/georgegkas#
```

Let's create the `.ssh` folder for `georgegkas` and set the appropriate ownership. Note that, the ssh key pair can be used only with the user it has been attached to. That means, if we have another user on the server, called `alex` for example, and we store our public key only in `georgegkas`, we are not allow to connect as `alex` with that key. To do so, we have to follow the same procedure on `alex`'s account as well. Execute the following commands. *Remember to change `georgegkas` to your user account.*

```
mkdir /home/georgegkas/.ssh
chmod 700 /home/georgegkas/.ssh
touch /home/georgegkas/.ssh/authorized_keys
```

Next we're going to copy our public key to our `authorized_keys` file of our server. In your server type:

```
nano /home/georgegkas/.ssh/authorized_keys
```

to use nano text editor.

Open `id_ed25519.pub` file we created earlier with `ssh-keygen`, copy the text and add it to the nano console of the server. To paste the string type `Ctrl+Shift+V`. Save the file with `Ctrl+O` and pressing enter and close the nano window with `Ctrl+X`.

Next step is to edit the `sshd_config` file. 

```
nano /etc/ssh/sshd_config
```

Using the arrow keys navigate to that line:

```
Port 22
```

Port 22 is the default port ssh uses. Change the number into something else greater than 1024, I chose 2456 for our example. This adds a layer to security by obscurity. Next nagivate until you see the line:

```
PermitRootLogin yes
```

If the value is set to `yes` change it to `no`. We won't allow anyone to connect as superuser. 

Finally, navigate until you see this line:

```
#PasswordAuthentication yes
```

Change the above line to `PasswordAuthentication no`. By the time we use the key pair, we won't allow anyone to connect using the user's password. 

Save the file `Ctrl+O` and enter, and close it `Ctrl+X`. We have to note in this point that the configurations saved in `sshd_config` will affect all the users in our system. That means, every user who wants to connect to the server needs to have a public ssh key installed on his home directory and use the port which defined above. We have to reload the ssh service.

```
systemctl restart ssh
```

Finally we need to reconfigure the permissions of our home directory.

```
chmod 400 /home/georgegkas/.ssh/authorized_keys
chown georgegkas:georgegkas /home/georgegkas -R
```

Exit the current ssh session. Bellow is the new method to login in your VM:

```
ssh georgegkas@88.23.54.32 -p 2456
```

Point the parameter `-p` we added in our command. This indicates on what port the ssh server listens to (remember that we change the default port in `sshd_config`). 

When you execute the above command, you will be prompt to enter the ssh private key password:

```
Enter passphrase for key '/home/georgegkas/.ssh/id_ed25519':
```

And now you are signed into the server.