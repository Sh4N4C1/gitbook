# baby (easy)

## port scan

![](baby\(easy\)/walkthrough\_20240409112957889.png)

88 port open, this maybe Domain Controller

## service enumeration

no smb

![](baby\(easy\)/walkthrough\_20240409113225381.png)

name is babydc.baby.vl

![](baby\(easy\)/walkthrough\_20240409113316354.png)

ldap allow anonymouse search

![](baby\(easy\)/walkthrough\_20240409114101595.png)

we have cred `BabyStart123!`

![](baby\(easy\)/walkthrough\_20240409114243357.png)

## change user password

baby.vl\Caroline.Robinson:BabyStart123! STATUS\_PASSWORD\_MUST\_CHANGE which mean we must change this user password. we can use smbpasswd

![](baby\(easy\)/walkthrough\_20240409114900271.png)

![](baby\(easy\)/walkthrough\_20240409115331101.png)

## winrm

after change Caroline.Robinson password, we can winrm into target

![](baby\(easy\)/walkthrough\_20240409115446480.png)

## root

we have SeBackupPrivilege

![](baby\(easy\)/walkthrough\_20240409115714797.png)

we have use robocopy to read flag.there are many way to use SeBackupPrivilege.this is just easy way

![](baby\(easy\)/walkthrough\_20240409122412671.png)
