# Flow4-NodeRed
Este repositorio trata de una estación climática que recibe un objeto JSON con valores de temperatura y humedad por MQTT y se genera un dashboard con indicadores para cada variable y una gráfica con dichos datos.

## Introducción

El flow 4 muestra el uso de los nodos gauge y chart. Recibe un JSON con las variables de temperatura y humedad y grafica sus valores con un indicador de aguja y una gráfica de tiempo.

## Material Necesario

Para realizar este flow necesitas lo siguiente

- [Ubuntu 20.04](https://releases.ubuntu.com/20.04/)
- [Docker Engine](https://docs.docker.com/engine/install/ubuntu/#install-using-the-convenience-script)
- [NodeRed](https://nodered.org/docs/getting-started/local)
- [Nodos Dashboard](https://flows.nodered.org/node/node-red-dashboard)

## Material de referencia

En los siguientes enlaces puedes encontrar cursos en la plataforma de edu.codigoiot.com que te permitirán realizar las configuraciones necesarias

- [Instalación de Virutal Box y Ubuntu 20.04](https://edu.codigoiot.com/course/view.php?id=812)
- [Introducción a Docker](https://edu.codigoiot.com/course/view.php?id=996)
- [Aplicacion multicontenedor de servidor IoT con Docker compose](https://edu.codigoiot.com/mod/lesson/view.php?id=3889&pageid=3804&startlastseen=no)
- [Servidor del Internet de las Cosas con nodeRed](https://edu.codigoiot.com/course/view.php?id=997)


## Instrucciones

Estas son las instrucciones para crear el flow desde cero. Este repositorio incluye el archivo JSON con el Flow funcional.

1. Crear un nuevo flow
2. Agregar un nodo mqtt
	- Agregar un nuevo broker y agregar únicamente la url
	- Server: ```mosquitto``` (o el nombre de tu aplicaciòn)
	- Tema: ```codigoIoT/mqtt/clima```
	- Output: a String
3. Agregar un nodo JSON. Siempre convertir a JavaScript Object
4. Agregar dos nodos function

Nodo function temperatura

```
msg.payload = msg.payload.temp;
msg.topic = "Temperatura";
return msg;
```
Nodo function humedad

```
msg.payload = msg.payload.hum;
msg.topic = "Humedad";
return msg;
```

5. Crear una pestaña y dos grupos de informacion
- Pestaña: Clima local
- Grupo1: Indicadores
- Grupo2: Gráfica
6. Agregar 2 nodos gauge y configurarlos
- Asociarlos al grupo indicadores
- Determine etiquetas y rangos
7. Agregar el nodo chart
- Asociarlos al grupo indicadores
8. Hacer clic en Deploy y consultar el [Dashboard](http://localhost:1880/ui)
9. Enviar un mensaje MQTT al broker local con los valores de temperatura y humedad como temp y hum. Ejemplo:

    ```
    docker exec -it [id_contenedor] mosquitto_pub -h localhost -t codigoIoT/mqtt/clima -m '{"temp":19,"hum":51}'
    ```

### Requisitos previos

Para que este flow funcione, debes cumplir con los siguientes requisitos previos
1. Tener instalado Docker Engine.
2. Tener instaldo nodeRed por Docker Compose
3. Tener el contenedor de NodeRed con el volumen de data activado
4. Configurar el broker local para que use el archivo ```mosquitto.conf``` con un listener en el puerto 1883 para todas las IPs ```0.0.0.0```
5. Tu usuario de linux debe ser parte del grupo sudoers y del grupo docker
    - Puedes comprobar que tu usuario está en ambos grupos con el comando 
    
        ```
        groups $USER
        ```

    - En caso de que tu usuario no forme parte de dichos grupos, puedes arreglarlo con los siguientes comandos
        ``` 
        su
        sudo usermod -aG sudo newuser
        exit
        ```
### Instrucciones de preparación del entorno

Para ejecutar este flow, es necesario lo siguiente.
1. Detener el contenedor de Mosquitto.

    ```
    docker stop [id_contenedor]
    ```

2. Crear el directorio donde se colocará el archivo msoquitto.conf

    ```
    mkdir -p ~/DockerVolumes/Mosquitto/config
    ```

3. Agregar al directorio creado el archivo mosquitto.conf ubicado en este repositorio.
4. Detener Docker Compose.

    ```
    docker compose stop
    ```
5. Levantar de nuevo Docker Compose

    ```
    docker compose up -d
    ```

6. Arrancar el contenedor de NodeRed con el comando
        
    ```
    docker start $(docker ps -a -q)
    ```

2. Dirigirse a [localhost:1880](http://localhost:1880/)
3. Importar el flow desde el repositorio
4. Hacer clic en el boton Deploy

### Instrucciones de operación

Para ver el resultado de este flow es necesario enviar un mensaje MQTT. Puedes hacerlo con el broker MQTT local en Docker Compose.

```
docker exec -it [id_contenedor] mosquitto_pub -h localhost -t codigoIoT/mqtt/clima -m '{"temp":19,"hum":51}'
```

Deberás observar cómo se actualizan los indicadores de aguja. 

Para que se dibujen las lineas en la gráfica historica, necesitas enviar al menos un segundo dato. Puedes enviar de nuevo el mismo comando.

## Resultados

A continuación puede verse una vista previa del resultado de este flow.

![](https://github.com/sergiomerhz/Flow4-NodeRed/blob/main/Imagenes/Captura%20desde%202023-05-29%2022-09-00.png?raw=true)

![](https://github.com/sergiomerhz/Flow4-NodeRed/blob/main/Imagenes/Captura%20desde%202023-05-29%2022-08-30.png?raw=true)


## Evidencias



# Notas
- Este repositorio cuenta con las instrucciones para crear el flow pero también incluye el archivo JSON resultante, así que solo tienes que importarlo a nodeRed.

# Créditos

Desarrollado por Sergio Merino
- [GitHub](https://github.com/sergiomerhz)

Desarrollado por Hugo Escalpelo
- [hugoescalpelo.com](https://hugoescalpelo.com/)
- [Página en Facebook](https://www.facebook.com/Hugo-Escalpelo-Profesional-337708683840136)
- [GitHub](https://github.com/hugoescalpelo)