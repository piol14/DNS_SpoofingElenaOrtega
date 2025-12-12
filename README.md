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
<img width="831" height="239" alt="image" src="https://github.com/user-attachments/assets/9311fdb8-e39b-40a1-ac94-beea959fd700" />

- **IP:** `192.168.1.45`  
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
- Para ello, modificaremos el archivo `/var/www/html/index.html ` y pondremos lo siguiente:
<img width="905" height="565" alt="image" src="https://github.com/user-attachments/assets/858bf2b4-b96b-439f-af6f-4b92a369b6cd" />
- Despues crearemos el archivo `login.php`en el mismo directorio que servira para capturar las creedenciales del html falso y pondremos lo siguiente:
<img width="860" height="544" alt="image" src="https://github.com/user-attachments/assets/997841e8-394c-4ca2-ab89-ffefc698f615" />
- Le damos permisos al index.html, al login.php y a capturas.txt , archivo donde se guardaran las creedenciales de la victima con el comando `sudo chown www-data /var/www/html/archivo` 
<img width="435" height="53" alt="image" src="https://github.com/user-attachments/assets/2f157f4a-9405-4c4c-9ccf-ab43674d19f0" />

- Encendemos apache con `sudo service apache2 start`
<img width="314" height="44" alt="image" src="https://github.com/user-attachments/assets/fde9537b-5bcf-4e0d-9d76-857b4ca38a16" />
 Y comprobamos que funciona con `sudo service apache2 status`
<img width="818" height="367" alt="image" src="https://github.com/user-attachments/assets/0f4397e3-2ded-4321-994d-b2c54fa2e670" />



