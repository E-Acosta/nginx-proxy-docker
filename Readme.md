# NGINX Reverse Proxy + SSL
------------------------------------------------
_Este repositorio contiene los archivos necesarios para la instalacion de un proxy inverso utilizando docker_

### Requisitos
+ Docker
+ Acceso a registros DNS

### Instalacion

1. **Crear Redes de docker**
    Para que los servicios que se van a utilizar sean accesibles por el proxy es necesario que se encuentren en el mismo namespace de red.
    En el caso de este repositorio es necesario contar con las redes `u-network` y `proxy-net`
    Para crear redes en docker utilizamos el siguiente comando:<br>
    >`docker network create network_name --driver bridge `

    Para mas informacion acerca de redes en docker [aqui](https://docs.docker.com/network/)
2. **Levantar el proxy**
    >`docker-compose up -d`
3. **Detener el proxy**
    >`docker-compose down`
4. **Configurar los servicios**
    Una vez el proxy esta corriendo es necesario que los servicios cumplan con 2 condiciones especiales para que se puedan acceder a través de una dirección dns.
    + *Estar en el mismo namespace de red del proxy*
    + *Exponer internamente el puerto necesario del contenedor*
    + *Tener las siguientes variable de entorno*: `VIRTUAL_HOST` y `VIRTUAL_PORT`

    4.1. **Variables de Entorno**
    | Variable | Descripcion | Ejemplo |
    | -------- | ----------- | ------- |
    | VIRTUAL_HOST | Dns con el que será accedido el contenedor. Si el contenedor tiene mas de un dns entonces se pueden especificar separados por ','| `VIRTUAL_HOST=api.example.com` o `VIRTUAL_HOST=api.example.com,backend.example.com` |
    | VIRTUAL_PORT | Puerto que internamente expone el contenedor. Esta varible es opcional, si no se coloca se toma por defecto el puerto 80 del contenedor | `VIRTUAL_PORT=3000`|
    | HTTPS_METHOD | Define si el entrypoint sera seguro o no. Los posibles valores que puede tener son: `noredirect` para no redirigir del puerto 80 al 443,`redirect` para forzar la redireccion del puerto 80 al 443 (es la opcion por default) y `nohttps` para no permitir el trafico https | `HTTPS_METHOD=nohttps`|
    | LETSENCRYPT_HOST | Dns que utilizara LETSENCRYPT para validar el certificado, se recomieda que sea exactamente el mismo del VIRTUAL_HOST|`LETSENCRYPT_HOST=api.racingkids.co` |
    | CERT_NAME | Nombre del certificado ssl que utilizará este contenedor| `CERT_NAME=shared`|

5. **Configuracion SSL**
    5.1 **LETSENCRYPT**
    _Para habilitar el ssl con LETSENCRYPT solo es necesario agregar la variable de entorno `LETSENCRYPT_HOST` al contenedor que se quiere agregar https, y el dominio que se utilice debe ser accesible publicamente para que el api de LETSENCRYPT pueda validarlo_<br>
    5.2 **CUSTOM SSL**    
    _Para utilizar un certificado ya generado se agreaga el certificado y la llave privada a la carpeta **`./certs`** ambos con el mismo nombre, y ese nombre se agrega como valor de la variable de entorno `CERT_NAME` en el contenedor que lo quiera utilizar_ 

----------------------------------------------------------------

#### Referencias
+ [NGINX REVERSE PROXY](https://hub.docker.com/r/jwilder/nginx-proxy)
+ [LETSCRIPT  Companion](https://hub.docker.com/r/jrcs/letsencrypt-nginx-proxy-companion)