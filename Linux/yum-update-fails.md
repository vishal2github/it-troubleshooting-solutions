# yum update fails


## Problem

Package updates fail on CentOS/RHEL systems


## Specifications

ISO image: CentOS-7-x86_64-DVD-2207-02.iso

Platform: VMware® Workstation 17 Pro

Version: 17.6.0 build-24238078


## Error Message

Cannot resolve host / failed to fetch metadata


## Cause

Mirrorlist and baseurl fetching issues


## Solution

Check connectivity: `ping -c 4 8.8.8.8`

If that works, test DNS: `ping -c 4 google.com`

Also check DNS configuration:
  - A working file usually contains something like:
    - nameserver 8.8.8.8
    - nameserver 1.1.1.1

If 8.8.8.8 fails, it's a network configuration problem in the VM.

If 8.8.8.8 works but google.com fails, it's a DNS problem.

If both work, then the main issue is the retired CentOS 7 repositories, and we'll update the repo configuration.

---

Inspect repo file: `sudo grep -E 'mirrorlist|baseurl' /etc/yum.repos.d/CentOS-Base.repo`

Back up the repo file: `sudo cp /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.bak`

---

Replace mirrorlist= lines with comments and uncomment the baseurl= lines:

`sudo sed -i -e 's/^mirrorlist=/#mirrorlist=/' -e 's|^#baseurl=http://mirror.centos.org|baseurl=http://vault.centos.org|' /etc/yum.repos.d/CentOS-Base.repo`

---

Verify: `grep -E 'mirrorlist|baseurl' /etc/yum.repos.d/CentOS-Base.repo`

You should now see something similar to:
```
#mirrorlist=http://mirrorlist.centos.org/...
baseurl=http://vault.centos.org/centos/$releasever/os/$basearch/

#mirrorlist=http://mirrorlist.centos.org/...
baseurl=http://vault.centos.org/centos/$releasever/updates/$basearch/

#mirrorlist=http://mirrorlist.centos.org/...
baseurl=http://vault.centos.org/centos/$releasever/extras/$basearch/
```

---

Then clean and rebuild yum metadata: `sudo yum clean all` and `sudo yum makecache`

Finally you can update: `sudo yum update -y` or `sudo yum update`
