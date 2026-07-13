# Docker Swarm

## 1. Inicialización del Clúster Swarm

Para comenzar a trabajar con Docker Swarm, lo primero que hacemos es inicializar el clúster con el siguiente comando:

```bash
docker swarm init
```

Al ejecutar este comando, nos mostrará que el nodo actual se ha convertido en el nodo manager del clúster y nos indicará los comandos que se necesitan para agregar otros nodos al clúster como workers o managers, utilizando un token de autenticación proporcionado por el nodo manager.

Mostrándonos algo como esto:

```text
Swarm initialized: current node (pi7pd0cry7v0apgdzro9rawk9) is now a manager.

To add a worker to this swarm, run the following command:

    docker swarm join --token SWMTKN-1-0lme3vh7vup4lgzgudgf23kbz9zyf7fvmu0pxhne47wyoh5z8j-0irnic2un9pr2r1ixsh6e3a1r 192.168.65.3:2377

To add a manager to this swarm, run 'docker swarm join-token manager' and follow the instructions.
```

Si ejecutamos alguno de esos comandos en otro nodo, este se unirá al clúster como worker o manager según el comando que hayamos ejecutado.

En este caso trabajaremos con un clúster de un solo nodo, por lo que no es necesario ejecutar los comandos para agregar otros nodos al clúster.

Podemos verificar cuáles son los nodos que forman parte del clúster con el siguiente comando:

```bash
docker node ls
```

```text
ID                            HOSTNAME         STATUS    AVAILABILITY   MANAGER STATUS   ENGINE VERSION
5aqogej4hu57dl9cumal252vz *   docker-desktop   Ready     Active         Leader           29.6.1
```

Y esto nos mostrará información sobre los nodos que forman parte del clúster. Como podemos ver en este caso, solo hay un nodo que es el nodo manager, su estado es "Ready" y su disponibilidad es "Active".

---

## 2. Creación y Visualización de Servicios

Una vez inicializado el clúster, podemos crear un servicio en él con el siguiente comando:

```bash
docker service create --name mi_servicio --publish 8080:80 --replicas 3 nginx
```

Esto creará un servicio llamado "mi_servicio" con 3 réplicas del contenedor nginx, que se ejecutarán en los nodos del clúster según la disponibilidad de recursos y la carga de trabajo.

En este caso, donde solo hay un nodo, las 3 réplicas se ejecutarán en el mismo nodo.

Para poder observar los servicios que se están ejecutando en el clúster, podemos ejecutar el siguiente comando:

```bash
docker service ls
```

```text
ID             NAME          MODE         REPLICAS   IMAGE          PORTS
k0nsli3d3uod   mi_servicio   replicated   3/3        nginx:latest   *:8080->80/tcp
```

Si quieres ver dónde se están ejecutando las réplicas del servicio, podemos ejecutar el siguiente comando:

```bash
docker service ps mi_servicio
```

```text
ID             NAME            IMAGE          NODE             DESIRED STATE   CURRENT STATE            ERROR     PORTS
x2k8t1gg1iab   mi_servicio.1   nginx:latest   docker-desktop   Running         Running 30 seconds ago
8t5c6ju4camq   mi_servicio.2   nginx:latest   docker-desktop   Running         Running 30 seconds ago
38hhnfaxm2fh   mi_servicio.3   nginx:latest   docker-desktop   Running         Running 30 seconds ago
```

Esto nos mostrará información sobre las tareas que se están ejecutando para el servicio "mi_servicio", incluyendo el nodo en el que se están ejecutando, su estado y la imagen del contenedor que se está utilizando.

---

## 3. Balanceo de Carga Automático

Ahora, para ver cómo se balancea automáticamente la carga de trabajo dentro del clúster, monitorearemos los logs del servicio que acabamos de crear:

```bash
docker service logs -f mi_servicio
```

Y en una consola aparte, ejecutaremos repetidamente el siguiente comando para hacer peticiones al servicio:

```bash
curl http://localhost:8080
```

Cada vez que ejecutemos el comando curl, veremos en los logs del servicio que se están recibiendo las peticiones y que se están distribuyendo entre las réplicas del servicio, lo que nos permite observar cómo Docker Swarm balancea automáticamente la carga de trabajo dentro del clúster:

```text
mi_servicio.1.x2k8t1gg1iab@docker-desktop    | 10.0.0.2 - - [07/Jul/2026:03:12:46 +0000] "GET / HTTP/1.1" 200 896 "-" "Mozilla/5.0 (Windows NT; Windows NT 10.0; es-MX) WindowsPowerShell/5.1.26100.8655" "-"
mi_servicio.2.8t5c6ju4camq@docker-desktop    | 10.0.0.2 - - [07/Jul/2026:03:12:50 +0000] "GET / HTTP/1.1" 200 896 "-" "Mozilla/5.0 (Windows NT; Windows NT 10.0; es-MX) WindowsPowerShell/5.1.26100.8655" "-"
mi_servicio.3.38hhnfaxm2fh@docker-desktop    | 10.0.0.2 - - [07/Jul/2026:03:12:54 +0000] "GET / HTTP/1.1" 200 896 "-" "Mozilla/5.0 (Windows NT; Windows NT 10.0; es-MX) WindowsPowerShell/5.1.26100.8655" "-"
```

---

## 4. Escalado del Servicio

También podemos escalar el servicio para aumentar o disminuir el número de réplicas que se están ejecutando en el clúster, con el siguiente comando:

```bash
docker service scale mi_servicio=5
```

Lo que aumentará el número de réplicas del servicio "mi_servicio" a 5, y podemos verificarlo con el comando:

```bash
docker service ls
```

```text
ID             NAME          MODE         REPLICAS   IMAGE          PORTS
k0nsli3d3uod   mi_servicio   replicated   5/5        nginx:latest   *:8080->80/tcp
```

---

## 5. Actualización del Servicio (Rolling Update)

Otra cosa que podemos hacer es actualizar el servicio para cambiar la imagen del contenedor que se está utilizando, con el siguiente comando:

```bash
docker service update --image ubuntu/apache2 mi_servicio
```

Cuando ejecutamos el comando, podemos ver cómo va haciendo el cambio de manera gradual. Esto es para evitar que el servicio quede inactivo mientras se realiza la actualización.

Y una vez que termina, si entramos al servicio con el comando curl, veremos que ahora se está utilizando la nueva imagen del servicio.

En este caso se usó un cambio exagerado para que se note el cambio, sobre todo al entrar desde un navegador.

---

## 6. Reversión de Cambios (Rollback)

Y si quieres deshacer el cambio y volver a la imagen anterior, por si algo no funciona como esperabas, podemos ejecutar el siguiente comando:

```bash
docker service rollback mi_servicio
```

Esto deshace la actualización y vuelve a la imagen anterior del servicio, permitiendo que el servicio vuelva a su estado anterior sin perder la configuración ni los datos.

Y podemos comprobarlo al ejecutar el comando `curl` de nuevo o entrando al navegador, y veremos que ahora se está utilizando la imagen anterior del servicio.

---

## 7. Eliminación y Desmantelado del Clúster

Para eliminar el servicio que creamos, podemos ejecutar el siguiente comando:

```bash
docker service rm mi_servicio
```

Para hacer que el nodo salga del Swarm (lo que desmantelará el clúster al tratarse del nodo manager), podemos ejecutar el siguiente comando:

```bash
docker swarm leave --force
```

Se requiere de `--force` si es el nodo manager, ya que al ser el nodo principal del clúster, no se puede salir del mismo sin forzar la salida.

Usar `--force` en el nodo manager eliminará el clúster y todos los nodos asociados, por lo que se debe tener cuidado al usar este comando.