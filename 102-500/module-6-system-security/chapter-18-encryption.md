---
description: '110.3: Securing data with encryption v2  Weight: 4'
---

# Chapter 18: Encryption

<details>

<summary>Key terms</summary>

<mark style="color:red;">`/etc/ssh/shs_host_dsa_key`</mark> <mark style="color:red;"></mark><mark style="color:red;">and</mark> <mark style="color:red;"></mark><mark style="color:red;">`ssh_host_dsa_key.pub`</mark> SSH configuration files `/etc/ssh/ssh_host_dsa_key` stores the DSA private key and `/etc/ssh/ssh_host_dsa_key.pub` stores the DSA public key

<mark style="color:red;">`/etc/ssh/ssh_host_rsa_key and ssh_host_rsa_key.pub`</mark> SSH configuration files `/etc/ssh/ssh_host_rsa_key` stores the RSA private key and `/etc/ssh/ssh_host_rsa_key.pub` stores the RSA public key

<mark style="color:red;">`gpg`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> GnuPG (GPG) is the open source implementation of PGP (Pretty Good Privacy) standard which is **based on public-private key** encryption. **Linux uses these keys to verify the signatures of packages**. GPG encrypts and signs data and provides utilities for managing keys and accesssing public key directories.

<mark style="color:red;">`gpg-agent`</mark> To help make the use of GPG easier and more convenient, **the `gpg-agent` daemon can cache the passphrase for the gpg keyfile**. This allows the passphrase to be used once and then cached for the determined amount of time.

<mark style="color:red;">`ssh`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> The ssh protocol is used to provide secure remote login and other services. The SSH protocol **uses public key cryptography for authenticating the remote host** and providing an encrypted channel.

<mark style="color:red;">`ssh-add`</mark> A program **used to add RSA or DSA identities to the SSH agent's repository**. If no file is specified the `ssh-add` utiility will add the keysfiles to the `~/.ssh/id_rsa` and `~/.ssh/id_dsa` files by default. If the identity file requires a passphrase then the user will be prompted to enter one. **The `ssh-add` program works only if the `ssh-agent` process is running.**

<mark style="color:red;">`ssh-agent`</mark> Part of the Open SSH package that **provides a secure way of storing the private key**. For private keys that require a passphrase, `ssh-agent` allows the user to connect multiple times without having to repeatedly type the passphrase

<mark style="color:red;">`ssh-keygen`</mark> Command used to **generate and manage keys used by SSH**; it uses the **RSA algorithm by default**. This program will prompt the user for the location to store the key and the passphrase

<mark style="color:red;">`ssh_known_hosts`</mark> The `/etc/ssh/ssh_known_hosts` file on the server must **hold the public keys of all the hosts** that need to be authenticated

<mark style="color:red;">`~/.gnupg/`</mark> The configuration for gpg-agent is stored in the `~/.gnupg/gpg-agent.conf` file

<mark style="color:red;">`~/.ssh/authorized_keys`</mark> List of **authorized public keys for servers**, verified by the server to authenticate the user when the user attempts to log in.

<mark style="color:red;">`~/.ssh/id_dsa and ~/.ssh/id_dsa.pub`</mark> Default files that stores the DSA private key and DSA public key of the user when generated with the ssh-keygen command

<mark style="color:red;">`~/.ssh/id_ecdsa and id_ecdsa.pub`</mark>

<mark style="color:red;">`~/.ssh/id_ed25519 and id_ed25519.pub`</mark>

<mark style="color:red;">`~/.ssh/id_rsa and id_rsa.pub`</mark> Default files that stores the RSA private key and RSA public key of the user when generated with the ssh-keygen command.

</details>

## Configuring OpenSSH Client

The SSH configuration file, `/etc/ssh/ssh_config` , is used to configure the options for clients programs such as `ssh`, `sftp` , and `scp` and contains key-value pairs on each line. The file can be edited and changes can be made as necessary.

```bash
sysadmin@localhost:~$ cat /etc/ssh/ssh_config

# This is the ssh client system-wide configuration file.  See                   
# ssh_config(5) for more information.  This file provides defaults for          
# users, and the values can be changed in per-user configuration files          
# or on the command line.                                                       
Output omitted...                                                                                
Host *                                                                          
#   ForwardAgent no
#   ForwardX11Trusted yes
#   RSAAuthentication yes
#   PasswordAuthentication yes
#   BatchMode no
#   CheckHostIP yes
#   StrictHostKeyChecking ask
#   IdentityFile ~/.ssh/id_rsa
#   Port 22
#   Cipher 3des
Output omitted...
```

The importnt keywords and their meanings are explained below:

| Key                      | Description                                                                                                                                                                              |
| ------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Host`                   | Applies all forwarded declarations and options in the configuration file for those hosts that match one of the patterns given after the Host keyword                                     |
| `ForwardAgent`           | Specifies which connection authentication agent should be forwarded to the remote machine                                                                                                |
| `ForwardX11Trusted`      | Specifies if X11 sessions should be automatically redirected to the remote machine                                                                                                       |
| `RSAAuthentication`      | Specifies if RSA authentication is to be used                                                                                                                                            |
| `PasswordAuthentication` | Set to `yes` to use password based authentication; `no` otherwise                                                                                                                        |
| `BatchMode`              | Specifies if username and password check on connection will be disabled. This option is generally used while invoking `ssh` from scripts to provide a non-interactive mode of operation. |
| `CheckHostIP`            | Specifies if the IP address of the host should be checked for DNS spoofing                                                                                                               |
| `StrictHostKeyChecking`  | Specifies if new hosts should be automatically added by ssh to the `.ssh/known_hosts` file                                                                                               |
| `IdentityFile`           | Specifies an alternate RSA authentication identity file to use                                                                                                                           |
| `Port`                   | Specifies the port number on which `ssh` connects to the remote host (default value is 22)                                                                                               |
| `Cipher`                 | Specifies the cipher method to be used for encryption                                                                                                                                    |

{% hint style="info" %}
The `/etc/ssh/ssh_config` file is the default file for all systemwide users using `ssh` services. However, an `ssh` configuration file in a user's home directory `~/.ssh/config` takes precedence over the systemwide configuration file.
{% endhint %}

Some of the other client configuration files are listed below:

| File                     | Purpose                                                                                                                         |
| ------------------------ | ------------------------------------------------------------------------------------------------------------------------------- |
| `~/.ssh/known_hosts`     | **List of servers**, along with host keys accessed by the user                                                                  |
| `~/.ssh/authorized_keys` | **List of authorized public keys for servers**, verified by the server to authenticate the user when the user attempts to login |
| `~/.ssh/id_rsa`          | RSA private key of the user                                                                                                     |
| `~/.ssh/id_rsa.pub`      | RSA public key of the user                                                                                                      |
| `~/.ssh/id_dsa`          | DSA private key of the user                                                                                                     |
| `~/.ssh/id_dsa.pub`      | DSA public key of the user                                                                                                      |
| `~/.ssh/id_ecdsa`        | ECDSA private key of the user                                                                                                   |
| `~/.ssh/id_ecdsa.pub`    | ECDSA public key of the user                                                                                                    |
| `~/.ssh/id_ec25519`      | EC25519 private key of the user                                                                                                 |
| `~/.ssh/id_ec25519.pub`  | EC25519 public key of the user                                                                                                  |

A sample `~/.ssh/known_hosts` file can be generated using the following commands:

```
sysadmin@localhost:~$ cat ~/.ssh/known_hosts
cat: /home/sysadmin/.ssh/known_hosts: No such file or directory
sysadmin@localhost:~$ ssh sysadmin@192.168.1.2
The authenticity of host '192.168.1.2 (192.168.1.2)' can't be established.
ECDSA key fingerprint is f5:42:6a:3c:2a:01:6f:79:44:d3:95:0c:bb:7e:2f:3f.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '192.168.1.2' (ECDSA) to the list of known hosts. 
sysadmin@192.168.1.2's password:

sysadmin@localhost:~$ cat ~/.ssh/known_hosts                 
|1|nY9O7O+HqkAS8xCvPgs3JaG7cv0=|DiXxxE6ccKWkMFxj/RI6nFLFP2I= ecdsa-sha2-nistp2
```

## Configuring SSHD

The `/etc/ssh/sshd_config` file is used to continue the SSH daemon. This file also contains key value pairs on each line. A snippet of the `/etc/ssh/sshd_config` file is below:

```bash
sysadmin@localhost:~$ cat /etc/ssh/sshd_config
# Package generated configuration file                                          
# See the sshd_config(5) manpage for details                                    
                                                                                
# What ports, IPs and protocols we listen for                                   
Port 22                                                                         
# Use these options to restrict which interfaces/protocols sshd will bind to    
#ListenAddress ::                                                               
#ListenAddress 192.168.1.1                                                          
Protocol 2                                                                      
# HostKeys for protocol version 2                                               
HostKey /etc/ssh/ssh_host_rsa_key                                               
HostKey /etc/ssh/ssh_host_dsa_key                                               
HostKey /etc/ssh/ssh_host_ecdsa_key                                             
HostKey /etc/ssh/ssh_host_ed25519_key                                           
                                                                                
# Lifetime and size of ephemeral version 1 server key                           
KeyRegenerationInterval 3600                                                    
ServerKeyBits 1024                                   
```

The kyewords and their meaning are explained in the following table:

| Keyword                   | Meaning                                                                                                                             |
| ------------------------- | ----------------------------------------------------------------------------------------------------------------------------------- |
| `Port`                    | Specifies the port which `sshd` listens to for incoming connections; the default port is 22                                         |
| `ListenAddress`           | Specifies the IP address on which the `sshd` server socket will bind                                                                |
| `HostKey`                 | Specifies where the private host key is stored                                                                                      |
| `KeyRegenerationInterval` | Specifies the time interval in seconds for the server to automatically regenerate its key                                           |
| `ServerKeyBits`           | Specifies the number of bits to be used by `sshd` for RSA key generation                                                            |
| `LoginGraceTime`          | Specifies the time interval in seconds to wait for the user’s response before disconnecting the server                              |
| `PermitRootLogin`         | Specifies if root login over SSH is permitted or not                                                                                |
| `RSAAuthentication`       | Specifies if RSA authentication can be used                                                                                         |
| `PermitEmptyPasswords`    | Specifies if user logins to the server with empty password is allowed                                                               |
| `PasswordAuthentication`  | Specifies if password based authentication must be used                                                                             |
| `X11Forwarding`           | Specifies whether X11 forwarding must be turned on or off. If GUI has been installed on the server, then this option can be enabled |
| `AllowUsers`              | Specifies users who will be allowed access                                                                                          |
| `AllowGroups`             | Specifies groups who will be allowed access                                                                                         |

Some of the other configuration files used by `sshd` are listed below:

| File                                | Purpose                            |
| ----------------------------------- | ---------------------------------- |
| `/etc/ssh/ssh_host_rsa_key`         | RSA private key used by `sshd`     |
| `/etc/ssh/ssh_host_rsa_key.pub`     | RSA public key used by `sshd`      |
| `/etc/ssh/ssh_host_dsa_key`         | DSA private key used by `sshd`     |
| `/etc/ssh/ssh_host_dsa_key.pub`     | DSA public key used by `sshd`      |
| `/etc/ssh/ssh_host_ecdsa_key`       | ECDSA private key used by `sshd`   |
| `/etc/ssh/ssh_host_ecdsa_key.pub`   | ECDSA public key used by `sshd`    |
| `/etc/ssh/ssh_host_ed25519_key`     | ED25519 private key used by `sshd` |
| `/etc/ssh/ssh_host_ed25519_key.pub` | ED25519 public key used by `sshd`  |

## SSH Authentication and Keys

SSH supports several different authentication methods:

1. Public key authentication
2. Host-based authentication
3. Password authentication

### Public key authentication

**The public key authentication method is the most commonly-used SSH authentication method**. It is implemented both on the server as well as the client side. To use this, a public-private key pair must be generated using a key-generation utility.

{% hint style="success" %}
&#x20;**RSA** is the most **commonly-used public key generation algorithm**
{% endhint %}

The algorithm generates keys such that the public and private keys are linked. **The private key stored on the client's machine is protected by a passphrase** (similar to a password) so that in the event of unauthorized access to the client machine, the passphrase would still need to be cracked to access the client's private key.

{% hint style="danger" %}
If the **user forgets the passphrase there is no option to recover it** and the key will have to be replaced by a new key. The passphrase of the private key can be changed using the command <mark style="color:red;">`ssh-keygen -p`</mark> .
{% endhint %}

The system administrator can select either RSA or DSA keys while configuring the SSH public key based authentication.

> DSA (Digital Signature Algorithm) is a US government standard defined for digital signatures while RSA is named after its creators Ron Rivest, Adi Shamir and Leonard Adleman)

**DSA is faster while signing than RSA**, but **RSA is faster for verification**. So the net time taken by both algorithms is comparable. If a 1024 bit encryption key is set up with DSA, the signature it generates will be smaller in size compared to the signature generated by RSA.

The <mark style="color:red;">**`ssh-keygen`**</mark> command is **used to generate and manage keys used by SSH**; it uses the RSA algorithm by default. This program will prompt the user for the location to store the key (<mark style="color:orange;">`~/.ssh`</mark> is the default) and the passphrase. For example, to generate a RSA key pair on the client side, execute the following:

```bash
sysadmin@localhost:~$ ssh-keygen                                                
Generating public/private rsa key pair.                                         
Enter file in which to save the key (/home/sysadmin/.ssh/id_rsa):               
Enter passphrase (empty for no passphrase):                                     
Enter same passphrase again:                                                    
Your identification has been saved in /home/sysadmin/.ssh/id_rsa.               
Your public key has been saved in /home/sysadmin/.ssh/id_rsa.pub.               
The key fingerprint is:                                                         
9a:64:6c:d5:28:ba:c5:bc:e4:30:8f:33:ff:32:78:bc sysadmin@localhost              
The key's randomart image is:                                                   
+--[ RSA 2048]----+                                                             
|                 |                                                             
|         o       |                                                             
|      . o .      |                                                             
|     = o         |                                                             
|    + X S        |                                                             
|     & +         |                                                             
|    =o*          |                                                             
|    .+=          |                                                             
|     .E=.        |                                                             
+-----------------+
```

In the example above, the keys are generated and stored in the `/home/sysadmin/.ssh/` directory. The private key is stored in the `id_rsa` file and the public key in the `id_rsa.pub` file.&#x20;

{% hint style="warning" %}
The public key needs to be copied to the server that you want to securely login to and only has to be done once.
{% endhint %}

The <mark style="color:red;">**`ssh-copy-id`**</mark> command is **used to copy the public key to the server**. In the example above, the public key is copied to the `user1` account on the server named `netdevgroup1`&#x20;

```bash
sysadmin@localhost:~$ ssh-copy-id user1@netdevgroup1
```

This command will add the contents of the client's `~/.ssh/id_rsa.pub` file to the `~/.ssh/authorized_keys` file of `user1` on the server `netdevgroup1`. When the user connects via an <mark style="color:red;">`ssh`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> session to the server, the user's public key from the `~/.ssh/authorized_keys` file will be used to encrypt data such that it can be decrypted only using the private key accessible to the user and no-one else.

When a user tries to login to the server `netdevgroup1` using the <mark style="color:red;">`ssh`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command, the user will be prompted for their passphrase.

```bash
sysadmin@localhost:~$ ssh netdevgroup1
Enter passphrase for key '/user1/.ssh/id_rsa': 2are ramp2d
Last login: Tue Oct 21 00:00:49 2014 from user1@localhost
```

The next time the client logs on to the server, his public key is matched with the list of public keys on the server. If a match is found, then a signature is generated by the client using the private key. The signature is verified by the server using the public key, which is linked with the private key and the user is aunthenticated.

Some of the key options of the <mark style="color:red;">`ssh-keygen`</mark> command are:

| Option         | Meaning                                                                                                                                         |
| -------------- | ----------------------------------------------------------------------------------------------------------------------------------------------- |
| `-b num_bits`  | Specifies the number of bits for the key, the range for RSA keys is 768 – 2048 bits (default is 2048 bits) while DSA keys are exactly 1024 bits |
| `-F host_name` | Find the occurrence of the specified hostname in the `known_hosts` file                                                                         |
| `-R host_name` | Deletes all keys for the specified hostname from the `known_hosts` file                                                                         |
| `-f file_name` | Specifies the file name for the key                                                                                                             |

{% hint style="info" %}
Once SSH configuration has been verified on the client, passwortd-based authentication can be completely disabled if the system administrator wants to have a password-less policy for maintaining security.

The <mark style="color:red;">`password -l`</mark> command can be used to lock a user's account, but key-based authentication will continue to work. This makes the system more secure as that user may only log in from authorized client machines.
{% endhint %}

## SSH Host Based Authentication













