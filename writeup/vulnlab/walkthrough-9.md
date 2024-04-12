# Retro (easy)

## port scan

![](retro\(easy\)/walkthrough\_20240410092844437.png)

## service enumeration

![](retro\(easy\)/walkthrough\_20240410092914346.png)

![](retro\(easy\)/walkthrough\_20240410093014102.png)

![](retro\(easy\)/walkthrough\_20240410093130625.png)

## brute force

since the admin say 'Dear Trainees' and 'bundle every one'

we can make a quicky username list

![](retro\(easy\)/walkthrough\_20240410093917705.png)

`trainee` user looks interesting. this account seems have weak password, because admin say `struggle with remembering strong passwords`

make a small password list

![](retro\(easy\)/walkthrough\_20240410095745113.png)

now we have valid cred `trainee@retro.vl:trainee`

we can use trainee user access Notes smb share

![](retro\(easy\)/walkthrough\_20240410094744130.png)

![](retro\(easy\)/walkthrough\_20240410094827163.png)

so we have some new username. `We should start with the pre created computer account` tell us we will focus on some account end with `$`

again, create a short username list

i think the `banking` user is computer account

banking user also have weak password `banking@retro.vl:banking`

![](retro\(easy\)/walkthrough\_20240410095639286.png)

## pre create computer account abuse

![](retro\(easy\)/walkthrough\_20240410102621504.png)

https://github.com/SecureAuthCorp/impacket/pull/1304

![](retro\(easy\)/walkthrough\_20240410103644145.png)

now we can access smb with banking$ user

![](retro\(easy\)/walkthrough\_20240410105140352.png)

![](retro\(easy\)/walkthrough\_20240410105323141.png)

![](retro\(easy\)/walkthrough\_20240410110521900.png)

## attack adcs

```
certipy-ad find -username 'banking$' -password 'banking123' -dc-ip $target -stdout -vulnerable
```

the ESC1 template is vulnerable, and computers account can enroll. we can use banking user finish this attack

![](retro\(easy\)/walkthrough\_20240410111750169.png)

![](retro\(easy\)/walkthrough\_20240410111954506.png)

![](retro\(easy\)/walkthrough\_20240410112055060.png)

![](retro\(easy\)/walkthrough\_20240410112320829.png)
