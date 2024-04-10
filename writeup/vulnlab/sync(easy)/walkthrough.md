# Sync (easy)

## port scan


![](walkthrough_20240410115753112.png)

## service enumeration


![](walkthrough_20240410120039033.png)

## rsync discover

since there have a serivce called rsync

![](walkthrough_20240410115814113.png)

```
@RSYNCD: 31.0
#list
```


![](walkthrough_20240410115929232.png)

and we have `web backup`

more enumeration

![](walkthrough_20240410120220313.png)

dump web backup

```bash
rsync -av rsync://$target/httpd  ./backup
```


![](walkthrough_20240410120309551.png)


## crack custom encrypt hash

on db folder, we can see there have a site.db. 

triss:a0de4d7f81676c3ea9eabcadfd2536f6
admin:7658a2741c9df3a97c819584db6e6b3c


![](walkthrough_20240410120524045.png)

on index.php, we can see how the webapp encrypt password

![](walkthrough_20240410120821714.png)

![](walkthrough_20240410121119506.png)

so we can use cracken generate a wordlist

```bash
cracken -w /usr/share/wordlists/rockyou.txt '6c4972f3717a5e881e282ad3105de01e|triss|?w1' > wordlist
```

![](walkthrough_20240410121340430.png)

cracked !

![](walkthrough_20240410121428033.png)


## ssh login as triss

we can use cracked cred login into ftp service

![](walkthrough_20240410121931094.png)

seems we are in /home/triss/

![](walkthrough_20240410121956561.png)

generate ssh key pair

![](walkthrough_20240410122119634.png)

```bash
mkdir .ssh
put id_rsa.pub ./authorized_keys
chmod 0644 ./authorized_keys
```

![](walkthrough_20240410122340358.png)


## login as jennifer

there have a user called jennifer found on /home folder
.we can use same cred login as jennifer


![](walkthrough_20240410122606932.png)


## privilege escalation

![](walkthrough_20240410123017067.png)


![](walkthrough_20240410123101640.png)

mabey some crontab stuff running? i will upload pspy

yes, we can see there have a backup script running

![](walkthrough_20240410124221907.png)

![](walkthrough_20240410124314859.png)

this script will create a `/tmp/backup` folder and add some file into. and then zip all file into /backup folder

we can just create a /tmp/backup too, and then link to flag file or ssh key. the script will backup file which we want

![](walkthrough_20240410124741780.png)

![](walkthrough_20240410125230336.png)