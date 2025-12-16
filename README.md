# DNS_SpoofingElenaOrtega 
## Definición 
Ataque para alterar entradas en un servidor DNS para redirigir a la victima a una web falsa creada por el atacante. Se puede hacer en redes publicas o en cualquiera donde el atacante tenga acceso a las tablas del ARP para obligar a los usuarios a acceder a la pagina web especifica. Se utiliza como phising para capturar creedenciales o para que instalen un malware en sus dispositivos. 


## Funcionamiento 

- Primero usa arpsoof para engañar al equipo victima  y conseguir que apunte al equipo atacante cuandi el usuario escriba el dominio contaminado en su navegador. Envenena la cache de resolucion de la victima
- Otro comando arpsoof para engañar al servidor del dominio web para que piense que la IP de la victima es el del atacante
- Crear un archivo HOST que apunte a la IP del equipo del atacante hacia la pagina web alterada. Esta se utilizara cuando la victima solicite el nombre del dominio
- Recopilar datos de la victima en la red engañandolas para que se autentifiquen y ingresen informacion para capturarlas en el servidor atacante

## Protocolos afectados 

1. DNS: El principal afectado. El dns spoofing hace que no verifique la autenticidad de las respuestas
2. UDP: Este atacante hace que el UDP no establezca una conexion confiable ya que no identifica la suplantacion de origen
3. Cache Dns: La cache almacena registros falsos y el resolver acepta una respuesta falsa antes que la verdadera
4. DNSSEC: El DNS spoofing explota la falta de autenticacion criptografica que ofrece este protocolo

## Ejemplo vulnerabilidad CVE 
En 2025 destaca el CVE-2025-40778, una vulnerabilidad  crítica en BIND 9 que permite insertar registros DNS falsos en la caché del servidor. Esto puede provocar redireccionamientos, robo de credenciales o suplantación de servicios.
La mitigación se basa en actualizar los servidores DNS, activar DNSSEC y limitar la recursión solo a clientes de confianza. 

## Ataque realizado  

### 1. Escenario
- **Máquina atacante:** Kali Linux  
- **Máquina víctima:** Windows (equipo local)  
- **Ambas en la misma red local**

---

### 2. Recopilación de IP necesarias

#### - IP de la máquina Kali  
Comando ejecutado: `ip a`  
<img width="831" height="230" alt="image" src="https://github.com/user-attachments/assets/5d020bab-6c85-4890-95ac-b7337d5b528e" />


- **IP:** `192.168.1.47`  
- **Interfaz:** `eth0`

---

#### - Gateway de la red  
Comando ejecutado: `route -n`  
<img width="663" height="108" alt="image" src="https://github.com/user-attachments/assets/85666da5-653c-4091-ad6f-7c00edd8bb9d" />

- **Gateway:** `192.168.1.1`

---

#### - IP de la máquina víctima (Windows)  
Comando ejecutado: `ipconfig`  
<img width="623" height="148" alt="image" src="https://github.com/user-attachments/assets/8eb06165-fc86-45e1-923c-4a46dd595edb" />

- **IP:** `192.168.1.44`

---

### 3. Configuración de la web falsa con Apache2

- En la máquina atacante se instala Apache2:

```bash
sudo apt update
sudo apt install apache2

````
- Editamos el index.html por defecto del apache y ponemos un formulario que pida al usuario el usuario y la contraseña

- Para ello, modificaremos el archivo **/var/www/html/index.html** y pondremos lo siguiente:
<img width="905" height="565" alt="image" src="https://github.com/user-attachments/assets/858bf2b4-b96b-439f-af6f-4b92a369b6cd" />

- Despues crearemos el archivo login.php  en el mismo directorio que servira para capturar las creedenciales del html falso y pondremos lo siguiente:

<img width="860" height="544" alt="image" src="https://github.com/user-attachments/assets/997841e8-394c-4ca2-ab89-ffefc698f615" />

- Le damos permisos al index.html, al login.php y a capturas.txt, archivo donde se guardarán las credenciales de la víctima con el comando  sudo chown www-data /var/www/html/archivo


  <img width="435" height="53" alt="image" src="https://github.com/user-attachments/assets/2f157f4a-9405-4c4c-9ccf-ab43674d19f0" />



- Encendemos apache con `sudo service apache2 start`  
  <img width="314" height="44" alt="image" src="https://github.com/user-attachments/assets/fde9537b-5bcf-4e0d-9d76-857b4ca38a16" />

- Y comprobamos que funciona con `sudo service apache2 status`  
  <img width="818" height="367" alt="image" src="https://github.com/user-attachments/assets/0f4397e3-2ded-4321-994d-b2c54fa2e670" />


## Configuracion Ettercap 
- Modificamos el archivo /etc/ettercap/etter.conf:
  - Cambiamos el ec_uid y ec_gid a 0, dandole permisos de root al ettercap
    
    <img width="500" height="84" alt="image" src="https://github.com/user-attachments/assets/4147f907-bcc3-40d1-b7b2-5db8aba97311" />
  
    - Descomentamos en el apartado linux redir_command_on, redir_command_off, redir6_command_on, redir6_command_off para la intercepción y manipulación activa del tráfico cuando el atacante logra posicionarse como intermediario
    
    <img width="849" height="206" alt="image" src="https://github.com/user-attachments/assets/9ff8e77f-02d1-471c-bb42-67bbdd6a481e" />


  - Modificamos el archivo /etc/ettercap/etter.dns:
  Ponemos la url que redirigiremos a la victima a nuestra web falsa y la ip del atacante para que funcione el apache

<img width="454" height="100" alt="image" src="https://github.com/user-attachments/assets/94b2e955-c631-4dd7-a12a-f8087c015298" />

## Ataque Ettercap 

- Ettercap es una herramienta para realizar ataques Man-in-the-Middle mediante el envenenamiento ARP, permitiendo interceptar y manipular el tráfico de una red local. Se utiliza principalmente en auditorías de seguridad para capturar contraseñas, analizar protocolos y realizar inyecciones de datos en tiempo real. 
- Ejecutamos el siguiente comando para ejecutar el dns spoofing sudo ettercap -T -q -i eth0 -M arp:remote /IP VICTIMA// /GATEWAY// -P dns_spoof
- 
- El comando explicado: El comando ejecuta Ettercap en modo texto (-T) y silencioso (-q), usando la interfaz eth0 (-i) para realizar un ataque de intercepción (MITM) mediante el engaño de tablas ARP (-M arp:remote) entre la víctima y el router.

Antes de mostrar los resultados del ataque, nos podemos fijar con el comando arp -a en la victima que las Macs del gateway y la de la ip de kali son **diferentes** 

<img width="495" height="234" alt="image" src="https://github.com/user-attachments/assets/0936f719-5812-41f6-8da6-e073a170d52d" />

Pero, despues de ejecutar el ataque, las Macs del gateway y de la kali son iguales 

<img width="466" height="103" alt="image" src="https://github.com/user-attachments/assets/21d1f73e-4724-41e3-9ac9-f868f14cd5ae" />

Esto significa que el arp ha funcionado, ahora comprobaremos que si buscamos en la maquina victima marcosaguilar.com, aparecera la web creada antes que capturara creedenciales mediante un formulario:

<img width="764" height="457" alt="image" src="https://github.com/user-attachments/assets/63d1d79a-46a3-4315-84fc-963d3af740de" />

Introducimos las siguientes creedenciales: 

- Usuario: elena
- Contraseña: norris2puntos

Ahora accedemos al archivo capturas.txt que se encuentra en la carpeta /var/www/html y vemos que ha capturado las creedenciales: 


 <img width="709" height="50" alt="image" src="https://github.com/user-attachments/assets/209e56cf-d8a3-48ee-be41-0614c3c28376" />

## Como evitar DNS spoofing 

- Usar DNSSEC: Implementa firmas digitales en los registros DNS para asegurar que la respuesta sea auténtica y no haya sido manipulada.

- Configurar VPN: Al cifrar todo el tráfico desde tu dispositivo hasta un servidor seguro, los atacantes en la red local no pueden ver ni interceptar tus solicitudes DNS.

- Usar DoH o DoT: Activar DNS over HTTPS o DNS over TLS en el navegador o sistema para cifrar las consultas DNS y evitar que sean legibles para terceros.

- Tablas ARP Estáticas: En entornos críticos, configurar manualmente las direcciones MAC de los dispositivos (como el router) para impedir el envenenamiento ARP que facilita el spoofing.

- Herramientas de Detección: Utilizar software como Arpwatch o sistemas de detección de intrusos (IDS) que alerten cuando hay cambios sospechosos en las tablas de red.
