
## Port forwarding to access wsl2
```
Get wsl2 eth0 ip address : On wsl2, "ip addr" or hostname -I : ex : 172.27.57.198
Powershell as administrator :
PS C:\WINDOWS\system32> netsh interface portproxy add v4tov4 listenport=8081 listenaddress=0.0.0.0 connectport=8081 connectaddress=172.27.57.198
PS C:\WINDOWS\system32> netsh interface portproxy show v4tov4

Écouter sur ipv4 :             Connecter à ipv4 :

Adresse         Port        Adresse         Port
--------------- ----------  --------------- ----------
0.0.0.0         8081        172.27.57.198   8081

New-NetFirewallRule -DisplayName "Port8081" -Direction Inbound -Action Allow -Protocol TCP -LocalPort 8081
```
