---
layout: post
title: Cómo desarrollar tu primer aplicación distribuida con Mesos 
---

Como desarrolladores muchos nos hemos encontrado en algún momento dado
con la necesidad de construir una applicación distribuida ya sea para
aprovechar la potencia de procesamiento de un cluster, para tener un
sistema de recuperación automático o bien porque necesitamos trabajar
con sistemas que se encuentran repartidos en distintos servidores en
un cluster.

Mesos es un gestor que permite ejecutar de manera sencilla
applicaciones distribuidas
*¿Qué es Mesos?*

Utilizaremos el ejemplo de una aplicación distribuida (de tratamiento
de datos en paralelo) para introducir los conceptos básicos de Mesos
*ejemplo*

*Problemas al construir SISDIS* de reinventar la rueda:

- En cuanto hay varias aplicaciones que se ejecutan en el cluster se
  plantea el problema de cómo se pueden *repartir los recursos*
- Cada vez hay que definir un sistema de *tolerancia a fallos* en
  caso de fallo
- Cada vez es necesario definir una manera apropriada de ejecutar
  tareas de forma remota en una máquina específica (*ejecución remota*)
- El Planificador no puede ser un cuello de botella

*Cómo hacer esto con Mesos*

Mesos es un planificador o gestor de cluster que se ocupa de
proporcionar tolerancia a fallos, repartición justa de recursos entre
aplicaciones y ejecución remota de tareas para aplicaciones que
necesitan ejecutarse en un cluster. Para acceder a estas
funcionalidades las aplicaciones sólo necesitan implementar dos
interfaces definidas en la API de Mesos conocidas como ejecutor y
planificador (o scheduler)

*¿Como funciona Mesos?*

Mecanismo de ofertas


*Ejemplo: mi primer framework*


![_config.yml]({{ site.baseurl }}/images/config.png)


[Viadeo Profile](http://www.viadeo.com/profile/002lz309wldwqir)
[LinkedIn Profile](https://www.linkedin.com/pub/i%C3%B1igo-mediavilla/3a/700/276)


