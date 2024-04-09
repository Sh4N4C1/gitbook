# Escape (easy)

## port scan

just 3389 (rdp) open

![](walkthrough_20240409141150713.png)

## rdp

```bash
rdesktop 10.10.111.132
```

![](walkthrough_20240409141244709.png)

## bypass sandbox

kioskuser0 user exist

```bash
xfreerdp /u:"kioskuser0" /p:"" /v:"$target"
```

it is edge

![](walkthrough_20240409142512714.png)

![](walkthrough_20240409144252907.png)

![](walkthrough_20240409144600943.png)

looks like we can't fond everything. it seems a sandbox

but we can launch edge browser with press [win] and then press [win + s]

![](walkthrough_20240409151017321.png)

we can read file
![](walkthrough_20240409151111709.png)

![](walkthrough_20240409151158212.png)

we can download cmd.exe

![](walkthrough_20240409151605610.png)

we can't use right click to rename cmd.exe
but we can use F2 shotcut rename it. the only application can run which is `msedge.exe`

![](walkthrough_20240409152532818.png)

## read rdp password

get beacon

![](walkthrough_20240409152924321.png)

and there have a hidden folder

![](walkthrough_20240409153914792.png)

![](walkthrough_20240409153933974.png)

looks like rdp config file. And the rdp software is called "Remote Desktop Plus"

![](walkthrough_20240409154014834.png)

launch rdp.exe

![](walkthrough_20240409154426037.png)

and import profile

![](walkthrough_20240409154541207.png)

but we can't see password

![](walkthrough_20240409154806642.png)

we can open edit profile
and then open this tool to show password

http://www.nirsoft.net/utils/bullets_password_view.html

![](walkthrough_20240409161421304.png)

```powershell
runas /user:admin powershell.exe
```

![](walkthrough_20240409160054253.png)

we are in administrator group. but there is UAC. we must bypass UAC

## bypass UAC

![](walkthrough_20240409160255182.png)

![](walkthrough_20240409160328547.png)

![](walkthrough_20240409160347414.png)

![](walkthrough_20240409160426073.png)
