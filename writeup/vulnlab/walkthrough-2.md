# Escape (easy)

## port scan

just 3389 (rdp) open

![](escape\(easy\)/walkthrough\_20240409141150713.png)

## rdp

```bash
rdesktop 10.10.111.132
```

![](escape\(easy\)/walkthrough\_20240409141244709.png)

## bypass sandbox

kioskuser0 user exist

```bash
xfreerdp /u:"kioskuser0" /p:"" /v:"$target"
```

it is edge

![](escape\(easy\)/walkthrough\_20240409142512714.png)

![](escape\(easy\)/walkthrough\_20240409144252907.png)

![](escape\(easy\)/walkthrough\_20240409144600943.png)

looks like we can't fond everything. it seems a sandbox

but we can launch edge browser with press \[win] and then press \[win + s]

![](escape\(easy\)/walkthrough\_20240409151017321.png)

we can read file ![](escape\(easy\)/walkthrough\_20240409151111709.png)

![](escape\(easy\)/walkthrough\_20240409151158212.png)

we can download cmd.exe

![](escape\(easy\)/walkthrough\_20240409151605610.png)

we can't use right click to rename cmd.exe but we can use F2 shotcut rename it. the only application can run which is `msedge.exe`

![](escape\(easy\)/walkthrough\_20240409152532818.png)

## read rdp password

get beacon

![](escape\(easy\)/walkthrough\_20240409152924321.png)

and there have a hidden folder

![](escape\(easy\)/walkthrough\_20240409153914792.png)

![](escape\(easy\)/walkthrough\_20240409153933974.png)

looks like rdp config file. And the rdp software is called "Remote Desktop Plus"

![](escape\(easy\)/walkthrough\_20240409154014834.png)

launch rdp.exe

![](escape\(easy\)/walkthrough\_20240409154426037.png)

and import profile

![](escape\(easy\)/walkthrough\_20240409154541207.png)

but we can't see password

![](escape\(easy\)/walkthrough\_20240409154806642.png)

we can open edit profile and then open this tool to show password

http://www.nirsoft.net/utils/bullets\_password\_view.html

![](escape\(easy\)/walkthrough\_20240409161421304.png)

```powershell
runas /user:admin powershell.exe
```

![](escape\(easy\)/walkthrough\_20240409160054253.png)

we are in administrator group. but there is UAC. we must bypass UAC

## bypass UAC

![](escape\(easy\)/walkthrough\_20240409160255182.png)

![](escape\(easy\)/walkthrough\_20240409230708463.png)

![](escape\(easy\)/walkthrough\_20240409160347414.png)

![](escape\(easy\)/walkthrough\_20240409160426073.png)
