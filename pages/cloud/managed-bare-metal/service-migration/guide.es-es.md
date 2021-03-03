---
title: Migrar una infraestructura a Managed Bare Metal
excerpt: Cómo gestionar todos los aspectos relacionados con la migración de una infraestructura a la solución Managed Bare Metal
slug: sddc-migration
section: Primeros pasos
order: 5
---

> [!primary]
> Esta traducción ha sido generada de forma automática por nuestro partner SYSTRAN. En algunos casos puede contener términos imprecisos, como en las etiquetas de los botones o los detalles técnicos. En caso de duda, le recomendamos que consulte la versión inglesa o francesa de la guía. Si quiere ayudarnos a mejorar esta traducción, por favor, utilice el botón «Contribuir» de esta página.
> 

**Última actualización: 24/11/2020**

## Objetivo

La migración de un servicio Managed Bare Metal tiene dos aspectos:

* El contexto Managed Bare Metal de OVHcloud que incluye la parte cliente, la administración de su infraestructura.
* El contexto VMware que incluye todo el ecosistema VMware, en la propia infraestructura.

**Cómo conocer todos los aspectos relacionados con la migración a un servicio Managed Bare Metal**

> [!primary]
>
> Si elige migrar una infraestructura a un nuevo vDC, siga [esta guía](https://docs.ovh.com/gb/en/managed-bare-metal/vdc-migration/).
>

## Requisitos

* Estar conectado al [área de cliente de OVHcloud](https://www.ovh.com/auth/?action=gotomanager&from=https://www.ovh.es/&ovhSubsidiary=es){.external}, en la sección "`Cloud Metal" de la columna izquierda`{.action} y, seguidamente, `Managed Bare Metal`{.action}.
* Tener un producto [Managed Bare Metal](https://www.ovhcloud.com/es/managed-bare-metal/){.external}.

## Procedimiento

En esta guía abordaremos los conceptos de **infraestructura de origen** y de **Managed Bare Metal de destino**.

### Contexto Managed Bare Metal

#### Seguridad

##### **Contexto de conexión al Managed Bare Metal**

Para conectarse a la plataforma VMware, puede bloquear el acceso al vSphere por defecto. Para ello, consulte nuestra guía sobre la [política de acceso al vCenter](../modificar-politica-acceso-al-vcenter/).

Tras el cambio de la política de acceso, si esta pasa a ser "restringida", hay que aplicar por supuesto las mismas IP de conexión en el Managed Bare Metal de destino que en la infraestructura de origen.

##### **Usuarios del Managed Bare Metal**

En el ciclo de vida de la infraestructura original, puede haber creado una lista de usuarios para necesidades ocupacionales o de organización. Por lo tanto, deberá crearlos de nuevo en el Managed Bare Metal de destino y asignarles los permisos adecuados, en función de la configuración del Managed Bare Metal de destino.

Consulte nuestras guías para [cambiar los permisos de un usuario](../cambiar-los-permisos-de-un-usuario/), [cambiar la contraseña de un usuario](../cambiar-contrasena-usuario/) y [asociar un mensaje de correo electrónico a un usuario](../asociar-direccion-correo-electronico-a-usuario-vsphere/).

##### **KMS**

Si máquinas virtuales están protegidas por encriptado y esto es un requisito para el Managed Bare Metal de destino, será necesario crear el contexto de encriptado en este último.

Si lo desea, consulte nuestra guía sobre la [activación del cifrado de las máquinas virtuales](../vm-encrypt/) para activar el KMS en la solución Managed Bare Metal de destino.

#### Red

##### **vRack**

> [!warning]
>
> Las VMnetwork situadas en la misma región no podrán interconectarse con un vRack.
>

En el marco de una migración, puede asociar sus servicios Managed Bare Metal al del mismo vRack. Consulte nuestra guía sobre [el uso de Private Cloud en un vRack](../vrack-essentials/).

##### **Red pública**

> [!warning]
>
> Si su solución Private Cloud/Dedicated Cloud es anterior a 2016, contacte con nuestro equipo de soporte para comprobar los requisitos necesarios.
>

Si las direcciones IP públicas asociadas a la infraestructura de origen son necesarias en el Managed Bare Metal de destino, será necesario realizar la transferencia.

Consulte nuestra guía sobre cómo [migrar bloques de IP entre dos servicios Managed Bare Metal](../anadir-bloque-ip/#migrar-un-bloque-de-ip-entre-dos-managed-bare-metal).

El siguiente vídeo también muestra cómo mover un bloque de direcciones IP entre dos Hosted Private Cloud. Este método también es aplicable a una solución Managed Bare Metal.

<iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/Gemao3Fd7rI" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

### Contexto VMware

#### Configuración de los clusters

##### **Configuración de VMware HA**

La migración requiere rehacer la configuración de VMware High Availability (HA), incluyendo el orden y la prioridad del boot. Consulte [nuestra guía sobre la configuración](../vmware-ha-high-availability/).

A continuación ofrecemos una lista de los elementos que deben tenerse en cuenta:

- Ajustes de monitoring del host
- Ajustes de monitorización de las MV
- Control de admisión
- Opciones HA avanzadas
- Sustituciones de MV

**Consejos de automatización:**
La miniaplicación de comando Powercli "Get-Cluster" devuelve información sobre los parámetros de configuración HA y DRS que pueden aplicarse al cluster de destino con el applet de comandos "Set-Cluster".

##### **Configuración de VMware DRS**

La migración implica la reconfiguración de la función VMware DRS (Distributed Resource Scheduler), especialmente de las reglas de afinidad o antiafinidad para los grupos de hosts y VMs. Consulte nuestra guía sobre la [configuración de VMware DRS](../vmware-drs-distributed-ressource-scheduler-new/).

A continuación le ofrecemos una lista de los elementos que deben tenerse en cuenta:

- Nivel de automatización
- Grupos de máquinas virtuales y hosts
- Reglas de afinidad/antiafinidad de las MV/hosts
- Reemplazo de máquinas virtuales

**Consejos de automatización:** [Este hilo de discusión de VMware](https://communities.vmware.com/t5/VMware-PowerCLI-Discussions/Backup-Restore-DRS-VM-affinity-anti-affinity-rules-can-these-be/td-p/733981/page/2) explica las opciones de exportación e importación de las reglas de afinidad a través de powercli.

##### **Pools de recursos**

La migración requiere reconstruir los pools de recursos, incluyendo las reservas, los recursos compartidos y las aplicaciones virtuales. Esto también se aplica a las vApps y a cualquier configuración de comandos de arranque que se establezca en las vApps.

Para más información, consulte la documentación de [VMware para la gestión de pools de recursos](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.resmgmt.doc/GUID-60077B40-66FF-4625-934A-641703ED7601.html){.external}.

A continuación ofrecemos una lista de los elementos que deben tenerse en cuenta:

- Parámetros de recursos compartidos CPU/memoria
- Reservas CPU/memoria
- Opción escalable CPU/Memory
- Límites CPU/memoria

**Consejos de automatización:** La miniaplicación de comandos Powercli "Get-ResourcePool" para recopilar la información de la lista de recursos compartidos y el applet de comandos "New-ResourcePool" para recrear la lista de recursos compartidos en el Managed Bare Metal de destino.

##### **Clusters de los datastores**

Si los clusters de datastores están presentes en la infraestructura de origen, es posible que sea necesario volver a crear estos clusters de datastores en el Managed Bare Metal de destino, si es necesario tener el mismo nivel de estructura y el SDRS.

A continuación le ofrecemos una lista de los elementos que deben tenerse en cuenta:

- Nivel de automatización SDRS
- Espacio SDRS, I/O, regla, estrategia, parámetros de evacuación de las MV
- Reglas de afinidad/antiafinidad SDRS
- Modificaciones de las MV SDRS

##### **Redes**

La migración implica la recreación de los grupos de puertos virtuales del vRack en el Managed Bare Metal de destino para garantizar la coherencia de la red de MV. Si hay VLAN vRack en uso en el vRack de la infraestructura de origen, pueden utilizarse para extender el dominio L2 al Managed Bare Metal de destino con el fin de permitir un plan de migración más escalonado. Para más información, consulte nuestra guía sobre el [uso del cloud privado en un vRack](../vrack-essentials/).

A continuación le ofrecemos una lista de los elementos que deben tenerse en cuenta:

- Tipo de VLAN de portgroups
- Configuración de seguridad
- Parámetros de Teaming y Failover
- Asignación de recursos de red del cliente

Para más información, consulte la documentación de VMware :
<br>- >- [Teaming & Failover](https://docs.vmware.com/en/VMware-vSphere/6.0/com.vmware.vsphere.hostclient.doc/GUID-BB8EC262-5F85-4F42-AFC5-5FED456E2C11.html)

**Consejos de automatización:** La miniaplicación de comando Powercli "Export-VDPortGroup" puede obtener información de grupos virtuales distribuidos, que pueden importarse a continuación en el Distributed Switch de destino utilizando el applet de comandos "New-VDPortgroup -BackupPath".

##### **OVHcloud Veeam Backup**

Si la aplicación Veeam proporcionada por OVHcloud se utiliza actualmente para guardar las MV en la infraestructura de origen, será necesario volver a crear la configuración de backup para las MV en el Managed Bare Metal de destino después de la migración.

A continuación le ofrecemos una lista de los elementos que deben tenerse en cuenta:

- Realizando el backup de la lista de máquinas virtuales...
- Parámetros de backup

Consulte nuestra guía para [activar y utilizar Veeam Backup Managed](https://docs.ovh.com/gb/en/managed-bare-metal/veeam-backup-as-a-service/).

**Consejos de automatización:** La API OVHcloud proporciona información relacionada con cada backup de MV a través de:

> \[!api]
>
> @api {GET} /dedicatedCloud/{serviceName}/datacenter/{datacenterId}/vm/{vmId}
>

La sección "Backup" del archivo de devolución de JSON proporciona información sobre la configuración actual del backup.

#### Organización del inventario

Por motivos organizativos, las MV, los hosts o los datastores pueden haber sido colocados en repertorios.

Si esta organización sigue siendo necesaria, deberá crearla de nuevo en el Managed Bare Metal de destino.

**Consejos de automatización:** Utilice el applet de comandos Powercli "Get-Folder" para recopilar información sobre la carpeta y el applet de comandos "New-Folder" para recrear la carpeta en el Managed Bare Metal de destino.

#### VM

Existen varias formas de migrar las MV de una infraestructura a otra. Le ofrecemos la posibilidad de realizar la migración utilizando la solución Veeam y la tecnología Veeam Replication.

Para ello, son necesarios los siguientes elementos:

* licencias SPLA (en la infraestructura de origen y el Managed Bare Metal de destino);
* une licence Veeam;
* una dirección IP disponible en la infraestructura de origen y el Managed Bare Metal de destino;
* una máquina virtual [Veeam Backup & Replication](../../storage/veeam-backup-replication/) en la infraestructura original;
* [Autorizar a la máquina virtual Veeam Backup & Replication a conectarse](../autorizar-direcciones-ip-a-conectarse-al-vcenter/) al vCenter de origen y de destino.

Consulte nuestra guía [Veeam Backup & Replication](../../storage/veeam-backup-replication/).

En el siguiente vídeo se explica cómo configurar un Hosted Private Cloud con la solución Veeam Backup & Replication. Este método también es aplicable a una solución Managed Bare Metal.

<iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/f8ufrsP4PQw" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>


<br>En el siguiente vídeo se explica cómo replicar la infraestructura Hosted Private Cloud con la solución Veeam Backup & Replication. Este tutorial también es válido para una solución Managed Bare Metal.

<iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/NqNtKrJSH8w" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>


<br>También puede consultar [la documentación de Veeam](https://www.veeam.com/veeam_backup_10_0_user_guide_vsphere_pg.pdf){.external}.


## Más información

Interactúe con nuestra comunidad de usuarios en <https://community.ovh.com/en/>.
