# Nmap Scanning & Service Vulnerability Checks

Ovaj dokument sadrži osnovne Nmap komande za skeniranje mrežnih servisa, identifikaciju verzija servisa i osnovnu provjeru ranjivosti.

---

## 2. Nmap Scanning

### 2.1 Full port scan targeta

```bash
sudo nmap -p- <IP>
```

Skenira sve portove (1–65535).

---

### 2.2 Detaljni scan (servis + verzija + OS)

```bash
sudo nmap -sS -sV -O <IP>
```

- SYN scan  
- detekcija verzije servisa  
- pokušaj detekcije operativnog sistema  

---

### 2.3 General vulnerability scan

```bash
sudo nmap --script vuln <IP>
```

Pokreće poznate vulnerability skripte.

---

## 21 FTP (Port 21)

```bash
sudo nmap -sV -p21 <IP>
sudo nmap --script ftp-anon,ftp-banner,ftp-syst -p21 <IP>
```

Ranjivo:
- anonymous login
- plain-text login
- stare verzije (npr. vsftpd 3.4 u lab primjerima)

Moguće:
- backdoor trojan
- remote shell
- kompromitacija sistema

Fix:
- koristiti FTPS ili SFTP
- ugasiti FTP ako nije potreban

---

## 22 SSH (Port 22)

```bash
sudo nmap -sV -p22 <IP>
sudo nmap --script ssh2-enum-algos -p22 <IP>
ssh -v user@<IP>
```

Ranjivo:
- SSHv1
- password authentication
- brute force moguć

Fix:
- SSH keys
- disable root login
- fail2ban

### Slabi key algoritmi

```
diffie-hellman-group1-sha1
diffie-hellman-group14-sha1
diffie-hellman-group-exchange-sha1
```

### Slabi server host key algoritmi

```
ssh-dss
ssh-rsa (SHA1 varijanta)
```

### Slabi encryption algoritmi

```
3des-cbc
des-cbc
blowfish-cbc
arcfour / rc4
*-cbc
```

### Slabi MAC algoritmi

```
hmac-md5
hmac-md5-96
hmac-sha1
hmac-sha1-96
umac-64@openssh.com
```

---

## 23 Telnet (Port 23)

```bash
sudo nmap -sV -p23 <IP>
```

Ranjivo:
- sav saobraćaj ide plain-text

Fix:
- ugasiti telnet
- koristiti SSH

---

## 80 HTTP (Port 80)

```bash
sudo nmap -sV -p80 <IP>
sudo nmap --script http-title,http-headers,http-methods,http-enum,http-robots.txt -p80 <IP>
sudo nmap --script http-trace -p80 <IP>
```

Ranjivo:
- HTTP bez enkripcije
- directory listing
- TRACE metoda

Ako vidiš metode:
```
PUT, DELETE, TRACE, CONNECT
```
moguća modifikacija sadržaja ili XST napad.

### /server-status

Apache modul `mod_status`.

Ako je javno dostupan može otkriti:
- aktivne requestove
- IP adrese klijenata
- URL-ove koje korisnici posjećuju

Fix:
- onemogućiti mod_status
- dozvoliti samo localhost ili admin IP

---

## 443 HTTPS (Port 443)

```bash
sudo nmap -sV -p443 <IP>
sudo nmap --script ssl-cert,ssl-enum-ciphers -p443 <IP>
```

Ranjivo:
- SSLv3
- TLS 1.0
- slabi cipheri
- istekao certifikat

Fix:
- modern TLS
- zabraniti stare protokole

---

## 445 SMB (Port 445)

```bash
sudo nmap -sV -p445 <IP>
sudo nmap --script smb-os-discovery,smb-security-mode -p445 <IP>
sudo nmap --script smb-vuln* -p445 <IP>
sudo nmap --script smb-protocols -p445 <IP>
```

Ranjivo:
- SMBv1
- MS17-010 (EternalBlue)
- SMB Ghost

Fix:
- patch Windows
- disable SMBv1
- blokirati port 445 firewallom

---

## 3389 RDP (Port 3389)

```bash
sudo nmap -sV -p3389 <IP>
sudo nmap --script rdp-enum-encryption -p3389 <IP>
```

Ranjivo:
- slaba enkripcija
- BlueKeep (stari sistemi)

Fix:
- update sistema
- NLA (Network Level Authentication)
- firewall pravila

---

## 3306 MySQL (Port 3306)

```bash
sudo nmap -sV -p3306 <IP>
sudo nmap --script mysql-info -p3306 <IP>
```

Ranjivo:
- otvoren prema mreži
- root bez lozinke

Fix:
- jaka lozinka
- bind na localhost
- firewall blokada

---

## 5432 PostgreSQL (Port 5432)

```bash
sudo nmap -sV -p5432 <IP>
sudo nmap --script pgsql-info -p5432 <IP>
```

Provjera verzije i dostupnosti PostgreSQL servisa.
