# Prácticas de Redes en Docker

## 1. Creación y Configuración de Redes

Para probar cómo funcionan las redes en Docker, primero crearemos una red básica:

```bash
docker network create mi-red-test
```

Si queremos verificar que se creó correctamente, podemos listar las redes disponibles:

```bash
docker network ls
```

### Redes personalizadas con Bridge
Podemos crear redes con configuraciones específicas. Por ejemplo, definiendo una subred y una puerta de enlace:

```bash
docker network create -d bridge --subnet 172.124.10.0/24 --gateway 172.124.10.1 mi-red-personalizada
```

**Lo que se está haciendo aquí:** Se crea una red con el driver `bridge`, asignando un rango de IP específico para los contenedores y definiendo una puerta de enlace. Esto permite un mayor control y facilita la comunicación interna.

Para inspeccionar los detalles de la red recién creada:

```bash
docker network inspect mi-red-personalizada
```
Esto mostrará detalles como contenedores conectados, configuración de IP y el controlador utilizado.

---

## 2. Conectando Contenedores a Redes

### Al momento de la creación
Para conectar un contenedor a una red específica al momento de crearlo, usamos el flag `--network`:

```bash
docker run -dti --network mi-red-personalizada --name alpine-test-network alpine
```

Para comprobar la conexión, podemos inspeccionar el contenedor:
```bash
docker inspect alpine-test-network
```
*(La información se encuentra en el apartado `"NetworkSettings": { "Networks": {...} }`)*.

También podemos verificar la IP asignada dentro del contenedor:
```bash
docker exec -ti alpine-test-network sh
/ # ip a
```

### Conectar un contenedor ya existente
Si el contenedor ya fue creado (por defecto en la red `bridge`), podemos agregarlo a nuestra red manualmente:

```bash
docker run -dti --name alpine-test-network2 alpine
docker network connect mi-red-personalizada alpine-test-network2
```

**NOTA:** Si agregas un contenedor a una red personalizada después de haberlo creado, este estará conectado a **dos redes**:
1. La red `bridge` por defecto.
2. Tu red personalizada (`mi-red-personalizada`).

Para desconectarlo de la red inicial:
```bash
docker network disconnect bridge alpine-test-network2
```

---

## 3. Comunicación y Resolución DNS

Dentro de la red personalizada `mi-red-personalizada`, las IPs asignadas son:
- **Contenedor 1 (`alpine-test-network`):** 172.124.10.2
- **Contenedor 2 (`alpine-test-network2`):** 172.124.10.3

### Prueba de comunicación por IP
Entramos al contenedor 1 y hacemos ping al contenedor 2:
```bash
docker exec -ti alpine-test-network sh
/ # ping 172.124.10.3
```
**Output:**
```plaintext
PING 172.124.10.3 (172.124.10.3): 56 data bytes
64 bytes from 172.124.10.3: seq=0 ttl=64 time=2.61 ms
```

### Resolución DNS automática
A diferencia de la red `bridge` por defecto, las redes personalizadas permiten la comunicación por **nombre de contenedor**:

```bash
/ # ping alpine-test-network2
```
**Output:**
```plaintext
PING alpine-test-network2 (172.124.10.3): 56 data bytes
64 bytes from alpine-test-network2.mi-red-personalizada (172.124.10.3): seq=0 ttl=64 time=0.261 ms
```
Esto confirma que el DNS interno de Docker está funcionando correctamente.

---

## 4. Aislamiento entre Redes

Para probar el aislamiento, crearemos una segunda red con un rango de IPs distinto:

```bash
docker network create -d bridge --subnet 172.124.11.0/24 --gateway 172.124.11.1 mi-otra-red
docker run -dti --network mi-otra-red --name alpine-test-network3 alpine
```
*   **Contenedor 3 IP:** 172.124.11.2

Si intentamos hacer ping desde el **Contenedor 1** al **Contenedor 3**, la comunicación fallará porque están en redes aisladas:
```bash
docker exec alpine-test-network sh -c "ping alpine-test-network3"
```
**Output:** `ping: bad address 'alpine-test-network3'`

Para permitir la comunicación, debemos conectar el Contenedor 3 a la primera red:
```bash
docker network connect mi-red-personalizada alpine-test-network3
```
Ahora el ping será exitoso. Para revertirlo, usamos `disconnect`.

---

## 5. Asignación Manual de Direcciones IP

Podemos forzar una IP específica dentro del rango de nuestra red usando el flag `--ip`:

**Al crear el contenedor:**
```bash
docker run -dti --network mi-red-personalizada --ip 172.124.10.15 --name alpine-test-network4 alpine
```

**Al conectar uno existente:**
```bash
docker run -dti --name alpine-test-network5 alpine
docker network connect mi-red-personalizada --ip 172.124.10.16 alpine-test-network5
```

---

## 6. Redes Especiales (none y host)

### Red `none`
El contenedor no tendrá ninguna interfaz de red configurada (está totalmente incomunicado).
```bash
docker run -dti --network none --name alpine-test-none alpine
```
Si inspeccionamos esta red, veremos que no hay IPs asignadas. **Nota:** Un contenedor en modo `none` no puede conectarse a otras redes simultáneamente.

### Red `host`
El contenedor comparte directamente la red de la máquina host. No tiene una IP propia de Docker.
```bash
docker run -dti --network host --name alpine-test-host alpine
```
Aunque en `inspect` no aparezca una IP, está usando la del host. Al igual que con `none`, no se puede conectar a otras redes si está en modo `host`.

---

## 📌 Cosas a tener en cuenta

*   **Eliminación:** No se pueden eliminar redes que tengan contenedores conectados. Usa `docker network rm [nombre]`.
*   **Estado:** Un contenedor detenido no aparece como "conectado" activamente en la inspección de red, pero mantiene su configuración.
*   **Mínimos:** Todo contenedor debe estar conectado al menos a una red para funcionar (por defecto es `bridge`).
*   **DNS:** La red `bridge` integrada (por defecto) **no admite DNS**. Solo las redes creadas por el usuario permiten comunicarse por nombre.
*   **Persistencia de IP:** Si asignas una IP manualmente, Docker la respetará incluso después de reiniciar el contenedor.
*   **Conflicto de IP:** Si asignas una IP manualmente y otro contenedor la toma mientras el primero está apagado, el primero no podrá iniciar.
*   **Limitaciones de Redes Especiales:** Un contenedor no puede estar en más de una red si ya está conectado a `none` o `host`.

### Comandos de Limpieza
```bash
# Eliminar una red específica
docker network rm mi-red-personalizada

# Eliminar todas las redes que no se estén usando
docker network prune
```
