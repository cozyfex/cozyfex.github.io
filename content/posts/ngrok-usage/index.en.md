---
title: "ngrok Usage"

date: 2021-05-25T16:25:42+09:00

categories:

- ngrok
- local
- test
- usage

tags:

- ngrok
- local
- test
- usage

keywords:

- ngrok
- local
- test
- usage

---

## What is ngrok?

{{<admonition note ngrok true>}}  
One command for an instant, secure URL to your localhost server through any NAT or firewall.  
Go to [ngrok](https://ngrok.com/)  
{{</admonition>}}

## Install ngrok

### Download

{{<admonition note Download true>}}  
You need to download `ngrok` from official site.  
{{</admonition>}}

### Move `ngrok`

```shell
# Create directories
mkdir ~/ngrok
mkdir ~/ngrok/bin

# Move
mv ~/Downloads/ngrok ~/ngrok/bin/

# Create a symbolic link
ln -s ~/ngrok/bin/ngrok /usr/local/bin/ngrok
```

### Connect Account

1. `Sign Up` or `Login`
2. Go to `Setup & Installation` under `Getting Started`
3. Check your `authtoken`
4. Execute below command

```shell
ngrok authtoken <your-authtoken>
```

## Listen Http

```shell
ngrok http 80
```

```shell
ngrok by @inconshreveable

Session Status                online
Account                       <your-account>
Version                       2.3.40
Region                        United States (us)
Web Interface                 http://127.0.0.1:4040
Forwarding                    http://3181a7385cf5.ngrok.io -> http://localhost:80
Forwarding                    https://3181a7385cf5.ngrok.io -> http://localhost:80

Connections                   ttl     opn     rt1     rt5     p50     p90
                              0       0       0.00    0.00    0.00    0.00
```

