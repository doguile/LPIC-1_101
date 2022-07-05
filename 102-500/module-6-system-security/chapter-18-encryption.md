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















