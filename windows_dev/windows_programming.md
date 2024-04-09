# Microsoft Windows Programming

## Windows Data Types

`DWORD` - 0 up to 4,294,967,295

```c
DWORD dwVariable = 42;
```

`SIZE_T` - Size Type - Used to represent the size of an object in bytes

```c
SIZE_T stVariable = sizeof(int);
```

`VOID` - Used to represent the absence of type

```c
VOID vVariable = NULL;
```

`LPVOID` - Long Pointer to Void - Used to represent a pointer to any type

```c
LPVOID lpvVariable = NULL;
```

`HANDLE` - Used to represent a handle to an object

```c
HANDLE hVariable = CreateFile(...);
```

`LPCSTR` - Long Pointer to Constant String - Used to represent a pointer to a constant string

```c
LPCSTR lpcstrVariable = "Hello, World!";
```

`PCSTR` - Pointer to Constant String - Used to represent a pointer to a constant string

```c
PCSTR pcstrVariable = "Hello, World!";
```

`LPWSTR` - Long Pointer to Wide String - Used to represent a pointer to a wide string

```c
LPWSTR lpwstrVariable = L"Hello, World!";
```

`ULONG_PTR` - Unsigned Long Pointer - Used to represent an unsigned long pointer

```c
PVOID Pointer = malloc(100);
Pointer = (ULONG_PTR)Pointer + 10;
```

## ANSI and Unicode Data Types

`ANSI` - American National Standards Institute - Uses 1 byte to represent a character
`Unicode` - Uses 2 bytes to represent a character

```c
char cVariable = 'A';
wchar_t wcVariable = L'A';
```

## Windows api error

5 - Access Denied
2 - File Not Found
3 - Path Not Found
6 - Invalid Handle
8 - Not Enough Memory
87 - Invalid Parameter

## Windows NtStatus

0xC0000005 - Access Violation
0xC0000006 - In Page Error
0xC0000007 - Page File Error
0xC0000008 - Invalid Handle
0xC0000009 - Bad Initial Stack
0xC000000A - Bad Initial PC
0xC000000B - Invalid Object
0xC000000C - Invalid Object Type
0xC000000D - No Such File
0xC000000E - Stack Overflow
0xC000000F - Too Many Modules
0xC0000010 - File Not Found
0xC0000011 - Bad File Type
0xC0000012 - Not A Directory
0xC0000013 - Bad Data
0xC0000014 - Port Not Set
0xC0000015 - Port Not Canceled
0xC0000016 - Invalid Port Attributes
0xC0000017 - Port Closed
0xC0000018 - Invalid Device Request
0xC0000019 - Invalid Transport Request
0xC000001A - No Such Device
0xC000001B - Device Not Ready
0xC000001C - Invalid Device State
0xC000001D - No Memory
0xC000001E - No More Entries
0xC000001F - Not All Assigned
0xC0000020 - Some Not Mapped
0xC0000021 - Oplock Break In Progress
0xC0000022 - Volume Mounted
0xC0000023 - Too Many Links
0xC0000024 - Old Name
0xC0000025 - Directory Not Empty
0xC0000026 - Filename Exced Range
0xC0000027 - Delete Pending
0xC0000028 - Incompatible With Global Short Name Registry Setting
0xC0000029 - Short Name Not Allowed
0xC000002A - Cannot Delete
0xC000002B - Cannot Read
0xC000002C - Configuration Conflict
0xC000002D - Cannot Write
0xC000002E - Unknown Revision
0xC000002F - Revision Mismatch
0xC0000030 - Invalid Owner
0xC0000031 - Invalid Primary Group
0xC0000032 - No Impersonation Token
0xC0000033 - Cant Disable Mandatory
0xC0000034 - No Logon Servers
0xC0000035 - No Sids
0xC0000036 - Unknown Package
0xC0000037 - Unknown Package Type
0xC0000038 - Logon Session Exists
0xC0000039 - Logon Session Not Found
0xC000003A - Package Already Exists
0xC000003B - Package Does Not Exist
0xC000003C - Package Not Installed
0xC000003D - Invalid Package
0xC000003E - Invalid Package Type
0xC000003F - Cannot Load Registry File
0xC0000040 - Debug Error
0xC0000041 - Error

```c
#define NT_SUCCESS(Status) (((NTSTATUS)(Status)) >= 0)
NTSTATUS Status = NtCreateFile(...);
if (!NT_SUCCESS(Status))
{
    printf("Error: 0x%08X\n", Status);
}
```
