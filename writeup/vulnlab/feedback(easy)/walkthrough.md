# Feedback (easy)

## port scan


![](walkthrough_20240409163928953.png)

## service enumeration

apache tomcat looks interesting

we can't access tomcat manage page, so brute path


![](walkthrough_20240409170033880.png)

![](walkthrough_20240409170111102.png)

![](walkthrough_20240409170247355.png)


```
       <!-- Build with Java, Struts2 & Log4J -->     
```


there have Log4J,Struts2 software

## exploit

there is a Struts2 RCE
https://gist.github.com/shafdo/8ad7590ed1a08392cbf22d20a2fbb862

and we need to change url and proxy

![](walkthrough_20240409171250677.png)

launch!

![](walkthrough_20240409170906835.png)


# Post Exploitation

we should read tomcat config file beacuse there have tomcat manage page login cred

![](walkthrough_20240409171215560.png)

![](walkthrough_20240409171321964.png)


# Privilege Escalation

we can use cred found in tomcat-user.xml to login as root

get a reverse shell (ssh via key auth). and `su root`


![](walkthrough_20240409171659793.png)