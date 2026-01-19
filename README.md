2.1 Full port scan targeta
sudo nmap -p- <IP>

2.2 Detaljni scan (servis + verzija + OS)
sudo nmap -sS -sV -O <IP>

2.3 General vuln scan
sudo nmap --script vuln <IP>

21 FTP
sudo nmap -sV -p21 <IP>
sudo nmap --script ftp-anon,ftp-banner,ftp-syst -p21 <IP>
Ranjivo: anonymous login / plain-text login
Fix: FTPS/SFTP, ugasiti FTP ako ne treba
ranjiv ako je :vsftpd.3.4 (poznat primjer u labovima)
Backdoor trojan (Remote shell) + kompromitacija sistema

22 SSH
sudo nmap -sV -p22 <IP>
sudo nmap --script ssh2-enum-algos -p22 <IP>
ssh -v user@<IP>
Ranjivo: SSHv1 / password auth / brute force
Fix: SSH keys + disable root + fail2ban
key algoritmhms:
diffie-hellman-group1-sha1
diffie-hellman-group14-sha1
diffie-hellman-group-exchange-sha1

sever host key alg:
ssh-dss (DSA – jako loše i zastarjelo)
ssh-rsa (ako je SHA1 varijanta, često se smatra zastarjelim)

ecnr:
3des-cbc
des-cbc
blowfish-cbc
arcfour / rc4
bilo šta *-cbc (CBC je staro, nije nužno “hack”, ali je minus)

mac:
hmac-md5
hmac-md5-96
hmac-sha1
hmac-sha1-96
umac-64@openssh.com

23 Telnet
sudo nmap -sV -p23 <IP>
Ranjivo: sve plain-text
Fix: ugasiti telnet, koristiti SSH

80 HTTP
sudo nmap -sV -p80 <IP>
sudo nmap --script http-title,http-headers,http-methods,http-enum,http-robots.txt -p80 <IP>
sudo nmap --script http-trace -p80 <IP>
Ranjivo: HTTP bez enkripcije / directory listing / TRACE
Fix: HTTPS + update server + ugasiti listing
Ako vidiš metode tipa:PUT,DELETE,TRACE,CONNECT
to je ozbiljnije (moguć upload/modifikacija ili XST).

Šta znači?
/server-status je Apache modul mod_status.
Zašto je to ranjivo?
Ako je dostupan bez zaštite, može pokazati:
aktivne requestove
IP adrese klijenata
URL-ove koje ljudi posjećuju 
onemogućiti mod_status ili dozvoliti samo localhost/admin IP:informacije o serveru i performansama





443 HTTPS
sudo nmap -sV -p443 <IP>
sudo nmap --script ssl-cert,ssl-enum-ciphers -p443 <IP>
Ranjivo: SSLv3/TLS1.0, slabi cipheri, istekao cert
Fix: zabraniti stare protokole, modern TLS

445 SMB
sudo nmap -sV -p445 <IP>
sudo nmap --script smb-os-discovery,smb-security-mode -p445 <IP>
sudo nmap --script smb-vuln* -p445 <IP>
sudo nmap --script smb-protocols -p445 <IP>
Ranjivo: SMBv1 (MS17-010/EternalBlue), SMB Ghost
Fix: patch Windows + disable SMBv1 + blokirati 445

3389 RDP
sudo nmap -sV -p3389 <IP>
sudo nmap --script rdp-enum-encryption -p3389 <IP>
Ranjivo: slaba enkripcija / BlueKeep stari sistemi
Fix: update, NLA, firewall

3306 MySQL
sudo nmap -sV -p3306 <IP>
sudo nmap --script mysql-info -p3306 <IP>
Ranjivo: otvoren prema mreži / root bez šifre
Fix: lozinka + bind localhost + firewall block

5432 PostgreSQL
sudo nmap -sV -p5432 <IP>
sudo nmap --script pgsql-info -p5432 <IP>

