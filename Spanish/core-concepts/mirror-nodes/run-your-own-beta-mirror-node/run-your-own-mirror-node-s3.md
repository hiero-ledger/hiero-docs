# Run Your Mirror Node with Amazon Web Services S3 (AWS)

## Prerrequisitos

- An [Amazon Web Services](https://aws.amazon.com/free/?trk=ps\_a131L0000085DvcQAE\\&trkCampaign=acq\_paid\_search\_brand\\&sc\_channel=ps\\&sc\_campaign=acquisition\_US\\&sc\_publisher=google\\&sc\_category=core\\&sc\_country=US\\&sc\_geo=NAMER\\&sc\_outcome=acq\\&sc\_detail=aws%20account\\&sc\_content=Account\_e\\&sc\_segment=432339156165\\&sc\_medium=ACQ-P|PS-GO|Brand|Desktop|SU|AWS|Core|US|EN|Text\\&s\_kwcid=AL!4422!3!432339156165!e!!g!!aws%20account\\&ef\_id=Cj0KCQjw8IaGBhCHARIsAGIRRYrLfWc3ykRf\_hAUeVvf4nNEYvacHwk\_w1jAuSj6hQZ8\_muh0T5p3acaAkZDEALw\_wcB:G:s\\&s\_kwcid=AL!4422!3!432339156165!e!!g!!aws%20account\\&all-free-tier.sort-by=item.additionalFields.SortRank\\&all-free-tier.sort-order=asc\\&awsf.Free%20Tier%20Types=*all\\&awsf.Free%20Tier%20Categories=*all) account.
- Entendimiento básico de los nodos irror de Hedera.
- [Docker](https://www.docker.com/) (`>= v20.10.x)` installed and opened on your machine. Ejecuta `docker -v` en tu terminal para comprobar la versión instalada.
- [Java](https://www.java.com/en/) (openjdk@17: Java version 17), [Gradle](https://gradle.org/install/) (the latest version), and [PostgreSQL](https://www.postgresql.org/) (the latest version) are installed on your machine.

## 1. Create an IAM user

This step will teach you how to create a new IAM (_Identity and Access Management)_ user and generate new access keys in your AWS account. The **access key,** **secret** and **project ID** will be used to access S3 from the Hedera Mirror Node.

- Create an [IAM (Identity and Access Management) user](https://docs.aws.amazon.com/IAM/latest/UserGuide/getting-set-up.html#create-an-admin) with either an administrator or custom policy. If you're unfamiliar with using AWS, go with the administrator policy:

{% tabs %}
{% tab title="Administrator Policy" %}

- Refer to AWS documentation to create an IAM user with an administrator policy [here](https://docs.aws.amazon.com/IAM/latest/UserGuide/getting-started\_create-admin-group.html)
  - This sets up an IAM user with Administrator Access Policy
  - This user has full access and can delegate permissions to every service and resource in AWS.
- Once that is complete, select **Users** from the left IAM navigation bar
- Select the **Administrator** from the **User name** column
- Select the **Security credentials** tab
- Select **Create access key**
- Copy or download your **Access key ID** and **Secret access key**
  {% endtab %}

{% tab title="Custom Policy" %}

- Enable access to billing data
  - Sigue el paso 2 [here](https://docs.aws.amazon.com/IAM/latest/UserGuide/getting-started\_create-admin-group.html)
- Desde la barra de navegación izquierda del IAM seleccione **Políticas**
- Haga clic en **Crear política**
  - Servicio
    - Introduce **S3** como tu servicio
  - Acciones
    - Nivel de acceso
    - Selecciona **Lista** y **Lectura**
- Recursos
  - Selecciona **Especifica el ARN del recurso del cubo para la GetBucketLocation**
  - Añadir ARN
    - hedera-mainnet-streams
  - Añadir ARN
    - hedera-mainnet-streams/\*
- Haga clic **Siguiente:Etiquetas**
- Haga clic **Siguiente: revisión**
  - Introduzca un nombre para la política
- Haga clic en **Crear política**
- Desde la barra de navegación izquierda de la consola IAM seleccione **Usuario** **grupos**
- Haga clic en **Crear grupo**
- Introduzca un nombre de grupo de usuarios
- Seleccione la política creada en el paso anterior
- Haga clic en **Crear grupo**
- Haga clic en **Usuarios** de la barra de navegación izquierda de la consola IAM
- Haga clic en **Agregar usuario**
- Introduzca nombre de usuario
- Seleccione **Acceso programático para el tipo de acceso**
- Haz clic **Siguiente: permisos**
- Seleccione el grupo que fue creado en el paso anterior
- Haz clic **Siguiente: Etiquetas**
- Haga clic **Siguiente: revisión**
- Haga clic en **Crear usuario**
- Copia o descarga tu **ID de clave de acceso** y tu **clave de acceso secreta**
  {% endtab %}
  {% endtabs %}

## 2. Clonar el repositorio de nodo espejo

- Abre tu terminal y ejecuta los siguientes comandos para clonar el nodo espejo [repository](https://github.com/hashgraph/hedera-mirror-node), luego `cd` en la carpeta `hedera-mirror-node`:

<pre class="language-bash"><code class="lang-bash"><strong>clon de git https://github.com/hashgraph/hedera-mirror-node.git
</strong>cd hedera-mirror-node
</code></pre>

## 3. Configurar nodo espejo

El archivo `application.yml` es el archivo de configuración principal para el nodo Mirror de Hedera. En este paso, actualizaremos el archivo de configuración con sus ajustes específicos, como su clave de acceso AWS, secreto y la red Hedera que desea replicar.

- Abre el archivo `application.yml` en el directorio raíz con un editor de texto de tu elección.
- `cd` en el directorio `hedera-mirror-node` desde tu terminal o IDE.
- Encuentre los siguientes campos y reemplace los marcadores de posición con su clave de acceso AWS real, clave secreta, ID del proyecto y la red que desea replicar:

| Objeto                | Descripción                                          |
| --------------------- | ---------------------------------------------------- |
| **accessKey**         | Clave de acceso AWS                                  |
| **Proveedor de nube** | s3                                                   |
| **Clave secreta**     | Clave secreta AWS                                    |
| **red**               | Introduzca una red para ejecutar un nodo espejo para |

{% code title="application.yml" %}

```yaml
hedera:
  espejo:
    importador: 
      descargador:
        accessKey: ENTER ACCESS KEY HERE
        cloudProvider: "s3"
        secretKey: ENTER SECRET KEY AERE
      red: PREVIEWNET/TESTNET/MAINNET #Elige una red
```

{% endcode %}

## 4. Ejecuta tu nodo espejo

Iniciar y ejecutar el nodo Mirror de Hedera usando Docker. Docker paquetes herramientas de desarrollo en un entorno autónomo llamado _container_ que puede incluir bibliotecas, código, tiempo de ejecución y más.

- Desde el directorio del nodo espejo, ejecute el siguiente comando:

```bash
docker compone -d db && docker logs hedera-mirror-node-db-1 --follow
```

## 5. Accede a tus datos de nodo espejo

Después de que el nodo espejo se ejecute con éxito, descarga datos de la red Hedera y lo almacena en una base de datos PostgreSQL. Para acceder a los datos del nodo espejo, ingrese el contenedor de la base de datos y conéctese usando Docker y la herramienta de línea de comandos `psql`.

- Abrir un nuevo terminal y ejecutar el siguiente comando para ver la lista de contenedores:

```bash
acopladores ps
```

<figure><img src="../../../.gitbook/assets/docker ps (1).png" alt=""><figcaption></figcaption></figure>

- Ejecuta el siguiente comando para ingresar al contenedor `hedera-mirror-node-db-1`:

```bash
docker exec -it hedera-mirror-node-db-1 bash
```

- Introduzca el siguiente comando para acceder y consultar la base de datos:

```bash
psql "dbname=mirror_node host=localhost user=mirror_node password=mirror_node_pass port=5432"
```

- Introduzca el siguiente comando para ver la lista completa de todas las tablas de la base de datos:

```bash
\dt
```

<figure><img src="../../../.gitbook/assets/list of relations s3 mirror.png" alt=""><figcaption></figcaption></figure>

- Para salir de la consola de base de datos `psql`, ejecuta el comando quit:

```bash
q
```

- Por último, ejecute el siguiente comando para detener Docker y eliminar los contenedores creados:

```bash
docker componer abajo
```

#### ¡Felicidades! Has ejecutado y desplegado con éxito un nodo Hedera Mirror con Amazon Web Services S3 (AWS) 🚀
