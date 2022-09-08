---
description: This section does not cover the LPIC-1 certification
---

# ⛔ Hardening Linux

## IPTABLES

Iptables es una herramienta de filtrado de paquetes en Linux que permite a un administrador de sistemas configurar reglas de filtrado de paquetes IP del firewall del kernel de Linux.

> **IPTABLES se encarga de analizar cada paquete de tráfico de red entra en una máquina y decidir, en función de un conjunto de reglas, qué hacer con ese paquete**

Iptables se encuentra estructurado de la siguiente forma:

* Reglas
* Cadenas (INPUT, OUTPUT, etc)
* Tablas (FILTER, NAT, etc)

Dentro de las cadenas tenemos **INPUT** y **OUTPUT**, estas cadenas nos permite decidir que paquete entra y sale, estas 2 cadenas forman parte de la tabla **FILTER** que su nombre propio lo indica y se encarga de filtrar en base a la cadena que se le especifique.

{% hint style="warning" %}
Iptables por defecto toma la tabla `FILTER` en caso de que no se lo especifiquemos.&#x20;
{% endhint %}

#### Sintaxis de Iptables

```bash
iptables [-t tabla] -A/I cadena [opciones] -j acción
```

Cuando establecemos reglas en iptables la propia herramienta tiene un orden descendente por lo que va leyendo las reglas desde arriba hacia abajo, para poder añadir una regla al principio o al final tenemos que especificarselo mediante la siguiente opcion:

`-A [APPEND]:` Esta opcion inserta la regla al final de la lista

`-I [INSERT]:` Esta opcion inserta la regla al principio

{% code lineNumbers="true" %}
```bash
#Esta regla acepta los paquetes de entrada del protocolo SSH 
iptables -t filter -A INPUT -p ssh -j ACCEPT

#Al no poner -t, iptables automaticamente toma por referencia la tabla filter, con
# -i/-o especificamos la interfaz por donde entra/sale el paquete
iptables -A INPUT -i lo -j ACCEPT
iptables -A OUTPUT -o lo -j ACCEPT
```
{% endcode %}











&#x20;
