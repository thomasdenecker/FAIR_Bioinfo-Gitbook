# The toolbox

Before going any further, you will find in this section a set of tools that will be essential for the rest of the project.

## **Text editor**

A text editor is software designed for creating and editing text files.There are many solutions \(each programmer has his favorite\). We propose 4 of them:

* [Sublime Text](https://www.sublimetext.com/)
* [Atom](https://atom.io/)
* [Notepad++](https://notepad-plus-plus.org/fr/)
* [Emacs](https://www.gnu.org/software/emacs/)

They should not be confused with word processing tools that format text. You shouldn't use them to write code! The best known are Word and the equivalents at Libre office, WordPad, Google Docs,....

{% hint style="info" %}
For the most courageous among you, it is quite possible to use the text editors available in the terminal such as VI, VIM, nano,... We do not recommend it for beginners.
{% endhint %}

## The Terminal: the Swiss army knife

In this formation, the terminal is the star. We will use it all the time. So we will first see how to install and open it.

### What is a terminal?

A terminal is the way for us to communicate with the computer using a number of commands. When we send these commands, the computer answers us.

* What are the files in this folder? `ls` 
* Where am I? `pwd` 
* Who's following? `whoami` 
* Can you create a toto folder for me, please? `mkdir toto`
* ...

The language to speak with our computer is called bash. We will often change the font to highlight the `bash` commands in the text.

### **In Ubuntu**

Terminal is already installed in Ubuntu. To open terminal in Ubuntu, open the applications by clicking at the bottom left. 

![](.gitbook/assets/image%20%28114%29.png)

After, write `terminal` in the search bar and click on Terminal.

![](.gitbook/assets/image%20%2880%29.png)

### **In Mac OsX**

Terminal is already installed. To open terminal in Ubuntu, open Finder and click on Application.

![](.gitbook/assets/image%20%28188%29.png)

### In Windows

#### Installation 

Since the last updates, it is possible to install a Ubuntu terminal on Windows. You have to go to the windows store and then search and download: ubuntu 18.04 LTS.

![](.gitbook/assets/image%20%2890%29.png)

Once the installation is complete, all that remains is to launch it

![](.gitbook/assets/image%20%2879%29.png)

#### Configuration at first launch

You must enter a user name and then type a password twice. This is normal if you do not see any characters when you enter your password. They are hidden.

Example :

```text
Installing, this may take a few minutes...
Please create a default UNIX user account. The username does not need to match your Windows username.
For more information visit: https://aka.ms/wslusers
Enter new UNIX username: tdenecker
Enter new UNIX password:
Retype new UNIX password:
passwd: password updated successfully
Installation successful!
To run a command as administrator (user "root"), use "sudo <command>".
See "man sudo_root" for details.

tdenecker@DESKTOP-B8ON598:~$
```

#### Find the terminal

Using cortana, you can type ubuntu to find the terminal easily :

![](.gitbook/assets/image%20%2877%29.png)

Et voilà! You are equipped for the rest of the training!

## A couple of ssh-keys

 A couple of ssh-keys is an encryption solution that avoid us to reminder multiple passwords because two machines will communicate and exchange their access keys together without us but the first time for a configuration step.

This secure protocol may be necessary when you request an account from a shared resource such as a cloud or a computer cluster. 

If you don't have yet an ssh-key couple \(on Linux it is stored into the `.ssh/`repository and the two files are nammed `id_rsa.pub` for the public key and `id_rsa` for the private key\). You may create one couple with the `ssh-keygen` software:

```text
$ ssh-keygen -t rsa -C "FAIR_BIOINFO"
Generating public/private rsa key pair.
Enter file in which to save the key (/home/tdenecker/.ssh/id_rsa):
/home/tdenecker/.ssh/id_rsa already exists.
Overwrite (y/n)? y
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/tdenecker/.ssh/id_rsa.
Your public key has been saved in /home/tdenecker/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:5QwtwncvoLl1j0qnEahb5lT3YyqqPOQzsz+kZPBf4Dc FAIR_BIOINFO
The key's randomart image is:
+---[RSA 2048]----+
|                 |
|     .   .       |
|      o = +      |
|  .   .* O .     |
|   o .+.S * .    |
|    =.o+E+ =     |
|   =.+=oo.o =    |
|   .B*o..= o .   |
|    =O+oo..      |
+----[SHA256]-----+id_rsa.pub
```

Your **private** key should **never be shared** \(but kept in the `.ssh` repository\), only the public key`id_ras.pub`can be shared. Here is an example of a public key, note that it is a one-line file:

```text
$ cat .ssh/id_rsa.pub
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQC/GcB30kTCSEJkbdo7SpgW2qavEGmVJYy0JzdMmf8EOWz8++ShTb0S4uuuVMsr4uJmztKkTzcEbV6sNWL9dlPi1RtU6dtZ2OTcx1IrDCSY82MgrzPU3hbOlZT6FPcgUvk5M0oQJgAR12ngQRn6yOm6jYXXczPncrIYwcVkRuyepv+Bdbu8//OIAeyY1d469JDQeXEBLRoNp6UxaOfI10VHHeWQ7o3Rd09k0BM+GT7Hn21NGeA0BWYPmMD6YxORii1T5lzhwGnGqxY2CXFL87Y0Cg6V0lsyF/Ze60iBQc6ckNZJMBa8+YtCQquWIh4Fugi0oRvb444v1Usn6HIHsFDb FAIR_BIOINFO
```

During the steps of configuring your account on a cloud/cluster, you may need to copy and paste your public key. Check the one-line format of your copy.

The 2 keys will now be able to exchange between the machines and this secure protocol replaces a log requiring a password.

