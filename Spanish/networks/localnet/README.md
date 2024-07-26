# Red local

## Introducción

Hedera Localnet proporciona a los desarrolladores un marco integral para probar y refinar localmente aplicaciones basadas en Hedera. Al operar fuera de las redes públicas, Localnet es crucial en el ciclo de vida del desarrollo de software, eliminar los cuellos de botella de E/S de red, minimizando los conflictos de recursos compartidos y ofreciendo un control completo sobre las configuraciones de red. Esta red local comprende dos ofertas de productos principales, [Nodo Local](https://github.com/hashgraph/hedera-local-node) y [Solo](https://github.com/hashgraph/solo), cada una con distintos propósitos en el proceso de desarrollo y prueba. Para los desarrolladores que acaban de comenzar con Local Node, aquí está la ruta recomendada para las pruebas:

<figure><img src="../../.gitbook/assets/localnet-dev-testing-path.png" alt="" width="375"><figcaption><p>Hedera Local Node Testing Workflow</p></figcaption></figure>

1. **Nodo local (modo único y multinode)**: Comience probando su prototipo inicial en un nodo local. Este paso permite iteraciones rápidas y depuración en un entorno controlado. Si tu aplicación necesita manejar escenarios más complejos, ejecuta la configuración _Multinode_.&#x20
2. **Solo**: luego muévete a _Solo_ para pruebas avanzadas bajo condiciones realistas de red.&#x20
3. **Previewnet**: Luego prueba en Hedera Previewnet para la verificación última/sangrado del borde/próximo código.
4. **Testnet**: Por último, pruebe en la red de pruebas la verificación de código estable antes de desplegarlo en Hedera Mainnet.

***

## Nodo local

### Resumen

El Nodo Local replica una red Hedera compuesta por un solo nodo (o pocos si están configurados) en la máquina local de un desarrollador, ofrecer un entorno controlado para desarrollar, probar y experimentar con [aplicaciones descentralizadas (dApps)](. /../support-and-community/glossary.md#decentralized-application-dapp). Esta configuración local utiliza [Docker Compose](https://docs.docker.com/compose/) para crear múltiples contenedores, cada uno con un rol específico dentro de la red, incluyendo, pero sin limitarse a:

- **Nodo de Consenso**: Simula el comportamiento del mecanismo de consenso de Hedera, las transacciones de procesamiento/ordenamiento y la participación en el algoritmo de consenso de la red.
- [**Nodo espejo**](../../support-and-community/glossary.md#mirror-nodes)**:** Proporciona acceso a los datos históricos, registros de transacciones y el estado actual de la red sin participar en consenso. Esto es útil para consultas y análisis.
- [**Relé JSON-RPC**](../../support-and-community/glossary.md#json-rpc-relay): Ofrece una implementación local de la [Ethereum]JSON-RPC (../../support-and-community/glossary.md#ethereum) APIs JSON-RPC para Hedera para habilitar interacciones con contratos y cuentas inteligentes. Esto es especialmente útil para desarrolladores familiarizados con Ethereum tooling y ecosistema.
- [**Mirror Node Explorer**](../../support-and-community/glossary.md#network-explorer): Una interfaz basada en la web que permite a los desarrolladores auditar las transacciones, cuentas y otras actividades de red visualmente.

### Configuración y configuración

La configuración de un nodo simula las funciones de la red en una escala más pequeña (en un solo nodo), ideal para el desarrollo de depuración, pruebas y prototipos. La configuración de múltiples nodos distribuye múltiples instancias de los nodos de red de Hedera a través de una única máquina usando contenedores Docker, destinados a pruebas avanzadas y emulación de red.

➡️ [**Configuración del nodo único**](single-node-configuration.md)

➡️ [**Configuración Multinode**](multinode-configuration.md)

### Modos Operacionales

El nodo local ofrece dos modos dependiendo de las necesidades de un desarrollador:

<details>

<summary><strong>Modo completo</strong></summary>

El modo completo se activa con la bandera `--full`, y el sistema está diseñado para capturar y almacenar datos completos. Así es como funciona:

- **Carga de datos**: Cada nodo dentro de la red genera archivos de flujo de registro durante la operación. Los archivos de flujo de registros son una secuencia de registros de transacciones agrupados en un intervalo específico. La red Hedera consolida periódicamente estos registros de transacción en archivos de secuencias, que luego se ponen a disposición de los nodos de red y los nodos de réplica. En modo completo, estos archivos se suben sistemáticamente a su propio directorio dentro del cubo `minio`. MinIo es una plataforma de almacenamiento de objetos que proporciona herramientas dedicadas para almacenar, recuperar y buscar bloques. Este proceso es administrado por contenedores de carga específicos asignados a cada nodo, a saber:
  - `record-streams-uploader-N`(contiene streams de grabación)
  - `account-balances-uploader-N` (contiene archivos de saldos de cuenta)
  - `record-sidecar-uploader-N` (contiene una lista de `TransactionSidecarRecords` que fueron creados a través de un intervalo específico y relacionados con el mismo `RecordStreamFile`.

</details>

<details>

<summary><strong>Modo Turbo</strong></summary>

El modo Turbo es el valor predeterminado al ejecutar el nodo local. Este modo prioriza la eficiencia y la velocidad, con las siguientes características clave:

- **Acceso a datos locales**: En lugar de subir datos a la nube, los archivos de flujo de registros se leen directamente desde sus directorios locales correspondientes en cada nodo. Este método reduce significativamente la latencia y el consumo de recursos, lo que lo hace ideal para escenarios en los que el acceso inmediato a los datos y el alto rendimiento son prioritarios sobre el almacenamiento a largo plazo y la accesibilidad externa.

</details>

Con estas dos opciones, los usuarios pueden adaptar la operación del nodo local para satisfacer mejor sus necesidades, ya sea asegurando la captura y copia de seguridad completa de datos o optimizando para el rendimiento y la velocidad.

***

## Solo

Solo ofrece una avanzada solución de pruebas de redes privadas y adopta una primera estrategia de Kubernetes para crear una red que imita completamente un entorno de producción. Explore el repositorio Solo [here](https://github.com/hashgraph/solo).

_**Más información próximamente...**_

***

## Recursos adicionales

- [**Repositorio de Nodos Locales de Hedera**](https://github.com/hashgraph/hedera-local-node)
- [**Documentación de Componer Docker**](https://docs.docker.com/compose/intro/features-uses/)
- [**Ejecuta un nodo local en Gitpod**](../../tutorials/local-node/how-to-run-hedera-local-node-in-a-cloud-development-environment-cde/gitpod.md) **\[tutorial]**
- [**Ejecuta un nodo local en Codespaces**](../../tutorials/local-node/how-to-run-hedera-local-node-in-a-cloud-development-environment-cde/codespaces.md) **\[tutorial]**
