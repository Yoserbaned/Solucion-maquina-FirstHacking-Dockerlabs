# Solucion-maquina-FirstHacking-Dockerlabs

Esta maquina/laboratorio controlado, se aplican tecnicas de pen testing basicas. que va desde el reconocimiento de puertos y versiones , hasta   usar un exploit para la version desactualizada del servicio ftp para despues, iniciar una sesion meterpretter con metasploit y despues de ahi, tener el control total de los archivos de la maquina objetivo.


# 1. RECONOCIMIENTNTO
-utilizamos la herramienta de nmap con el siguiente comando :

 nmap -T5 -sV  172.17.0.2
Starting Nmap 7.95 ( https://nmap.org ) at 2025-12-23 21:36 -04
Nmap scan report for 172.17.0.2
Host is up (0.000015s latency).
Not shown: 999 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 2.3.4
MAC Address: 02:42:AC:11:00:02 (Unknown)
Service Info: OS: Unix

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 0.60 seconds
