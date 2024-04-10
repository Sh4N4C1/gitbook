# Retro (easy)

## port scan

![](walkthrough_20240410092844437.png)


## service enumeration

![](walkthrough_20240410092914346.png)

![](walkthrough_20240410093014102.png)

![](walkthrough_20240410093130625.png)


## brute force


since the admin say 'Dear Trainees' and 'bundle every one'

we can make a quicky username list

![](walkthrough_20240410093917705.png)

`trainee` user looks interesting. this account seems have weak password, because admin say `struggle with remembering strong passwords`

make a small password list

![](walkthrough_20240410095745113.png)

now we have valid cred `trainee@retro.vl:trainee`



we can use trainee user access Notes smb share

![](walkthrough_20240410094744130.png)

![](walkthrough_20240410094827163.png)

so we have some new username. `We should start with the pre created
computer account` tell us we will focus on some account end with `$`

again, create a short username list

i think the `banking` user is computer account

banking user also have weak password `banking@retro.vl:banking`

![](walkthrough_20240410095639286.png)

## pre create computer account abuse

![](walkthrough_20240410102621504.png)

https://github.com/SecureAuthCorp/impacket/pull/1304

![](walkthrough_20240410103644145.png)

now we can access smb with banking$ user

![](walkthrough_20240410105140352.png)


![](walkthrough_20240410105323141.png)


![](walkthrough_20240410110521900.png)

## attack adcs

```
certipy-ad find -username 'banking$' -password 'banking123' -dc-ip $target -stdout -vulnerable
```

the ESC1 template is vulnerable, and computers account can enroll. we can use banking user finish this attack

![](walkthrough_20240410111750169.png)

![](walkthrough_20240410111954506.png)

![](walkthrough_20240410112055060.png)

![](walkthrough_20240410112320829.png)