### 1：Generating a new SSH key

you can choose blow one way to gen ssh-key

If your system support the Ed25519 algorithm, use:

> ssh-keygen -t ed25519 -C "your_email@example.com"


If your system doesn't support the Ed25519 algorithm, use:

> ssh-keygen -t rsa -b 4096 -C "your_email@example.com"

```shell
aaron@pc:~$ ssh-keygen -t rsa -b 4096 -C "tom@tom.com"
Generating public/private rsa key pair.
Enter file in which to save the key (/home/aaron/.ssh/id_rsa):
Created directory '/home/aaron/.ssh'.
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/aaron/.ssh/id_rsa
Your public key has been saved in /home/aaron/.ssh/id_rsa.pub
The key fingerprint is:
SHA256:dadahqwakdnbswjdakn tom@tom.com
The key's randomart image is:
+---[RSA 4096]----+
|  .   = +.  .oo  |
|   o + = + o   ..|
|+ . o B + + o  oo|
|oO   = + = o    +|
|B o . . S o   .oo|
|.o   . o . . . .+|
|o +   o     .    |
| B     .         |
|E .              |
+----[SHA256]-----+

```

add id_rsa_pub text-characters to Github 

- open https://github.com/settings/ssh/new (the path is settings -> SSH and GPG keys -> SSH Keys[New SSH key])

- then cat && copy `/home/aaron/.ssh/id_rsa.pub` content to textbox

![Logo](https://github.com/rsgoo/learn-notes/blob/master/ZoneForResource/github-add-ssh-key.png)

#### additional notes

how to check system whether support `Ed25519 algorithm`

> ssh -Q key

```shell
> ssh -Q key
ssh-ed25519
ssh-ed25519-cert-v01@openssh.com
sk-ssh-ed25519@openssh.com
sk-ssh-ed25519-cert-v01@openssh.com
ssh-rsa
ssh-dss
ecdsa-sha2-nistp256
ecdsa-sha2-nistp384
ecdsa-sha2-nistp521
sk-ecdsa-sha2-nistp256@openssh.com
ssh-rsa-cert-v01@openssh.com
ssh-dss-cert-v01@openssh.com
ecdsa-sha2-nistp256-cert-v01@openssh.com
ecdsa-sha2-nistp384-cert-v01@openssh.com
ecdsa-sha2-nistp521-cert-v01@openssh.com
sk-ecdsa-sha2-nistp256-cert-v01@openssh.com
```







