# Servidor Local (LAMP)

## Instalando un servidor web con Debian 11 en VirtualBox, sobre Windows 10.

### Instalar [VirtualBox]
Omito pasos para la instalación.

Configuración de VirtualBox:

- Menú Archivo -> Administrador de red anfitrión -> Propiedades
- Deshabilito el servidor DHCP y configuro manualmente las IP
- En dirección IPv4: 192.168.100.1
- En máscara de red IPv4: 255.255.255.0

### Descargo [Debian] y lo instalo en VirtualBox

Creo una nueva máquina con estas características:
- Memoria RAM: 1024MB
- Disco duro virtual 8GB (VDI)

Configuro dos adaptadores de red, lo que entiendo es que uno me servirá para conectarme a internet y el otro para entrar con ssh y poder ver mis direcciones IP (hosts virtuales) en el navegador del anfitrión.
- Red -> Adaptador 1
    - Conectado a: NAT
- Red -> Adaptador 2
    - Conectado a: Adaptador sólo-anfitrión
    - Nombre: VirtualBox Host-Only Ethernet Adapter

En la instalación omito la mayoría de pasos a seguir, pero los siguientes son importantes: 
- Configurar la red: utilizo la primera opción enp0s3
- Sin entorno gráfico 
- Instalar SSH
- Instalar utilidades estándar del sistema

### Arrancar Debian

Entro con mi usuario y contraseña, después hago actualizaciones como usuario `root` 

```sh
apt update && apt upgrade -y
```

Enseguida reviso los dos adaptadores de red que configuré en VirtualBox utilizando este comando:

```sh
ip a
```
Me aparece que tengo la red `lo` que corresponde a localhost, `enp0s3` ésta es la que configuré como NAT y es la que está conectada a internet y por último la `enp0s8` es la que configuré como Adaptador solo anfitrión y se encuentra desactivada. 

Tendré que editar este archivo como root  `/etc/network/interfaces` para agregarle una dirección IP y poder conectarme con ssh.
Con mi editor, entro al archivo y pongo estas líneas 
```sh
# Interfaz secundaria host-only
allow-hotplug enp0s8
iface enp0s8 inet static
    address 192.168.100.100
    netmask 255.255.255.0
```

Reinicio el servidor para que surtan efecto los cambios
```sh
reboot
```
Reviso nuevamente mis interfaces de res con
```sh
ip a
```
Y veo que `enps08` ya tiene asignada la IP. Con esto ya me puedo conectar desde Windows al server, por medio de ssh con mi terminal wsl de Windows. Para conectarme lo hago así:
```sh
ssh jose@192.168.100.100
y pongo el password
```
Agregaré otra IP como ejemplo para cuando configure un host virtual poder dirigirlo a esta IP y verlo en el navegador de mi máquina anfitrión. Edito el archivo `vim /etc/network/interfaces`
```sh
# Agregar direcciones IP
auto enp0s8:1
allow-hotplug enp0s8
iface enp0s8:1 inet static
    address 192.168.100.101/32
    netmask 255.255.255.0
```

Después reinicio Debian `reboot` para que surtan efecto los cambios.
### Instalé PHP 8.1 y Apache siguiendo estas instrucciones. [Instalación]

Para acceder al cli de PHP lo hago con el comando 
```sh
php -a
```



[Instalación]: https://es.linuxcapable.com/how-to-install-php-8-1-on-debian-11-bullseye/
[VirtualBox]: <https://www.virtualbox.org/>
[Debian]: <https://www.debian.org/index.es.html>
