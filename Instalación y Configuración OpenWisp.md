Instalación y Configuración OpenWisp
--
**1.Paso**
```
sudo pip install ansible
```
**2.Paso**
```
ansible-galaxy install openwisp.openwisp2
```

**3.Paso**
```
mkdir ~/openwisp2-ansible-playbook
cd ~/openwisp2-ansible-playbook
```

**4.Paso**
```
nano hosts
```
```
[openwisp2]
<Dir Ip del servidor cliente>
```
**5.Paso**
```
nano playbook.yml
```
```
- hosts: openwisp2
  become: "{{ become | default('yes') }}"
  roles:
    - openwisp.openwisp2
  vars:
    openwisp2_default_from_email: "CorreoDeUsuario@gmail.com"
```
**6.Paso** : conectar por SSH
```
opkg update
opkg install postfix
```

```
```
- openwisp-dashboard : el panel de administración de su dispositivo OpenWISP.

- openwisp-api : API HTTP de varios módulos de openwisp que se pueden escalar 
simplemente teniendo varios contenedores de API según los requisitos.

- openwisp-websocket : contenedor dedicado para manejar solicitudes de websocket, por ejemplo. para actualizar la ubicación de dispositivos de red móviles.

- openwisp-celery : ejecuta todas las tareas en segundo plano para OpenWISP, por ejemplo. actualizando las configuraciones de su dispositivo.

- openwisp-celery-monitoring : ejecuta tareas en segundo plano que realizan comprobaciones de monitoreo activo, por ejemplo. comprobaciones de ping y comprobaciones de configuración. También ejecuta ta

- openwisp-celerybeat : ejecuta tareas periódicas en segundo plano. p.ej. revocando todos los certificados caducados.

- openwisp-nginx : contenedor orientado a Internet que facilita toda la comunicación HTTP y Websocket entre el mundo exterior y los contenedores de servicios.

- openwisp-freeradius : contenedor Freeradius para OpenWISP.

- openwisp-openvpn : contenedor OpenVPN para administración VPN lista para usar.

- openwisp-postfix : servidor de correo para enviar correos a MTA.

- openwisp-nfs : servidor NFS que permite almacenamiento compartido entre diferentes máquinas. No se ejecuta en máquinas de un solo servidor, pero se proporciona para la configuración de K8.

- openwisp-base : es la imagen base que no se ejecuta en su servidor, pero openwisp-api y openwisp-dashboard la usan como base.

- Redis : servicio de almacenamiento en caché de datos (requerido para acciones como iniciar sesión).

- PostgreSQL : contenedor de base de datos SQL para OpenWISP.
