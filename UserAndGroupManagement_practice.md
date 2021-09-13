# Chapter 3. User and group management

## ## Create, delete, and modify local user accounts
1. Check all default `useradd` command settings
2. Add group named `developers`
3. Add group named `resrv` and make sure it is in the reserved range.
4. Create a new user `student_tom`
    1. Make sure his home directory gets created and is located in `/home`
    2. Make sure his shell is set to `/bin/bash`
    3. Assign user to `developers` group.
    4. Change primary group to `resrv`
    5. Change primary group to default.
    6. Set password and try to log in via ssh.
5. Check what is the default location or `MAIL_DIR`
6. Check what is the maximum number of days a password may be used
7. Check what is the minimum number of days allowed between password changes.
8. Check what is the minimum acceptable password length. Change it to 16 symbols.
9. Check what is the number of days warning given before a password expires.
10. Review `/etc/passwd` file. Understand the syntax. Try to edit it via shell comand.
11. Review `/etc/shadow` file. Understand the syntax. Try to edit it via shell comand.
12. Change comment for student user to `Student Studentovski`
13. Add new user `test_rabbit`.
14. Lock user `test_rabbit`. Check what changes
15. Unlock user `test_rabbit`.
16. Disable user `test_rabbit`. Check what changes.
17. Enable user `test_rabbit`.
18. Set user `student` to expire on 2023-09-01.
19. Delete user `test_rabbit` from the system. Make sure his `home` is deleted as well.
20. Lock password for `student` user.
21. Force `student` to change his password.
22. List all users created on the system

<br>

## ## Create, delete, and modify local groups and group memberships
1. Add group named `qa_engineers`
2. Delete group named `qa_engineers`
3. Check all the groups that are create don the systems
4. Check what groups `root` user is in.
5. Add group named `testers` and add user `root` to it.

<br>

## ## Manage system-wide environment profiles
1. View all env variables (two different commands)
2. Print out specific env variable (two different commands)
3. Set a local env variable (`script_location=/opt/scripts`)
4. Set a user env variable (`guest_account=true`)
5. Set a system variable (`script_password=test123`)
6. Unset all set env variables.

<br>

## ## Manage template user environment
1. Make sure newly created accounts has `Company Rules` file created.
2. Make sure newly created accounts has `Company Share` directory created.
3. Make sure newly created accounts has `welcome` file created.

<br>

## ## Configure user resource limits
1. Set hard limit for student user for `nproc` to 50
2. Set soft limit for student user for `nproc` to 20
3. Set hard limit for group `developers` for `nofile` to 2000
4. Set soft limit for group `develoeprs` for `nofile` to 1500
5. Print all limits
6. Print soft limit for `nproc` limit specifically
7. Print hard limit for `nproc` limit specifically
8. Set the hard limit of file descriptors to hard limit
9. Reduce the hard limit of files descriptors by 100
10. Try tro set hard limit back to the previous value (+100). Did it work? (it shouldn't. Only root can increase it.)

<br>

## ## Manage user privileges
1. Deny student user logins from everywhere
2. Deny student user logins from everywhere but local network
3. Allow student user to login from specific IP (any random)
4. Allow all except root user to log in from 10.10.10.10

<br>

## ## Configure PAM
1. Check if `passwd` is pam-aware
2. Check if `vi` is pam-aware
3. Edit `sshd` and `login` pam configuration so it would deny login if a user is located in `blocked_users.txt` file.
