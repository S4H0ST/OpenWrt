INSTALACIÓN DE INTERFAZ WEB LuCi y CONFIGURACIÓN
---
Para esta parte de la configuración se va a realizar con la herramienta PuTTY y la interfaz web LuCi.

**1. Paso:** entramos en PuTTY e instalamos el editor de texto ***nano***.
```Json
opkg update 
opkg install nano
```

**2. Paso:** instalar el interfaz web LuCi.
```Json
opkg update 
opkg install luci
```
**3. Paso:** entramos a la carpeta ***/etc/config/*** .
```Json
cd /etc/config
```

**4. Paso:** entramos para editar primeramente el archivo ```network```.
```Json
nano network
```
**5. Paso:** una vez dentro editamos los campos de ```config interface 'lan'```.

```Json
config interface 'lan'
       option device 'br-lan'
        option proto 'dhcp'
```
**6. Paso:** también editamos ```config device```
```JSON
config device
        option name 'br-lan'
        option type 'bridge'
        list ports 'eth1'
```
Ahora con las teclas **Ctr+S** guardamos y con **Ctr+X** salimos del editor de texto.

**7. Paso:** a continuación vamos a editar el archivo ```firewall```
```Json
nano firewall
```

**8. Paso:** nos dirijimos a la seccion de ***config zone*** de la conexión ***wan*** que es la interfaz de red creada anteriormente y modificamos **option input** que de manera predefinida lo encontramos en ```REJECT``` y lo modificamos a ```ACCEPT``` para que acepte todos los paquetes de entrada de la red *wan*
```Json
config zone
        option name             wan
        list   network          'wan'
        list   network          'wan6'
        option input            ACCEPT
        option output           ACCEPT
        option forward          REJECT
        option masq             1
        option mtu_fix          1
```
Ahora con las teclas **Ctr+S** guardamos y con **Ctr+X** salimos del editor de texto.

**9. Paso:** Para poder conectarse por ***ssh***, basta con haber realizado el cambio anterior, recargar y habilitar el ```firewall``` con los siguientes comandos.
```Json
/etc/init.d/firewall reload
/etc/init.d/firewall enable
```

**10. Paso:** a continuación vamos a editar el archivo ```wireless```
```Json
nano wireless
```
**11. Paso:** aquí solamente nos interesa modificar el apartado de ```config wifi-device 'radio0'``` que corresponde con la tarjeta de red wifi integrada del dispositivo, donde modificaremos:
-  El ancho de banda (*option band*) que estará inicialmente en 2g o 5g , lo modificamos por **11g**.
- ```option disable``` para habilitar la tarjeta de red y lo cambiamos a **'0'**.
- De manera opcional, cambiamos *option channel* para cambiar el lugar donde queremos que se levante el canal, el valor **'1'** es automatico , pero se recomienda que no sea un valor muy elevado y que no entre conflicto con el canal de otras redes.

```Json
config wifi-device 'radio0'
        option type 'mac80211'
        option path 'ffe0a000.pcie/pcia000:02/a000:02:00.0/a000:03:00.0'
        option channel '36'
        option band '11ac'
        option htmode 'VHT80'
        option disabled '0'
```
Ahora con las teclas **Ctr+S** guardamos y con **Ctr+X** salimos del editor de texto.

**12. Paso:** Aplicar la configuración de red sin reiniciar el servicio, lo que debería evitar una interrupción en la conectividad de red.
```Json
/etc/init.d/network reload
/etc/init.d/network enable
```
**13. Paso:** levantar la interfaz de red wifi con los siguientes comandos.
```Json
uci commit wireless
wifi
```
Para comprobar que ya esta, nos dirigimos al navegador y escribimos en el buscador, la dirección ip del dispositivo cliente AP.