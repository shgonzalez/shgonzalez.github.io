---
layout: post
categories: Cloud AWS Windows
title: Configuracion de PuTTY para acceder a instancias en AWS via Bastion Host.
date:   2020-12-12 09:00:00 -0400
comments: true
image: /assets/img/aws_logo.jpg
description: Guia practica para acceder a instancias de AWS utilizando ssh con claves publicas/privadas por medio de un bastion host.
tags: PuTTY putty ssh aws instances Amazon Web Services keys agent forwarding pageant puttygen
published: true
---
![aws.jpg][aws.jpg]

[aws.jpg]: /assets/img/aws_logo.jpg


### Introducción

Las VMs en nuestro entorno de AWS solo permiten el acceso utilizando un par de claves públicas y privadas. La clave privada nunca debe ser compartida y debe estar donde se inicializa la conexión SSH (origen) y la clave pública se guarda en el home del usuario en el archivo ~/.ssh/authorized_keys (destino). Esta es una guía para usuarios con sistema operativo Windows.

### Diagrama de conectividad usando jumpbox o bastion host.

![bastion.jpg][bastion.jpg]

[bastion.jpg]: /assets/img/scheme_bastion_aws.png


### Crear par de claves públicas y privadas con PuTTYGen o PuTTY Key Generator

Abrimos la aplicación:

![puttygen1.png][puttygen1.png]

[puttygen1.png]: /assets/img/puttygen1.png

Presionamos el botón Generate y movemos el mouse en el espacio donde dice `Key`.

![puttygen2.png][puttygen2.png]

[puttygen2.png]: /assets/img/puttygen2.png


Al finalizar nos aparecerá la clave pública. Opcionalmente podemos poner el nombre de usuario en el `Key Comment`

![puttygen3.png][puttygen3.png]

[puttygen3.png]: /assets/img/puttygen3.png

Luego presionamos `Save private key` y seleccionamos que se va a guardar sin passphrase. Presionamos `Yes`.

![puttygen4.png][puttygen4.png]

[puttygen4.png]: /assets/img/puttygen4.png

Ahora este archivo va a estar en formato `.ppk` que es propio de PuTTY.

### Agregar las claves publicas en los servidores o instancias de AWS

Ahora nos queda agregar la clave pública en el archivo ~./ssh/authorized_keys. Allí abrimos un editor de preferencia en el servidor de bastion o jumpbox y en los servidores de la red privada que necesitemos acceder.

![authorized_keys.png][authorized_keys.png]

[authorized_keys.png]: /assets/img/authorized_keys.png

> Obs: la salida fue ofuscada.

### Configurar el agente PageAnt

Bien, ahora que todos los servidores poseen las claves públicas, debemos configurar el agente que va almacenar las claves privadas que anteriormente guardamos y que están en formato ppk. El agente es pageant.exe. En este programa debemos agregar dichas claves con el botón “Add Key”


![pageant.png][pageant.png]

[pageant.png]: /assets/img/pageant.png

> Obs: la salida fue ofuscada.

Este programa se estará ejecutando en background al cerrar.


### Configuración de PuTTY SSH con Agent Forwarding

Por último, ahora debemos configurar PuTTY para la conexión a los servidores. En nuestra configuración primero nos conectamos al Jumpbox y luego saltamos a los servidores de la red privada usando directamente `ssh <hostaname / ip destino>`.
Abrimos PuTTY y cargamos en la sección de `Host Name` el nombre de usuario o login y el nombre del Jumpbox/Bastion host.

![putty1.png][putty1.png]

[putty1.png]: /assets/img/putty1.png
> Obs: la salida fue ofuscada.

Luego en la sección de SSH -> Auth, tildamos la opción “Allow agent forwarding”

![putty2.png][putty2.png]

[putty2.png]: /assets/img/putty2.png

Con esta configuración de `agent forwarding` nos permitirá conectarnos vía ssh a los servidores donde están configurados nuestras llaves sin necesidad de ingresar la contraseña. Por último abrimos la conexión con `Open`

![putty3.png][putty3.png]

[putty3.png]: /assets/img/putty3.png
> Obs: la salida fue ofuscada.


### Conclusión
Esta es una guía práctica de como configurar PuTTY para conectarse a AWS utilizando agent forwarding.


A continuación les comparto un playbook de [Ansible](https://www.ansible.com/) que puede a ayudar a configurar múltiples servidores.

<script src="https://gist.github.com/shgonzalez/0fce91939f7fd8c4d094d92b5d7e5b31.js"></script>


Referencias:
* Configurar el inicio de PageAnt en Windows [gistfile](https://gist.github.com/chunter/3ec25dd802c2163265eacfcb6f53cb7d)
* [Bastion Host AWS ](https://aws.amazon.com/es/blogs/security/tag/bastion-host/)