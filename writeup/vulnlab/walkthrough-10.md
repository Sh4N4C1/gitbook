# Trusted (chain) (easy)

## port scan

![](trusted\(chain\)\(easy\)/walkthrough\_20240410162803244.png)

![](trusted\(chain\)\(easy\)/walkthrough\_20240410162812364.png)

## service enumeration

![](trusted\(chain\)\(easy\)/walkthrough\_20240410164028145.png)

![](trusted\(chain\)\(easy\)/walkthrough\_20240410164210710.png)

## LFI

we have lfi

![](trusted\(chain\)\(easy\)/walkthrough\_20240410164731750.png)

![](trusted\(chain\)\(easy\)/walkthrough\_20240410164908221.png)

work

![](trusted\(chain\)\(easy\)/walkthrough\_20240410164913245.png)

there have a db.php

![](trusted\(chain\)\(easy\)/walkthrough\_20240410171539110.png)

we can use

```
php://filter/convert.base64-encode/resource=C:\xampp\htdocs\dev\db.php
```

![](trusted\(chain\)\(easy\)/walkthrough\_20240410171731719.png)

and we have sql cred

![](trusted\(chain\)\(easy\)/walkthrough\_20240410171910453.png)

![](trusted\(chain\)\(easy\)/walkthrough\_20240410171934094.png)

on news DB, we can see some cred

![](trusted\(chain\)\(easy\)/walkthrough\_20240410172013228.png)

crackstation show us some cracked hash `rsmith:IHateEric2`

![](trusted\(chain\)\(easy\)/walkthrough\_20240410172106189.png)

work!

## system on lab

![](trusted\(chain\)\(easy\)/walkthrough\_20240410172407120.png)

we can write output file

![](trusted\(chain\)\(easy\)/walkthrough\_20240410172756353.png)

![](trusted\(chain\)\(easy\)/walkthrough\_20240410172832753.png)

get a beacon

![](trusted\(chain\)\(easy\)/walkthrough\_20240410173430248.png)

![](trusted\(chain\)\(easy\)/walkthrough\_20240410173448608.png)

## root on trusteddc

![](trusted\(chain\)\(easy\)/walkthrough\_20240410183758092.png)

since we are system user on labdc, we can dump KRBTGT user hash and create golden ticket with mimikatz

![](trusted\(chain\)\(easy\)/walkthrough\_20240410182139124.png)

![](trusted\(chain\)\(easy\)/walkthrough\_20240410182253693.png)

![](trusted\(chain\)\(easy\)/walkthrough\_20240410182532223.png)
