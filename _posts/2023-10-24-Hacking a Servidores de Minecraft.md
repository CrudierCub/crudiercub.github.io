---
title: Hackig a servidores de Minecraft
date: 2023-10-24
categories: [Hacking, Minecraft]
tags: [hacking, minecraft]
---

Buenaass, en este post estaremos hablando y explotando una vulnerabilidad conocida como 
**Bungee Exploit** la cual nos permite burlar la seguridad de una *Network*. A pesar de que
esta vulnerabilidad es muy antigua, sigue funcionando y no hay mucha información sobre ella, 
por lo que explicare sobre como funciona y como explotarla.

## ¿Qué es el Bungee Exploit?
El Bungee Exploit es una vulnerabilidad en las versiones 
[BungeeCoord](https://www.spigotmc.org/wiki/bungeecord/) y 
[Velocity](https://papermc.io/software/velocity),
estas versiones son usadas para crear una *Network* la cual conecta varios servidores de 
Minecraft entre si, Esta vulnerabilidad consiste conectarse directamente a un servidor 
evadiendo la verificación de la Network

## ¿Cómo funciona?
La vulneravilidad es posible ya que los servidores conectados al Bugeecord o Velocity 
unicamente verifican que el usuario se conecte primero a una proxy (no necesariamente al 
Bungeecord o Velocity propio) y luego al servidor de Minecraft

# Explotacion


## Descargando herramientas
Para explotar esta vulnerabilidad usaremos una herramienta llamada [MCPTool](https://www.mcptool.net)
desarollada por [wrrulos](https://github.com/wrrulos). Esta herramienta crea una proxy la cual
redirige nuestra conexión al servidor vulnerable que queremos entrar 

Para descargrla desde GitHub debemos usar el siguiente comando:
```bash
git clone https://github.com/wrrulos/MCPTool.git && cd MCPTool
```
## Instalando dependencias
Ahora debemos instalar las dependencias de la herramienta, para ello usaremos el siguiente comando:
```bash
python3 setup.py
```

### Intalando Python
En caso de que no tengas python instalado o prefieres realizar una instalacion manual, puedes
seguir los siguiente pasos:

Para instalar Python en **Windows** debes descargarlo desde la página oficial:
[https://www.python.org/](https://www.python.org/)

Para instalar Python en **Linux**, puede usar el siguiente comando:
```bash
sudo apt install python3-pip
```

### Instalando NodeJS
Para instalar NodeJS en **Windows** debes descargarlo desde la página oficial:
[https://nodejs.org/es/](https://nodejs.org/es/)

Para instalar NodeJS en **Linux**, puede usar el siguiente comando:
```bash
sudo apt install nodejs -y && sudo apt install npm -y
```

### Instalando Java 17
Para instalar Java 17 en **Windows** debes descargarlo desde la página oficial:
[https://www.oracle.com/java/technologies/javase/jdk17-archive-downloads.html](https://www.oracle.com/java/technologies/javase/jdk17-archive-downloads.html)

Para instalar Java 17 en **Linux**, puede usar el siguiente comando:
```bash
sudo apt install openjdk-17-jdk openjdk-17-jre
```

### Instalando Nmap
Para instalar Nmap en **Windows** debes descargarlo desde la página oficial:
[https://nmap.org/download.html](https://nmap.org/download.html)

Para instalar Nmap en **Linux**, puede usar el siguiente comando:
```bash
sudo apt-get install nmap
```
### Instalando Ngrok
Para instalar Ngrok en **Windows** debes descargarlo desde la página oficial:
[https://ngrok.com/download](https://ngrok.com/download)

Para instalar Ngrok en **Linux**, puede usar el siguiente comando:
```bash
curl -s https://ngrok-agent.s3.amazonaws.com/ngrok.asc | sudo tee /etc/apt/trusted.gpg.d/ngrok.asc >/dev/null && echo "deb https://ngrok-agent.s3.amazonaws.com buster main" | sudo tee /etc/apt/sources.list.d/ngrok.list && sudo apt update && sudo apt install ngrok
```

## Usando la herramienta
Ahora que tenemos todo lo necesario para usar la herramienta, podemos ejecutarla usando el siguiente comando para iniciarla:
```bash
python3 main.py
```

Una vez iniciada la herramienta, comprobara que todas las dependencias esten instaladas, 
en caso de que no lo esten, puedes repetir los pasos anteriores para instalarlas
![MCPTool](https://crudiercub.github.io/assets/images/HackingMinecraftServers/MCPTool.JPG "MCPTool")

### Escaneando el servidor
Ahora que tenemos la herramienta iniciada, debemos escanear el servidor donde estamos 
realizando las pruebas, para eso debemos obtener la direccion **IP numerica** con el comando
*server*, como se muestra en la siguiente imagen:

![MCPTool](https://crudiercub.github.io/assets/images/HackingMinecraftServers/server.JPG "MCPTool")

Ahora que sabemos que el servidor utiliza Bungeecord (informacion que nos servira despues) 
y que la direccion IP es 212.193.3.96, despues debemos escanear el servidor 
con el comando **scan < ip > < puertos > < escaner > < bot >**. Antes de usar el comando
explica cada uno de los parametros:

#### IP
Ingresa un rango de IP's numericas como 127.0.0.1-255  
Ingresa una IP numerica como 127.0.0.1

#### Rango de puertos
Ingresa un unico puerto como 25565  
Ingresa un rango de puertos como 25565-25585

#### Escaner
Puedes realizar el escaneo utilizando diferentes escaners  
Escaners validos:
- **nmap** (0)
- **Quboscanner** (1)
- **Masscan** (2)

Este parametro acepta tanto el nombre como el numero del escaner

#### Bot
Aqui debes ingresar *y* (yes) o *n* (no) dependiendo si quieres que un bot se conecte al servidor


# Siguiendo con el escaneo
Ahora que sabemos que es lo que hace el comando y que significa cada parametro, podemos continuar
con el escaneo del servidor

![MCPTool](https://crudiercub.github.io/assets/images/HackingMinecraftServers/scan.JPG "MCPTool")

El resultado del escaneo nos muestra que se encontro un servidor en el puerto 25575 y otro, 
Recordemoso que el servidor usa Bungeecord como vimos al usar el comando *server*, Pero ahora 
en el apartado **checker** nos muestra que el servidor es vulnerable a Bungee Exploit  
Por esta razon es util usar *"y"* en el parametro **bot**, para comprobar si el servidor es vulnerable
o no y tener una lista de los usuarios conectados y su **UUID** que nos servira despues

> Yo ya conocia la ip y puerto del servidor desde antes del escaneo, pero el comando scan te puede revelar esta informacion
{: .prompt-info }

### Creando la proxy
Para crear el servidor proxy que redirigira nuestra conexion al servidor especificado, tenemos 2
comandos disponibles: *Waterfall* y *Velocity*, que corresponden a Bungeecord y Velocity 
respectivamente, si conoces si el servidor usa BungeeCord o Velocity, puedes usar el comando 
correspondiente, de lo contraria puedes intentar con los 2 comandos hasta que alguno funcione.
Ambos comandos funcionan de la misma manera, pero *Velocity* tiene 
un parametro extra el cual es *< forwarding-mode >* el cual tienes los 
siguientes modos validos:
- **none**
- **legacy**
- **BungeeCord**
- **BungeeGuard**  

Esta opcion depende mas que nada de la confguracion del servidor, pero esa informacion 
no la tenemos, por lo tanto para poder saber que modo usar, puedes probar con cada una de
las opciones hasta que alguna te funcione  
En mi caso usare el comando *Waterfall* ya que al momento de hacer el escaneo, se encontro
que el servidor usa Bungeecord

![MCPTool](https://crudiercub.github.io/assets/images/HackingMinecraftServers/waterfall.JPG "MCPTool")

### Conectandonos al servidor
Despues de haber creado un servidor en el puerto 25570 de nuestro localhost, ahora
podemos conectarnos a esa direccion para ser redirigido al servidor que especificamos

![MCPTool](https://crudiercub.github.io/assets/images/HackingMinecraftServers/connect.JPG "Minecraft")

Como podemos ver, nos conectamos al servidor y nos redirigio al servidor especificado, evitando el
mensaje *"If you wish IP fowarding, please enable it in your BungeeCord config as well!"* que nos
aparce si nos intentamos conectar directamente al servidor sin seguir los pasos anteriores  
Ahora que estamos conectados al servidor, lo mas probable es que nos pida iniciar sesion o registrarnos
ya que el servidor puede contar con plugins de autenticacion, el plugin mas usando es 
[AuthMe](https://www.spigotmc.org/resources/authmereloaded.6269/) y lo mas probable es que no
coozcas la contraseña de ningun usuario, por lo que ahora veremos como burlar la autenticacion  
Si al conectarte al servidor, no te pide ninguna contraseña, felicidades, lo mas probable es
que hayas entrado a una *network premium*, asi que ahora puedes ver como conseguir permisos 
de administrador

### UUID Spoof
Normalmente los servidores y plugins no toman en cuenta el *Nickname*, sino que utilizan
el UUID (Universally Unique Identifier) del usuario para identificarlo, este UUID puede ser
modificado y simular ser otro usuario sin afectar nuesto *Nickname*, lo que sera util para
burlar el plugin de autenticacion
Para obtener el UUID de un jugador, podemos usar el comando *player < username >* (en otra ventana
para no cerrar la proxy anteriormente creada) el cual nos dara la informacion que buscamos

![MCPTool](https://crudiercub.github.io/assets/images/HackingMinecraftServers/player.JPG "MCPTool")

Ahora que tenemos el UUID de un usuario *admin*, podemos usar el comando el comando 
*/set-uuid < uuid >* para cambiar nuestro UUID por el del administrador del servidor

![MCPTool](https://crudiercub.github.io/assets/images/HackingMinecraftServers/set-uuid.JPG "MCPTool")

Despues de utilizar el comando, podemos volver a conectarnos al servidor proxy **(sin apagarlo)** 
y ahora toda nuestrainformacion como posicion, inventario, rango, etc. sera la del usuario 
*admin* y no la nuestra, sin embargo nosotros seguimos con nuestro *Nickname* y no el del *admin*

![MCPTool](https://crudiercub.github.io/assets/images/HackingMinecraftServers/admin.JPG "MCPTool")

Como nuestro *nickname* no cambio, el plugin de autenticacion nos pedira iniciar sesion
como si fuera nuestra cuenta y no la del *admin*, pero como nuestro UUID es la del *admin*,
tendremos los permisos de el y podremos hacer lo que queramos en el servidor

![MCPTool](https://crudiercub.github.io/assets/images/HackingMinecraftServers/op.JPG "MCPTool")

Recomiendo darle op a una cuenta alterna ya que si te das op a ti mismo, el servidor
te dira que el usurio ya es op, debido a que el servidor guarda el UUID y Nickname de
los usuarios con op, y recordemos que estamos usando el UUID del *admin* y no el nuestro,
es por esto que nos aparecera ese mensaje y debemos usar una cuenta alterna para darnos op

## Arreglando la vulnerabilidad

### BungeeCord
Para arreglar esta vulnerabilidad, es tan facil como instalar el plugin 
[BungeeGuard](https://www.spigotmc.org/resources/bungeeguard.79601/) en BungeeCord, 
este plugin evita que los usuarios se conecten directamente a los servidores y solo 
puedan conectarse a traves de nuestra proxy, pidiendo un token de autenticacion en 
todos los servidores, el cual solo se puede obtener al conectarse a la proxy del servidor  
Una vez instalado el plugin, debemos dirigirnos al archivo `/plugins/BungeeGuard/token.yml`
y apuntar el token que aparece en el archivo, ya que lo necesitaremos para las configuraciones  

### Velocity
Asegurate de tener instalada la version Velocity 1.1.0 o superior, ya que a partir de esta
version, velocity viene con BungeeGuard incluido y no necesitas instalarlo  
Pon "bungeeguard" en `player-info-forwarding-mode` en el archivo `velocity.toml` y guarda el
`forwarding-secret` ya que este es el token que necesitaremos para las configuraciones y 
luego reincia la proxy

### En los servidores
- Verifica que estes usando [Paper](https://papermc.io/) 1.9.4+ o tener 
[ProtocolLib](https://www.spigotmc.org/resources/protocollib.1997/) instalado  
- Asegurate de tener `bungeecord` puesto en `true` en el archivo `spigot.yml`
- Agrega el archivo `BungeeGuard.jar` en la carpeta `/plugins` y reincia el servidor
- Ve al archivo `/plugins/BungeeGuard/config.yml` y agrega los tokens a la lista `allowed-tokens `

```yaml
# Allowed authentication tokens.  
allowed-tokens:
  - "AUSXEwebkOGVnbihJM8gBS0QUutDzvIG009xoAfo1Huba9pGvhfjrA21r8dWVsa8"
```
- Reinicia el servidor  

> Debes realizar esta configuracion en todos los servidores Spigot o Paper conectados a la Network
{: .prompt-danger}

Con estas configuraciones tu servidor ahora esta protegido contra el Bungee Exploit y
no deberias tener problemas con usuarios que se conecten directamente a los servidores

## Conclusión
Esta vulnerabilidad es muy antigua y no hay mucha informacion sobre ella, pero sigue funcionando
en muchisimos servidores, por lo que es importante que los administradores de servidores esten 
informados sobre este tipo de vulnerabilidades y como solucionarlas, ya que es muy facil de
explotar y puede causar muchos problemas en los servidores  

> Al momento de publicar este post, el error ya fue arreglado en el servidor que use para las pruebas
{: .prompt-warning}

Quiero agradecer a **Leyend Network** por permitirme realizar las pruebas en su servidor 
y dejarme publicar este post con la informacion de su servidor, si quieres jugar en su servidor
la ip para conectarse es **leyend.servermc.xyz**

Con esto me despido, espero que les haya gustado este post y les haya sido util, para informarse
y de ser necesario, arreglar el problema para proteger su Network 