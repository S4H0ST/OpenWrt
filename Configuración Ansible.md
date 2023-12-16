Configuración para el uso de Ansible
---
**Requisito previo:** haber realizado la [Instalación de OpenWRT ](/Instalación_OpenWRT.md), disponer de PuTTY o Termius para conectarnos por ssh

- **1. Paso:** nos conectamos por ssh al Ap ```ssh root@<IP del cliente ap>```
- **2. Paso:** instalamos el interprete Python3 
```
opkg update
opkg install python3
```

- **3. Paso:** nos instalamos el cliente SFTP para transferir datos por ssh
```
opkg update
opkg install openssh-sftp-client
```
- **4. Paso:** Salimos de la conexión ssh y creamos un archivo denominado ```inventory.txt``` que contendrá lo siguiente: 

```
#1# [Servidores]
#2#  Ap1	    ansible_host=<Dirección Ip>  ansible_user=<Usuario> ansible_sudo_pass=<Contraseña>  ansible_python_interpreter=/usr/bin/python3

#3# [Ap1:vars]
nueva_variable=Ejemplo  #Declaración de una variable
```
1. Nombre del grupo de dispositivos
2. Nombre identificador , dirección Ip , usuario y contraseña para conectarse por ssh , y el interprete que se va a utilizar que ya descargamos al inicio.
3. Nombre del grupo de variables [GrupoDispositivos:vars]

- **5. Paso:** Creamos una carpeta que se llame ***tasks*** donde meteremos los archivos que nos servirán para configurar más facilmente los AP's. 

A partir de aqui  ya empieza los pasos más especificos para Ap 3825 para la configuración de OpenWRT

- **6. Paso:** creamos 3 archivos principales, ```wireless.yml```, ```firewall.yml``` y ```network.yml```

- **7. Paso:** Veamos como funcionan:

***Wireless***
---
```
- hosts: Aps
  tasks:
  - name: enable wireless device
    ansible.builtin.replace:
        path: /etc/config/wireless
        regexp: option disabled '1'
        replace: option disabled '0'
  - name: change band
    ansible.builtin.replace:
        path: /etc/config/wireless
        regexp: option band '2g'
        replace: option band '11ac'

  - name: Reload network config
    ansible.builtin.shell:
        cmd: /etc/init.d/network reload
        chdir: /etc/init.d

    ansible.builtin.shell:
        cmd: /etc/init.d/network enable
        chdir: /etc/init.d

    ansible.builtin.shell:
        cmd: uci commit wireless
        chdir: /etc/config
```
***Firewall***
---
```
- hosts: Aps
  tasks:
  - name: accept input
    ansible.builtin.replace:
        path: /etc/config/firewall
        regexp: option input            REJECT
        replace: option input            ACCEPT
  - name: Reload network config
    ansible.builtin.shell:
        cmd: /etc/init.d/network reload
        chdir: /etc/init.d

    ansible.builtin.shell:
        cmd: /etc/init.d/network enable
        chdir: /etc/init.d

    ansible.builtin.shell:
        cmd: /etc/init.d/firewall reload
        chdir: /etc/init.d

    ansible.builtin.shell:
        cmd: /etc/init.d/firewall enable
        chdir: /etc/init.d
```

***Network***
---
```
- hosts: Aps
  tasks:
  - name: Copy network profile
    ansible.builtin.copy:
        src: ../config/network
        dest: /etc/config/network
  - name: Enable network config
    ansible.builtin.shell:
      cmd: /etc/init.d/network enable
      chdir: /etc/init.d
  - name: Reload network config
    ansible.builtin.shell:
      cmd: /etc/init.d/network reload
      chdir: /etc/init.d
```
- **hosts** : nombre del grupo de dispositivos que afecta (se definen en el archivo ```inventory.txt```)

- **tasks** : se definen las tareas que se van hacer

- **name** : cada tarea tiene un nombre

- **ansible.builtin.replace:** : modulo ansible para remplazar lineas de un archivo

- **path** : ruta donde vamos a remplazar

- **regexp** : linea o lineas que coincidan y se quieran remplazar

- **replace** : linea que se quiere usar para remplazar 

- **ansible.builtin.shell:**: modulo para ejecutar comandos de consola

- **cmd:**: comando que se quiere ejecutar

- **chdir:** directorio donde se quiere ejecutar

- **ansible.builtin.copy:**: modulo para copiar archivos enteros y copiarlos en una ruta especifica

- **src** : se indica la ruta del archivo que queremos copiar
- **dest** : se indica la ruta de destino donde se quiere colocar el archivo copiado

**Nota:** Ansible es muy sensible con la sintaxis, por lo que hay que tener cuidado de que este todo bien colocado, error común suele ser falta o abuso de espacios o comillas.

En esta configuración no se explica el funcionamiento de lo que se remplaza ya que eso ya esta explicado en la [configuración de la Interfaz Web en OpenWRT](/Configuración%20Interfaz%20Web%20LuCi.md)

- **8. Paso:** Para aplicar la configuración a los dispositivos, abrimos una terminal y escribimos lo siguiente y le damos a ***enter***

```
ansible-playbook -i inventory.txt <ruta>/wireless.yml <ruta>/firewall.yml  <ruta>/network.yml
```
Una vez cargado ya habremos realizado y configurado todos los dispositivos con la misma configuración.
