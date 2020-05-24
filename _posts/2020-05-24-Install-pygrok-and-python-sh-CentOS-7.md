---
layout: post
categories: Linux Python
title: Instalar pygrok y python-sh en CentOS 7
date:   2020-05-24 18:00:00 -0400
comments: true
#image: /assets/img/elephant.jpg
description: Instalar pygrok y python-sh en CentOS 7
tags: python linux centos 7 operating systems yum gcc failed python-devel 
published: true
---

Al ejecutar la instalación se encuentran con este error

```
unable to execute gcc: No such file or directory
    error: command 'gcc' failed with exit status 1
```

Para corregir este error primeramente deben tener instalado `gcc` en el equipo, pueden instalarlo con `yum`

Si durante la instalación de la libreria sigue apareciendo este error

```
    regex_2/_regex.c:46:20: fatal error: Python.h: No such file or directory
     #include "Python.h"
                        ^
    compilation terminated.
    error: command 'gcc' failed with exit status 1

```

Deben instalar via `yum` el siguiente paquete `python-devel`

Con estos pasos se soluciona el inconveniente.

