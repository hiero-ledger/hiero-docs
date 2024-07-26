# Configuración de un nodo único

## Usar configuración de un nodo único

La configuración de un nodo simula las funciones de la red en una escala más pequeña (nodo único), ideal para el desarrollo de depuración, pruebas y prototipos.

<details>

<summary><strong>Single Node Mode Requirements</strong></summary>

Asegúrese de que la implementación **`VirtioFS`** para compartir archivos está habilitada en la configuración de docker.

![](<../../.gitbook/assets/docker-compose-settings (1).png>)

Asegúrese de que las siguientes configuraciones se establecen como mínimo en las **Configuraciones** de Docker -> **Recursos** y están disponibles para su uso:

**CPUs:** 6

**Memoria:** 8GB

**Intercambio:** 1 GB

**Tamaño de imagen de disco:** 64 GB

![](../../.gitbook/assets/docker-settings.png)

Asegúrese de que **`Permitir que los sockets Docker por defecto sean usados (requiere contraseña)`** está habilitado en Docker **Ajustes -> Avanzados**.

![](../../.gitbook/assets/docker-socket-setting.png)

**Nota:** La imagen puede parecer diferente si estás en una versión diferente

</details>

#### **Iniciar y detener el nodo**

Antes de lanzar los comandos de red, confirme que Docker está instalado y abierto en su máquina. Para detener tu nodo local, usa los siguientes comandos `npm` o `docker`. Antes de continuar con esta operación, asegúrese de realizar una copia de seguridad de cualquier archivo creado manualmente en el directorio de trabajo.

<details>

<summary><strong>comandos npm</strong></summary>

{% code overflow="wrap" %}

```bash
# comando npm para iniciar la red local y generar cuentas en modo separado
npm run start -- -d

# comando npm para detener
npm run stop

# comando npm para reiniciar nodo
npm run restart
```

{% endcode %}

</details>

<details>

<summary><strong>acoplador comandos</strong></summary>

```bash
# Comando Docker para iniciar la red local. No genera cuentas
docker compone -d

# Comando Docker para detener servicios
docker compone stop

# Comando Docker para reiniciar la red local
docker compone reiniciar

# Comando Docker para detener la red local y eliminar contenedores
docker componer abajo
```

</details>

Alternativamente, ejecuta `docker compose down -v; git clean -xfd; git reset --hard` para detener el nodo local y restablecerlo a su estado original. La lista completa de comandos disponibles se puede encontrar [here](https://github.com/hashgraph/hedera-local-node?tab=readme-ov-file#commands).

#### Diagrama de modo de nodo único

El siguiente diagrama ilustra la arquitectura y el flujo de datos en modo de nodo único.

<figure><img src="../../.gitbook/assets/localnet-single-node-diagram.png" alt="" width="563"><figcaption><p>Diagrama de modo de nodo único</p></figcaption></figure>
