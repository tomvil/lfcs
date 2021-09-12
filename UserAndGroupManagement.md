# Chapter 3. Create, delete, and modify local user accounts

## ## Create, delete, and modify local user accounts
User management is one of the most common but critical activities that you will perform as a system administrator.

**Add a User to the system**
  - `useradd username`
    - Command available on all distributions
    - Will not create home directory (if not set to do so in `/etc/login.defs`)
    - Will not set password (have to do it with `passwd username` help)
    - Examples:
      - `useradd student` - add user with username `student`
      - `useradd -G developers student` - add user with username `student` and add it to group `developers`
      - `useradd -m -s /bin/bash student` - add user with username `student`, create home directory for him and set default shell to `/bin/bash`.
      - `useradd -D` - prints out all default parameters (home, shell and etc.). Also can be found in `/etc/default/useradd`
      - Other `useradd` parameters that can be used:
        - `-c` Any text string. It is generally a short description of the login, and is currently used as the field for the user's full name.
        - `-e` date after which the/ user will be disabled
        - `-g` primary group. NOTE: if not specified it will be created a new group with same name of user that will be become user's primary group
        - `-G` secondary groups
        - `-m` create home directory. Useless because CREATE_HOME is yes
        - `-p` configure password. NOTE: value must be provided encrypted
        - `-s` shell to use

```
[root@2e77ed18151c ~]# grep CREATE_HOME /etc/login.defs
CREATE_HOME	yes
```

Some more variables that can be set `/etc/login.defs`:
  - `MAIL_DIR` Location of mail spool
  - `PASS_MAX_DAYS` Maximum number of days a password may be used
  - `PASS_MIN_DAYS` Minimum number of days allowed between password changes.
  - `PASS_MIN_LEN` Minimum acceptable password length.
  - `PASS_WARN_AGE`  Number of days warning given before a password expires.
  - `CREATE_HOME` -> create home directory
  - `USERGROUPS_ENAB` -> means that a group with same name of user must be created. This group will become default user group. This means that value of GROUP in /etc/default/useradd is overwritten

When a user is created in the system, there are two files which are modified:
  - `/etc/passwd` contains users information, but no passwords
  - Syntax:
    - user name
    - x: means that password isn't stored here
    - userid: user id (UID)
    - groupid: primary group id (GID)
    - User Info: The comment field
    - home: home directory
    - shell: shell
    - edit file vai cmd: `vipw`
  - `/etc/shadow` contains passwords plus passwords properties
    - edit file via cmd: `vipw -s`

**Modify a User on the system**
  - `usermod params username`
    - Command used to update an existing user account
    - Can update any attribute of a user account except the account.
    - Examples:
      - `usermod -c COMMENT student` - add comment COMMENT to user `student`
      - `usermod -aG lxd student` - `a` means append, `G` means secondary group. This command adds `student` user to `lxd` group.
      - `usermod -L student` - will lock user `student`
      - `usermod -U student` - will unlock user `student`
      - `usermod -e 1 student` - will disable user `student`
      - `usermod -e "" student` - will enable user `student`
      - `usermod -e 2021-10-01 student` - will disable user `student` on 2021-10-01

**Remove a User from the system**
  - `userdel username`
    - Legacy command available on all distributions
    - Combine with `-r` to remove home directory
    - Examples:
      - `userdel student` - will delete `student` account, but home directory will stay
      - `userdel -r student` - will delete `student` account and home its home directory as well.
      - `-f` can be used to force the action, in case student is logged in at the moment.

**Other useful commands to know**
  - `passwd` - Change password of current user
  - `passwd username` - Change password for specific user
  - `passwod -l username` - Lock password for specific user
  - `passwd --expire username` - will force to change the password for username

  - `chage` - Change user password expiry information
    - If used without parameters will prompt for information (interactive)
    - It will permit to change date when the password was last changed
  - `chage -E 2021-07-02` - Sets a date after which user will be locked.
  - `chage -d0 username` - will force to change the password for username
  - `chage -l username` - will show information about password for specific user.

<br>

## ## Create, delete, and modify local groups and group memberships
Group management is another common, but critical activity you will perform as a system administrator.

**Add a Group to the system**
  - `groupadd groupname`
    - Command available on all distributions. Can be used for scripting.
    - When a new group is added `/etc/group` file fill be updated.
    - Examples:
      - `groupadd -r groupname` - adds group in the reserved range
      - `groupadd groupname` - simply adds the group.

**Remove group from the system**
  - `groupdel groupname`
    - Simply removes the group from the system

**Manage Users and Groups**
  - `usermod -aG groupname username`
    - Add specific user to specific group.
    - `a` = append
    - `G` = list of secondary groups
  - `gpasswod -a username groupname`
    - Add specific user to specific group.
  - `gpasswd -d username groupname`
    - Delete specific user from specific group.

**Some useful commands**
  - `cat /etc/group` - shows current groups created on the system
  - `groups username` or just `groups` - shows all groups, or groups assigned to specific user.
  - `newgrp groupname` switch to the groupname group..
  - `vigr` - edit groups via text editor.

<br>

## ## Manage system-wide environment profiles
Application configuration in Linux is usually done with environment variables.

**Types of variables**
  - Local
    - Variables available to the current session only
  - User
    - Variables defined for the specific user only
  - Systems
    - Variables available to all users on the system

**To view the variables**
  - `$ env` - view all defined variabels
  - `$ printenv` or `$ printenv VAR` - view all or a specific `env` variable
  - `$ echo $VAR` - print out specific variable.

**To set variables**
  - Local
    - `export VAR='something`
    - `export VAR=$VAR:newinfo`
    - Example with path:
      - `$ export PATH=$PATH:/opt/exabgp`
  - User
    - Add `export VAR='something` in one of the files:
      - `~/.bashrc` or `~/.bash_profile`
      - `~/.bash_login` or `~/.profile`
  - System
    - Add `VAR='something'` to one of the following files:
      - `/etc/profile.d/custom.sh`
      - `/etc/bash.bashrc` (bash only)
      - `/etc/environment`

**To unset variables**
  - Unset variable
  - Or remove from files that it was added.

<br>

## ## Manage template user environment
  - `/etc/skel`
    - The contents of this directory will be used to populate the home directory for all new users.
    - This directory can contain:
      - Configuration files such as a `.bashrc, .bash_profile, .nanorc` or similar files.
      - Directories for documents, logs, scripts and etc.
      - Documents, like a welcome or readme, script templates or information files.
    - **NOTE: Any changes to this directory will be applied to new user accounts only.**

<br>

## ## Configure user resource limits
To ensure the availability and usability of your server you may need to apply limits to some users or groups.

**Configure limits**
  - **`/etc/security/limits.conf`**
    - Limits configuration file
    - Two types of limits:
      - Hard = set by admin and can't be changed.
      - Soft = can be adjusted by user, up to the specified hard limit.
    - File format:
      - Domain = User, group or wildcard
      - Type = Hard or Soft
      - Item = Memory size, file size, # of files and etc.
      - Value = Limits, either by numeric value or size in KB
    - **NOTE 1: User limits take priority over group limits.**
    - **NOTE 2: Limits will be set in the next opened session.**

Configuration example:
```
1. @student        hard    nproc           20
2. @faculty        soft    nproc           20
3. ftp             hard    nproc           0
4. @student        -       maxlogins       4
```

**Show applied limits for you**
  - `$ ulimit`
  - `$ ulimit -a` - show all limits

**Show specific limit**
  - `$ ulimit -H -n` - will show hard limit of file descriptors
  - `$ ulimit -S -n` - will show soft limit of file descriptors

**Change value of limit**
  - `$ ulimit -n 1600` - will increase maximum number of files descriptors to 1600

<br>

## ## Manage user privileges
  - `/etc/security/access.conf`
    - Remote access configuration file
      - Permissions (+ or -) grand or deny access
      - User can be a user, group or ALL
      - Point of origin could be a hostname, domain name, IP address, IP subnet or ALL.

Also it can be done in `sudoers` file. We have talked about it in `Essentials Commands` chapter.

<br>

## ## Configure PAM
PAM in Linux allows to configure and reconfigure how authentication takes place in PAM-aware applications, without the need to rewrite the application.

  - **PAM (Pluggable Authentication Module)**
    - Controls authentication of users
    - Has the following components:
      - Application must be written to be PAM-aware
      - Configuration files are in the `/etc/pam.d/` directory.
      - PAM modules are usually found in `/lib64/security`. But can vary based on distributions.

  - **PAM WorkFlow**
    - User launches a PAM-aware application
    - The applications calls `libpam`
    - The library checks for files in `/etc/pam.d/` directory to sww which PAM modules to load, including system-auth.
    - Each modules is executed by following the rules defined for that application.

  - **PAM Config (modules-type, control-flag, pam-module)**
    - module-type
      - authentication, account, password, session
    - control-flag
      - required, requisite, optional, sufficient
    - pam-module
      - Any module, reference by name

**Useful commands:**
  - `ldd /usr/bin/passwd | grep pam` check if program uses PAM libraries (`passwd` in this case)
