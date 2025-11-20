
## Port forwarding to access wsl2
```
Get wsl2 eth0 ip address : On wsl2, "ip addr" or hostname -I : ex : 172.27.57.198
Powershell as administrator :
PS C:\WINDOWS\system32> netsh interface portproxy add v4tov4 listenport=8001 listenaddress=0.0.0.0 connectport=8001 connectaddress=172.27.57.198
PS C:\WINDOWS\system32> netsh interface portproxy show v4tov4

Écouter sur ipv4 :             Connecter à ipv4 :

Adresse         Port        Adresse         Port
--------------- ----------  --------------- ----------
0.0.0.0         8001        172.27.57.198   8001
```
