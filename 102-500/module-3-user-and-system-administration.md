# Module 3: User and System Administration

## Chapter 6: User and Group Accounts

> **107.1: Manage user and group accounts and related system files v2**
>
> Weight: 5

<details>

<summary>Key terms</summary>

**`/etc/group`** System file that defines the groups a user belongs plus various system groups automatically created as part of the Linux installation

**`/etc/passwd`** System file containing details of special system accounts used to run services, and user accounts on the system.

**`/etc/shadow`** System file containing the encrypted password and password aging information. Viewable only by the root user

**`/etc/skel`** System directory containing a default set of "skeleton" (template) files and directories which are copied by default to the home directory of a user that is created with the `useradd` command. This provide a uniform directory structure for all new users of the system

**`chage`** Command used by root to update information related to password expiry. Using this command, the administrator can enforce a password changing and expiry policy for specific user accounts

**`getent`** An alternative to the groups command that displays a users group record in /etc/group. Also used to queary other administrative files (passwd, shadow, hosts, etc) called databases which store user information

**`groupadd`** Command used to add a new group. By default the groupadd command will automatically assigns a GID to the new group automatically by using the next available GID

**`groupdel`** Command used to delete a group that is no longer needed. You cannot delete a group if it is a user's primary group before first deleting the user with the `userdel` command

**`groupmod`** Command used to modify the properties (i.e GID) of an existing group account.

**`passwd`** Command is used to update a user's password.

**`useradd`** Command used by root to add a new user account to the system. The `useradd -D` command displays the default options (home directory, login shell, etc) if they are not specified when useradd is used.

**`userdel`** Command used by root to delete user account information preventing them from logging in and optionally removing all and the files related the account.

**`usermod`** Commnad used by root to modify existing a user account parameters such as home directory, loginname, login shell, password aging information, etc.

</details>

















































