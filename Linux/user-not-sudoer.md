# User has not the privilege to run 'sudo'


## Problem

User can't execute 'sudo' commands


## Specifications

ISO image: CentOS-7-x86_64-DVD-2207-02.iso

Platform: VMware® Workstation 17 Pro

Version: 17.6.0 build-24238078


## Error Message

User is not in sudoers list


## Cause

During OS installation:  
The 'user' wasn't choosen as an administrator and wasn't added in sudoers list explicitly later.  

After OS installation:  
'user' was generally created without explicitly giving administration privileges.


## Solution

### Solution 1 (Adding 'user' to wheel)

Current status: You are logged in as a normal user (say, userX)

`su -` (Switch to root)  
`grep '^%wheel' /etc/sudoers` (Expected output: `%wheel   ALL=(ALL)   ALL`)  
`usermod -aG wheel userX`  
`id userX` [Expected output: `uid=1000(userX) gid=1000(userX) groups=1000(userX),10(wheel)`]

Log out of the 'userX' session and log back in (Highly recommended), or start a new shell  
`sudo whoami` (Expected output: `root`)

---

### Solution 2 (Direct sudoers file entry)

`su -` (Switch to root)

`visudo` (Add the following line):  
 - `userX   ALL=(ALL)   NOPASSWD: ALL`  
 - save and exit

---

With complying with anyone of the above solutions, the user 'userX' will be able to use 'sudo' commands.

---

### Recommended solution

For CentOS/RHEL systems, adding users to wheel is generally preferred because:  
 - Easier to manage multiple admins
 - Keeps /etc/sudoers cleaner
 - Follows the distribution's standard practice
