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

The host-based authentication model allows a host to authenticate on behalf of all or some users on that host. For example, a team is working remotely at two locations, London and Tokyo, then the system administrator may want to configure SSH such that instead of maintaining key pairs for all 25 users in the London team accessing the Tokyo server, they will setup host-based authentication on the Tokyo server instead.

The <mark style="color:orange;">`/etc/ssh/shs_known_hosts`</mark> file on the server must hold the public keys of all the hosts that need to be authenticated. The entry in this file implies that the host is trusted by the server and knows its public key. This file contains three fields for each record: hostname or IP address, key type and the public key itself. A sample record will look like:

```bash
122.110.17.32 ssh-rsa ABFFB3NzaC1yc2EABFFDAQABAAABAQC6XtOSGVEY9PUnMXS6vzvJigeQQtGYwdX2v2zAAsqwYRlaNN/ddV76btf4PL812r91WYGTgcXT0r0bfSGJ9dmJQ8dPenMAKyviR2BLV1SaIqxqUSjdkXFrlHkC7alILoKrwhMvNWb+Jaa3ecuYffKThNadFTHftyntdaVkYxwW7Hr1MknksfZKMPsJjW+Mp3aZVV2wVnQkOgkSsVY8y2pT7h7KuTa66IdqkwO2ZTEXL2D1X1wIEqGqAJ2VFPQayzclqaGbCzFUYyFsCT1WUL+BzRnehI9L9IVlP3katLSokoBzbxHeu0eb92VXngnrQJ1C0dA+5O4vp2KxFGEMuwdV
```

This line in the server's <mark style="color:orange;">`/etc/ssh/ssh_known_hosts`</mark> file indicates the server trusts the host specified by IP address `122.110.17.32`

Also, the <mark style="color:orange;">**`/etc/ssh/sshd_config`**</mark> file **must be updated to enable host-based authentication** by setting the value to `yes`

```bash
sysadmin@localhost:~$ cat /etc/ssh/sshd_config
# Package generated configuration file                                          
# See the sshd_config(5) manpage for details                                    
.
<Output Omited>
.                                                                               
# Authentication:                                                               
LoginGraceTime 120                                                              
PermitRootLogin without-password                                                
StrictModes yes                                                                 
 
HostbasedAuthentication yes
```

This will enable host-based authentication for all users of  the host. To filter a subset of users, add the criteria to the `/etc/ssh/sshd_config`

```bash
sysadmin@localhost:~$ cat /etc/ssh/sshd_config
# Package generated configuration file                                          
# See the sshd_config(5) manpage for details                                    
⁠⁠ 
.
<Output Omited>
.                                                                               
# Authentication:                                                               
LoginGraceTime 120                                                              
PermitRootLogin without-password                                                
StrictModes yes                                                                 
 
Match Group dbadmin
HostbasedAuthentication yes
```

The `Match Group dbadmin` entry will turn on host-based authentication only for users who are part of the `dbadmin` group.

## SSH Client Utilities

The <mark style="color:red;">`openssh`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> and <mark style="color:red;">`openssh-clients`</mark> packages **must be installed on the client machine to connect to an OpenSSH server.** The <mark style="color:red;">`ssh`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command is the remote login clinet packaged by OpenSSH. The <mark style="color:red;">`slogin`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> executable is actually a symbolic link, which references `/usr/bin/ssh`. It is a replacement for programs such as `rsh` or `telnet` for providing secure remote terminals. To login using `ssh` ,execute the following:

```bash
sysadmin@localhost:~$ ssh user1@netdevgroup1.com
OR
sysadmin@localhost:~$ ssh -l user1 netdevgroup1.com
```

If this is the first login attempt to the `netdevgroup1.com` ,then the output will look like:

```bash
The authenticity of host 'netdevgroup1.com (212.18.12.34)' can't be established. RSA key fingerprint is 33:a4:ae:c5:61:10:29:2b:69:48:bc:e1:f6:45:h1:9c. Are you sure you want to continue connecting (yes/no)? yes Warning: Permanently added 'netdevgroup1.com' (RSA) to the list of known hosts.
```

This will add the server to the client's list of known hosts as seen in the last line of the output above.

To execute only the single `ls` command without logging on, execute the command:

```bash
ssh -l user1 netdevgroup1.com `ls -l /usr/games`
```

To pass configuration options to `ssh` ,execute the following:

```bash
sysadmin@localhost:~$ ssh -o “Compression=yes” -l user1 netdevgroup1.com
```

Some of the key options of the `ssh` command are as follows:

| Option             | Meaning                                                                                                                                                                |
| ------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `-F config_file`   | <p>Specifies the configuration file to be used.</p><p>Default file is <code>/etc/ssh/ssh_config</code></p>                                                             |
| `-i identity_file` | <p>Specifies the file to read private key information.</p><p>Default files for SSH protocol version 2 are <code>~/.ssh/id_rsa</code> or <code>~/.ssh/id_dsa</code></p> |
| `-p port_num`      | Specifies the port to connect to on the remote server                                                                                                                  |
| `-e escape_char`   | <p>Sets the escape character for the session</p><p>The default is <code>~</code></p>                                                                                   |

For secure copying of remote files over an encrypted channel, the `scp` command is used. For example, to copy all the files from the local archives directory to the user's directory on the server execute the command:

```bash
scp / archives/* user1@pluto.netdevgroup.com:/archives/
```

## Understanding SSH Agent

if the user's private key is protected by a passphrase, then the passphrase needs to be entered by the user while invoking any program such as `scp` or `ssh`.&#x20;

> This can be inconvenient if the user is creating multiple sessions or wants to use `scp` within scripts for copying some file from the user's system to the server.

The SSH agent is an application, which is used to cache the decrypted private key and provide it to SSH client program when required. This effectively means the passphrase has to be entered only once by the user.&#x20;

{% hint style="info" %}
Generally, the agent rins after the user logs in and maintains the cached information for the duration of the session.
{% endhint %}

There are several SSH agents available for Linux. The OpenSSH package includes the `ssh-agent` program.

The <mark style="color:red;">**`ssh-agent`**</mark> program **runs as a daemon process** and can be verified by executing the command:

```
sysadmin@localhost:~$ ps -x |grep ssh-agent                                    
 408 ?        S+     0:00 grep --color=auto ssh-agent
```

If the <mark style="color:red;">**`ssh-agent`**</mark> program is not running with the current shell, then **it can be started by a user** executing the following command:

```
eval `ssh-agent`
OR
eval ssh-agent
```

The <mark style="color:red;">`ssh-add`</mark> command is **used to add private keys to the agent's repository**. The agent will be running on the user's terminal or desktop and authentication data is not shared with any other system over the network. The connection to the agent is forwarded to SSH remote logins and when any process needs to access the private key, the agent will service the request and return the result.

The SSH agent's implementation creates a socket for every user that is accessible using the `SSH_AUTH_SOCK` environment variable. Also the agent's PID is stored in the `SSH_AGENT_PID` environment variable.

To automatically run the <mark style="color:red;">`ssh-agent`</mark> for all users, add the entry to start the agent to the <mark style="color:orange;">`/etc/profile`</mark> file. Alternatively if the users start X sessions, then it can be started with each session as follows:

```bash
sysadmin@localhost:~$ ssh-agent startx
```

To kill the agent's instance, execute the command:

```bash
ssh-agent -k
```

## `ssh-add` Utility

The `ssh-add` utility is a helper program and is used to add RSA or DSA identities to the SSH agent's repository. If no file is specified, the `ssh-add` utility will add the keys to the `~/.ssh/id_rsa` and `~/.ssh/id_rsa.pub` files by default.&#x20;

> If the identity file requires a passphrase then the user will be prompted to enter the passphrase.

The `ssh-add` program works only if the `ssh-agent` process is running. The identity file should be readable only to the user, if they can be read by other users then it indicates possible incorrect configuration or some unauthorizes acces. The `ssh-add` program will not add such identity files.

To add identity files, execute the command:

```bash
sysadmin@localhost:~$ ssh-add                                                 
Enter passphrase for /home/sysadmin/.ssh/id_rsa:                                
Identity added: /home/sysadmin/.ssh/id_rsa (/home/sysadmin/.ssh/id_rsa)
```

If the agent is not running, then an error message will be displayed:

```bash
sysadmin@localhost:~$ ssh-add                                                 
Could not open a connection to your authentication agent.
```

To view the fingerprints of the identities stored in the agent, execute the command:

```bash
sysadmin@localhost:~$ ssh-add -l                                              
2048 ea:7b:d9:56:af:9e:29:19:bd:e7:cb:ee:04:a1:4a:24 /home/sysadmin/.ssh/id_rsa 
(RSA)
```

Some of the most useful options of the `ssh-add` command are as follows:

| Option       | Meaning                                                                                                                             |
| ------------ | ----------------------------------------------------------------------------------------------------------------------------------- |
| `-d id_file` | Deletes the identity specified by the file from the agent                                                                           |
| `-D`         | Deletes all identities stored by the agent                                                                                          |
| `-x`         | <p>Locks the <code>ssh-agent</code> with a password</p><p>This will restrict addition, deletion and listing of identity entries</p> |
| `-X`         | Unlocks the `ssh-agent`                                                                                                             |

## SSH Tunneling

When a client connects to a host via programs such as <mark style="color:red;">`telnet`</mark>, <mark style="color:red;">`ftp`</mark>, or <mark style="color:red;">`ssh`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> the socket created for communication on each side uses the IP address and the port number of the service. By default **TCP/IP is not secure connection stream** and is open to network attacks. SSH encapsulates the TCP/IP connections in a secure layer and thus creates a tunnel for communication.

The data passing through the tunnel is encrypted as well as verified for integrity. As per the requirements of the users, **multiple tunnels can be created. This feature is called SSH tunneling or SSH Port Forwarding.**

To use this feature, the <mark style="color:red;">`AllowTcpForwarding`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> option in the SSH daemon's configuration file must be set to `yes`. **The port forwarding implementation maps the local port of the user with the remote port on the server and forwards all the network traffic bound for the local port to the remote host.**

For example, if the system administrator wants to protect the network traffic of users accessing sensitive Oracle data, they can setup and use SSH Tunneling. The host where the Oracle server is running should have the SSH server setup and the clients who are accessing the Oracle server instance should have the SSH client installed. The following steps should be followed:

1.  Add a data source in the `/etc/odbc.ini` file:

    ```
    ORACLE_SSH]
     Driver          = ORACLE
     Database        = //localhost:9102/mydb
     User            = testdbuser
     Password        = testdbpassword
    ```

2\. Start the SSH daemon on the Oracle server.

3\. To setup port forwarding on the client machine, execute the command:

```bash
ssh -L 9102:testdbhost:1521 testdbhost
```

4\. If the client accesses the database now, then all network traffic from port `9102` will be forwarded to port `1521` on the Oracle server.

Consider another example of using the Oracle WebLogic admin console, which is by default accessible only on port `8586` on the server. This port is restricted for all other hosts other than localhosts. Suppose due to an urgent issue, the WebLogic administrator has to access this console on a holiday. The SSH port forwarding feature is useful in such scenearios. To setup por forwarding execute the command:

```bash
sysadmin@localhost:~$ ssh -L 8586:localhost:8586 test_user@weblogicserver1
```

Now if the user opens an instance of the web browser and accesses the WebLogic console port `8586`, it will be accessible.

{% hint style="info" %}
The system administrator can select any port as long as it is not privileged port and currently not in use by any other service.
{% endhint %}

{% hint style="danger" %}
These 2 examples are based on local port forwarding.
{% endhint %}

SSH also allows remote port forwarding, which is used for connecting the SSH server to another host where the connection is initiated by the server.

For example, a team member has an Apache server setup and running on a laptop in their home office. If the development `dev` team at the office needs to access it for making prototype urgently, then SSH remote port forwarding can be used to make this Apache instance accessible to all the team members.

To give access to the Apache service on port `8000` to users at work, execute the following command on the home office laptop:

```bash
[dbadmin1 ~]$ ssh devuser@dev.netdevgroup1.com -R 8000:192.168.1.12:8000
```

As long as the SSH tunnel exists, users will be able to access the Apache instance running on the user's laptop.

SSH is also capable of forwarding graphical application over the network. To enable X11 forwarding, the <mark style="color:red;">`/etc/ssh/sshd_config`</mark> file must contain the option:

```
X11Forwarding yes
```

To **start a SSH session with X11 support**, execute the command:

```bash
sysadmin@localhost:~$ ssh -X pluto.netdevgroup.com
```

To **verify if X11 forwarding is working correctly**, execute the command:

```bash
sysadmin@localhost:~$ echo $DISPLAY
localhost:10.0
```

If `localhost:10.0` is displayed, then configuration is correct.

## Understanding GnuPG Keys

GnuPG (GPG) is the open source implementation of the PGP (Pretty Good Privacy) standard, which is based on public-private key encryption. **Linux uses these keys to verify the signatures of packages.**

For example, if a package that has been downloaded from the internet is corrupted, then an error message will be shown to the user indicating that the package has a bad GPG signature. GPG encrypts and signs data and provides utilities for managing keys and accessing public key directories.

Linux systems can install GnuPG via the `gnupg` package. Window-based systems can download GnuPG from http://www.gnupg.org.

## Using GPG

The <mark style="color:red;">`gpg`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command with the <mark style="color:red;">`--gen-key`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> option is used to create GPG keys. To generate a new key execute the command:

```bash
gpg --gen-key
```

The <mark style="color:red;">`gpg`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command operates in an interactive mode and the user will be prompted to provide options . The output will look like:

```bash
gpg: directory `/home/sysadmin/.gnupg' created                                  
gpg: new configuration file `/home/sysadmin/.gnupg/gpg.conf' created            
gpg: WARNING: options in `/home/sysadmin/.gnupg/gpg.conf' are not yet 
active during this run                                                                    
gpg: keyring `/home/sysadmin/.gnupg/secring.gpg' created                        
gpg: keyring `/home/sysadmin/.gnupg/pubring.gpg' created                        
Please select what kind of key you want:                                        
   (1) RSA and RSA (default)                                                    
   (2) DSA and Elgamal                                                          
   (3) DSA (sign only)                                                          
   (4) RSA (sign only)                                                          

Your selection? 1
RSA keys may be between 1024 and 4096 bits long.                                

What keysize do you want? (2048)                                                
Requested keysize is 2048 bits                                                  

Please specify how long the key should be valid.                                
         0 = key does not expire                                                
      <n>  = key expires in n days                                              
      <n>w = key expires in n weeks                                             
      <n>m = key expires in n months                                            
      <n>y = key expires in n years                                             
Key is valid for? (0)                                                           

Key does not expire at all                                                      
Is this correct? (y/N) y                                                        
                                                                                
You need a user ID to identify your key; the software constructs the user ID    
from the Real Name, Comment and Email Address in this form:                     
    "Heinrich Heine (Der Dichter) <heinrichh@duesseldorf.de>"                   
                                                                                
Real name: Sysadmin                                                             
Email address: sysadmin@example.com                                             
Comment: Linux student                                                          
You selected this USER-ID:                                                      
    "Sysadmin (Linux student) <ysadmin@example.com>"                           
⁠⁠ 
                                                                                
Change (N)ame, (C)omment, (E)mail or (O)kay/(Q)uit? o                           
You need a Passphrase to protect your secret key.
Re-enter your Passphrase
We need to generate a lot of random bytes. It is a good idea to perform         
some other action (type on the keyboard, move the mouse, utilize the            
disks) during the prime generation; this gives the random number                
generator a better chance to gain enough entropy.                               
<Output omitted>
gpg: checking the trustdb                                                       
gpg: 3 marginal(s) needed, 1 complete(s) needed, PGP trust model                
gpg: depth: 0  valid:   1  signed:   0  trust: 0-, 0q, 0n, 0m, 0f, 1u           
pub   2048R/950B76C6 201-10-29                                                 
      Key fingerprint = 50D6 24A7 C121 51EB 397B  1C92 A6EA 5A3D 97E3 667A      
uid                  Sysadmin (Linux Student) <sysadmin@example.com>            
sub   2048R/4BA1698A 2015-03-16
```

The RSA and DSA methods are the same as those used in SSH encryption while Elgamal is another algorithm. The user will be prompted for other options as follows:

* The key size must be specified, RSA keys can be 1024-4096 bits long.
* The key validity must be specified in terms of number of days, weeks, month or years. The value `0` indicates that the key will never expire
* The username, email ID and comment must be specified. This is for linking the key with a user.
* A passphrase for proctecting the key must be entered twice.

When the process is complete, an asymmetric public and private key pair is created. Whatever data is encrypted by one key, can be decrypted by the other. In practice, the user will publish their public key. Then, they will use their private key to encrypt data, so others can use the public key to decrypt it.

The output from the process will include some information about the keys:

```
pub   2048R/950B76C6 201-10-29                                                 
      Key fingerprint = 50D6 24A7 C121 51EB 397B  1C92 A6EA 5A3D 97E3 667A      
uid                  Sysadmin (Linux Student) <sysadmin@example.com>            
sub   2048R/4BA1698A 2015-03-16
```

**The `950B76C6` is the public key identifier**, which can be used to refer to this key. The <mark style="color:red;">`--armor`</mark> option ensures that the file contains only ASCII characters instead of binary. For example, **to export this public key to a file**, you can execute:

```bash
gpg --armor --output pub_key_file --export 950B76C6
```

You can also **export public keys by referring to the name that was entered** for the key, like:

```bash
gpg --armor --output pub_key_file --export 'Linux Student'
```

To **import the public key of the other user** with whom the client wants to communicate, execute the command:

```bash
sysadmin@localhost:~$ gpg --import pub_key_file
```

Users can also upload their keys to public key servers, which host public keys from users across the globe. To **upload a key to a key server**, execute the command:

```
sysadmin@localhost:~$ gpg --keyserver http://example.com --send-keys 950B76C6
```

If you wdfdf











