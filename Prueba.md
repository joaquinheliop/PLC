- [1. Esquema](#1-esquema)
- [2. Inicial](#2-inicial)
- [3. LAN](#3-lan)
- [4. WAN](#4-wan)
- [5. Seguridad del Mikrotik](#5-seguridad-del-mikrotik)

# 1. Esquema

	  
	  |				  
	  | WAN (DHCP Client)		     
	  | Ether1 - (Public IP)             
  | -------------------------------- |
  |                                  |
  | Mikrotik                         |
  |                                  |
  | -------------------------------- |
  |                                  |
	  | Ether2 - 192.168.11.1            
	  | LAN (DHCP Server)                
	  |                                  
		
  
# 2. Inicial

1. Resetear el MK a fábrica _(System > Reset configuration)_

2. Conectar de nuevo _(Mediante Winbox, pestaña neighbors)_

3. Setear nombres de interfaces _(en interfaces, doble click en la que se quiere configurar)_
   
# 3. LAN
1. _IP > Adressess > +_
~~~
Address: 192.168.11.1/24
Network: 192.168.11.0
Interface: Ether2-LAN 
~~~

2. Configurar servidor DHCP:
   1. _IP > DHCP Server > DHCP Setup_
   2. Seleccionar Ether2-LAN
   3. DHCP Address Space 192.168.11.0/24, rango de IPs a configurar. Ej: 
~~~
192.168.11.10-192.168.11.100
DNS Servers 192.168.11.1
Gateway: 192.168.11.1
~~~

3. Habilitar DNS:
   1. _IP > DNS_
   2. Servers: 8.8.8.8 _(habilitar box: Allow Remote Requests)_
   
# 4. WAN
1. Cliente DHCP:
   1. _IP > DHCP Client > +_
   2. Interface: Ether1-WAN _(Add default route: yes)_

2. Habilitar DDNS:
   1. _IP > Cloud_
   2. Tildar DDNS Enabled y copiar el DDNS Name _(Si no carga quiere decir que no está conectado a internet)_:
~~~
???
~~~

# 5. Seguridad del Mikrotik

1. Deshbilitar servicios
   1. _IP > Services_ 
   2. Deshabilitar todo menos SSH y winbox

2. Configurar una contraseña para el usuario admin (o cargar nuevos)
   1. _System > users_
   2. Doble click a admin y botón password para cambiar contraseña:
~~~
UDlZp{6v9]u9uj27Wj]3
~~~

3. Cargar reglas de Firewall
   1. _IP > Firewall_
   2. Agregar reglas de bloqueo _(con el +)_:
			chain : input
      - **r1:**	
        - src address: 192.168.11.0/24
      -	**r2:**
        - src address: 192.168.150.0/24 (esta regla va a ser para la VPN)
      -	**r3:**
        - protocol: tcp
        - dst port: 22,8291,1194 _(si no funca la vpn, probar poner esta regla primero y dps moverla al 3er lugar)_
      - **r4:** 
        - Connection State: tildar Established y Related
      - **r5:** 
        - ir a pestaña actions y en action setear: drop

   3. Agregar reglas de NAT _(pestaña NAT arriba)_:
      1.  +:
          - Chain: srcnat
          - Out interface: Ether1-WAN
          - Action (pestaña arriba): Masquerade	


    
