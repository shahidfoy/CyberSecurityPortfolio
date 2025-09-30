# Web Scanners

## skipfish

example commands:
```bash
sudo skipfish -o /home/l/Desktop/skipfishr/ https://0ae6005c03bdaa838044b21c00ee00d9.web-security-academy.net
```

## dirb

example commands:
```bash
dirb https://0ae7006103b1588d8172433c00ed00a4.web-security-academy.net/
```

```bash
dirb http://192.168.1.224/ /usr/share/wordlists/dirb/common.txt
```


## feroxbuster 
https://github.com/epi052/feroxbuster

word list repository:
https://github.com/danielmiessler/SecLists
https://github.com/danielmiessler/SecLists/blob/master/Discovery/Web-Content/common.txt

example commands:
```bash
feroxbuster --url https://target-url.com --depth 2 --wordlest words
```

```bash
feroxbuster -u https://0a6a001404c9fbb4815157ad00500079.web-security-academy.net/ -w /home/l/Desktop/common.txt
```

