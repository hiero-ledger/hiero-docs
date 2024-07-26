# Ejecuta tu propio nodo de espejo con Google Cloud Storage (GCS)

## Prerrequisitos

- Una [Google Cloud Platform](https://cloud.google.com/)account.
- Entendimiento básico de los nodos irror de Hedera.
- [Docker](https://www.docker.com/) (`>= v20.10.x)` instalado y abierto en su máquina. Ejecuta `docker -v` en tu terminal para comprobar la versión instalada.
- [Java](https://www.java.com/en/) (openjdk@17: Java version 17), [Gradle](https://gradle.org/install/) (la última versión), y [PostgreSQL](https://www.postgresql.org/) (la última versión) están instalados en su máquina.

## 1. Obtén información de pago de Google Cloud Platform

En este paso, generarás tus claves de acceso HMAC a Google Cloud Platform. Estas claves son necesarias para autenticar solicitudes entre su equipo y Google Cloud Storage. Son similares a un nombre de usuario y una contraseña. Sigue estos pasos para recuperar tu **clave de acceso, secreto**, y **ID del proyecto**:

- Crea un nuevo [project](https://cloud.google.com/resource-manager/docs/creating-managing-projects) y vincula tu [cuenta de facturación](https://cloud.google.com/billing/docs/how-to/manage-billing-account).
- En la barra de navegación izquierda, selecciona **Almacenamiento en la nube > Configuración.**
- Haga clic en la pestaña **Interoperabilidad** y desplácese a la sección **Cuenta de usuario HMAC**.
- Si aún no tiene un conjunto de proyectos predeterminado, establézcalo ahora.
- Haga clic en **crear claves** para generar claves de acceso para su cuenta.

<figure><img src="../../../.gitbook/assets/gcs mirror2.png" alt=""><figcaption></figcaption></figure>

- Deberías ver la **clave de acceso** y las columnas **secreta** rellenan en la tabla de teclas de acceso.
- Utilizarás estas claves para configurar el archivo **`application.yml`** en un paso posterior.

## 2. Clonar repositorio de nodo irror de Hedera

- Abre tu terminal y ejecuta los siguientes comandos para clonar `hedera-mirror-node` [repository](https://github.com/hashgraph/hedera-mirror-node) y luego `cd` en la carpeta `hedera-mirror-node`:

```bash
git clon https://github.com/hashgraph/hedera-mirror-node
cd hedera-mirror-node
```

## 3. Configurar nodo espejo

El archivo **`application.yml`** es el archivo de configuración principal para el nodo Mirror de Hedera. Actualizaremos ese archivo con tus claves GCP y la red Hedera que quieres reproducir.

- Abre el archivo `application.yml` en el directorio raíz con un editor de texto de tu elección.
- Encuentra la siguiente sección y reemplaza los marcadores de posición con tu **clave de acceso**, **clave secreta**, **ID del proyecto**, y la red que quieres replicar:

| Objeto                | Descripción                                              |
| --------------------- | -------------------------------------------------------- |
| **accessKey**         | Tu clave de acceso desde tu cuenta GCP                   |
| **Proveedor de nube** | GCP                                                      |
| **Clave secreta**     | Tu clave secreta de tu cuenta GCP                        |
| **gcpProjectId**      | Su ID de proyecto GCP                                    |
| **red**               | Introduzca la red que desea ejecutar su nodo espejo para |

{% code title="application.yml" %}

```yaml
hedera:
  espejo:
    importador:
      descargador:
        accessKey: ENTER ACCESS KEY HERE
        cloudProvider: "GCP"
        secretKey: ENTER SECRET KEY HERE
        gcpProjectId: ENTER GCP PROJECT ID HERE
      network: PREVIEWNET/TESTNET/MAINNET #Elige una red
```

{% endcode %}

- Guarda los cambios y cierra el archivo.

## 4. Inicia tu nodo Hedera Mirror

Ahora vamos a iniciar el nodo Mirror de Hedera usando Docker. Docker le permite ejecutar fácilmente aplicaciones en un entorno autónomo llamado _container_.

- Desde el directorio `hedera-mirror-node`, ejecuta el siguiente comando:

```bash
docker compone -d db && docker logs hedera-mirror-node-db-1 --follow
```

## 5. Accede a tus datos del nodo Hedera Mirror

Este paso te muestra cómo acceder a los datos que tu nodo Hedera Mirror está recopilando. El nodo réplica almacena sus datos en una base de datos PostgreSQL, y está usando Docker para conectarse a esa base de datos. Para acceder a los datos del nodo espejo, tendremos que introducir el contenedor **`hedera-mirror-node-db-1`**.

- Abrir un nuevo terminal y ejecutar el siguiente comando para ver la lista de contenedores:

```bash
acopladores ps
```

- Introduzca el siguiente comando para acceder al contenedor Docker:

```bash
docker exec -it hedera-mirror-node-db-1 bash
```

- Introduzca el siguiente comando para acceder a la base de datos:

```bash
psql "dbname=mirror_node host=localhost user=mirror_node password=mirror_node_pass port=5432"
```

- Introduzca el siguiente comando para ver la lista completa de tablas de base de datos:

```bash
\dt
```

![](<../../../.gitbook/assets/image (4).png>)

- Para salir de la consola `psql`, ejecuta el comando quit:

```bash
q
```

- Por último, ejecute el siguiente comando para detener y eliminar los contenedores creados:

```bash
docker componer abajo
```

#### ¡Felicidades! Has ejecutado y desplegado con éxito un nodo Hedera Mirror con Google Cloud Storage (GCS) 🚀
