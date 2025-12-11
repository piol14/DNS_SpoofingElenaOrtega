# DNS_SpoofingElenaOrtega 
## Definición 
Ataque para alterar entradas en un servidor DNS para redirigir a la victima a una web falsa creada por el atacante. Se puede hacer en redes publicas o en cualquiera donde el atacante tenga acceso a las tablas del ARP para obligar a los usuarios a acceder a la pagina web especifica. Se utiliza como phising para capturar creedenciales o para que instalen un malware en sus dispositivos. 


## Funcionamiento 

- Primero usa arpsoof para engañar al equipo victima  y conseguir que apunte al equipo atacante cuandi el usuario escriba el dominio contaminado en su navegador. Envenena la cache de resolucion de la victima
- Otro comando arpsoof para engañar al servidor del dominio web para que piense que la IP de la victima es el del atacante
- 
