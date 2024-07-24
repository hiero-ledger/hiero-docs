# Configuración multinode

## Usar configuración de Multinode

La configuración de Multinode es una característica avanzada diseñada para escenarios específicos que requieren múltiples nodos de consenso. Esta configuración requiere mayores recursos e implica más complejidad, haciéndola adecuada principalmente para entornos de pruebas y desarrollo. Antes de intentar usar la configuración multinode, es crucial asegurarse de que el nodo local funciona correctamente en el modo predeterminado de un solo nodo.

<details>

<summary><strong>Modo Multinode Requisitos</strong></summary>

Para ejecutar el modo multinode, asegurarse de que las siguientes configuraciones se establecen como mínimo en las **Configuraciones** de Docker -> **Recursos** y al menos 14 GB de memoria están disponibles para Docker:

- **CPUs:** 6
- **Memoria:** 14 GB
- **Intercambio:** 1 GB
- **Tamaño de imagen de disco:** 64 GB

<img src="../../.gitbook/assets/localnode-multinode-requirements.png" alt="" data-size="original">

</details>

{% hint style="info" %}
_**📣 Nota**: Crear una red descentralizada donde cada nodo se ejecuta independientemente en su propia máquina no está soportado actualmente. Sin embargo, están disponibles capacidades avanzadas de configuración y redes que permiten a los nodos comunicarse entre sí similares a sus interacciones en la red principal de Hedera._
{% endhint %}

#### **Iniciando modo multinode**

Para iniciar el nodo local de Hedera en modo multinode, añade la bandera `--multinode` a tu [comando de inicio](single-node-configuration.md#npm). Por ejemplo:

```bash
# comando npm para iniciar la red local en modo multinode
npm run start -- -d --multinode

# comando docker para iniciar la red local en modo multinode
docker compone -d --multinode
```

Verifica el lanzamiento exitoso del modo multinode inspeccionando la salida de Docker de `docker ps --format "tabla {{.Names}}" | red grep` o el panel de escritorio de Docker. Debe identificar cuatro nodos en ejecución:

```bash
network-node
network-node-1
network-node-2
network-node-3
```

_📣 **Nota**: En modo multinode, necesitas al menos tres nodos saludables para la red operativa._

#### **Iniciando y deteniendo nodos**

Los nodos individuales pueden iniciarse o detenerse para probar consensos, sincronización, y procesos de selección de nodos usando los comandos `npm` o `docker`:&#x20

<details>

<summary><strong>comandos npm</strong></summary>

```bash
# comando npm para iniciar un nodo individual
npm run start network-node-3

# comando npm para detener un nodo individual
npm run stop network-node-3

# comando npm para reiniciar un nodo individual
npm run restart network-node-3
```

</details>

<details>

<summary><strong>acoplador comandos</strong></summary>

```bash
# Comando Docker para iniciar un nodo individual
docker compone start network-node-3

# Comando Docker para detener un nodo individual
docker compone stop network-node-3

# Comando Docker para reiniciar un nodo individual
docker compone reinicio network-node-3

# Comando Docker para comprobar los registros del nodo individual
docker compone logs network-node-3 -f

# Comando Docker para detener la red local y eliminar contenedores
docker compone registros
```

</details>

Alternativamente, ejecuta `docker compose down -v; git clean -xfd; git reset --hard` para detener el nodo local y restablecerlo a su estado original.

#### Diagrama del modo multinode

El siguiente diagrama ilustra la arquitectura y el flujo de datos en modo multinode.

<figure><img src="../../.gitbook/assets/multinode-diagram.jpeg" alt="" width="535"><figcaption><p>Diagrama de modo multinode</p></figcaption></figure>
