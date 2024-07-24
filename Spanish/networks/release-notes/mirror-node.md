---
description: Notas del nodo espejo de Hedera
---

# Hedera Mirror Node

Para las últimas versiones soportadas en cada red, por favor visite el estado de Hedera [page](https://status.hedera.com/).

## Últimas versiones

## [v0.105.0](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.105.0)

Se agregó un documento de diseño para la implementación de [HIP-904](https://hips.hedera.com/HIP/hip-904.html) Airdrops sin fricciones en el nodo espejo. Por favor vea la [epic](https://github.com/hashgraph/hedera-mirror-node/issues/8081) para monitorear el progreso del desarrollo de aerolíneas.

Citus, nuestra base de datos fragmentada, continúa avanzando con esta publicación abriendo paso a uno de nuestros dos clústeres de testnet. Monitorearemos su despliegue durante un periodo de tiempo y haremos las correcciones necesarias. El controlador ZFS CSI que usamos para Citus vio una actualización. Finalmente, se arreglaron múltiples problemas con el script PostgreSQl a Citus migration script.

Para `/api/v1/contracts/call`, el tamaño máximo de datos fue aumentado a 128 KiB para proporcionar una mejor compatibilidad con Ethereum. Además, la lógica adicional y la validación se agregaron para alinearse más estrechamente con los escenarios de error de los nodos de consenso.

### Actualizando

Si está usando el controlador CSI ZFS, por favor asegúrese de que sus CRDs están actualizados antes de actualizar:

```
for crd in zfsbackups zfsnodes zfsrestores zfssnapshots zfsvoles; do kubectl patch crd $crd.zfs.openebs.io --type merge -p '{"metadata": {"annotations": {"helm. h/resource-policy": "keep", "meta.helm.sh/release-name": "mirror", "meta.helm.sh/release-namespace": "common"}, "labels": {"app.kubernetes.io/managed-by": "Helm"}}}'; hecho
```

## [v0.104.0](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.104.0)

Esta versión añade un caché Redis a la API REST para mejorar el rendimiento de `/api/v1/transactions`. Esta funcionalidad está actualmente desactivada por defecto a medida que la ajustamos.

[HIP-857](https://hips.hedera.com/hip/hip-857) NFT Allowances API hizo mucho progreso esta versión. Está tardando un poco más de lo normal de una API, ya que es nuestra primera API REST basada en Java que requiere algún trabajo de base adicional. Esta versión habilita por defecto la carta Helm rest-java permitiendo a los usuarios probar la API de permisos NFT en todos los entornos. Mientras que la mayoría de las funciones están presentes, por favor tenga en cuenta que algunas partes todavía están en desarrollo. Un nuevo índice fue añadido a la tabla de permisos de NFT para acelerar las búsquedas por cuenta del gasto. La comprobación de existencia de ID de entidad numérica fue eliminada para mejorar su rendimiento y para soportar nodos espejo parciales. Finalmente, añadimos pruebas de aceptación inicial para verificar el final de la API.

Nuestro despliegue de Citus está cerca de la línea de meta. Citus está ahora desplegado en previewnet y ahora ejecuta tanto las implementaciones PostgreSQL como Citus en paralelo. Internamente, lo hemos desplegado en un entorno de staging mainnet con una base de datos de tamaño completo para más pruebas. Este despliegue fue posible debido al drástico aumento del tiempo de migración que implementamos esta liberación. Mainnet previamente tardó más de un mes en migrar, pero con esta publicación debería completarse dentro de una semana más o menos. Espere que la red de pruebas sea migrada a Citus muy pronto.

## [v0.103.0](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.103.0)

Esta versión añade soporte para hacer información de metadatos de [HIP-646](https://hips.hedera.com/hip/hip-646), [HIP-657](https://hips.hedera.com/hip/hip-657), y [HIP-765](https://hips.hedera.com/hip/hip-765) disponibles en la API REST. En particular, esto añade un campo `metadata` codificado en base64 al punto final `/api/v1/tokens`. También agrega los campos `metadata` y `metadata_key` al punto final \`/api/v1/tokens/{id}.

La API de llamadas de contrato vio algunas notables mejoras de rendimiento con la implementación de carga perezosa para artículos anidados. Anteriormente estaba cargando toda la información de la cuenta, incluso para llamadas más simples que no necesitaban los datos. Con el interruptor para hacer estas consultas adicionales perezosas, vemos una mejora del 50-90% en el rendimiento de las peticiones. Ese cambio más una mejora en el rendimiento de la consulta de conteo de NFT debería resultar en un rendimiento y estabilidad adicionales de la API.

Todavía se está trabajando en [HIP-857](https://hips.hedera.com/hip/hip-857) API REST de permisos NFT. Esta versión añade soporte para direcciones EVM y alias al nuevo punto final y corrige el formato de respuesta de error.

## [v0.102.0](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.102.0)

Esta es una versión de corrección de errores más pequeña con mejoras incrementales en algunos proyectos en vuelo.

Para [HIP-857](https://hips.hedera.com/hip/hip-857), ahora existe una versión alfa de la API REST de permisos NFT. Se puede utilizar para experimentar mientras trabajamos para implementar los parámetros de consulta restantes y para aplastar cualquier error. La biblioteca Jooq se integró en el módulo rest-java para permitir consultas SQL dinámicas basadas en la entrada del usuario. La siguiente versión debería aprovechar esta funcionalidad para implementar completamente las partes restantes de la API.

Nuestra implementación de Citus fue desplegada con éxito en el entorno de rendimiento y está pasando pruebas de rendimiento iniciales. Los resultados preliminares muestran que Citus mejora el rendimiento de la ingesta en 600ms mientras recorta los datos a través de múltiples nodos. Promtail fue habilitado en los nodos Citus para capturar registros de bases de datos y un nuevo panel ZFS fue añadido a Grafana.

## [v0.101.0](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.101.0)

Esta versión añade soporte para almacenar la nueva información de metadatos mutables disponible en [HIP-646](https://hips.hedera.com/hip/hip-646), [HIP-657](https://hips.hedera.com/hip/hip-657), y [HIP-765](https://hips.hedera.com/hip/hip-765). Por ahora, sólo persiste los datos y en futuras versiones lo expondremos a través de las APIs REST.

La API REST `/api/v1/tokens` ahora soporta múltiples parámetros de `token.id`. Esto permite a los usuarios consultar eficientemente múltiples tokens en una sola llamada.

La API REST `/api/v1/contracts/call` vio algunas mejoras de rendimiento importantes en esta versión. El primer cambio fue cambiar los nodos de Kubernetes a una clase diferente de máquina que proporciona asignación de recursos dedicados. El endpoint también fue cambiado de una pila de MVC reactiva a una pila de MVC sincrónica. Simularmente, el módulo habilitó la nueva tecnología de hilos virtuales que reemplaza los hilos de la plataforma. Estos cambios se combinaron para mejorar el rendimiento de la solicitud en 1-2x.

Otro cambio importante fue habilitar el lote entre los hilos de descarga y analizador en el importador. Por ahora, esta funcionalidad está configurada para comportarse como antes sin ningún tipo de lotes. Cuando se configura manualmente, esto puede reducir los tiempos de sincronización de los datos históricos por al menos 12x. En el futuro, analizaremos formas de habilitar automáticamente esta funcionalidad cuando el importador esté intentando ponerse al día.

## [v0.100.0](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.100.0)

Esta versión implementa [HIP-859](https://hips.hedera.com/hip/hip-859), agregando soporte para devolver gas consumido en el resultado del contrato APIs REST. El campo actual 'gasUsed' en la API contiene la cantidad de gas cargado, mientras que el nuevo campo 'gasConsumed' contiene la cantidad de gas realmente utilizado durante la ejecución de EVM. Proporcionar estos datos adicionales permitirá a los usuarios proporcionar un gas más preciso al invocar un contrato y reducir las tarifas que se les cobran.

`/api/v1/contracts/call` ahora soporta una propiedad `hedera.mirror.web3.evm.maxGas`. El valor por defecto sigue siendo de 15 millones, pero los operadores ahora pueden elegir aumentarlo para satisfacer sus necesidades. La versión y características de EVM se han actualizado a `v0.46`. Esto trae paridad de características con el último software de nodos de consenso para la ejecución de EVM.

Hubo una gran cantidad de trabajo para mejorar nuestra integración con Citus. Tres migraciones repetibles fueron mejoradas para funcionar óptimamente con Citus: migración de saldo de cuentas, migración de saldo de fichas y migración de aprobación de transferencias sintéticas. La inserción de cuenta de token fue optimizada para mejorar su rendimiento eliminando el join en la tabla de token. El particionado a intervalo se eliminó para tablas relacionadas con la entidad ya que causó un rendimiento degradado debido a tener particiones dispersas. Por último, el despliegue ahora soporta discos de diferentes tamaños para los trabajadores individuales para optimizar para datos desequilibrados.

## [v0.99.0](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.99.0)

Esta versión contiene la implementación de [HIP-873](https://hips.hedera.com/hip/hip-873) agregando decimales de token a la API REST. Antiguamente los usuarios tenían que hacer consultas `N + 1` para determinar información exacta del balance de tokens consultando `/api/v1/accounts/{id}/tokens` una vez y `/api/v1/tokens/{id}` tiempos para obtener la información decimal relevante. Este HIP añade `decimals` a `/api/v1/tokens/{tokenId}/balances` y `/api/v1/accounts/{id}/tokens` para que la información decimal sea devuelta directamente junto a las relaciones de tokens y las consultas adicionales `N` son innecesarias. También agrega los campos `name` y `decimals` a la respuesta `/api/v1/tokens` para exponer más información de token existente en esa API.

La API REST `/api/v1/contracts/call` ahora soporta un `hedera.mirror.web3.evm configurable. propiedad axDataSize` para que los operadores de nodo espejo puedan ajustar el tamaño de una carga útil que desean soportar. El valor por defecto para el tamaño máximo de los datos se incrementó de `24 KiB` a `25 KiB` para creaciones y de `6 KiB` a `25 KiB` para llamadas. Este cambio hace posible que las funciones de la vista con entradas grandes como [oracles](https://hedera.com/blog//etcas-dora-price-feeds-now-live-on-hedera) ahora funcionen en la red.

Había algunos elementos para mejorar el rendimiento y la seguridad del nodo espejo. Una nueva propiedad `hedera.mirror.importer.downloader.maxSize=50MiB` controla el tamaño máximo de archivo de flujo que intentará descargar. Esto protege el nodo réplica contra archivos grandes cargados accidentalmente o a través de actores maliciosos. El importador fue refactorizado para soportar la ingestión por lotes de archivos para que sea posible procesar múltiples archivos de flujo en una transacción. Esto ayudará a allanar el camino para futuras mejoras como mejorar los tiempos históricos de sincronización.

La base de datos vio una serie de mejoras incluyendo la nueva [documentación de configuración](https://github.com/hashgraph/hedera-mirror-node/blob/main/docs/database/README.md#setup) con recomendaciones para cómo configurar la base de datos. Nuestro despliegue de Citus tuvo algunas adiciones notables incluyendo una gran mejora en el rendimiento ajustando su configuración de recursos. La versión de Stackgres se actualizó a 1.8 y ZFS a 2.4.1. El cálculo de las participaciones en la entidad fue optimizado para Citus para que se ejecute eficientemente en una base de datos reducida. Finalmente, se corrigieron las métricas de la base de datos para que las tablas particionadas se agregaran adecuadamente bajo el nombre de la tabla padre.

## [v0.98.0](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.98.0)

Esta versión vio la implementación de [HIP-844](https://hips.hedera.com/hip/hip-844) Manejo y externalización de mejoras para las actualizaciones nonce de la cuenta. Este HIP resuelve problemas donde los nodos de consenso y los nodos de espejo son nonces de cuenta están dessincronizados. Los nodos de consenso ahora envían el nodo de réplica de la cuenta actualizada nonce en lugar del nodo de réplica tratando de incrementar el nonce basado en su estado anterior.

Se produjeron dos cambios importantes en la base de datos que ayudaron a reducir su tamaño sustancialmente. El índice de clave primaria de la tabla `topic_message` fue eliminado en favor de depender de un índice similar en la tabla `transaction`. Este simple cambio ha sacado 800 GiB de la base de datos mainnet. El rendimiento del cálculo de la recompensa de apuesta se mejoró para que sólo escribiera cuentas que eligieran recibir recompensas. Esto reduce el tiempo de ejecución del cálculo de la recompensa de apuesta de 47 minutos a menos de 2 minutos. Una migración también elimina las filas de apuestas existentes que no tenían una elección de recompensa, recortando esas tablas por 155 GiB. Tenga en cuenta que para realizar estos operadores de nodo espejo de ahorro de discos necesitará realizar manualmente un vacuum completo en las tablas `entity_stake` y `entity_stake_history`. Así que en total el tamaño de la base de datos de nodos réplica fue reducido en casi 1 TB esta versión!

En esta publicación se pagó un poco de deuda técnica. Hemos eliminado el soporte para el formato de archivo de evento del importador. Este formato nunca fue completamente implementado en el nodo réplica, no soporta la última versión, y no se expresó ningún interés del usuario en estos datos durante sus 4 años de existencia. Las pruebas de aceptación fueron refactorizadas para utilizar los modelos generados por OpenAPI, asegurándonos de alimentar nuestra propia especificación de API. Las pruebas `MockPool` de Lucha fueron eliminadas en favor de una cobertura adicional en otros, más fáciles de mantener las pruebas. Las pruebas de la API REST ahora utilizan la correcta configuración de sólo lectura y de base de datos común que utilizan los otros módulos. Finalmente, se eliminaron `RestoreClientIntegrationTest` y las imágenes de prueba asociadas.

Nuestro despliegue de Citus ha experimentado una serie de mejoras. Rendimiento fue optimizado para las inserciones de hash reduciendo el recuento de fragmentos para las tablas hash. Las mejoras de la entidad mejoraron al aumentar el número de recursos de CPU a la base de datos. Por último, la lista de transacciones y las cuentas por puntos finales de identificación mejoraron su rendimiento de lectura para Citus.

\
[v0.97.0](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.97.0)
---------------------------------------------------------------------------------------------------------------

Esta versión ve algunos cambios incrementales a la API REST. La API REST ahora soporta un encabezado [RFC5988](https://www.rfc-editor.org/rfc/rfc5988) compatible con `Link` en su respuesta como una alternativa al `links.next`en el cuerpo de respuesta. Cualquiera de los enlaces ahora se puede utilizar para la paginación, pero la cabecera `Link` proporciona un enfoque estándar que está soportado por más herramientas. El endpoint `/api/v1/accounts/{id}?timestamp` ahora muestra información de apuesta históricamente precisa en su respuesta para que los usuarios puedan ver sus recompensas pendientes. La marca de tiempo en la respuesta del punto final `/api/v1/tokens/{id}/balances` ahora es más precisa al reflejar la marca de tiempo del balance máximo de los tokens en su respuesta.

El gráfico de casco fue verificado para ser compatible con Kubernetes 1.28 y vio cómo sus dependencias se desplomaban hasta la última versión. El nuevo módulo rest-java se convirtió de WebFlux a servlets con hilos virtuales. Esto debería aumentar su escalabilidad una vez que implementemos HIP-857 NFT allowance REST API en un futuro relase. Alguna refactorización interna de `BatchEntityListener` a `BatchPublisher` permitirá futuras mejoras en sincronización histórica y procesamiento por lotes.

El endpoint `/api/v1/contracts/call` vio un montón de correcciones importantes de errores esta versión. El soporte para bloques históricos debería completarse con algunos errores pendientes. Las operaciones soportadas [documentation](https://github.com/hashgraph/hedera-mirror-node/tree/main/docs/web3#supportedunsupported-operations) fueron actualizadas para reflejar este mayor nivel de compatibilidad.

## [v0.96.0](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.96.0)

Con muchos desarrolladores que se toman algo de tiempo libre para la temporada navideña, esta versión es un poco más pequeña de lo normal pero aún contiene algunos cambios importantes. Las libretas de direcciones de arranque de previewnet y testnet se actualizaron para reflejar el estado actual de la red. El tamaño de volumen por defecto para Loki se incrementó de 100 GB a 250 GB para tener en cuenta las crecientes cantidades de actividad de registro. El procesamiento de `EthereumTransaction` se hizo más resistente para que el importador no se detenga si encuentra una transacción mal codificada. Finalmente, una fuga de memoria en la API REST debería reducir considerablemente los errores de memoria y mejorar el rendimiento de las peticiones.

Para mejorar el rendimiento de las tablas de entidades cuando se utilizan con una base de datos SQL distribuida, introdujimos una nueva base de datos `temporary` [schema](https://www.postgresql.org/docs/current/ddl-schemas.html). Este esquema se utiliza para mantener los datos temporales insertados al procesar entidades de un archivo de registro. Anteriormente, esta información fue añadida a tablas temporales creadas dentro del ámbito de la transacción, pero estas tablas temporales no pudieron ser distribuidas en Citus. Con el esquema temporal, esta información se puede distribuir ahora de forma adecuada para asegurar un rendimiento óptimo de ingesta óptima. Este cambio requiere que las sentencias DDL manuales se ejecuten antes de la próxima actualización (vea la siguiente sección).

### Rompiendo Cambios

Como ya se ha mencionado, se introdujo un nuevo esquema de base de datos para manejar el procesamiento de entidades actualizables. Este cambio\
no requiere ningún paso manual para los nuevos operadores que utilizan uno de nuestros scripts de inicialización o cartas de helm a\
configurar la base de datos. Sin embargo, los operadores existentes que actualicen a 0.96.0 o posterior son necesarios para crear el esquema por\
configurando y ejecutando un [script](https://github.com/hashgraph/hedera-mirror-node/blob/v0.96.0/hedera-mirror-importer/src/main/resources/db/scripts/init-temp-schema.sh) _**antes**_ de la actualización.

```
PGHOST=127.0.0.1 ./init-temp-schema.sh
```

Otro cambio de ruptura se refiere a los operadores que utilizan nuestro gráfico `hedera-mirror-common`. El mayor aumento del tamaño del volumen de Loki fue realizado en el `PersistentVolumeClaim` embebido en el `StatefulSet` de Loki. Kubernetes no permite cambios en este campo inmutable por lo que para solucionar el `StatefulSet` tendrá que ser eliminado manualmente para la actualización del gráfico común.

## [v0.95.0](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.95.0)

Esta versión vio los componentes de Java actualizados para usar [Java 21](https://www.oracle.com/news/announcement/ocw-oracle-releases-java-21-2023-09-19/). En una versión futura, exploraremos las nuevas características de lenguaje en 21 como hilos virtuales para desbloquear escalabilidad adicional. Se abordaron algunas partidas de deuda técnica, incluida la eliminación de la configuración de pruebas redundantes mediante la creación de una jerarquía común de pruebas. Se eliminaron explícitas anotaciones `@Autowired` en constructores de pruebas, reduciendo la boilerplate. Por último, varias clases fueron renombradas para alinearse con nuestros estándares de nombramiento, incluyendo la eliminación del prefijo `Mirror` de clases que no se usaron a través de módulos.

[HIP-584](https://hips.hedera.com/hip/hip-584) nodo de archivo EVM para bloques históricos vio algunas adiciones importantes, incluyendo soporte inicial para bloques históricos. La configuración EVM ahora se carga basándose en el número de bloque en lugar de utilizar siempre la última EVM. Esto asegura que `/api/v1/contracts/call` simula la ejecución ya que habría estado en los nodos de consenso en ese momento en el tiempo. Las consultas de base de datos fueron adaptadas para trabajar con filtros de marcas de tiempo para permitir la devolución de información histórica de bloques.

Nuestro esfuerzo distribuido de bases de datos ha visto algunas mejoras notables incluyendo la actualización de la versión de Citus a 12.1. El soporte de PostgreSQL 16 fue probado confirmando compatibilidad tanto con PostgreSQL como con Citus. Tanto `/api/v1/topics/{id}/messages/{sequenceNumber}` como `/api/v1/topics/{id}/messages` vieron optimizaciones implementadas cuando se usaban con Citus.

### Actualizando

Si está compilando localmente, asegúrese de que ha actualizado a Java 21 en su terminal e IDE. Para MacOS, recomendamos usar [SDKMAN!](https://sdkman.io/) para gestionar las versiones de Java de manera que la actualización sea tan simple como `sdk install java 21-tem`. Si estás usando un archivo personalizado `Dockerfile` asegúrate de que también se actualiza a Java 21. Recomendamos [Eclipse Temurin](https://hub.docker.com/\_/eclipse-temurin) como la imagen base para nuestros componentes Java.

## [v0.94.1](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.94.1)

Proporciona una solución importante a los cálculos de recompensa pendientes que regresaron debido al trabajo de deduplicación del saldo. La migración de la base de datos para esto tomará aproximadamente 17 minutos en mainnet.

## [v0.94.0](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.94.0)

Esta versión es principalmente una versión de corrección de errores junto con algunos elementos de deuda técnica menores. Un nuevo gráfico de Helm para el nuevo módulo de Java REST fue añadido en la anticipación de trabajo futuro para soportar nuevas APIs en Java sólo en lugar del enfoque basado en JavaScript actual. Se eliminó el apoyo a la exportación de métricas de Elasticsearch en favor de confiar exclusivamente en Prometeo. La API `/api/v1/contracts/call` algunas correcciones de errores notables y mejoras de rendimiento. Por último, se abordó cierta deuda técnica refactorizando `SqlEntityListener` para utilizar un nuevo `ParserContext` que debería reducir su carga de mantenimiento.

## [v0.93.0](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.93.0)

Este lanzamiento deduplica el historial del balance resultando en una reducción importante en el tamaño de la base de datos sin pérdida de granularidad del balance. La base de datos de red principal ha experimentado una reducción del 45% pasando de 50 TB a 28TB! Este proceso de deduplicación funciona al no actualizar el historial de saldo si la cuenta no experimentó un cambio de balance desde la última instantánea. Una migración a los balances históricos deduplicados corre de forma asíncrona en segundo plano y contra el estado mainnet tardó unas 24 horas en completarse. Debido a que el índice fue cambiado para revertir el pedido de `(timestamp, account_id)` a `(account_id, timestamp)`, esto requería un gran esfuerzo para reelaborar consultas en múltiples APIs REST. Además, las tablas de balance ahora están particionadas y esto significó cambios en las métricas de nuestra base de datos para agregar adecuadamente tablas hijas en su nombre padre.

HIP-584 continúa conectándose junto con múltiples correcciones de errores y optimizaciones. El cambio por petición de objetos a ser singletons resultó en una gran disminución del uso de memoria y CPU, permitiendo que se gestionaran más peticiones simultáneas. Las pruebas Web3 k6 fueron enganchadas en nuestras pruebas de rendimiento automatizadas para asegurarse de que ejecutan cada versión para asegurar que no haya regresiones. Por último, el apoyo a los bloques históricos avanzó con más del desplome puesto en marcha para procesar bloques no más recientes.

Se añadió un nuevo chequeo de estado de base de datos de cluster al monitor para proporcionar una correcta tolerancia contra fallos en las implementaciones multicluster. El proveedor local de flujo de archivos ahora permite que los archivos de entrada se agrupen por fecha para un procesamiento más rápido cuando el directorio contiene millones de archivos. Este es un paso hacia un modo de sincronización histórica más rápido. Finalmente, las consultas de la API REST fueron optimizadas para las implementaciones de Citus para que pudieran alcanzar la paridad con PostgreSQL.

## [v0.92.0](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.92.0)

[HIP-584](https://hips.hedera.com/hip/hip-584) El nodo de archivo de Mirror EVM vio más refinamientos en esta versión. El uso de la memoria se optimizó haciendo que la mayoría de las clases no tuvieran estado y aprovecharan `ThreadLocal` cuando fuera apropiado. El trabajo continúa haciendo que `/api/v1/contracts/call` soporte bloques históricos con soporte de nivel más bajo para información histórica de contratos, cuentas y contratos añadida. Se introdujo una cobertura adicional de prueba para la estimación del gas para comparar la estimación del gas es cercana al gas utilizado a través de HAPI. Finalmente, se resolvió un problema con la operación `blockhash` devolviendo `0x0`.

Hemos añadido una opción para deduplicar datos de cuenta y saldo de fichas para Citus que pueden reducir drásticamente el tamaño de las tablas de balance. Las tablas de saldos consumen actualmente el 50% de la base de datos. En la próxima versión, traeremos esta capacidad a PostgreSQL regular para realizar esos ahorros de costos allí también. Ahora ya no actualizamos el historial de tokens para operaciones de cambio de suministro como mint, quemar, o limpiar así reducir la cantidad de datos en esta tabla.

## [v0.91.0](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.91.0)

Esta versión añade soporte para filtros de transacción ad-hoc usando el [Lenguaje de Expresión de Primavera](https://docs.spring.io/spring-framework/reference/core/expressions.html) (SpEL). Los filtros SpEL pueden utilizarse para incluir o excluir transacciones que persistan en la base de datos. Filtrado anterior [capability](https://github.com/hashgraph/hedera-mirror-node/blob/main/docs/configuration. d#Transaction-and-entity-filtering) permitió a los operadores de nodo espejo incluir o excluir ciertos IDs de entidad o tipos de transacciones, pero si necesitaban algo más costumbre que no tuvieran suerte. SpEL en sí es bastante potente y permite el acceso a cualquier tipo de clase en el camino de clase, así que para reducir la superficie de ataque la limitamos para permitir únicamente el filtrado en la [TransactionBody](https://github. om/hashgraph/hedera-sdk-java/blob/develop/sdk/src/main/proto/transaction\_body.proto) y la [TransactionRecord](https://github.com/hashgraph/hedera-sdk-java/blob/develop/sdk/src/main/proto/transaction\_record.proto) contenida en el archivo de registro. Vea la [docs](https://github.com/hashgraph/hedera-mirror-node/blob/main/docs/configuration.md#spring-expression-language-support) para más detalles y ejemplos. A continuación hay un ejemplo que sólo incluye transacciones con ciertos memos:

```
hedera.mirror.importer.parser.include[0].expression=transactionBody.memo.contains("hedera")
```

El monitoreo vio mejoras en este lanzamiento con métricas nuevas añadidas para el tamaño de tabla e índices en el disco para ayudar a rastrear el tamaño creciente de la base de datos. De la misma manera, se implementaron nuevas métricas de caché para monitorear la velocidad y el tamaño de la caché. Se actualizaron los paneles de control para visualizar estas nuevas métricas.

[HIP-584](https://hips.hedera.com/hip/hip-584) El nodo de archivo de EVM vio soporte para `block.prevrandao` implementado. También, el soporte para los tipos de bloques `pending`, `safe` y `finalized` fueron añadidos, siendo los tres equivalentes a `latest` ya que los bloques de Hedera siempre son finales. El trabajo ha comenzado en soporte histórico para `/api/v1/contracts/call` para que bloques específicos en el pasado puedan ser consultados. Esta versión vio implementadas las consultas de nivel inferior para el balance de token.

[HIP-794](https://hips.hedera.com/hip/hip-794) El saldo de la cuenta de Sunset vio un par de objetos restantes completados. La marca de tiempo del balance que se añadió en v0.89.0 se usó para proporcionar marcas de tiempo más precisas para las cuentas y los balances REST API. Finalmente, el trabajo de conciliación fue deshabilitado ya que no tiene sentido conciliar los datos del balance que se genera en el nodo espejo.

## [v0.90.0](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.90.0)

Esta versión se centró principalmente en pruebas y correcciones de errores. Las pruebas de aceptación vieron una serie de mejoras, incluida una reducción de los costes totales mediante la creación de contratos en caché utilizados en múltiples pruebas. Se añadieron pruebas de aceptación para verificar el soporte de propiedades de apuesta-786 HIP-786 en REST API de interés de red. Un problema con los tipos de cambio que varían en diferentes entornos se resolvió consultando el tipo de cambio REST API y utilizando eso para calcular las comisiones a HAPI. El importador tenía una opción añadida para fallar en errores recuperables para encontrar problemas de flujo de registros antes del ciclo de vida.

## [v0.89.0](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.89.0)

[HIP-786](https://hips.hedera.com/hip/hip-786) añade soporte para las exportaciones de metadatos de staking enriched al flujo de registros para su uso por sistemas downstream. El nodo espejo ahora ingiere los nuevos campos `max_stake_rewarded`, `max_total_reward`, `reserved_staking_rewards`, `reward_balance_delayhold`, y `unreserved_staking_reward_balance` y los persiste en la base de datos. La API REST ha sido actualizada para exponer estos datos a través de `/api/v1/network/stake`.

[HIP-794](https://hips.hedera.com/hip/hip-794) El archivo de equilibrio solitario vio más refinamientos en esta versión. El nodo espejo ahora captura la marca de tiempo de consenso en la que se actualizó el saldo tanto para las cuentas como para la relación de token. Esta información se utilizará en el futuro para proporcionar una marca de tiempo de balance más precisa en la API y para deduplicar la información del balance. El seguimiento y migración del balance de entidades fue habilitado en la API de Rosetta. Por último, ahora seguimos el balance de todos los tipos de entidades y no sólo de cuentas y contratos.

[HIP-584](https://hips.hedera.com/hip/hip-584) El nodo Mirror EVM Archive sigue mejorando con la adición de soporte para el contrato del sistema PRNG. Faltan precompilaciones internas de Besu para la versión de Estambul están registradas correctamente. Se añadieron muchas pruebas nuevas en forma de pruebas de integración, aceptación y rendimiento.

En esta versión se abordaron varias partidas de deuda técnica. El componente importador vio notables mejoras en el uso de CPU y memoria en 10.000 transacciones por segundo. Ahora usa un 50% menos de memoria y un 33% menos de CPU. El framework de registro Log4j2 fue reemplazado por Logback para proporcionar una ruta de compilación a código nativo y para simplificar la configuración. El `EntityId` vio su mejora final con la adición de un caché para reducir la asignación de objetos temporales inmutables. Las pruebas fueron estandarizadas para utilizar el marco de registro y más sencillo de la agnostica `OutputCaptureExtension`. Finalmente, investigamos los enfoques de la inserción de transacciones paralelas y vimos un camino hacia adelante para una escalabilidad adicional más ingeniosa.

## [v0.88.0](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.88.0)

Esta versión contiene soporte para [HIP-794](https://hips.hedera.com/hip/hip-794) Sunsetting Account Balance File. Los nodos de consenso pronto dejarán de generar archivos de balance de cuentas cada 15 minutos debido al creciente número de cuentas que hacen que esta operación sea insostenible. Para rellenar los huecos, los nodos espejo generarán ahora información de instantánea del balance del flujo de registro. Este cambio será transparente tanto para los usuarios finales como para los operadores, ya que los mismos datos serán devueltos por las distintas APIs. Por ahora, estamos generando registros sintéticos `account_balance_file` (no archivos) hasta que podamos eliminar la dependencia de esta tabla en todas partes. En esta versión, actualizamos las cuentas por ID, balance y las API REST de suministro de red para no depender de esta tabla. El cálculo de la participación de entidades y una migración fungible de tokens se actualizaron de forma similar. La próxima versión verá más trabajo en esta área.

[HIP-584](https://hips.hedera.com/hip/hip-584) vio implementados los precompiles de tipo de cambio `tinycentsToTinybars` y `tinybarsToTinycents`. También se añadió soporte para la precompilación HTS `redirectForToken`. Pero el foco principal estaba en testing y correcciones de errores con un gran número de ellos aplastados en esta versión.

Había una buena cantidad de deuda técnica abordada en `0.88`. Para empezar, tenemos un nuevo módulo `hedera-mirror-rest-java` que pretende contener APIs REST nuevas o existentes convertidas desde JavaScript. Al crear nuevas APIs REST en Java y convertir lentamente las APIs existentes a Java podemos mejorar la calidad de esta área de la base de código y promover la reutilización de código con los otros módulos. Un miembro de la comunidad nos ayudó a eliminar la obsoleta propiedad de Spring Cloud Kubernetes `spring.cloud.kubernetes.enabled` ya que ya no se usó de todos modos. También nos tomamos el tiempo para eliminar las propiedades no utilizadas de los marcadores de posición de Flyway y eliminar la redundancia de código en las pruebas de aceptación de web3. Finalmente, eliminamos el campo `type` del ampliamente utilizado `EntityId` en el código base y eliminamos el innecesario `AssessedCustomFeeWrapper`.

## [v0.87](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.87.0)

Este lanzamiento pone fin a la iniciativa para asegurar que capturamos todos los cambios en las entidades de Hedera. Uno de los tickets más antiguos en el repositorio que se remonta a 2019 fue completado, finalmente persistiendo en la [FreezeTransaction](https://github.com/hashgraph/hedera-protobufs/blob/main/services/freeze.proto) detalles en la base de datos. Hay una nueva opción para almacenar los bytes de protobuf de [TransactionRecord](https://github.com/hashgraph/hedera-protobufs/blob/main/services/transaction\_record.proto) que está deshabilitado por defecto. La tabla de tarifas personalizadas se dividió en tablas principales e históricas separadas para asegurar la consistencia con otros datos y mejorar la eficiencia de las consultas.

Se añadió una migración asíncrona de base de datos para actualizar eficientemente la cantidad de permisos de criptografía de cada cuenta después de que se implementara el soporte para seguimiento de permisos en vivo en la última versión. Además, las nuevas pruebas de aceptación criptográfica y fungible verifican que el seguimiento de permisos en vivo funciona correctamente. Por último, ahora volvemos a ejecutar migraciones condicionales que históricamente sólo se ejecutarían al inicio inicial. Para migraciones como la inicialización del balance esto significa que automáticamente corregimos los balances de cuenta y relación de token después de ingerir el primer archivo de saldo. Para otras migraciones, significa que se activan automáticamente basándose en una versión específica del archivo de registro que se está ingiriendo.

La API REST tuvo un par de cambios notables. Ahora solo mostramos permisos activos en las API REST `/api/v1/accounts/{id}/allowances/crypto` y `/api/v1/accounts/{id}/allowances/tokens` proporcionar consistencia con cómo los nodos de consenso devuelven estos datos. La API `/api/v1/network/stake` vio un cambio en la forma en que su valor de juego se calcula cambiándolo a staked premiated plus not rewarded.

[HIP-584](https://hips.hedera.com/hip/hip-584) El nodo de archivo EVM vio un número de precompilaciones implementadas de `HederaTokenService`, `getApproved`, `isApprovedForAll` `updateTokenExpiryInfo`, `updateTokenInfo`, y `updateTokenKeys`. Un gran foco en las pruebas dio como resultado una mayor cobertura de integración y aceptación de pruebas. La cobertura extra resultó en un número de errores encontrados y aplastados, mejorando la fiabilidad de `/api/v1/contracts/call`.

También se ha trabajado mucho en la parte operativa de las cosas. Un buen número de métricas y tableros de Grafana vieron limpiar y mejorar la ayuda a la supervisión de la producción. Todas las dependencias de gráficos vieron bumps de versiones y ajustes de configuración para actualizarlos. La compatibilidad de Kubernetes 1.27 fue confirmada como un objetivo de despliegue al tiempo que garantizaba la compatibilidad con versiones anteriores de Kubernetes. El soporte de volumen ZFS comprimido ahora gestiona las actualizaciones de Kubernetes correctamente. Nuestro despliegue de Citus vio una actualización a Citus 12 con PostgreSQL 15. Esta versión trae las mejoras que hemos aportado al procedimiento almacenado `create_time_partitions` de Citus para que pueda soportar el tipo `bigint` que usamos para almacenar marcas de tiempo de consenso. Esto nos permitió eliminar la extensión `pg_partman` a favor de la nativa `create_time_partitions`. La extensión `pg_cron` también fue eliminada a favor de un servicio programado basado en Java corriendo en el importador.

## [v0.86](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.86.0)

Hubo un montón de progreso hacia nuestro objetivo capturando todos [cambios de entidades](https://github.com/hashgraph/hedera-mirror-node/issues/6110). Esta versión añade la característica muy solicitada de rastrear los permisos de hbar y token fungible restantes y mostrarlo a través del campo `amount` en la API REST. Es importante que este seguimiento sólo se aplique a los permisos nuevos o actualizados. Las asignaciones existentes verán su asignación restante ajustada apropiadamente en la próxima liberación.

Tradicionalmente, el nodo espejo sólo ha almacenado las transferencias agregadas desde el registro de transacciones. Ahora además almacenamos las transferencias detalladas desde el cuerpo de la transacción por defecto y la incorporamos en la tabla `transaction`. Para los nodos espejos parciales, ahora creamos entidades durante la inicialización del balance. Esto significa que incluso si un nodo espejo comienza ahora esta migración puede ser reejecutada para crear cada cuenta y contrato con información exacta del saldo. En la próxima versión, extenderemos esto para volver a ejecutar automáticamente esta migración al procesar el primer archivo de saldo de la cuenta.

Por último, hemos añadido una tabla de unión de `entity_transaction` para empezar a rastrear todas las entidades asociadas con una transacción. Esto nos permitirá proporcionar una mejor experiencia de búsqueda de transacciones y encontrar todas las transacciones asociadas con una entidad. Esta funcionalidad está deshabilitada en esta versión, ya que iteramos sobre ella para hacerlo performante.

Soporte para la transacción Ethereum tipo 1 como se especifica en [EIP-2930](https://eips.ethereum.org/EIPS/eip-2930) ya está disponible. Anteriormente sólo se admitían transacciones de Ethereum heredadas y tipo 2. Las nuevas transacciones EIP-2930 se pueden enviar directamente a HAPI a través de un [EthereumTransaction](https://github.com/hashgraph/hedera-protobufs/blob/main/services/ethereum\_transaction.proto) o a través del [JSON-RPC Relay](https://swirldslabs.com/hashio/).

[HIP-584](https://hips.hedera.com/hip/hip-584) El nodo de archivo EVM vio implementado un gran número de precompilaciones. Esto incluye todo lo siguiente:

- `aprobar`
- `AprobarNFT`
- `asociarToken`
- `asociarTokens`
- `burnToken`
- `createFungibleToken`
- `createFungibleTokenWithCustomFees`
- `createNonFungibleToken`
- `createNonFungibleTokenWithCustomFees`
- `deleteToken`
- `freezeToken`
- `pauseToken`
- `setApprovalForAll`
- `transferDesde`
- `transferFDeNFT`
- `transferNFT`
- `transferNFTs`
- `transferToken`
- `transferTokens`
- `unfreezeToken`
- `unpauseToken`

## [v0.85](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.85.0)

Con el reciente [reseteo de testnet](https://docs.hedera. om/hedera/networks/testnet#test-network-resets) que ocurrió en 2023-07-27, se creó un nuevo cubo de almacenamiento en la nube para almacenar los archivos generados por los nodos de consenso. Esta versión ahora usará ese nombre del cubo actualizado por defecto cuando la red esté configurada para testnet.

Hubo algunas optimizaciones hechas específicamente para el Relé JSON-RPC en esta versión que otros usuarios también podrían encontrar útil. La API REST `/api/v1/accounts/idOrAddress` ahora soporta un parámetro opcional de consulta `transactions` que cuando se establece en `false` omitirá la lista de transacciones anidadas. Por defecto es `true` para que coincida con el comportamiento anterior. Si no estás usando las transacciones de esta API, por favor considera configurarlo a `false` para reducir la cantidad de datos devueltos y proporcionar un mejor tiempo de respuesta para tus consultas. Adicionalmente, la API REST `/api/v1/contracts/results` fue actualizada para incluir más campos que coincidan con los resultados detallados devueltos de `/api/v1/contracts/results/{id}`.

[HIP-584](https://hips.hedera.com/HIP/hip-584.html) El nodo de archivo EVM está agregando funcionalidad a un ritmo estable. Esta versión añade soporte para la versión Ethereum eVM de Ethereum que añade un nuevo opcode `PUSH0`. Las llamadas estimadas de gas que estaban fallando en el contrato crearon más de 6 KB. Gran parte de la atención se centró en la implementación de varios precompilaciones incluyendo: autorización de permisos, quemar, crear, eliminar permisos, disociar, conceder KYC, revocar KYC y limpiar.

Haciendo progresos en la captura de todos los cambios de estado, ahora guardamos un historial de la participación diaria de la entidad de una cuenta. En el futuro, esta información se utilizará para proporcionar una cantidad exacta apostada al buscar la información histórica de una cuenta usando `/api/v1/accounts/{id}?timestamp=`.

Había muchas otras correcciones de errores y pequeñas mejoras. Por favor, consulte las notas de la versión de abajo para ver la lista completa.

## [v0.84](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.84.0)

Esta versión contiene soporte para [HIP-729](https://hips.hedera.com/hip/hip-729) contrato nonce externalización. Los nodos de consenso ahora rastrean y externalizan la información nonce contrato en el flujo de registros. Nonce de un contrato aumenta cada vez que crea otro contrato. Los nodos Mirror persisten en estos datos y exponen las nonce de un contrato en las API REST `/api/v1/contracts` y \`/api/v1/contracts/{id}.

[HIP-584](https://hips.hedera.com/hip/hip-584) El nodo de archivo EVM sigue progresando. Esta versión contiene soporte completo para lecturas del estado del contrato tanto para funciones precompiladas como no precompiladas. Se añadió soporte para modificaciones del estado del contrato para funciones no precompiladas con la excepción de la creación de una cuenta perezosa.

Nuestra [goal](https://github.com/hashgraph/hedera-mirror-node/issues/6110) de capturar todos los cambios de entidad vio más refinamientos. La información de las fichas vio que se añadía una tabla de historial para llevar un registro de cualquier cambio en los metadatos de token con el tiempo. También, ahora usamos el saldo de fichas en tiempo real en el balance de fichas REST API.

## [v0.83](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.83.0)

{% hint style="success" %}
**MAINNET ACTUALIZADO COMPLETADO: 7 de JULY 2023**
{% endhint %}

{% hint style="success" %}
**TESTNET ACTUALIZADO COMPLETADO: JUNE 29, 2023**
{% endhint %}

En esta versión hicimos el cambio altamente solicitado para mostrar la lista de transferencias NFT en la lista de transacciones REST API. Originalmente, solo el `/api/v1/transactions/{id}` mostró la lista de `nft_transfers` debido a problemas de rendimiento al unirse a otra tabla grande para una API tan utilizada y de gran peso. Para mostrar esta información mientras permanece, teníamos que desormalizar la información de transferencia NFT para anidarla bajo la tabla `transaction` como una columna JSONB. Esto evita un join extra y nos permite devolver la información dada con la consulta existente.

El nodo espejo se centra en rastrear todos los cambios posibles a las entidades Hedera con el tiempo. Para ello se creó una tabla de historia NFT para capturar todos los cambios posibles en un NFT con el tiempo. Además de persistir en estos datos, también estamos exponiendo más información histórica a través de la API. Ahora cuando el parámetro `timestamp` es suministrado en el endpoint `/api/v1/accounts/{id}` mostrará la vista histórica de esa cuenta. Anteriormente, el parámetro sólo se usaría para mostrar la lista de `transactions` en el momento dado. Espere mejoras adicionales en torno a la información de entidades históricas en la próxima versión.

[HIP-584](https://hips.hedera.com/HIP/hip-584.html) continúa haciendo avances en cada lanzamiento. Esta versión se centró en mejorar el soporte de precompilación para `/api/v1/contracts/call`. Ahora hay soporte para el código CREATE2 junto con lecturas de estado de contrato no estático para precompilaciones y funciones no precompiladas. Las modificaciones no estáticas del contrato para las funciones no precompiladas (excluyendo la creación de una cuenta perezosa) también fueron trabajadas. Por último, la cobertura de las pruebas de aceptación aumentó considerablemente y se abordaron varios errores.

Esta versión añade integración con el [Operador de Stackgres](https://stackgres.io/) para proporcionar un despliegue [Citus]altamente disponible (https://www.citusdata.com/). Stackgres es un operador establecido de Kubernetes para PostgreSQL y su soporte para la extensión Citus ha hecho fácil proporcionar un despliegue listo para la producción sin depender de un costoso, servicios de base de datos gestionados en la nube. Esto junto con la compresión de volumen ZFS que añadimos en una versión anterior debería reducir en gran medida el costo total de ejecutar un nodo espejo al tiempo que se proporciona escalabilidad horizontal.

### Actualizando

Espere que la actualización demore aproximadamente una hora debido a la migración de la transferencia de NFT en esta versión. Como siempre, las actualizaciones recomendadas se completan en un entorno de puesta en escena primero (p.e. un despliegue rojo/negro) para permitir la verificación del despliegue y reducir el tiempo de inactividad antes de abrirse al tráfico de clientes.

## [v0.82](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.82.0)

{% hint style="success" %}
**MAINNET ACTUALIZADO COMPLETADO: 22 de JUNIO de 2023**
{% endhint %}

{% hint style="success" %}
**TESTNET ACTUALIZADO COMPLETADO: 8 de JUNO, 2023**
{% endhint %}

[HIP-679](https://hips.hedera.com/HIP/hip-679.html) vio su trabajo inicial completado en esta versión para soportar un cubo reestructurado. El importador ahora soporta la ruta actual basada en ID de la cuenta junto con una futura ruta de cubo basada en ID. También añade una propiedad de tipo de ruta que puede cambiar automáticamente entre los dos para que la transición entre los dos formatos sea transparente para los operadores de los nodos. Por ahora, el tipo de ruta por defecto permanecerá como ID de cuenta hasta que el nodo se convierta en una realidad para reducir los costos de S3.

[HIP-584](https://hips.hedera.com/HIP/hip-584.html) El nodo de archivo EVM Mirror vio un gran número de mejoras para acercarlo a la paridad con los nodos de consenso. Se integraron los accesorios de estado y bases de datos apilados para permitir que los contratos inteligentes cambiaran temporalmente el estado. Se añadió un rastreador de operaciones para facilitar la depuración de contratos inteligentes en un entorno.

Finalmente, se añadió una tabla de búsqueda de mensajes para optimizar la búsqueda de mensajes en bases de datos distribuidas.

## [v0.81](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.81.0)

{% hint style="success" %}
**MAINNET ACTUALIZADO COMPLETADO: JUNIO 1, 2023**
{% endhint %}

{% hint style="success" %}
**TESTNET ACTUALIZADO COMPLETADO: MAY 24, 2023**
{% endhint %}

Esta versión viene con una serie de mejoras para soportar el Relé JSON-RPC. Ahora soportamos búsquedas de direcciones de alias y EVM para el parámetro `account.id` en el punto final del balance. Optimizamos el filtro nonce de la transacción en `/api/v1/contracts/{id}/results` desormalizando los datos. Finalmente, un problema con `function_parameters` vacío en la respuesta `/api/v1/contracts/results/{id}` fue resuelto.

El otro elemento grande en el que trabajamos fue [support](https://github.com/hashgraph/hedera-mirror-node/blob/main/docs/database/zfs.md) para la compresión del nivel de volumen con Citus. Sabemos que el tipo de datos de series de tiempo que los almacenes de nodos espejo serían altamente compresibles, y queríamos usarlos en nuestra ventaja tanto para reducir los crecientes costos de almacenamiento como para mejorar el rendimiento de lectura/escritura. PostgreSQL soporta una forma básica de compresión a nivel de columna llamada [TOAST](https://www.postgresql.org/docs/current/storage-toast.html), pero sólo tiene efecto para columnas muy grandes. Citus tiene compresión cuando utiliza su método de acceso de almacenamiento columnar, pero nos pareció demasiado lento para nuestras necesidades. Ya que con Citus sabíamos que no estaríamos usando un servicio SaaS teníamos más control sobre la implementación de la base de datos, así que decidimos experimentar con la compresión de volumen de Kubernetes. Al crear piscinas de node Kubernetes personalizadas exclusivamente para Citus, podríamos instalar [ZFS](https://en.wikipedia.org/wiki/ZFS) a través del [zfs-localpv](https://github. om/openebs/zfs-localpv) y habilitar la compresión [Zstandard](http://facebook.github.io/zstd/) en el volumen subyacente de la base de datos. Los resultados fueron una **proporción de compresión de 3,6 x con pérdida cero en rendimiento**. Para ponerlo en perspectiva, eso significa que el tamaño actual de la base de datos mainnet de 17TB podría reducirse a 4,7TB.

Otras áreas de mejora incluyen la mejora de la documentación en torno a los esfuerzos de recuperación de desastres. Esto incluye un [runbook](https://github.com/hashgraph/hedera-mirror-node/blob/main/docs/runbook/restore.md) al restaurar un nodo espejo desde una copia de seguridad. También hay un [runbook](https://github.com/hashgraph/hedera-mirror-node/blob/main/docs/runbook/stream-verification.md) sobre cómo realizar la verificación local de archivos de stream. Las pruebas de aceptación se han integrado previamente en el proceso de despliegue automatizado, pero han sufrido un largo tiempo de ejecución debido principalmente al uso de Gradle para descargar dependencias en tiempo de ejecución. Contenerizamos las pruebas de aceptación para que las dependencias se descarguen en tiempo de construcción reduciendo el tiempo de ejecución en 3-4x.

## [v0.80](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.80.0)

{% hint style="success" %}
**MAINNET ACTUALIZADO COMPLETADO: MAY 17, 2023**
{% endhint %}

{% hint style="success" %}
**TESTNET ACTUALIZADO COMPLETADO: MAY 11, 2023**
{% endhint %}

El trabajo continúa en [HIP-584](https://hips.hedera.com/HIP/hip-584.html) con esta versión la primera en soportar lecturas no estáticas del estado del contrato para funciones no precompiladas. Por favor vea la interfaz de usuario de Swagger [table](https://github.com/hashgraph/hedera-mirror-node/pull/5949/files) para `/api/v1/contracts/call` para un desglose de cuál funcionalidad está soportada en qué versión. Se copió más funcionalidad de gas estimada del código de servicios para avanzar en la estimación. Se añadió una nueva funcionalidad de marco de estado apilado para ser utilizado en el futuro para soportar escrituras de contratos y lecturas en caché.

La herramienta de formato de código [Spotless](https://github.com/diffplug/spotless/tree/main) se utilizó para dar formato a toda la base de código para ser consistente. Se añadió un gancho de commit git para asegurar que cualquier cambio nuevo se mantenga consistente y que los desarrolladores puedan centrarse en lo que importa.

Finalmente, hubo un gran número de correcciones de errores y mejoras de rendimiento. Vea a continuación los detalles completos.

## [v0.79](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.79.0)

{% hint style="success" %}
**MAINNET ACTUALIZADO COMPLETADO: MAY 9, 2023**
{% endhint %}

{% hint style="success" %}
**TESTNET ACTUALIZADO COMPLETADO: MAY 2, 2023**
{% endhint %}

[HIP-584](https://hips.hedera.com/HIP/hip-584. tml) El nodo de archivo EVM vio un mayor progreso de esta versión centrándose en probar y establecer las bases para estimar la funcionalidad de gas en la próxima versión. Mientras los nodos de consenso se someten a un esfuerzo de modularización que pagará dividendos por el camino. el nodo de archivo necesita funcionalidad para estimar gas antes de que ese proceso pueda ser completado. Para progresar en HIP-584, la lógica necesaria de EVM fue copiada temporalmente desde los nodos de consenso en el módulo web del nodo espejo. Se ha puesto un gran énfasis en aumentar la cobertura de las pruebas de aceptación de la llamada de contrato con precompilación.

Los usuarios que escriben dApps quieren ser capaces de monitorear para eventos de aprobación y transferencia de token. Transacciones HAPI como `CryptoTransfer`, `CryptoApproveAllowance`, `CryptoDeleteAllowance`, `TokenMint`, `TokenWipe`, y `TokenBurn` no emiten eventos que podrían ser capturados por herramientas de supervisión como [The Graph](https://thegraph. om/es/) ya que son ejecutados fuera de la EVM. Para dirigirse, el nodo espejo ahora genera eventos sintéticos de registro de contratos para estas transacciones HAPI no EVM.

Una nueva API de suscripción era [designed](https://github.com/hashgraph/hedera-mirror-node/blob/main/docs/design/contract-log-subscription.md) para [HIP-668 GraphQL API](https://github.com/hashgraph/hedera-improvement-proposal/pull/668). Una vez que se implemente en una futura versión, la nueva suscripción al registro del contrato transmitirá eventos del contrato a los clientes a través de una conexión WebSocket.

Para nuestra transición a la base de datos Citus, la compatibilidad de PostgreSQL 15 fue verificada y se hizo el valor predeterminado para este esquema v2. La búsqueda de información del balance histórico a través de `/api/v1/balances?timestamp=` fue optimizada para bases de datos reducidas por lo que permanece en rendimiento. Las pruebas de rendimiento mostraron una disminución en el recuento de fragmentos podía mejorar mucho el rendimiento, así que redujimos el número de fragmentos de 32 a 16. Esta prueba también nos permitió proporcionar una configuración inicial de recurso [recommended](https://github.com/hashgraph/hedera-mirror-node/blob/main/docs/database.md#database-migration-from-v1-to-v2) para el despliegue de Citus.

Hubo un gran foco en las mejoras de prueba en esta versión. Además de la cobertura de pruebas HIP-584 más importante, también optimizamos las pruebas de aceptación para reducir la duración total de la prueba en Kubernetes a la mitad sin reducir la cobertura. Los registros de prueba de aceptación fueron limpiados para reducir las sentencias de registro innecesarias y estandarizar su salida. El balance hbar utilizado por las pruebas ahora se registra al final de la ejecución de la prueba. Se añadieron pruebas de aceptación para la creación de cuentas huecas. Ahora generamos imágenes de instantánea multiplataforma desde la rama `main` para probar con nodo local. La configuración de [Testkube](https://testkube.io/) fue mejorada para hacerlo más configurable. Por último, todas las advertencias del compilador de pruebas de Java fueron arregladas y ahora fallarán en la compilación si ocurren advertencias futuras.

### Problemas conocidos

Hay un [bug](https://github.com/hashgraph/hedera-mirror-node/issues/5944) introducido por [#5776](https://github.com/hashgraph/hedera-mirror-node/pull/5776) que provoca que el importador falle al iniciar. Se recomienda aplazar la actualización a v0.79.0 hasta que podamos abordar esto en un v0.79.1. Alternativamente, se puede trabajar deshabilitando la migración defectuosa estableciendo `hedera.mirror.importer.migration.syntheticTokenAllowanceOwnerMigration.enabled=false`.

## [v0.78](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.78.0)

{% hint style="success" %}
**MAINNET ACTUALIZADO COMPLETADO: APRIL 21, 2023**
{% endhint %}

{% hint style="success" %}
**TESTNET ACTUALIZADO COMPLETADO: APRIL 13, 2023**
{% endhint %}

[HIP-584](https://hips.hedera.com/HIP/hip-584.html) El nodo de archivo de Mirror Evm ahora tiene soporte de precompilación de token. Esta fue la última pieza importante de funcionalidad necesaria para que el `/api/v1/contracts/call` sea considerado equivalente a `eth_call`. La nueva API fue añadida a la documentación de OpenAPI de REST para que aparezca en nuestra [interfaz de usuario de Swagger](https://mainnet-public.mirrornode.hedera.com/api/v1/docs). Se han trabajado varias optimizaciones de rendimiento para hacerlo escalable así como varias mejoras de prueba para verificar su exactitud. Se abordaron varios bugs que incluían el manejo adecuado de las reversiones. En los próximos lanzamientos, tenemos previsto afinar la llamada de contrato e implementar la estimación del gas del contrato.

Se puso un gran énfasis en el rendimiento y la resistencia de esta liberación. En el frente de rendimiento, hemos optimizado la lista de la API REST para ser escalable en Citus. Las pruebas de rendimiento ahora pueden activarse automáticamente a través de TestKube una vez completadas las pruebas de timón. Esas mismas pruebas de rendimiento k6 fueron mejoradas para elegir automáticamente los valores de configuración apropiados específicos del entorno. La tabla hash de transacción fue particionada y el proceso más ingenioso para insertar hash en paralelo. Este cambio acelera dramáticamente el tiempo para insertar los hash de transacción opcionales. De manera similar, se añadió una opción para controlar qué tipos de transacción deben causar una inserción de hash.

En el frente de la resistencia, el componente importador fue analizado para cualquier ruta de código que pudiera causar que el procesamiento de archivos de registros se detuviera debido a una entrada incorrecta de los nodos de consenso. Cualquier código de este tipo se hizo para manejar el error, registrar/notificar y pasar a la siguiente transacción. Este cambio hace que la ingestión del nodo espejo sea más resistente y avanza hacia la preferencia de disponibilidad por encima de la exactitud. Los nodos espejo parciales que podrían quedar atascados debido a tener una libreta de direcciones incompleta ahora pueden seguir ingiriendo con una nueva propiedad y lógica `consensusMode`. Los nodos espejo parciales ahora también podrán tener una cuenta corregida y saldo de fichas incluso si la entidad no tenía una bandera eliminada. Por último, fuimos capaces de completar un largo esfuerzo de refactorización para mover toda la lógica específica de transacción a los manejadores de transacciones individuales y corregir varios errores en el proceso.

Había algunos errores importantes corregidos en esta publicación que vale la pena destacar. Se estableció una solución para corregir el suministro total inexacto de tokens fungibles. Además, los NFTs para tokens borrados ya no aparecen como activos en la API REST.

## [v0.77](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.77.0)

{% hint style="success" %}
**MAINNET ACTUALIZADO COMPLETADO: APRIL 4, 2023**
{% endhint %}

{% hint style="success" %}
**TESTNET ACTUALADO COMPLETADO: MARCH 29, 2023**
{% endhint %}

Esta versión corrige el seguimiento de los saldos NFT. Históricamente, estos procedían del archivo de balance enviado por los nodos de consenso cada 15 minutos. Cuando comenzamos a rastrear los saldos de tokens fungibles en vivo y nos alejamos del uso de este archivo de saldo desafortunadamente rompimos el cálculo del balance NFT. No sólo hemos solucionado el problema, sino que también hemos tomado el tiempo de seguir el balance actualizado de NFT.

La API REST `/api/v1/contracts/{id}/state` muestra el estado actual de los valores de slot de un contrato. Los usuarios solicitaron la posibilidad de consultar los pares clave/valor para su contrato en un momento arbitrario en el pasado. Para abordar, ahora exponemos un parámetro de consulta `timestamp` que obtendrá el estado histórico del contrato. Esto permite que el repetidor JSON-RPC ofrezca un `eth_getStorageAt` apropiado con soporte para bloques históricos.

[HIP-584](https://hips.hedera.com/HIP/hip-584.html) continúa progresando. Algunos errores fueron aplastados, incluyendo el manejo de revertidos y la recolección de la razón de revertir y los datos crudos. Las pruebas de rendimiento se añadieron a k6 para cargar las llamadas al contrato de prueba con precompilaciones de token.

## [v0.76](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.76.0)

{% hint style="success" %}
**MAINNET ACTUALIZADO COMPLETADO: MARCH 23, 2023**
{% endhint %}

{% hint style="success" %}
**TESTNET ACTUALIZADO COMPLETADO: MARCH 13, 2023**
{% endhint %}

El nuevo `/api/v1/contracts/call` API REST especificado en [HIP-584](https://hips.hedera.com/HIP/hip-584.html) finalmente está listo para el uso inicial de producción. Esta versión añade soporte para limitar la velocidad de la API con un valor inicial de 100 peticiones por segundo por instancia. Se añadieron etiquetas al gas por segundo para indicar si la solicitud era una llamada, una estimación, o resultó en un error para aumentar la observabilidad. También se abordaron varias correcciones de errores.

[HIP-668](https://github.com/hashgraph/hedera-improvement-proposal/pull/668) API GraphQL fue añadida a nuestro despliegue con la adición de un nuevo gráfico de helm para esta API. Esto permitirá el uso inicial de la API en todos los entornos con la comprensión de que todavía es muy alfa y sujeto a cambios.

Hicimos mucha huella en nuestra integración con Citus. Citus se actualizó a 11.2 lo que mostró un buen incremento de rendimiento del 15-20% para una serie de patrones de consulta. Optimizamos el rendimiento de las APIs de resultados del contrato mediante la distribución basada en el ID del contrato en lugar de la cuenta del pagador. La búsqueda de una transacción por su hash en Citus se mejoró añadiendo la columna de distribución a la consulta y limitando los resultados a un rango de marcas de tiempo. La búsqueda de una cuenta por su ID también vio mejoras en Citus.

### Rompiendo Cambios

Los gráficos de Helm contienen algunos cambios de ruptura de los que debe ser consciente. El gráfico `hedera-mirror` permite el nuevo subgráfico `hedera-mirror-graphql` por defecto. El despliegue GraphQL requiere que exista un nuevo usuario de base de datos `mirror_graphql` para que se inicie correctamente. Puede crear el usuario iniciando sesión en la base de datos como propietario y ejecutando la siguiente consulta SQL:

```
crear usuario mirror_graphql con contraseña de inicio de sesión 'contraseña' sólo en rol;
```

Si está usando el subgráfico embebido de PostgreSQL (que no recomendamos para uso en producción), entonces tendrás que borrar su StatefulSet antes de actualizar debido a un bump importante en su versión de gráfico.

El gráfico `hedera-mirror-common` tenía todos sus componentes actualizados a nuevas versiones importantes que incluyen cambios de ruptura. Si estás usando este gráfico, ejecuta los siguientes comandos antes de actualizar:

```
kubectl delete daemonset mirror-prometheichard node-exporter
kubectl delete daemonset mirror-traefik
kubectl delete statefulset mirror-loki
kubectl delete ingressroute mirror-traefik-dashboard
kubectl apply --server-side --force-conflicts=true -f https://raw. ithubusercontent.com/promethe)[video] operator/promethe)[video] operator/v0.63.0/example/promethe)[video] operator-crd/monitoring.coreos.com_alertmanagerconfigs.yaml
kubectl apply --server-side --force-conflicts=true -f https://raw. ithubusercontent.com/promethe)[video] operator/promethe)[video] operator/v0.63.0/example/promethe)[video] operator-crd/monitoring.coreos.com_alertmanagers.yaml
kubectl aplicar --server-side --force-conflicts=true -f https://raw.githubusercontent.com/promethe)[video] operator/v0.63.0/example/promethebian operator-crd/monitoring.coreos.com_podmonitors.yaml
kubectl apply --server-side --force-conflicts=true -f https://raw.githubusercontent. om/promethe)[video] operator/promethe)[video] operator/v0.63.0/example/promethe)[video] operator-crd/monitoring.coreos.com_probes.yaml
kubectl apply --server-side --force-conflicts=true -f https://raw.githubusercontent.com/promethe)[video] operator/promethe)[video] operator/v0.63.0/example/promethe)[video] operator-crd/monitoring.coreos.com_prometheuses.yaml
kubectl apply --server-side --force-conflicts=true -f https://raw.githubusercontent.com/promethe)[video] operator/v0.63. /example/prometheum-operator-crd/monitoring.coreos.com_prometheusrules.yaml
kubectl apply --server-side --force-conflicts=true -f https://raw.githubusercontent.com/promethe)[video] operator/prometheanian operator/v0.63.0/example/promethe)[video] operator-crd/monitoring.coreos.com_servicemonitors.yaml
kubectl aplicar --server-side --force-conflicts=true -f https://raw.githubusercontent.com/promethe)[video] operator/v0.63.0/example/promethe)[video] operator-crd/monitoring.coreos.com_thanosrulers.yaml
```

## [v0.75](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.75.0)

{% hint style="success" %}
**MAINNET ACTUALIZADO COMPLETADO: ARCHIVO 2, 2023**
{% endhint %}

El trabajo continúa en [HIP-584](https://hips.hedera.com/HIP/hip-584.html) para acercarlo a la producción listo para simples llamadas de contrato. La lógica de caché se añadió a la capa del repositorio para optimizar su capacidad junto con las pruebas de rendimiento para verificar esas mejoras. Se añadió una métrica para rastrear el gas por segundo que se usaba junto con varias otras correcciones de errores.

La API del monitor y el tablero de mando utilizados internamente para observar nuestros sistemas de producción fueron contenedores. Adicionalmente, se integró en el gráfico del casco y se invocó como parte de las pruebas del casco para asegurar que el despliegue sea verificado.

Finalmente, hubo una serie de optimizaciones de consultas como parte de nuestro esfuerzo de Citus.

## [v0.74](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.74.0)

{% hint style="success" %}
**MAINNET ACTUALIZADO COMPLETEDO: FEBRUARY 18, 2023**
{% endhint %}

{% hint style="success" %}
**MAINNET ACTUALIZADO COMPLETEDO: FEBRUARY 14, 2023**
{% endhint %}

Esta versión cambia el cubo de testnet al nuevo creado para el [reset de testnet](https://docs.hedera.com/hedera/networks/testnet#test-network-resets) que ocurrió el 26 de enero de 2023. También actualiza la libreta de direcciones para reflejar los nodos adicionales añadidos a testnet desde el último reinicio. Si estás corriendo un nodo espejo de testnet, por favor mira las [instrucciones de reinicio](https://github.com/hashgraph/hedera-mirror-node/blob/main/docs/database.md#reset) para obtener ayuda para actualizar tu nodo.

En [HIP-668](https://github.com/hashgraph/hedera-improvement-proposal/pull/668), proponemos añadir un nuevo nodo espejo API GraphQL y agradeceríamos enormemente tus comentarios. En esta versión, se añadió un nuevo módulo GraphQL con una simple consulta de búsqueda de cuentas para proporcionar la base de trabajo futuro en este HIP. En la próxima versión, añadiremos el despliegue automatizado de este módulo a todos los entornos. Se considera una API alfa sujeta a cambios de ruptura en cualquier momento, por lo que no se recomienda depender para uso en producción. Esto se ha hecho explícito usando `/graphql/alpha` en su URL.

Finalmente, se implementaron varias optimizaciones de consultas para Citus asegurándose de que no cause regresiones con la base de datos existente. Este seguirá siendo el centro de atención de los próximos lanzamientos.

## [**v0.73**](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.73.0)

{% hint style="success" %}
**MAINNET ACTUALIZADO COMPLETEDO: FEBRUARY 10, 2023**
{% endhint %}

{% hint style="success" %}
**TESTNET UPDATE COMPLETEDO: FEBRUARY 3, 2022**
{% endhint %}

Esta versión es la primera con soporte para [HIP-584](https://hips.hedera.com/HIP/hip-584.html) EVM Archive Node. HIP-584 permite que los nodos espejo actúen como EVM de sólo lectura para la ejecución libre de contratos inteligentes. Esta nueva característica se considera alpha con gran parte del trabajo aún por implementar como soporte para contratos precompilados, estimación de gas, etc. Esta funcionalidad requiere que el nodo réplica esté configurado para ingerir los archivos sidecar opcionales de trazabilidad y requiere una red donde se generen esos archivos. Actualmente sólo la previewnet tiene habilitada la trazabilidad del contrato.

El nombre del cubo testnet ha sido actualizado al nuevo nombre del cubo después de su reciente [reset]trimestral (https://docs.hedera.com/hedera/testnet#test-network-resets). Asimismo, la libreta de direcciones de la red de pruebas de arranque se actualizó para reflejar los nodos adicionales de la red de pruebas que se han añadido desde el restablecimiento anterior. Los operadores de nodo de espejo ejecutando un nodo testnet deberían llenar manualmente el nuevo nombre del cubo o actualizar a esta versión.

El trabajo restante apuntaba a mejoras significativas en las pruebas y correcciones de errores. Nuestras pruebas de rendimiento se ampliaron a todos los extremos para captar problemas antes del ciclo de vida. La cobertura de prueba de aceptación adicional fue añadida junto con una serie de correcciones. La estabilidad de CI ha mejorado en gran medida con un enfoque en la corrección de pruebas defectuosas. El código huele según el reportado por [Sonar](https://sonarcloud.io/project/overview?id=hedera-mirror-node) se redujo a solo un puñado y en el siguiente lanzamiento se redujo hasta cero. Finalmente, fusionamos trabajo que permite pruebas de rendimiento nocturno en nuestros entornos de integración y escenificación de red principal a través de [TestKube](https://testkube.io/).

## [**v0.72**](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.72.0)

{% hint style="success" %}
**MAINNET ACTUALIZADO COMPLETADO: 25 de Enero, 2022**
{% endhint %}

{% hint style="success" %}
**TESTNET ACTUALIZADO COMPLETADO: 18 de Enero, 2022**
{% endhint %}

Esta versión es una versión más pequeña, ya que la mayoría del equipo tuvo tiempo libre para las vacaciones. Aún así, conseguimos implementar [HIP-583](https://hips.hedera.com/HIP/hip-583.html) Expandir soporte de alias en las transacciones de CryptoCreate y Cryptotransfer. Ahora permitimos que las cuentas vacías se finalicen más tarde en un contrato cuando se crea plenamente.

También hemos trabajado en añadir soporte para [Testkube](https://testkube.io/). Testkube nos permite automatizar nuestras pruebas en entornos de Kubernetes activando pruebas basadas en diversas condiciones. Específicamente, se utilizará para ejecutar pruebas de regresión de rendimiento nocturno contra un entorno de staging mainnet para asegurar que nuestro rendimiento de la API no retroceda. Continuaremos ampliando esta prueba automatizada en futuras versiones.

También hubo varias correcciones de errores en esta versión, principalmente enfocado en arreglar nuestro proceso de liberación después del cambio de Maven a Gradle en la última versión.

## [v0.71](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.71.0)

{% hint style="success" %}
**MAINNET ACTUALIZADO COMPLETADO: 19 de enero de 2023**
{% endhint %}

A partir de esta versión, todos los saldos de cuentas y tokens de la API REST reflejarán su información de saldo en tiempo real. Históricamente, el nodo espejo se ha basado en el archivo de balance subido por los nodos de consenso cada 15 minutos para su información de saldo. Hemos estado trabajando para alcanzar este hito para muchas versiones que gradualmente están desplegando el seguimiento del balance en tiempo real a más entidades y más APIs. Esta versión completa esta migración con la adición de saldos de token en tiempo real tanto a las cuentas como a las APIs REST.

El nodo espejo ahora implementa soporte para [HIP-583](https://hips.hedera.com/HIP/hip-583.html) en transacciones `CryptoCreate`. Con esto, los clientes pueden establecer directamente un alias durante la creación de la cuenta en lugar de depender de la creación implícita de la cuenta automática durante las transferencias. El nodo espejo respeta este alias explícito junto con la nueva dirección EVM explícita en `CryptoCreate` o el `TransactionRecord`. Esto evita el cálculo de direcciones EVM en el nodo espejo que nos ha causado algunos problemas en el pasado.

Esta versión completa la migración de Maven a Gradle para nuestro proceso de compilación. Se ha dedicado mucho trabajo a la nueva construcción para mejorar su rendimiento y estabilidad tanto a nivel local como en continua integración (CI). Los workflows de GitHub Actions se han consolidado de un flujo de trabajo por módulo a un único flujo de trabajo de compilación de Gradle con una estrategia de matriz ejecutándolos en paralelo para cada módulo y esquema de base de datos. Esto simplifica enormemente la configuración del flujo de trabajo haciendo más fácil el mantenimiento y depuración.

Seguimos avanzando en nuestra exploración de Citus. El esquema v2 para Citus ahora hace timestamp basado en particionamiento de datos y automatiza este proceso vía pg\_cron. Se creó un entorno específico de Citus y actualmente estamos realizando pruebas de rendimiento contra él a escala para verificar que cumple con nuestros requisitos.

Esta versión añade automatización para mantener nuestra aplicación GCP Marketplace actualizada con cada versión. Aunque no es totalmente automático debido a la naturaleza manual del envío de la versión de Marketplace, ahora cualquier nueva etiqueta de producción activará la generación y verificación de las imágenes del mercado.

Esta era una gran versión y había muchas otras mejoras y correcciones. Vea la nota de lanzamiento completa a continuación.

## [v0.70](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.70.0)

{% hint style="success" %}
**MAINNET ACTUALIZADO COMPLETADO: DECEMBRE 29, 2022**
{% endhint %}

{% hint style="success" %}
**TESTNET ACTUALIZADO COMPLETADO: DECEMBRE 14, 2022**
{% endhint %}

Como parte de [HIP-406](https://hips.hedera.com/HIP/hip-406.html), el nodo espejo está agregando una nueva cuenta de recompensas REST API. Esta API mostrará las recompensas de apuesta pagadas a una cuenta a lo largo del tiempo. El nodo espejo ahora también muestra transferencias de premios en la transacción REST (ej. `/api/v1/transactions`, `/api/v1/transactions/{id}`, y la lista de transacciones en `/api/v1/accounts/{id}`). Esto puede ser útil para mostrar qué transacción involucrada en su cuenta después de que el período de apuesta terminó el pago de la recompensa perezosa.

`GET /api/v1/accounts/{id}/rewards`

```
{
  "rewards": [{
    "account_id": "0.0.1000",
    "amount": 10,
    "timestamp": "123456789.0000000000001"
  }],
  "links": {
    "next": null
  }
}
```

La API REST vio más mejoras fuera de la apuesta. Las cuentas API REST ahora muestran un timestamp calculado de caducidad para reflejar la consulta `CryptoGetInfo` de HAPI. Anteriormente la fecha de caducidad sólo aparece si se envía explícitamente a través de una transacción que lo soporta (principalmente actualizar transacciones). Ahora si es `null` lo calculamos como `created_timestamp.seconds + auto_renew_period`. Todos los resultados del contrato se actualizaron para incluir un campo de dirección para la dirección EVM del contrato creado.

Esta versión avanza en poder ejecutar llamadas de contrato en el nodo espejo como se describe en [HIP-584](https://hips.hedera.com/HIP/hip-584.html). Se está sentando gran parte del trabajo preliminar que se perfeccionará aún más en las próximas publicaciones.\\

## [v0.69](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.69.0)

{% hint style="success" %}
**MAINNET ACTUALIZADO COMPLETADO: DECEMBRE 5, 2022**
{% endhint %}

{% hint style="success" %}
**TESTNET ACTUALIZADO COMPLETADO: NOVEMBRE 29, 2022**
{% endhint %}

Como se señaló en versiones anteriores, [HIP-367](https://hips.hedera.com/hip/hip-367) está obsoleto de la información de relación de tokens devuelta de consultas HAPI. En esta versión, su reemplazo de nodo espejo ahora está completo de características. Ahora rastreamos y mostramos el actual balance fungible de tokens en la API de relaciones de tokens, en lugar de depender del balance de 15 minutos de exportación desde los nodos de consenso. En una versión futura, las cuentas y los balances de las APIs REST serán actualizados para mostrar el saldo de tokens fungible actual.

El componente importador ahora soporta un proveedor local de flujo de archivos. Esto le permite leer archivos de flujo desde un directorio local en lugar de sólo los proveedores compatibles con S3 que soportó anteriormente. Este modo es útil para depurar archivos de flujo recibidos por banda o para reducir complejidad y latencia en una configuración de nodo local. Para probarlo, establece `hedera.mirror.importer.downloader.cloudProvider=LOCAL` y llena la carpeta `hedera.mirror.importer.dataPath`/`streams` con la misma estructura de archivo que los cubos de la nube.

Ahora mostramos la dirección EVM de un contrato `CREATE2` en los registros de contrato API REST. Anteriormente, convertiríamos el `shard.realm.num` de Hedera en una dirección EVM de 20 bytes pero esto no siempre reflejaba la verdadera dirección EVM del contrato. Utilizando la forma `CREATE2` de la dirección EVM proporciona una mayor compatibilidad con Ethereum.

Seguimos avanzando en la conversión de nuestro proceso de construcción a Gradle. Esta versión añade un plugin Golang Gradle para descargar el Go SDK y usarlo para construir y probar el módulo Rosetta.

## [v0.68](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.68.0)

{% hint style="success" %}
**MAINNET ACTUALADO COMPLETADO: AHORA 18, 2022**
{% endhint %}

{% hint style="success" %}
**TESTNET ACTUALIZADO COMPLETADO: NINVEMBRO 18, 2022**
{% endhint %}

Además de la ronda habitual de correcciones de errores, esta versión se enfoca en algunas mejoras internas para sentar las bases para algunas características futuras. Ahora rastreamos y persistimos el actual balance fungible de tokens en la base de datos. Esta información aún no está expuesta en ninguna API pero se desplegará en las relaciones de tokens, cuentas y balances de APIs REST en un futuro próximo.

Estamos continuando nuestro trabajo hacia [CitusDB](https://www.citusdata.com/) como un posible reemplazo de base de datos en esta versión agregando columnas de distribución y corrigiendo nuestras pruebas de esquema v2.

Finalmente, implementamos soporte inicial de Gradle para mejorar los tiempos de construcción y proporcionar una mejor experiencia de desarrolladores. Las pruebas iniciales muestran tiempos de construcción y prueba reducidos de 8 minutos en total a 2 minutos. Los scripts de compilación Gradle y Maven se mantendrán simultáneamente durante unas pocas versiones hasta que podamos asegurar que la construcción de Gradle alcance la paridad de características con Maven.

## [v0.67](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.67.0)

{% hint style="success" %}
**MAINNET ACTUALADO COMPLETADO: NOVEMBRE 10, 2022**
{% endhint %}

{% hint style="success" %}
**TESTNET ACTUALIZADO COMPLETADO: NOVEMBRE 10, 2022**
{% endhint %}

[HIP-367](https://hips.hedera.com/hip/hip-367) desaprobó la lista de saldos de tokens para una cuenta devuelta a través de HAPI. El nodo espejo ha estado trabajando en su reemplazo para algunas versiones por [storing](https://github.com/hashgraph/hedera-mirror-node/issues/4030) el saldo actual de la cuenta, [combining](https://github. om/hashgraph/hedera-mirror-node/issues/4150) y tablas de entidades, y [agregando una tabla de historial](https://github.com/hashgraph/hedera-mirror-node/issues/3251) para `token_account`. Este trabajo ha allanado el camino para una nueva relación de token API REST que listará todos los tokens fungibles y no fungibles asociados con una cuenta en particular. Esta API también devuelve algunos metadatos como el balance, el estado de KYC, el estado de congelación, y si es una asociación automática o no. Actualmente el saldo fungible del token que se devuelve es del archivo de saldo de cuenta de 15 minutos. Estamos en [actively](https://github.com/hashgraph/hedera-mirror-node/issues/4402) trabajando para seguir el balance de tokens fungible en tiempo real y se actualizará para reflejarlo en una futura versión.

`GET /api/v1/accounts/{id}/tokens`

```
{
  "tokens": [{
    "automatic_association": true,
    "balance": 15,
    "created_timestamp": "1234567890. 00000002",
    "freeze_status": "UNFROZEN",
    "kyc_status": "GRANTED",
    "token_id": "0. .1135"
  }],
  "enlaces": {
    "siguiente": null
  }
}
```

[HIP-513](https://hips.hedera.com/HIP/hip-513.html) detalla cómo la información de seguimiento de contratos inteligente desde nodos de consenso está disponible a través de archivos sidecar. Los cambios en el estado del contrato dentro del sidecar persisten por los nodos espejo y están disponibles en las APIs de resultados del contrato. Sin embargo, estos cambios de estado no siempre reflejan la lista completa de los valores de almacenamiento de contratos inteligentes, ya que no todos los espacios se modifican durante una invocación de contrato en particular. Ahora persistimos en una vista enrollada de los cambios de estado para rastrear los últimos pares de clave/valor. Esta información del estado del contrato ahora está expuesta a través de una nueva API REST `/api/v1/contracts/{id}/state` donde `id` es el `shard. ealm.num`, `realm.num`, `num`, o una dirección EVM codificada hexadecimal del contrato inteligente.

`GET /api/v1/contracts/{id}/state`

```
{
  "state": [{
    "address": "0x00000000000000000000000000000000000000000000000000000000001f41",
    "contract_id": "0.0. 00",
    "slot": "0x0000000000000000000000000000000000000000000000000000000000000000000001",
    "timestamp": "1676540001. 34390005",
    "value": "0x0000000000000000000000000000000000000000000000000000000000000000000010"
  }],
  "links": {
    "next": null
  }
}
```

En un push para una mayor descentralización, ahora el nodo aleatoriamente utilizado para descargar archivos de datos después de alcanzar el consenso. Anteriormente, la estructura de datos que usamos generalmente provocaba que usáramos el primer nodo devuelto en la lista verificada, que normalmente era `0.0.3`. Ahora escojemos aleatoriamente un nodo hasta que podamos descargar con éxito el archivo de transmisión. También cambiamos internamente todas las tablas que usaban el ID de la cuenta de nodos para usar su ID de nodo.

En el frente de pruebas, hemos mejorado varias herramientas de prueba y monitoreo para agregar soporte para nuevas APIs. También añadimos una sonda de inicio de prueba de aceptación para retrasar el inicio de las pruebas hasta que la red en su conjunto estaba sana. Esto evita que las pruebas de aceptación del nodo espejo informen de un falso positivo cuando una migración larga o un proceso de inicio en el consenso o los nodos espejo causa un retraso.

## [v0.66](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.66.0)

{% hint style="success" %}
**MAINNET ACTUALADO COMPLETADO: OCTOBER 24, 2022**
{% endhint %}

{% hint style="success" %}
**TESTNET ACTUALIZADO COMPLETADO: OCTOBER 17, 2022**
{% endhint %}

Continuando con nuestro objetivo de tener balances actualizados en todas partes, esta versión ahora muestra el saldo hbar actual en la API REST de balance. Si proporcionas un parámetro `timestamp`, se retornará al comportamiento anterior y usará el archivo de balance de 15 minutos. Esto nos permite seguir proporcionando una visión histórica de su equilibrio con el tiempo, mientras se muestra el último saldo si no se solicita un intervalo de tiempo específico. Se está trabajando activamente en el saldo de tokens en vivo para una próxima versión.

Otra característica importante de esta versión es el soporte para la eliminación de fallos de almacenamiento en la nube. El importador ahora se puede configurar con múltiples fuentes de descarga de S3 y se iterará sobre cada una hasta que uno sea exitoso. Esto hace que el nodo espejo sea más descentralizado y proporciona más resistencia frente al fallo de la nube. Las propiedades `hedera.mirror.importer.downloader` existentes usadas para configurar el `cloudProvider`, `accessKey`, `secretKey`, etc. continuará siendo soportado e insertado como la primera entrada en la lista de fuentes, pero se recomienda migrar la configuración al formato más reciente. También en el descargador hemos aumentado el tamaño del lote de descargas a 100 para mejorar la velocidad de sincronización histórica. Se añadió una propiedad `hedera.mirror.importer.downloader.sources.connectionTimeout` para evitar errores de conexión ocasionales.

```
hedera:
   espejo:
    importador:
      descargador:
        fuentes:
        - backoff: 30s
          connectionTimeout: 10s
          credenciales:
            accessKey: <redacted>
            secretKey: <redacted>
          maxConcurrency: 50
          projectId: myapp
          región: Off-2
          type: GCP
          uri: https://storage. oogleapis. om
        - credenciales:
            accessKey: <redacted>
            secretKey: <redacted>
          type: S3
```

[HIP-573](https://hips.hedera.com/hip/hip-573) da a los creadores de tokens la opción de eximir de una comisión personalizada a todos los coleccionistas de comisiones de su token. Esta versión del nodo espejo añade soporte para persistir en este nuevo campo `all_collectors_are_exempt` HAPI y exponerlo a través del `/api/v1/tokens/{id}` API REST.

Una característica importante de un nodo espejo es que cualquiera puede ejecutar uno y almacenar sólo los datos que le interesan. El nodo espejo ha soportado tal capacidad durante algunos años, pero la sintaxis de configuración era un poco complicada para ser correcta. Para resolver esta deficiencia, añadimos algo de [examples](https://github.com/hashgraph/hedera-mirror-node/blob/main/docs/configuration.md#transaction-and-entity-filtering) a la documentación de configuración para aclarar las cosas. Este filtrado de entidades se limitó históricamente a sólo crear, actualizar y eliminar operaciones en entidades. Ahora hemos expandido este filtrado para incluir IDs de cuentas de pagador y cuentas o fichas involucradas en transferencias.

### Rompiendo Cambios

Como parte del trabajo de tolerancia contra fallos de S3, hicimos una serie de cambios en las propiedades existentes para streamline cosas y sólo soportamos una propiedad para todos los tipos de secuencia. Las propiedades `hedera.mirror.importer.downloader.(balance|event|record).batchSize` fueron eliminadas a favor de un solo `hedera.mirror.importer.downloader.batchSize`. De la misma manera, las propiedades `hedera.mirror.importer.downloader.(balance|event|record).threads` fueron eliminadas a favor de `hedera.mirror.importer.downloader.threads`. Las propiedades `hedera.mirror.importer.downloader.(balance|event|record).prefix` fueron removidas a favor de la configuración hardcoded ya que nunca hubo necesidad de ajustar estas. Si está usando alguna de estas propiedades, por favor ajuste su configuración en consecuencia.

Si estás escribiendo archivos de flujo al disco después de descargar activando las propiedades `writeFiles` o `writeSignatures`, hay otro cambio de ruptura que hay que tener en cuenta. Como parte de nuestra migración fuera de los IDs de cuentas de nodos, cambiamos las rutas en el disco para usar el ID del nodo también. Si quieres evitar tener dos directorios para el mismo nodo por favor renombra tus directorios locales manualmente. Por ejemplo, cambia `${dataDir}/recordstreams/record0.0.3` a `${dataDir}/recordstreams/record0`.

## [v0.65](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.65.0)

{% hint style="success" %}
**MAINNET ACTUALADO COMPLETADO: OCTOBER 11, 2022**
{% endhint %}

{% hint style="success" %}
**TESTNET ACTUALIZADO COMPLETADO: OCTOBER 6, 2022**
{% endhint %}

El nodo espejo ahora calcula el consenso utilizando el peso de apuesta de todos los nodos como se describe en [HIP-406](https://hips.hedera.com/HIP/hip-406.html). Si la apuesta aún no está activada, cae de vuelta al comportamiento anterior de contar cada nodo como el peso `1/N` donde `N` es el número de nodos de consenso corriendo en la red. Las `/api/v1/accounts` y `/api/v1/accounts/{id}`APIs REST ahora exponen un campo `pending_reward` que proporciona una estimación de los pagos de recompensa en tinybars desde el último periodo de apuesta. La API REST `/api/v1/network/supply` actualizó su lista configurada de cuentas de suministro no liberadas para reflejar con precisión la separación de cuentas realizadas con fines de acopio por Hedera.

Esta versión implementa las acciones del contrato REST API detalladas en [HIP-513](https://hips.hedera.com/hip/hip-513). A continuación se muestra un ejemplo de una carga útil de acciones. Hemos añadido los campos `transaction_hash`, `transaction_index`, `block_hash` y `block_number` a los registros de contrato APIs REST. Este trabajo se realizó para optimizar el rendimiento del método JSON-RPC `eth_getLogs` usado por el repetidor. También para el repetidor, ahora apoyamos la búsqueda de resultados de contratos no Ethereum por su hash de transacción de 32 bytes.

`GET /api/v1/contracts/results/0.0.5001-1676540001-234390005/actions`

```
{
  "actions": [{
    "call_depth": 1,
    "call_type": "CALL",
    "call_operation_type": "CALL",
    "caller": "0. .5001",
    "caller_type": "CONTRACT",
    "from": "0x0000000000000000000000000000000000000000000000000000001389",
    "timestamp": "1676540001. 34390005",
    "gas": 1000,
    "gas_used": 900,
    "index": 1,
    "input": "0xabcd",
    "a": "0x70f2b2914a2a4b783faefb75f459a580616fcb5e",
    "destinatario": "0.0. 001",
    "recipient_type": "CONTRACT",
    "result_data": "0x1234",
    "result_data_type": "OUTPUT",
    "valor": 100
  }],
  "links": {
    "next": null
  }
}
```

Para soportar que los exploradores de nodo espejo puedan mostrar la transacción que creó una entidad, añadimos un campo `created_timestamp` a las cuentas REST API.

El módulo de Rosetta vio un gran número de mejoras en esta versión. Rosetta ahora soporta un memo de transacción ser devuelto en su respuesta de metadatas. Para mejorar el rendimiento de bloques grandes, Rosetta ahora limita el número de transacciones en su respuesta de bloques. El trabajo de conciliación del balance del importador fue deshabilitado en la imagen de Rosetta Docker ya que Rosetta realiza su propio proceso de reconciliación. Hubo otras varias correcciones en el equilibrio del proceso de reconciliación, prueba de gráficos fallando para PRs de repos bifurcados, y arreglando un bloque de búsqueda lenta por consulta hash en rosetta.

## [v0.64](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.64.0)

{% hint style="success" %}
**MAINNET ACTUALIZADO COMPLETADO: SEPTEMBER 26 2022**
{% endhint %}

{% hint style="success" %}
**TESTNET ACTUALIZADO COMPLETADO: SEPTEMBER 12, 2022**
{% endhint %}

En la última versión, empezamos a hacer un seguimiento del saldo actual de cada cuenta y contrato en lugar de confiar únicamente en el archivo de saldo escrito cada 15 minutos por nodos de consenso. En esta versión, ahora mostramos este saldo actualizado en `/api/v1/accounts` y `/api/v1/accounts/{id}` APIs REST en el campo `balance` existente. Los saldos de fichas y los balances REST API todavía muestran la información del saldo del archivo de saldo de 15 minutos. En una versión futura, veremos cambiarlas para rastrear el balance actual.

Como parte de [HIP-406](https://hips.hedera. om/cadera/hip-406), detalla un cálculo de recompensas pendiente que puede ser utilizado para estimar el pago de la recompensa entre su último evento de pago y el período de apuesta que acaba de finalizar. El nodo espejo ahora hace un cálculo similar diariamente y en una versión futura mostrará esta cantidad de recompensa pendiente en la API REST.

El trabajo de [reconciliation](https://github.com/hashgraph/hedera-mirror-node/tree/main/docs/importer#reconciliation-job) se ejecuta periódicamente y reconcilia los archivos de balance con las transferencias de criptomonedas que ocurrieron en los archivos de registro. Este trabajo nos permitió capturar un [issue](https://github.com/hashgraph/hedera-mirror-node/blob/main/docs/database.md#record-missing-for-fail\_invalid-nft-transfers) con transacciones faltantes para las criptomonedas `FAIL_INVALID` que se corrigieron en `v0.27.7`. Esta versión contiene las erratas para las transacciones faltantes que permiten que la conciliación proceda con éxito una vez más. También fue testigo de mejoras de rendimiento, incluyendo una propiedad `delay` para acelerar su velocidad y la persistencia en el estado del trabajo, por lo que no se reinicia desde el principio cada vez. Una nueva propiedad `remediationStrategy` proporciona un mecanismo para continuar después de no haber ayudado a depurar múltiples errores de reconciliación.

## [v0.63](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.63.0)

{% hint style="success" %}
**MAINNET ACTUALIZADO COMPLETADO: SEPTEMBER 7 2022**
{% endhint %}

{% hint style="success" %}
**TESTNET ACTUALIZADO COMPLETADO: SEPTEMBER 2, 2022**
{% endhint %}

Esta versión añade una característica muy solicitada: el nodo espejo ahora rastrea el saldo de la cuenta corriente. Anteriormente, el nodo espejo almacenaría información de balance cuya fuente era un archivo de balance que los nodos de consenso generan y cargan cada 15 minutos. Como resultado, la información sobre el saldo estuvo siempre atrasada hasta por 15 minutos para las cuentas activas. En esta versión pudimos averiguar una manera de rastrear esta información a escala con SQL. La próxima versión realmente expondrá esta información actualizada del saldo de la cuenta en `/api/v1/accounts` o `/api/v1/accounts/{id}`. En futuras versiones, buscará agregar balances en vivo a `/api/v1/balances` cuando no se proporciona un parámetro de marca de tiempo y rastrea los saldos de token actualizados.

El trabajo continúa en [HIP-513 Contract Traceability](https://hips.hedera.com/HIP/hip-513.html), con esta versión añadiendo algunos elementos importantes. Los nodos de consenso enviarán registros de migración que incluyan todos los bytecode de tiempo de ejecución del contrato inteligente y los valores de almacenamiento actuales. El nodo espejo ahora soporta recibir estos sidecars especiales de migración y actualizar su base de datos con los datos migrados. Esto allana el camino para que el nodo espejo tenga la información necesaria para ejecutar contratos inteligentes sin modificar el estado en una futura versión. También en esta versión ahora mostramos el código de inicio del contrato que se utilizó para crear sin éxito un contrato inteligente en un nuevo campo `failed_initcode` en el resultado del contrato REST API.

La API REST de suministro de red vio una actualización para ajustar las cuentas de suministro no liberadas utilizadas para calcular el suministro no liberado. Este cambio era necesario ya que Hedera ajusta las cuentas del tesoro para su uso con la apuesta.

## [v0.62](https://github.com/hashgraph/hedera-mirror-node/releases?page=2)

{% hint style="success" %}
**MAINNET ACTUALIZADO COMPLETADO: AUGUSTO 29, 2022**
{% endhint %}

{% hint style="success" %}
**TESTNET ACTUALIZADO COMPLETADO: AUGUSTO 22, 2022**
{% endhint %}

Mirror Node 0.62 vio [HIP-406](https://hips.hedera.com/HIP/hip-406.html) mejoras relacionadas a su API REST y soporte parcial para la trazabilidad del contrato HIP-513.

El `/api/v1/network/nodes` ahora usará la apuesta de la libreta de direcciones como un respaldo cuando no haya visto ninguna transacción `NodeStakeUpdate` en la red. Esta versión también contiene una nueva apuesta de red REST API `/api/v1/network/stake` para mostrar información de apuesta agregada común a todos los nodos:

```
{
  "max_staking_reward_rate_per_hbar": 17808,
  "node_reward_fee_fraction": 0. ,
  "stake_total": 35000000000000000000000,
  "staking_period": {
    "from": "1658774045.000000000",
    "to": "1658860445. 000000"
  },
  "staking_period_duration": 1440,
  "staking_periods_stored": 365,
  "staking_reward_fee_fraction": 1. ,
  "staking_reward_rate": 1000000000,
  "staking_start_Firsthold": 2500000000000000000
}
```

[HIP-513](https://hips.hedera.com/HIP/hip-513.html) Smart Contract Traceability añade soporte para que un sidecar opcional contenga información de trazabilidad del contrato. En esta versión, el nodo espejo soporta la descarga y persistente cambios en el estado del contrato, código de inicio del contrato, bytecode del tiempo de ejecución del contrato y acciones de contrato (trazas AKA). La API REST `/api/v1/contracts/{id}` ahora muestra el bytecode de tiempo de ejecución para los contratos recién creados. La siguiente versión soportará una migración paralela que rellenará los cambios de estado del contrato y el código de byte para todos los contratos existentes.

[HIP-435](https://hips.hedera.com/HIP/hip-435.html) Record Stream V6 requiere cambios en la API REST a prueba de estado para no romper cuando se habilitó V6. Con esta versión, la API fue actualizada para soportar archivos de registro en el nuevo formato v6.

La API de Rosetta vio algunas pequeñas correcciones y mejoras. Ahora utiliza el alias de red Hedera en todas partes del servidor Rosetta . También corrige el problema de que Rosetta no soportaba alias como la dirección de las transferencias criptográficas. Adicionalmente, el `sub_network_identifier` de Rosetta fue deshabilitado ya que no era necesario.

Este lanzamiento ha experimentado un número sorprendente de mejoras técnicas de la deuda. Tanto la API REST como la API de monitor se convirtieron de CommonJS a módulos ES6, permitiéndonos actualizar finalmente algunas de nuestras dependencias a la última versión. Las pruebas de especificaciones de REST API fueron organizadas en carpetas por punto final y cambiadas para usar un único contenedor de base de datos para toda la suite. En el importador, la información mutable del contrato fue fusionada en la tabla `entity`. El constructor `RecordItem` fue eliminado en todas partes para favorecer su método de construcción. Por último, añadimos pruebas de rendimiento analizador para poder generar grandes archivos de registro y estrés en la ingestión de registros de pruebas.

### Rompiendo Cambios

En una versión reciente, añadimos el campo `stake_total` a la API `/api/v1/network/nodes` para mostrar la estaca agregada de la red. Con la adición de la nueva API `/api/v1/network/stake`, ahora tenemos una API separada para devolver información agregada asociada a la red. Como tal, tomamos la decisión en esta versión de eliminar el campo `stake_total` de la respuesta de la API `/api/v1/network/nodes` para mantenerse consistente. Si estás usando este campo, por favor actualiza tu código para usar el campo `stake_total` en la API `/api/v1/network/stake`.

## [v0.61](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.61.0)

{% hint style="success" %}
**MAINNET ACTUALIZADO COMPLETADO: AUGUSTO 2, 2022**
{% endhint %}

{% hint style="success" %}
**TESTNET ACTUALIZADO COMPLETADO: 27 DE JULY, 2022**
{% endhint %}

Esta versión añade soporte inicial para la extensión de trazabilidad de contratos inteligentes [HIP-513](https://hips.hedera.com/hip/hip-513) La información de trazabilidad del contrato está ahora disponible dentro de un archivo sidecar opcional subido por separado a la nube. Los operadores de nodo Mirror pueden elegir si descargar esta información extra configurando las propiedades `hedera.mirror.importer.parser.record.sidecar` en el importador. Por defecto, los archivos sidecar no serán descargados. Habilitarlo permitirá que el nodo espejo persista en los datos del estado del contrato, acciones y bytecode. El soporte HIP-513 está incompleto en esta versión y la siguiente versión habilitará la persistencia completa de todos los tipos sidecar.

La API REST de transacciones ahora soporta múltiples filtros de consulta `transactiontype` para simplificar las búsquedas entre tipos.

La versión del nodo espejo en [GCP Marketplace](https://console.cloud.google.com/marketplace/details/mirror-node-public/hedera-mirror-node?project=mirrornode-non-prod-314918) fue actualizada a v0.60.0. Esto requirió la migración al nuevo GCP Producer Portal que debería ayudar a optimizar futuras actualizaciones de la versión.

Los componentes del monitor vieron una opción añadida para recuperar la libreta de direcciones en el arranque. Esto evita tener que configurar la lista de nodos para monitorear manualmente en entornos de preproducción y asegurarse de que la lista de nodos está actualizada. El monitor ahora utiliza modelos generados por OpenAPI para alimentar nuestro esquema OpenAPI. También hemos añadido una opción al monitor para establecer la propiedad máxima de longitud de memo para las transacciones publicadas.

## [v0.60](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.60.0)

{% hint style="success" %}
**MAINNET ACTUALIZADO COMPLETADO: 18 de JULY, 2022**
{% endhint %}

{% hint style="success" %}
**TESTNET ACTUALIZADO COMPLETADO: 14 de JULY, 2022**
{% endhint %}

Las dos grandes características de esta versión son el soporte para un período de retención de datos y la generación de números pseudorandina HIP-351.

En las redes públicas, los nodos espejos pueden generar decenas de gigabytes de datos cada día y esta tasa sólo se proyecta para aumentar. Los nodos Mirror ahora soportan un [periodo de retención opcional](https://github.com/hashgraph/hedera-mirror-node/blob/main/docs/database.md#retention) que está deshabilitado por defecto. Cuando está activado, el trabajo de retención purga datos históricos más allá de un período de tiempo configurado. Al reducir la cantidad total de datos en la base de datos, reducirá los costos operativos y mejorará el rendimiento de lectura/escritura. Sólo se eliminan los datos de inserción asociados con el saldo o los datos de transacción. Información acumulativa de entidades como cuentas, contratos, etc. no se eliminan.

[HIP-351](https://hips.hedera.com/hip/hip-351) añade una transacción pseudoraleatoria generadora de números. El nodo espejo ahora persiste este tipo de `PrngTransaction` incluyendo el número pseudorandino o los bytes que genera. Una versión futura expondrá esta información en la API REST.

Se han producido varias mejoras en esta versión. Los números de bloque ahora se migran para ser consistentes con otros nodos de réplica independientemente de su fecha de inicio configurada cuando recibe el primer archivo de registro v6 con el número de bloque canónico de los nodos de consenso. Añadimos la tarifa de recompensa al inicio del período de apuestas a la API REST de los nodos. Rosetta ahora muestra el tipo de operación de transferencia de criptomonedas como «FEE». Rosetta también muestra alias de cuenta como direcciones de cuenta en la respuesta de Rosetta DATA API.

## [v0.59](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.59.0)

{% hint style="success" %}
**TESTNET ACTUALIZADO COMPLETADO: JUNE 29, 2022**
{% endhint %}

La versión anterior vio soporte para la persistencia de [HIP-406](https://hips.hedera.com/HIP/hip-406.html) datos relacionados con la apuesta. La persistencia de Staking vio un mayor afinamiento en esta versión para adaptarse a los cambios en el protocolo `NodeStakeUpdateTransaction`. Los campos `decline_reward`, `staked_account_id`, `staked_node_id` fueron añadidos a `/api/v1/accounts` y `/api/v1/accounts/{id}` para mostrar propiedades de apuesta a nivel de cuenta. También añadimos campos relacionados con staking al existente `/api/v1/network/nodes` REST API (ver ejemplo más abajo).

`GET /api/v1/network/nodes`

```
{
  "nodes": [
    {
      "description": "address book 1",
      "file_id": "0.0.102",
      "max_stake": 50000,
      "memo": "0.0.4",
      "min_stake": 1000,
      "node_account_id": "0.0.4",
      "node_cert_hash": "0x01d...",
      "node_id": 1,
      "public_key": "0x4a...",
      "service_endpoints": [],
      "stake": 20000,
      "stake_not_rewarded": 19900,
      "stake_rewarded": 100,
      "stake_total": 100000,
      "staking_period": {
        "from": "1655164800.000000000",
        "to": "1655251200.000000000"
      },
      "timestamp": {
        "from": "16552512001.000000000",
        "to": null
      }
    }
  ],
  "links": {
    "next": null
  }
}
```

Soporte para el nuevo archivo de registro v6 definido en [HIP-435](https://hips.hedera.com/HIP/hip-435.html) fue añadido en esta versión. El archivo de registro v6 añade número de bloque así como soporte para los nuevos archivos de registro sidecar que llevan información detallada de trazabilidad del contrato que los nodos espejo pueden elegir opcionalmente descargar. Los archivos de registro y firma se encuentran ahora en un formato protobuf más mantenible que debería hacerlos más fáciles de mejorar con nuevos campos en el futuro sin necesidad de cambios violentos. Además, los archivos de registro v6 ahora se comprimirán lo que debería traducirse en costes reducidos de red y almacenamiento, mientras que potencialmente mejorará el rendimiento. Una vez que v6 esté habilitado en una versión futura de hedera-service, Se requerirá que los operadores de los nodos espejos se actualicen a una versión que soporte el nuevo formato v6 para evitar tiempos de inactividad.

Rosetta vio una serie de mejoras este lanzamiento para alinearlo mejor con la especificación de Rosetta. Una opción de segundos de duración válida configurable fue añadida a la API de construcción de transacciones para soportar la personalización de este valor. Soporte para un número de bloque consistente independientemente de `startDate` fue añadido en Rosetta ahora que Hedera tiene un bloque consistente como se define en [HIP-415](https://hips.hedera.com/HIP/hip-415.html). Un prefijo `0x` fue añadido a las direcciones de alias devueltas a través de la API para indicar que los datos están codificados en hex.

## [v0.58](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.58.0)

{% hint style="success" %}
**MAINNET ACTUALIZADO COMPLETADO: 22 de JUNIO de 2022**
{% endhint %}

{% hint style="success" %}
**TESTNET ACTUALIZADO COMPLETADO: 16 de JUNO, 2022**
{% endhint %}

Esta versión contiene soporte para HIP-406 Staking, HIP-410 Wrapped Ethereum Transaction y HIP-482 JSON-RPC Relay, así como una actualización a Java 17 que se ha retrasado mucho tiempo.

[HIP-406 Staking](https://hips.hedera.com/HIP/hip-406.html) está llegando y el nodo espejo se está preparando para ello. Esta versión hemos añadido soporte de persistencia para almacenar información de apuestas. En la próxima versión, expondremos esta información a través de nuestras APIs.

[HIP-410](https://hips.hedera.com/HIP/hip-410.html) y [HIP-482](https://hips.hedera.com/HIP/hip-482.html) están diseñados para mejorar la onramp para los desarrolladores existentes de Ethereum. Hacia ese fin, añadimos soporte de paginación a ambos de los registros de contratos REST APIs. Ahora puede páginas a través de los registros mediante una combinación de una marca de tiempo de consenso y parámetros del índice de registro. Los nuevos bloques APIs REST también vieron nuevos campos `gas_used` y `logs_bloom` añadidos que muestran los valores agregados para todas las transacciones dentro del bloque. Por último, hemos añadido una nueva programación de tarifas de red REST API. Actualmente, sólo expone el precio del gas para los tipos `ContractCall`, `ContractCreate`, y `EthereumTransaction` en tinybars.

`GET /api/v1/network/fees`

```
{
  "fees": [
    {
      "gas": 35561,
      "transaction_type": "ContractCall"
    },
    {
      "gas": 481934,
      "transaction_type": "ContractCreate"
    },
    {
      "gas": 35561,
      "transaction_type": "EthereumTransaction"
    }
  ],
  "timestamp": "163392000. 87357562"
}
```

Desde el inicio del nodo espejo en 2019, ha usado Java 11 para compilar y ejecutarse debido a que es la versión LTS más reciente. Después de que Java 17 LTS fue lanzado en septiembre de 2021 sabíamos que queríamos actualizar. Con esta versión, actualizamos a 17 después de validar que el nodo espejo todavía era funcional y performante. Si estás usando nuestras imágenes oficiales de contenedores, también están en Java 17, por lo que no habrá migración necesaria además de actualizar la imagen. Si está corriendo fuera de un contenedor, necesitará actualizar su JRE a 17 o reconstruir el jar desde el código fuente con `-Djava.version=11`.

## [v0.57](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.57.0)

{% hint style="success" %}
**MAINNET ACTUALIZADO COMPLETADO: MAY 25, 2022**
{% endhint %}

{% hint style="success" %}
**TESTNET ACTUALIZADO COMPLETADO: MAY 25, 2022**
{% endhint %}

Esta versión se centra en agregar los datos necesarios y las APIs necesarias para el relé JSON-RPC definido en [HIP-482](https://hips.hedera.com/hip/hip-482). El Relé JSON-RPC implementa el Ethereum JSON-RPC [standard](https://playground.open-rpc.org/?schemaUrl=https://raw.githubusercontent.com/ethereum/eth1.0-apis/assemblbled-spec/openrpc.json) y relés [HIP-410 transacciones Ethereum](https://hips.hedera.com/HIP/hip-410) a los nodos de consenso. Dado que el concepto de un bloque es crucial para las APIs JSON-RPC, esta versión también contiene la implementación de [HIP-415 Introducción de Blocks](https://hips.hedera.com/hip/hip-415).

El nodo espejo ahora expone el concepto de bloques como se introdujo en [HIP-415](https://hips.hedera.com/hip/hip-415#mirror-nodes). Ahora calculamos y almacenamos el gas acumulado utilizado y el filtro de bloqueo del registro del contrato para el bloque en su conjunto. Este HIP define tres nuevas APIs REST y esta versión incluye las tres: una lista bloquea la API REST, un get blocks REST API, y una lista de resultados de contrato REST API. La nueva API `/api/v1/blocks` soporta los parámetros usuales de consulta `limit` y `order` junto con `timestamp` y `block. umber` para apoyar a los operadores de igualdad y rango para marcas de tiempo de consenso y números de bloqueo, respectivamente. El `/api/v1/blocks/{hashOrNumber}` es idéntico a los bloques de la lista, pero sólo devuelve un solo bloque por su hash de bloque o su número de bloque. Finalmente, se añadió una API REST `/api/v1/contracts/results` que es idéntica a la existente `/api/v1/contracts/{id}/results` pero capaz de buscar a través de contratos.

`GET /api/v1/blocks`

```
{
  "blocks": [{
    "count": 4,
    "gas_limit": 150000000,
    "gas_used": 50000000,
    "hapi_version": "0.24. ",
    "hash": "0xa4ef824cd63a325586bfe1a66396424cd33499f895db2ce2292996e2fc5667a69d83a48f3883f2acab0edfb6bfeb23c4",
    "logs_bloom": "0x549358c4c2e573e02410ef7b5a5ffa5f36dd7398",
    "name": "2022-04-07T16_59_23.159846673Z. cd",
    "número": 1953336,
    "previous_hash": "0x4fbcefec4d07c60364ac42286d5dd989bc09c57acc7370b46fa8860de4b8721e63a5ed46addf1564e4f8cd7b956a5afa",
    "size": 8489,
    "timestamp": {
      "from": "1649350763. 59846673",
      "a": "1649350763. 82130000"
    }
  }],
  "links": {
    "next": null
  }
}
```

`GET /api/v1/blocks/{hashOrNumber}`

```
{
  "count": 4,
  "gas_limit": 1500000,
  "gas_used": 500000,
  "hapi_version": "0.24. ",
  "hash": "0xa4ef824cd63a325586bfe1a66396424cd33499f895db2ce2292996e2fc5667a69d83a48f3883f2acab0edfb6bfeb23c4",
  "logs_bloom": "0x549358c4c2e573e02410ef7b5a5ffa5f36dd7398",
  "name": "2022-04-07T16_59_23. 59846673Z. cd",
  "número": 1953336,
  "previous_hash": "0x4fbcefec4d07c60364ac42286d5dd989bc09c57acc7370b46fa8860de4b8721e63a5ed46addf1564e4f8cd7b956a5afa",
  "size": 8489,
  "timestamp": {
    "from": "1649350763. 59846673"
    "a": "1649350763.382130000"
  }
}
```

Se hicieron varios cambios en apoyo de [HIP-410 Transacciones Ethereum](https://hips.hedera.com/hip/hip-410#mirror-node). La API REST `/api/v1/accounts/{idOrAlias}` fue actualizada para aceptar una dirección EVM como un parámetro de ruta en lugar de un ID o alias. Se agregó un `ethereum_nonce` y `evm_address` a la respuesta de `/api/v1/accounts/{idOrAliasOrAddress}` y `/api/v1/accounts`. El `/api/v1/contracts/results/{transactionId}` existente fue actualizado para aceptar el hash de transacción Ethereum de 32 bytes como un parámetro de ruta además del ID de transacción que soporta ahora. Su respuesta, así como la similar `/api/v1/contracts/{idOrAddress}/results/{timestamp}`, fue actualizada para agregar los siguientes campos de transacción de Ethereum:

```
{
  "access_list": "0xabcd...",
  "block_gas_used": 564684,
  "chain_id": "0x0127",
  "gas_price": "0xabcd. .",
  "max_fee_per_gas": "0xabcd... ,
  "max_priority_fee_per_gas": "0xabcd...",
  "nonce": 1,
  "r": "0x84f0... ,
  "s": "0x5e03...",
  "transaction_index": 1,
  "type": 2,
  "v": 0
}
```

_Nota: Campos existentes omitidos por brevedad._

Se añadió un nuevo tipo de cambio REST API `/api/v1/network/exchangerate` que devuelve el [tipo de cambio](https://github.com/hashgraph/hedera-protobufs/blob/main/services/exchange\_rate.proto) archivo de red almacenado en `0.0.112`. Soporta un parámetro `timestamp` para recuperar el tipo de cambio en un momento determinado en el pasado.

```
{
  "current_rate": {
    "cent_equivalent": 596987
    "expiration_time": 1649689200
    "hbar_equivalent": 30000
  },
  "next_rate": {
    "cent_equivalent": 594920
    "expiration_time": 1649692800
    "hbar_equivalent": 30000
  },
  "timestamp": "1649689200. 23456789"
}
```

Una nueva API `/api/v1/contracts/results/logs` fue añadida con los mismos parámetros de consulta y respuesta que `/api/v1/contracts/{address}/results/logs` pero con la capacidad de buscar a través de contratos. No soporta la dirección como parámetro de consulta ya que se espera que los usuarios usen la API existente si necesitan registros para una dirección específica. Las mismas reglas en torno a no exceder `maxTimestampRange` todavía se aplican y permite que permanezca en rendimiento. La paginación es posible utilizando una combinación de los parámetros de fecha y de consulta.

Finalmente, esta versión completa nuestra implementación de [HIP-423 Long Term Scheduled Transactions](https://hips.hedera.com/hip/hip-423). Dos nuevos campos `wait_for_expiry` y `expiration_time` fueron añadidos a `/api/v1/schedules` y `/api/v1/schedules/{id}`

## [v0.56](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.56.0)

{% hint style="success" %}
**MAINNET ACTUALIZADO COMPLETADO: MAY 18, 2022**
{% endhint %}

{% hint style="success" %}
**TESTNET ACTUALIZADO COMPLETADO: MAY 17, 2022**
{% endhint %}

Este es un gran lanzamiento con soporte para seis diferentes Propuestas de Mejora de Hedera. La mayoría de estos cambios están en el lado más ingente de las cosas y las futuras versiones trabajarán para agregarlos a nuestras APIs.

[HIP-16](https://hips.hedera. om/hip/hip/hip-16) habilitará el vencimiento del contrato y traerá un nuevo campo de cuenta de renovación automática que contiene la cuenta responsable de cualquier cuota de renovación asociada al contrato. Las APIs REST de contrato ahora muestran este campo `auto_renew_account` junto con una nueva bandera `permanent_removal` que se establece en verdadero cuando el sistema expira un contrato.

Nos perdimos un requisito en nuestra implementación de [HIP-329](https://hips.hedera. om/hip/hip-329) CREATE2 para permitir a un cliente HAPI hacer una transferencia nativa de activos a un contrato conocido sólo por su dirección CREATE2. Hemos corregido este vacío lógico y ahora soportamos direcciones EVM en una `CryptoTransferTransfertion`.

[HIP-336](https://hips.hedera.com/hip/hip-336) vio más pulido para nuestro soporte de concesiones. Soporte para un parámetro opcional Spender ID en nuestro `/api/v1/accounts/{id}/nfts?spender.id={id}` API REST ahora está incluida.

[HIP-410](https://hips.hedera.com/hip/hip-410) trae consigo la habilidad de envolver una transacción nativa de Ethereum y enviarla a Hedera. El nodo espejo ahora puede analizar este nuevo `EthereumTransaction` junto con los resultados de su ejecución. Cualquier contrato creado o los resultados y registros generados por su ejecución se mostrará automáticamente en las API REST pertinentes. También se añadió soporte para especificar el initcode del contrato directamente en la creación del contrato. Para apoyar que las cuentas de posesión externa (EOA) puedan enviar transacciones de Ethereum, calculamos y almacenamos la dirección EVM de los alias de ECDSA secp256k1 de la cuenta. Por último, hemos añadido soporte para temas repetidos en los registros de contratos API REST para llevarlos más en línea con el método JSON-RPC `eth_getLogs`. Si proporciona varios parámetros diferentes del tema (p. ej. `topic0` y `topic1`) se considera una operación `AND` como antes, pero si pasa parámetros repetidos como `topic0=0x01&topic0=0x02&topic1=0x03` significa “(el tema 0 es 0x01 o 0x02) y (el tema 1 es 0x03)”.

[HIP-415](https://hips.hedera.com/hip/hip-415) está definiendo un bloque como el número de archivos de registros desde el inicio del stream (genesia AKA). Dado que sólo los nodos espejos tienen un historial completo, se utilizarán para proporcionar los nodos de consenso el número de bloque actual para actualizar su estado. Dado que los nodos de réplica parciales con una fecha de inicio efectiva después del arranque del flujo no tendrán todos los archivos de registro, pueden contener un valor inconsistente para su número de bloque en contraste con otros nodos espejo con todos los datos. Esta versión intenta corregirla con una migración para que estén en línea con los nodos espejo completos, por lo que todos tienen un valor consistente en el número de bloque.

[HIP-423](https://hips.hedera.com/hip/hip-423) Long term scheduled transactions enhances the existing scheduled transactions to allow time-based scheduling of transactions. Esta versión añade soporte ingente para los nuevos campos relacionados con el programa. La próxima versión expondrá estos campos a través de nuestro programa de API REST.

### Actualizando

Como parte de esta versión, hemos terminado de actualizar nuestras bases de datos PostgreSQL a la versión 14 y hemos actualizado todas las pruebas para usar esa versión también. Recomendamos que los operadores de los nodos espejos planifiquen su migración a PostgreSQL 14 a su conveniencia más temprana. Puede encontrar más detalles sobre el proceso de actualización en nuestra [guía de base de datos](https://github.com/hashgraph/hedera-mirror-node/blob/main/docs/database.md#upgrade).

Se estima que las migraciones de esta versión tardan hasta 2 horas en una base de datos mainnet. Sin embargo, la migración que ocupa la mayor parte de este tiempo sólo se ejecutará si necesita corregir los números de bloque. Esto sólo es necesario si el nodo réplica es un nodo réplica parcial y tiene una fecha de inicio efectiva que ocurre después del inicio de la secuencia.

## [v0.55](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.55.0)

{% hint style="success" %}
**MAINNET ACTUALIZADO COMPLETADO: MAY 4, 2022**
{% endhint %}

Esta versión se centra principalmente en finalizar nuestro soporte para [HIP-336](https://hips.hedera.com/hip/hip-336) Aprobación y API de Permisos para Tokens. Hemos añadido soporte para la nueva transacción `CryptoDeleteAllowance` y hemos eliminado el soporte para la transacción `CryptoAdjustAllowance` que no ha entrado en el diseño final. Los permisos de NFT se rastrean en la granularidad de transferencia NFT que permite información actualizada sobre las franquicias en el nodo espejo. La información actual del pender aparecerá tanto en `/api/v1/accounts/{id}/nfts` como en `/api/v1/tokens/{id}/nfts` API REST. También añadimos la bandera `is_approval` a las API que muestran las transferencias.

Con más desarrolladores usando computadoras que usan CPUs de la serie de Apple, se hace evidente el nodo espejo necesario para soportar arquitecturas basadas en ARM para acomodarlas. En esta versión añadimos imágenes Docker multiarquitectura usando [docker buildx](https://docs.docker.com/buildx/working-with-buildx/). Ahora enviamos las variantes `linux/amd64` y `linux/arm64` a nuestro [Google Container Registry](https://gcr.io/mirrornode). Si hay necesidad de sistemas operativos o arquitecturas adicionales en el futuro, puede ampliarse fácilmente.

También actualizamos nuestra aplicación [GCP Marketplace](https://console.cloud.google.com/marketplace/details/mirror-node-public/hedera-mirror-node) a la última versión.

## [v0.54](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.54.0)

{% hint style="success" %}
**MAINNET ACTUALIZADO COMPLETADO: APRIL 25, 2022**
{% endhint %}

{% hint style="success" %}
**TESTNET ACTUALIZADO COMPLETADO: APRIL 19, 2022**
{% endhint %}

Esta versión añade soporte para tres nuevas APIs REST y cuatro HIPs.

[HIP-21](https://hips.hedera. om/hip/hip-21) describe la necesidad de una consulta de información de red gratuita para permitir que SDKs y otros clientes puedan recuperar la lista actual de nodos. En v0.49.1, hemos añadido una nueva API gRPC `NetworkService.getNodes()`. En esta versión, estamos añadiendo una API de libreta de direcciones equivalente a nuestra API REST. Además de los parámetros estándar `order` y `limit`, soporta un parámetro de consulta `file.id` para filtrar por los dos libros de direcciones `0. .101` o `0.0.102` y un parámetro de consulta `node.id` para filtrar nodos y proporcionar paginación.

`GET /api/v1/network/nodes`

```
{
  "nodes": [
    {
      "description": "",
      "file_id": "0. .102",
      "memo": "0.0.3",
      "node_account_id": "0.0. ",
      "node_cert_hash": "0x334... ,
      "node_id": 0,
      "public_key": "0x308201. .",
      "service_endpoints": [
        {
          "ip_address_v4": "13. 24.142. 26",
          "puerto": 50211
        }
      ],
      "timestamp": {
        "from": "1636052707. 40848001",
        "a": nulo
      }
    }
  ],
  "links": {
    "next": null
  }
}
```

[HIP-336](https://hips.hedera.com/hip/hip-336) describe nuevas APIs de Hedera para aprobar y ejercitar permisos en una cuenta de delegado. Una dieta otorga a un Gasto el derecho de transferir una cantidad predeterminada de las barras o fichas del pagador a otra cuenta de la elección del gasto. En v0.50.0 añadimos soporte de base de datos para almacenar las nuevas transacciones de permisos. En esta versión, se crearon dos nuevas APIs REST para exponer la barra hbar y permisos de tokens fungibles. El soporte completo de permisos no estará disponible hasta una versión futura cuando los nodos de consenso lo activen en mainnet.

`GET /api/v1/accounts/{accountId}/allowances/crypto`

```
{
  "allowances": [
    {
      "amount_granted": 10,
      "owner": "0.0.1000",
      "spender": "0.0.8488",
      "timestamp": {
        "from": "1633466229.96874612",
        "to": "1633466568.31556926"
      }
    },
    {
      "amount_granted": 5,
      "owner": "0.0.1000",
      "spender": "0.0.9857",
      "timestamp": {
        "from": "1633466229.96874612",
        "to": null
      }
    }
  ],
  "links": {}
}
```

`GET /api/v1/accounts/{accountId}/allowances/tokens`

```
{
  "allowances": [
    {
      "amount_granted": 10,
      "owner": "0.0.1000",
      "spender": "0.0.8488",
      "token_id": "0.0.1032",
      "timestamp": {
        "from": "1633466229.96874612",
        "to": "1633466568.31556926"
      }
    },
    {
      "amount_granted": 5,
      "owner": "0.0.1000",
      "spender": "0.0.9857",
      "token_id": "0.0.1032",
      "timestamp": {
        "from": "1633466229.96874612",
        "to": null
      }
    }
  ],
  "links": {}
}
```

También en la API REST, añadimos soporte para [HIP-329](https://hips.hedera.com/HIP/hip-329.html) direcciones CREATE2. Ahora cualquier API que acepte un ID de contrato también aceptará la dirección EVM de 20 bytes como una cadena codificada en hex. Hemos mejorado el rendimiento de la API REST añadiendo cabeceras de control de caché para habilitar la caché distribuida a través de un CDN. El rendimiento de las transacciones de la lista por tipo REST API vio una solución para mejorar su rendimiento.

Como parte de [HIP-260](https://hips.hedera.com/hip/hip-260), los datos de llamada de precompilación de contrato ahora rellenan nuevos campos `amount`, `gas` y `function_parameter` dentro de `ContractFunctionResult` dentro del `TransactionRecord`. El nodo Mirror ahora almacena estos campos y los expone a través de sus resultados de contrato existentes APIs REST.

Se hicieron varias mejoras de seguridad en los nodos espejo contenedores. Todas las imágenes de Docker ahora se ejecutan como no-root independientemente de ejecutarse en Kubernetes o Docker Compose. Los gráficos del helm vieron cambios para adaptarse al estándar de seguridad de pod de Kubernetes [restricted](https://kubernetes.io/docs/concepts/security/pod-security-standards/#restringido). Esto asegura que el nodo espejo se ejecute con las mejores prácticas de seguridad y reduce su superficie de ataque total. El Estándar de Seguridad de Kubernetes Pod reemplaza a PodSecurityPolicy obsoleto y como tal hemos eliminado toda la configuración relacionada con este último.

### Actualizando

Esta versión tiene una migración larga que se espera que demore unos 75 minutos en completarse, dependiendo de su hardware y configuración de la base de datos. Como siempre, recomendamos un despliegue rojo/negro para eliminar los tiempos de inactividad durante las migraciones. Si estás usando la tabla `hedera-mirror-common`, por favor revisa las notas de actualización de la [kube-prometheus-stack](https://github.com/prometheichard community/helm-charts/tree/main/charts/kube-promethebian stack#upgrading-chart) para asegurar que el Operador de Prometheus pueda actualizarse correctamente.

## [v0.53](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.53.1)

{% hint style="success" %}
**MAINNET ACTUALIZADO COMPLETADO: APRIL 7, 2022**
{% endhint %}

{% hint style="success" %}
**TESTNET ACTUALADO COMPLETADO: MARCH 29, 2022**
{% endhint %}

Esta versión se centra principalmente en el área de integridad de los datos y asegurar que los datos en el nodo réplica son consistentes con los nodos de consenso. Para ello, añadimos una migración a la base de datos de erratas que sólo se ejecuta para mainnet y corrige tres [problemas conocidos](https://github.com/hashgraph/hedera-mirror-node/blob/main/docs/database.md#errata) que afectaron a los archivos de stream. El estado de los nodos de consenso nunca fue afectado, sólo la externalización de estos cambios en los archivos de flujo que consume el nodo espejo.

Para encontrar los datos incoherentes y garantizar que siga avanzando de forma coherente, hemos añadido un nuevo trabajo de conciliación de equilibrio. Este trabajo se ejecuta de noche y compara la información del balance con la información del archivo de registro para asegurarse de que están sincronizados. Hace tres cheques por cada archivo de saldo: verifica que los archivos de saldo sumen hasta 50 mil millones de barras, verifica que las transferencias agregadas de la barra coinciden con el archivo de saldo y verifica que las transferencias de fichas agregadas coinciden con el archivo de saldo. Se puede desactivar si no es necesario a través de `hedera.mirror.importer.reconciliation.enabled=false`.

También arreglamos un error que causaba que las transferencias con una cantidad cero aparecieran para las transacciones creadas con un saldo inicial cero. Esto se debió enteramente a que nuestro código insertó las transferencias extra, no por ningún problema en los archivos de la secuencia. También corregimos un error de API REST que causaba que el código de byte del contrato apareciera como doble codificado en hex.

Para la API de Rosetta, hemos añadido soporte de alias de cuenta a varios extremos. Y ahora soportamos resultados del contrato de análisis para funciones de contrato precompilado como las funciones HTS. Esta capacidad está deshabilitada por una bandera de características y será habilitada en una versión futura.

### Actualizando

Esta versión contiene un par de migraciones de base de datos de tamaño medio para corregir los datos erróneos de la base de datos. Se espera que tome unos 45 minutos contra una base de datos completa de la red principal.

## [v0.52](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.52.0)

{% hint style="success" %}
**MAINNET ACTUALADO COMPLETEDO: 18 MAR, 2022**
{% endhint %}

{% hint style="success" %}
**TESTNET ACTUALIZADO COMPLETADO: 15 MARCH, 2022**
{% endhint %}

[HIP-331](https://hips.hedera.com/HIP/hip-331. tml) es una propuesta de mejora aportada por la comunidad solicitando la adición de una nueva API REST para recuperar la lista de fichas no fungibles (NFTs). El nodo espejo tiene una API `/api/v1/tokens/{tokenId}/nfts` existente para recuperar todos los NFTs para un token dado, pero no cumplió con el requisito de mostrar NFTs a través de clases token. Esta versión añade la nueva API `/api/v1/accounts/{accountId}/nfts` para satisfacer esta necesidad. Es nuestra primera API con múltiples parámetros de consulta requeridos para la paginación y como tal tiene algunas restricciones sobre su uso. Consulte la OpenAPI [description](https://github.com/hashgraph/hedera-mirror-node/blob/21c6c464f77d555619af5955843e7d798fcd17b4/hedera-mirror-rest/api/v1/openapi.yml#L51) para obtener más detalles.

`GET /api/v1/accounts/0.0.1001/nfts?token.id=gte:1500&serialnumber=gte:2&order=asc&limit=2`

```
  {
    "nfts": [
      {
        "account_id": "0. .1001",
        "created_timestamp": "1234567890. 00000006",
        "deleted": false,
        "metadata": "bTI=",
        "modified_timestamp": "1234567890. 00000006",
        "serial_number": 2,
        "token_id": "0.0. 500"
      },
      {
        "account_id": "0.0. 001",
        "created_timestamp": "1234567890. 00000008",
        "eliminado": false,
        "metadata": "bTM=",
        "modified_timestamp": "1234567890. 00000008",
        "serial_number": 3,
        "token_id": "0. .1500"
      }
    ],
    "links": {
      "next": "/api/v1/accounts/0. .1001/nfts?order=asc&limit=2&token.id=gte:0.0.1500&serialnumber=gt:3"
    }
}
```

El nodo espejo ahora tiene pruebas de rendimiento escritas usando [k6](https://k6.io/) para todas nuestras APIs. Estas pruebas pueden ser usadas para verificar que el rendimiento no va de la liberación a la liberación. En el futuro, pretendemos integrarlos en una [suite de prueba de regresión nocturna](https://github.com/hashgraph/hedera-mirror-node/issues/3099) para mejorar nuestro enfoque actual de probar cada versión.

En esta versión se abordaron una serie de problemas de despliegue. Ahora inhabilitamos las elecciones de líder por defecto hasta que podamos solucionar los problemas con su implementación. De manera inteligente, hemos cambiado la estrategia de implementación de Kubernetes importador de una actualización continua para volver a crearla y evitar tener varios pods importadores ejecutándose simultáneamente. Se añadió una sonda de preparación migratoria al importador. Esto marcará los pods importadores como inlistos hasta que complete todas las migraciones de base de datos. Haciendo esto se asegurará de que Helm no finalice su lanzamiento y ejecute sus pruebas antes de que las migraciones se completen.

Seguimos afinando nuestra implementación de Rosetta con una serie de mejoras de rendimiento y correcciones de errores. El rendimiento de la secuencia de comandos de balance de Rosetta fue mejorado para reducir el tiempo inicial de inicio. El contenedor incrustado de PostgreSQL se actualizó a PostgreSQL 14. La imagen unificada Docker de Rosetta se actualizó para cumplir con los requisitos de persistencia de Rosetta.

## [v0.51](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.51.0)

{% hint style="success" %}
**MAINNET ACTUALIZADO COMPLETEDO: FEBRUARY 28, 2022**
{% endhint %}

{% hint style="success" %}
**TESTNET UPDATE COMPLETEDO: FEBRUARY 25, 2022**
{% endhint %}

Esta es una versión más pequeña centrada en mejoras de observabilidad y correcciones de API de Rosetta.

En el frente de observabilidad, hemos reducido el volumen de información de registro que la API REST produce a la mitad. También cambiamos la API REST para generar un registro de seguimiento consistente para todas las respuestas que incluyen IPs de cliente precisas, el tiempo transcurrido, y un código de estado. Hemos reducido el número de series temporales en aproximadamente un 50% que el nodo espejo produce para reducir los costes de control.

Para la API de Rosetta, hemos añadido una solución para el problema desaparecido de la transferencia de tokens que permite pasar la conciliación de datos de verificación. El tiempo global de conciliación se mejoró ajustando los parámetros de configuración y mejorando el rendimiento de seguimiento del balance NFT. Hemos trabajado alrededor de la carga lenta del saldo de la cuenta por Rosetta CLI cambiando a un enfoque dinámico de carga del saldo de la cuenta. También se han abordado otras cuestiones de Rosetta.

## [v0.50](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.50.0)

{% hint style="success" %}
**MAINNET ACTUALIZADO COMPLETEDO: FEBRUARY 23, 2022**
{% endhint %}

{% hint style="success" %}
**TESTNET UPDATE COMPLETEDO: FEBRUARY 15, 2022**
{% endhint %}

Esta versión añade soporte para tres nuevas propuestas de mejora: HIP-260 Smart Contract Traceability, HIP-329 CREATE2 Opcode y HIP-336 Allowance APIs. También actualiza la API REST para reflejar las últimas fases de HIP-226 y HIP-227 y actualiza la API Rosetta para HIP-31.

[HIP-260](https://hips.hedera.com/hip/hip-260) describe la necesidad de mejorar la trazabilidad de los contratos inteligentes proporcionando un rastro verificable de los cambios de estado del contrato en el registro de transacción. El nodo espejo ahora puede almacenar estos cambios de estado y exponerlos a través de los resultados del contrato REST API para mostrar los valores leídos y escritos para cada espacio. Esta información fue añadida a `/api/v1/contracts/results/{transactionId}` y `/api/v1/contracts/{id}/results/{timestamp}`. A continuación hay un ejemplo, con otros campos omitidos por brevedad:

```
{
  "state_changes": [{
      "address": "0x0000000000000000000000000000000000000000000000000000000000001f41",
      "contract_id": "0.0. 001",
      "slot": "0x00000000000000000000000000000000000000000000000000000000000000000002",
      "value_read": "0xaf846d22986843e3d25981b94ce181adc556b334ccfdd8225762d7f709841df0",
      "value_written": "0x0000000000000000000000000000000000000000000000002a8c408d0e29d623347c5"
    }, {
      "address": "0x00000000000000000000000000000000000000000000001f42",
      "contract_id": "0. .8002",
      "slot": "0xe1b094dec1b7d360498fa8130bf1944104b7b5d8a48f9ca88c3fc0f96c2d7225",
      "value_read": "0x00000000000000000000000000000000000000000000000000000001eafa3aaed1d27246",
      "value_written": null
   }]
}
```

[HIP-329](https://hips.hedera.com/hip/hip-329) añade soporte para [EIP-1014](https://eips.ethereum.org/EIPS/eip-1014) generó direcciones de contrato a través del código CREATE2. Como parte de esto, se añade un nuevo `evm_address` al registro de transacciones que estará presente para crear transacciones por contrato. Además, esta `evm_address` puede ser poblada en cualquier `ContractID` que aparezca en el cuerpo de la transacción. El nodo espejo se actualizó para poder asignar este `evm_address` a su número de contrato correspondiente y exponer esta propiedad en los contratos REST API. También almacenamos información completa del contrato para los contratos infantiles, ya que ahora aparecen como transacciones internas separadas en el flujo de registros, llenar un vacío de larga data en la falta de datos de contratos inteligentes.

[HIP-336](https://hips.hedera.com/hip/hip-336) la funcionalidad de asignación permite al propietario de una cuenta delegar otra cuenta para gastar hbars o tokens en su nombre. Esta función proporciona una implementación de [ERC20](https://ethereum.org/es/Developopers/docs/standards/tokens/erc-20), IERC20 y [ERC721](https://docs.openzeppelin.com/contracts/2.x/api/token/erc721) en la red Hedera. El nodo espejo se actualizó para soportar estos nuevos tipos de transacciones y almacenar los permisos absolutos o relativos, fungibles o no fungibles. En una versión posterior, expondremos esta información a través de una API REST como se detalla en el diseño [document](https://github.com/hashgraph/hedera-mirror-node/blob/v0.50.0/docs/design/allowances.md).

Se añadieron varios campos nuevos al contrato APIs REST como se describe en [HIP-226](https://hips.hedera.com/hip/hip-227) y [HIP-227](https://hips.hedera.com/hip/hip-226). Los campos `bloom`, `result`, y `status` fueron añadidos a la respuesta API de resultados del contrato. `result` y `status` muestran información similar, siendo la primera la respuesta de HAPI número mientras que la segunda devuelve `0x1` o `0x0` para mostrar si la transacción fue exitosa o no, como es común en las APIs de web3. También añadimos `bloom` a la respuesta de los registros de contrato API. Por último, ahora devolvemos una respuesta parcial para las llamadas contractuales sin ningún resultado.

El componente importador añadió una nueva propiedad `hedera.mirror.importer.parser.record.entity.persist.topics` para controlar la persistencia de mensajes temáticos. Esto puede establecerse en falso para operadores de nodo réplica si los datos del mensaje del tema no están siendo usados. Solo en red principal, estos datos actualmente ocupan hasta 2TB de almacenamiento.

El componente Monitor obtuvo soporte para la validación de nodos paralelos para mejorar el rendimiento del arranque. Ahora toda la validación se hace en un hilo de fondo, añadiendo y eliminando nodos según sea necesario, mientras que el hilo del editor continúa publicando transacciones sin interrupciones. Este re-trabajo también arregló problemas con la suspensión de suscripción durante la validación del nodo y tardó demasiado en validar un nodo hacia abajo.

Rosetta vio algunas mejoras importantes, como añadir soporte para [HIP-31](https://hips.hedera.com/hip/hip-31) esperaban decimales de token. La imagen unificada de Rosetta Docker vio la funcionalidad añadida para restaurar automáticamente la base de datos usando una instantánea de base de datos al inicio inicial.

### Rompiendo Cambios

Como parte de HIP-329 CREATE2, renombramos la `solidity_address` existente en el contrato REST API a `evm_address`. Este nuevo nombre refleja con precisión el nombre en el HIP y protobuf y evita vincular la dirección a la Solidez cuando Hedera soporta algo más que contratos de solidez.

## [v0.49](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.49.1)

{% hint style="success" %}
**MAINNET ACTUALIZADO COMPLETEDO: FEBRUARY 15, 2022**
{% endhint %}

{% hint style="success" %}
**TESTNET UPDATE COMPLETEDO: FEBRUARY 4, 2022**
{% endhint %}

Esta versión implementa tres Hedera Improvement Proposals (HIPs) y actualiza la base de datos de nodos espejo a la última versión.

[HIP-21](https://hips.hedera. om/hip/hip-21) describe la necesidad de una consulta de información de red gratuita para permitir que SDKs y otros clientes puedan recuperar la lista actual de nodos. Para satisfacer esta necesidad hemos añadido una nueva API GRPC de streaming `NetworkService.getNodes()` para obtener la lista de nodos actuales del archivo de red de la libreta de direcciones. Al convertirlo en una API de streaming evitamos que el cliente tenga que manejar la paginación por sí mismo. mientras que aún nos permite dividir la gran libreta de direcciones en trozos más pequeños. Dado que hay dos archivos de libreta de direcciones, proporcionamos una opción para elegir qué `FileID` regresar.

```
mensaje AddressBookQuery {
    .proto. ileID file_id = 1; // El ID del archivo de libreta de direcciones en la red. Puede ser 0. .101 o 0.0.102.
    límite int32 = 2; // El número máximo de direcciones de nodo a recibir antes de parar. Si no se establece o se establece a cero, devolverá todas las direcciones de los nodos en la base de datos.
}

servicio NetworkService {
    rpc getNodes (AddressBookQuery) devuelve (stream .proto.NodeAddress);
}
```

[HIP-171](https://hips.hedera.com/hip/hip-171) describes the need for returning the payer account in the topic message REST API response. Esta versión hace justo eso mientras que también añade en el mensaje de tema la información que estaba presente en la API de gRPC pero que falta en la API REST.

```
 {
+ "chunk_info": {
+ "initial_transaction_id": {
+ "account_id": "0.0. 000",
+ "nonce": 0,
+ "programado": falso,
+ "transaction_valid_start": "1234567890-000000006"
+ },
+ "número": 2,
+ "total": 5
+ },
   "consensus_timestamp": "1234567890. 00000001",
   "topic_id": "0.0.7",
   "message": "bWVzc2FnZQ==",
+ "payer_account_id": "0.0. 000",
   "running_hash": "cnVubmluZ19oYXNo",
   "running_hash_version": 2,
   "sequence_number": 1
}
```

Continuando con nuestro soporte para la creación de cuentas automáticas [HIP-32](https://hips.hedera.com/hip/hip-32) añadimos soporte de alias a nuestras cuentas REST API. Ahora cuando consultas `/v1/api/accounts/:id` el `id` puede ser una entidad Hedera en el formulario `0.0.x` o un alias codificado en hex. Los 'alias' de una cuenta ahora también aparecerán en todas las cuentas de salida API REST.

Se realizaron muchas pruebas para asegurar que PostgreSQL 14 funcionara correctamente con el nodo espejo y proporcionara un rendimiento tan bueno o mejor a las versiones anteriores. Ahora estamos migrando nuestros nodos de espejo Hedera administrados a PostgreSQL 14. Recomendamos que otros operadores de nodos espejo consideren actualizar a la última versión de la base de datos lo antes posible y han proporcionado la actualización [instructions](https://github. om/hashgraph/hedera-mirror-node/blob/main/docs/database.md#upgrade) para ayudar en ese proceso.

## [v0.48](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.48.0)

{% hint style="success" %}
**MAINNET ACTUALIZADO COMPLETADO: 26, 2022**
{% endhint %}

{% hint style="success" %}
**TESTNET ACTUALIZADO COMPLETADO: 18 de Enero, 2022**
{% endhint %}

[HIP-206](https://hips.hedera.com/hip/hip-206) agrega una marca de tiempo de consenso padre al registro de transacción para transacciones internas que ocurren como precompilaciones HTS invocadas desde un contrato inteligente. Para completar el soporte `nonce` en la última versión, añadimos `parent_consensus_timestamp` a `/api/v1/accounts/{id}` y `/api/v1/transactions`. Este campo ayuda a definir las relaciones padre/hijo entre las transacciones.

[HIP-226](https://hips.hedera.com/hip/hip-226) describe los resultados del contrato recientemente añadido REST API. Cada versión que hemos estado iterando y añadiendo más funcionalidad a la API hasta que coincida con la descripción en el HIP. Esta versión añade la lista de registros generados por una ejecución de contrato inteligente. Esta es una muestra de la nueva respuesta JSON:

```
{
    "amount": 30,
    ...
    "logs": [
      {
        "address": "0x000000000000000000000000000000000000000000001389",
        "contract_id": "0.0. 001",
        "data": "0x0123",
        "index": 0,
        "topics": [
          "0x97c1fc0a6ed5551bc831571325e9bdb365d06803100dc20648640ba24ce69750",
          "0x8c5be1e5ebec7d5bd14f71427d1e84f3dd0314c0f7b2291e5b200ac8c7c3b925",
          "0xddf252ad1be2c89b69c2b068fc378daa952ba7f163c4a11628f55a4df523b3ef",
          "0xe8d47b56e8cdfa95f871b19d4f50a857217c44a95502b0811a350fec1500dd67"
        ]
      },
      {
        "address": "0x00000000000000000000000000000000000000000000138a",
        "contract_id": "0. .5002",
        "data": "0x0123",
        "index": 1,
        "topics": [
          "0x97c1fc0a6ed5551bc831571325e9bdb365d06803100dc20648640ba24ce69750",
          "0x8c5be1e5ebec7d5bd14f71427d1e84f3dd0314c0f7b2291e5b200ac8c7c3b925"
        ]
      }
    ]
}
```

En la última versión, hemos añadido una nueva API JSON-RPC de Web3. Con esta versión, hemos añadido un gráfico de casco `hedera-mirror-web3` que puede ser usado para desplegarlo en Kubernetes. Se añadieron métricas adicionales al módulo Java y se creó un nuevo tablero Grafana para visualizarlos. La API Web3 también fue añadida al archivo docker-compose.

### Actualizando

Si está actualizando un despliegue existente de nuestro gráfico Helm, hay algunos cambios de ruptura a tener en cuenta. Primero, desplegamos el nuevo gráfico de la API Web3 por defecto y requiere una base de datos de usuario `mirror_web3` con permiso de lectura. Por favor, crea el nuevo usuario de la base de datos antes de actualizar o puedes desactivar el subgráfico `hedera-mirror-web3`.

Hemos actualizado los recursos de `PodDisruptionBudget` de `policy/v1beta1` a `policy/v1` y como resultado ahora requiere Kubernetes 1.21 o superior. Para la tabla `hedera-mirror`, si está usando el subgráfico opcional `postgresql`, debe escalar las réplicas PostgreSQL a una antes de iniciar una actualización para actualizar repmgr.

Si estás usando el gráfico `hedera-mirror-common`, hay una serie de cambios rompientes en los subgráficos comunitarios que utiliza. Antes de actualizar, tendrás que eliminar las implementaciones `prometheum-adapter` y `kube-state-metrics`. También necesitará reinstalar algunas definiciones de recursos personalizadas. Ejecutar los siguientes comandos para hacerlo:

```
kubectl delete deployment -l app.kubernetes.io/name=kube-state-metrics --cascade=orphan
kubectl delete deployment -l app=prometheus-adapter
kubectl apply --server-side -f https://raw.githubusercontent.com/prometheus-operator/prometheus-operator/v0.53.1/example/prometheus-operator-crd/monitoring.coreos.com_alertmanagerconfigs.yaml
kubectl apply --server-side -f https://raw.githubusercontent.com/prometheus-operator/prometheus-operator/v0.53.1/example/prometheus-operator-crd/monitoring.coreos.com_alertmanagers.yaml
kubectl apply --server-side -f https://raw.githubusercontent.com/prometheus-operator/prometheus-operator/v0.53.1/example/prometheus-operator-crd/monitoring.coreos.com_podmonitors.yaml
kubectl apply --server-side -f https://raw.githubusercontent.com/prometheus-operator/prometheus-operator/v0.53.1/example/prometheus-operator-crd/monitoring.coreos.com_probes.yaml
kubectl apply --server-side -f https://raw.githubusercontent.com/prometheus-operator/prometheus-operator/v0.53.1/example/prometheus-operator-crd/monitoring.coreos.com_prometheuses.yaml
kubectl apply --server-side -f https://raw.githubusercontent.com/prometheus-operator/prometheus-operator/v0.53.1/example/prometheus-operator-crd/monitoring.coreos.com_prometheusrules.yaml
kubectl apply --server-side -f https://raw.githubusercontent.com/prometheus-operator/prometheus-operator/v0.53.1/example/prometheus-operator-crd/monitoring.coreos.com_servicemonitors.yaml
kubectl apply --server-side -f https://raw.githubusercontent.com/prometheus-operator/prometheus-operator/v0.53.1/example/prometheus-operator-crd/monitoring.coreos.com_thanosrulers.yaml
```

## [v0.47](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.47.0)

{% hint style="success" %}
**MAINNET ACTUALIZADO COMPLETADO: 3 de enero de 2022**
{% endhint %}

{% hint style="success" %}
**TESTNET ACTUALIZADO COMPLETADO: DECEMBRE 30, 2021**
{% endhint %}

Esta versión continúa centrándose en Smart Contracts 2.0. El nodo espejo es útil para depurar una ejecución de contrato inteligente y nuestro enfoque ha sido proporcionar APIs para facilitar la vida de los desarrolladores. A tal fin, hemos añadido soporte para el ID de transacción en una sola ocasión, un nuevo registro de contrato REST API, y un nuevo componente de la API web3.

El nuevo módulo de API Web3 proporciona una implementación de APIs JSON-RPC existentes para la red Hedera. La API JSON-RPC es un estándar ampliamente utilizado para interactuar con los contadores distribuidos. El objetivo de proporcionar una implementación de Hedera de estas APIs es facilitar la migración de dApps existentes a Hedera y simplificar el desarrollador on-ramp. Actualmente, el módulo Web3 sólo proporciona una implementación parcial de la [API JSON-RPC de Ethereum](https://eth.wiki/json-rpc/API). Específicamente, sólo el método `eth_blockNumber` ha sido implementado en esta versión ya que nos enfocamos en poner el terreno en primer lugar.

Como parte de [HIP-32](https://hips.hedera.com/HIP/hip-32.html) y [HIP-206](https://hips.hedera.com/HIP/hip-206.html) se añadió un campo `nonce` al protobuf `TransactionID` para garantizar la unicidad para las transacciones generadas por la plataforma. Este campo `nonce` fue añadido a cualquier API REST que devuelva datos de transacción. Un parámetro de consulta `nonce` fue añadido a `/api/v1/transactions/:transactionId`, `/api/v1/transactions/:transactionId/stateapprov`, y `/api/v1/contracts/results/:transactionId` para poder distinguir entre una transacción enviada por el usuario y una transacción interna generada como resultado de esa transacción. Tenga en cuenta que `/api/v1/transactions/:transactionId` sin un parámetro `nonce` devolverá todas las transacciones independientemente de nonce mientras que las otras APIs predeterminarán `nonce` a `0`.

La nueva API REST `/api/v1/contracts/{id}/results/logs` proporciona una API de búsqueda para buscar registros entre las ejecuciones de contratos para un contrato en particular. Se apoya la búsqueda por la marca de tiempo y temas de consenso. Tenga en cuenta que por razones de rendimiento actualmente no soporta paginación y requiere que se proporcione un rango de fecha o fecha para buscar por temas.

## [v0.46](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.46.0)

{% hint style="success" %}
**MAINNET ACTUALIZADO COMPLETADO: DECEMBRE 20, 2021**
{% endhint %}

{% hint style="success" %}
**TESTNET ACTUALIZADO COMPLETADO: DECEMBRE 17, 2021**
{% endhint %}

0.46 es una versión empaquetada por características e incluye soporte para tres nuevos HIPs, tres nuevas API REST, un panel de control rediseñado, una nueva suite de pruebas BDD para la API de Rosetta, y un nuevo módulo. ¡Vamos a bucear!

[HIP-222](https://hips.hedera.com/hip/hip-222) añade soporte para claves ECDSA (secp256k1) a la red Hedera para ayudar a mejorar la experiencia del desarrollador de migrar un dApp a Hedera. El nodo espejo se actualizó para poder analizar y almacenar el nuevo tipo de clave junto con su nueva firma correspondiente. Dado que este tipo de clave también se considera una clave primitiva como ED25519 (p. ej. no es una lista de claves compleja o una clave de umbral), ahora puede buscar cuentas y tokens por su clave pública ECDSA de 66 caracteres. Como bono añadido, ahora también permitimos buscar por claves públicas complejas que son una lista de claves o una clave de umbral con exactamente una clave primitiva.

[HIP-32](https://hips.hedera.com/hip/hip-32) la creación de una cuenta automática permite a un nuevo usuario recibir a través de un CryptoTransfer _sin_ haber creado una cuenta en la red. El nodo espejo se actualizó para almacenar el alias del usuario y las transferencias de mapas que contienen un alias al ID de cuenta recién creado. En una próxima versión, agregaremos la posibilidad de consultar una cuenta por su alias.

[HIP-206](https://hips.hedera. om/hip/hip-206) integra el Servicio Hedera Token (HTS) en el Servicio Hedera Smart Contract Service (HSCS), lo que permite a los contratos transferir, mint, grabar, asociar y disociar tokens de forma programática. Mientras que el soporte para los nodos de consenso todavía se está trabajando, el nodo espejo ahora tiene soporte preliminar para transacciones precompiladas HTS. Esto añade un nuevo campo `nonce` al ID de transacción así como el concepto de relaciones parentes/hijo entre las transacciones. En una próxima versión, agregaremos soporte `nonce` y expondremos las relaciones padre/hijo a la API REST.

La API REST vio tres nuevas APIs REST creadas para soportar la nueva característica Smart Contracts 2.0. Después de ejecutar un contrato inteligente, los desarrolladores pueden usar la API `/api/v1/contracts/{id}/results` para buscar los resultados de ejecución de un contrato por rango de fecha o por cuenta de pagador (e. . `desde`). Una vez que se encuentre el resultado, la nueva API `/api/v1/contracts/{id}/results/{timestamp}` puede recuperar información detallada sobre la llamada inteligente al contrato. Si ya conoce el ID de la transacción, puede recuperar directamente los resultados del contrato inteligente mediante la nueva API `/api/v1/contracts/results/{transactionId}`. En una próxima versión, agregaremos soporte para registros, cambios de estado y más a esta API. Abajo hay un ejemplo de respuesta:

`/api/v1/contracts/resultados/{transactionId}`

```json
{
    "amount": 30,
    "block_hash": "0x6ceecd8bb224da491",
    "block_number": 17,
    "call_result": "0x0606",
    "contract_id": "0. .5001",
    "created_contract_ids": ["0.0. 001"],
    "error_message": "",
    "from": "0x000000000000000000000000000000000000000000000000001f41",
    "function_parameters": "0x0707",
    "gas_limit": 987654,
    "gas_used": 123,
    "hash": "0x3531396130303866616264653464",
    "timestamp": "167654. 00123456",
    "to": "0x0000000000000000000000000000000000000000001389"
}
```

Otros cambios variados incluyen ahora validar las pruebas de la API REST con la especificación OpenAPI. Esto debería ayudar a mantener el archivo de especificación mejor sincronizado con el código hasta que podamos integrar completamente la especificación. El panel de control basado en Node.js vio una actualización visual para facilitar su uso. La API de Rosetta fue testigo de una nueva serie de pruebas de comportamiento de criptografía y token. Como resultado de estas pruebas, varios errores fueron encontrados y abordados. Finalmente, refactorizamos algunas clases comunes en un módulo `hedera-mirror-common` para compartir código con un futuro módulo de API.

#### Migración de base de datos

Debido a las características mencionadas, hemos tenido que hacer cambios en el esquema de base de datos que pueden tardar algún tiempo en completarse. Hemos probado las migraciones con una base de datos mainnet con transacciones de 1.9B+ y se completó en unas cinco horas. Los operadores de nodos espejo pueden ver que la migración toma más o menos tiempo dependiendo del tamaño de sus datos, hardware de base de datos y parámetros de configuración de base de datos.

## [v0.45.0](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.45.0)

{% hint style="success" %}
**MAINNET ACTUALIZADO COMPLETADO: DECEMBRE 8, 2021**
{% endhint %}

{% hint style="success" %}
**TESTNET ACTUALIZADO COMPLETADO: DECEMBRE 6, 2021**
{% endhint %}

El nodo espejo ahora captura el historial completo de cambios que ocurren en cuentas y contratos a lo largo del tiempo. Antes de esto, el nodo espejo sólo mantendría el estado actual de una entidad Hedera. Con este cambio, todo creado, actualizar y eliminar transacciones que ocurren para una entidad causarán que se cree una instantánea para representar cómo apareció en cada uno de esos puntos en el tiempo. Esta información puede ser usada para consultar la API para una entidad en una marca de tiempo de consenso particular en el pasado.

Actualmente, esta opción de búsqueda histórica sólo está soportada en los contratos REST API. Por ejemplo, ahora puede buscar `/api/v1/contracts/0.0.1000?timestamp=lte:1609480800` para ver el estado del contrato `0.0.1000` el 1 de enero de 2021. En relación con los contratos, hemos añadido nuevas pruebas de aceptación para APIs relacionadas con contratos. El [documento de diseño](https://github.com/hashgraph/hedera-mirror-node/blob/main/docs/design/smart-contracts.md) fue actualizado para detallar las nuevas APIs que estamos proponiendo implementar. Esos cambios también se detallan en Hedera Improvement Proposals (HIPs) para [resultados del contrato](https://github.com/hashgraph/hedera-improvement-proposal/pull/226) y [registros de ejecución del contrato](https://github.com/hashgraph/hedera-improvement-proposal/pull/227) REST APIs. Por favor, eche un vistazo y háganos saber si tiene algún comentario.

En el frente de CitusDB, seguimos avanzando. Todas nuestras tablas de referencia ahora se eliminan a favor de la base de datos o las enums de la aplicación. Esto debería ayudar a mejorar el rendimiento y mejorar la migración de la base de datos. Hemos actualizado nuestro arness de prueba para usar la última versión de CitusDB que usa PostgreSQL 14. Finalmente, ahora creamos tablas distribuidas con identificadores de entidad utilizados como columnas de distribución para particionar y coubicarlas con otras tablas según corresponda.

La API de Rosetta también vio algunas mejoras, incluyendo la capacidad de crear cuentas en línea en `/construction/submit`. También se abordó una cuestión con la reconciliación del equilibrio simbólico.

## [v0.44.0](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.44.0)

Haciendo [progress](https://github.com/hashgraph/hedera-mirror-node/issues/2675) al transicionar nuestra base de datos a [CitusDB](https://citusdata.com/), esta versión añade un nuevo esquema `v2` con soporte inicial para CitusDB. Se añadieron pruebas automatizadas contra CitusDB a nuestro pipeline de CI para que se ejecute simultáneamente con el esquema basado en 'v1' PostgreSQL. El ID de la cuenta de pagador de transacción se añadió a las tablas relacionadas con la transferencia. Esto se utilizará como una columna de distribución para el particionado de bases de datos a través de una dimensión que no está basada en el tiempo. Esto permite al nodo réplica escalar lecturas y escrituras mientras más pagadores de transacciones usan el sistema.

El resto de la versión se centra principalmente en las mejoras de rendimiento. Ya no persistimos la información mínima de la entidad para cada entidad identificadora encontrada en una transacción. Esto fue un lastre en el rendimiento, pero también causó problemas con nuestros planes para rastrear el historial de entidades en una próxima versión. Algunas de nuestras tablas de referencia fueron eliminadas a favor de usar un enum de aplicación en su lugar para mapear valores protobuf a cadenas descriptivas.

En la API REST, la recuperación de cuentas por clave pública fue optimizada para mejorar su rendimiento. Si su aplicación no requiere información de saldo, puedes ver ganancias de rendimiento adicionales estableciendo el nuevo parámetro `balance` a `false` para llamadas API de cuenta. El código fue optimizado para reemplazar `Array.concat` con `Array.push` y para caché construcción de ID de entidad. El mayor cambio es probablemente el que potencialmente rompe el parámetro `limit`.

### Rompiendo Cambios

El número máximo de filas que el API REST puede devolver fue cambiado de 500 a 100. De sabias, el número predeterminado de filas que retorna la API REST si el parámetro `limit` no está especificado fue cambiado de 500 a 25. Si se envía una solicitud solicitando más de 100 no fallará. En cambio, utilizará de forma transparente los dos valores menores. Como resultado, esto no debería ser un cambio de ruptura a menos que la solicitud haga suposiciones sobre el número exacto de resultados que se están devolviendo. Podemos ajustar estos valores en el futuro por razones de rendimiento, por lo que es una buena práctica actualizar su aplicación para manejar límites y resultados arbitrarios.

## [v0.43.0](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.43.0)

{% hint style="success" %}
**MAINNET ACTUALIZADO COMPLETADO: AHORA 18, 2021**
{% endhint %}

{% hint style="success" %}
**TESTNET ACTUALIZADO COMPLETADO: NUNCA 12, 2021**
{% endhint %}

### Contratos inteligentes

Con el mayor foco de Hedera en [Contratos Inteligentes](https://hedera. om/blog/hedera-evm-smart-contracts-now-bring-high-speed-programmability-to-tokenization), nos tomamos el tiempo para revamp el soporte inteligente del nodo espejo y sentamos las bases para futuras mejoras. Como se detalla en el [documento de diseño](https://github.com/hashgraph/hedera-mirror-node/blob/main/docs/design/smart-contracts.md), los planes incluyen nuevas APIs REST específicas para el contrato y APIs compatibles con Ethereum en el futuro.

Para prepararse para eso, el esquema de base de datos y el importador fueron actualizados para normalizar y almacenar toda la información relacionada con el contrato, corregir errores de larga data como no almacenar bytecode de contrato y contratos de hijos. La tabla de contratos se dividió de la tabla de entidades genéricas y se comenzó a trabajar para hacer que todas las tablas de entidades mantengan un historial de todos los cambios. La API REST ahora soporta la búsqueda y recuperación de contratos específicos. A continuación se muestra un ejemplo de recuperación de un contrato:

`/api/v1/contracts/{id}`

```
{
  "admin_key": {
    "_type": "ProtobufEncoded",
    "key": "7b2233222c2233222c2233227d"
  },
  "auto_renew_period": 7776000,
  "bytecode": "0xc896c66db6d98784cc03807640f3dfd41ac3a48c",
  "contract_id": "0. .10001",
  "created_timestamp": "1633466229.96874612",
  "deleted": false,
  "expiration_timestamp": "1633466229. 6874612",
  "file_id": "0.0.1000",
  "memo": "Primer contrato",
  "obtainer_id": "0.0.101",
  "proxy_account_id": "0.0. 00",
  "solidity_address": "0x000000000000000000000000000000000000000003E9",
  "timestamp": {
    "from": "1633466229. 6874612",
    "a": "1633466568.31556926"
  }
}
```

### Arqueo de Datos

En los últimos meses, se ha puesto en marcha para analizar posibles reemplazos de `PostgreSQL` ya que la necesidad de manejar una cantidad cada vez mayor de datos ejerce presión sobre la base de datos de nodo réplica existente. Después de acordar los criterios de aceptación, prioridad fue colocada en una base de datos distribuida compatible con PostgreSQL que puede fragmentar los datos de nuestra serie temporal a través de muchos nodos para lecturas y escrituras a escala. Eso garantizaría el tiempo más rápido para comercializar y facilitar la transición para Hedera y otros que utilizan el software de nodo espejo de código abierto. Las cuatro bases de datos distribuidas que elegimos para nuestra prueba de concepto incluían [CitusDB](https://citusdata.com/), [CockroachDB](https://cockroachlabs.com/), [TimescaleDB](https://www.timescale.com/) y [YugabyteDB](https://www.yugabyte.com/).

Después de un análisis detallado de cada uno de ellos elegimos CitusDB para nuestra próxima base de datos debido a su excelente compatibilidad PostgreSQL (es una extensión PostgreSQL) y su soporte maduro para fragmentar datos de la serie temporal. Sus rutas distribuidas del motor de consultas y paraleliza DDL, DML, y otras operaciones sobre tablas distribuidas a través del clúster. Y su almacenamiento de columnas puede comprimir datos de hasta 8x, acelera los escaneos de tablas y soporta proyecciones rápidas. Esta versión contiene algo de trabajo fundamental para que nuestro esquema esté listo para particionar. Puede seguir nuestro [progress](https://github.com/hashgraph/hedera-mirror-node/issues/2675) mientras trabajamos para integrar CitusDB en nuestro código base durante los próximos meses. Planeamos mantener el apoyo a ambas bases de datos durante un período de tiempo después de que el trabajo se haya completado.

### Mejoras de rendimiento

Como suele ser el caso, nos tomó el tiempo para optimizar varias piezas del sistema para trabajar a escala. Nuestras transacciones API REST vieron algunas mejoras de rendimiento reescribiéndolas usando Expresiones de Tabla Común (CTE). Esto pagará dividendos futuros con CitusDB, ya que permite que las consultas se ejecuten en paralelo más fácilmente. Se abordó un problema con el tiempo de espera de `/api/v1/topics/{id}/messages` para algunos temas y las columnas de número de reinos y temas se combinaron para reducir el tamaño de la tabla y del índice. `/api/v1/tokens/{id}/balances` también vio algunas mejoras de rendimiento que disminuyeron su tiempo promedio de respuesta. Las opciones de configuración para una ingestión histórica más rápida fueron documentadas para que los operadores de los nodos espejo puedan obtener datos históricos más rápido.

## [v0.42.0](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.42.0)

{% hint style="success" %}
**MAINNET ACTUALADO COMPLETADO: OCTOBER 22, 2021**
{% endhint %}

{% hint style="success" %}
**TESTNET ACTUALIZADO COMPLETADO: OCTOBER 18, 2021**
{% endhint %}

Esta versión vio muchas mejoras en la funcionalidad de Hedera Token Service del servidor espejo. Soporte para [HIP-24](https://github.com/hashgraph/hedera-improvement-proposal/blob/master/HIP/hip-24.md) funcionalidad de pausa en el Servicio Hedera Token se completó. El importador puede ingerir el nuevo token pausa y reanudar los tipos de transacción y actualizar el token apropiadamente. Del mismo modo, el token REST API fue actualizado para mostrar la nueva pausa clave y estado de pausa.

A lo largo de estas líneas, la API REST de token también fue actualizada para mostrar el memo de token y una bandera para mostrar si se elimina. Ahora cuando una cuenta está disociada de un token, su suministro será actualizado correctamente para mostrar la transferencia negativa. Y si el token en ese disociado es de tipo NFT, todos los NFT propiedad de esa cuenta se marcarán correctamente como eliminados. También arreglamos problemas con algunos importes especiales de transferencias negativas que aparecen en las transacciones REST API.

Se ha añadido una nueva API REST para mostrar el suministro liberado. Tener el nodo de réplica de código abierto calcula y muestra el suministro de liberación evita cualquier punto de fallo con el sistema actual porque un usuario podría preguntar múltiples nodos de réplica y comparar sus respuestas (o ejecutar su propio nodo de réplica).

`GET /api/v1/network/supply`

```
{
	"timestamp": "123456870.854775807",
	"released_supply": 1800000000000000000000000,
	"total_supply": 5000000000000000000
}
```

Continuando con el tema de la mejora de la API de Rosetta, se añadió el soporte de NFT a las APIs de datos y construcción. Nos tomamos el tiempo de convertirlo a una biblioteca de configuración estándar y reorganizar la estructura de paquetes para ser más lisa y coherente. Y se añadieron contextos a cada capa para permitir la cancelación adecuada y el soporte de tiempo de espera.

## [v0.41.0](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.41.0)

{% hint style="success" %}
**MAINNET ACTUALIZADO COMPLETADO: OCTOBER 6, 2021**
{% endhint %}

{% hint style="success" %}
**TESTNET ACTUALIZADO COMPLETADO: SEPTEMBER 30, 2021**
{% endhint %}

Esta versión centra nuestros esfuerzos en mejorar nuestra [API de Rosetta](https://www.rosetta-api.org/) y hacerlo listo para su uso en producción. Se añadió un nuevo gráfico de casco de Rosetta para implementaciones de producción en Kubernetes. Las mejoras de observabilidad incluyen sondas de salud, métricas, registros de solicitudes, alertas y un panel de control de Grafana. Las pruebas de integración de Postman fueron añadidas para verificar la funcionalidad de post-implementación. Por último, se corrigieron algunos errores importantes, incluyendo direcciones IP faltantes y un fallo en la conciliación del balance de token.

El componente importador se optimizó para realizar transacciones en 15.000 TPS o superior. Este cambio incluyó mejoras para reducir el uso de CPU y memoria al tiempo que aumentaba la memoria asignada disponible para el proceso.

Otras mejoras incluyen la revalidación periódica de nodos principales en el monitor y la adición del soporte TLS para la conexión de base de datos de la API REST.

## [v0.40.0](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.40.0)

{% hint style="success" %}
**MAINNET ACTUALIZADO COMPLETADO: SEPTEMBER 27, 2021**
{% endhint %}

{% hint style="success" %}
**TESTNET ACTUALIZADO COMPLETADO: SEPTEMBER 16, 2021**
{% endhint %}

Esta versión añade soporte para [HIP-23](https://github.com/hashgraph/hedera-improvement-proposal/blob/master/HIP/hip-23.md) Asociación de Token Automático. Esta función permite que los usuarios opten por recibir fichas fungibles o no fungibles automáticamente como parte de una transferencia sin tener que estar previamente asociados con el token. El nodo espejo ahora almacena estas asociaciones creadas implícitamente y las devuelve a través de su API REST. Adicionalmente, mostramos el `max_automatic_token_associations` en las cuentas REST API.

Además de actualizarlo para HIP-23, la API REST vio bastantes otras correcciones y mejoras. La API de cuentas ahora muestra su memo y el campo `receiverSigRequired`. Los paquetes de la API REST fueron renombrados para usar el alcance del paquete NPM `@hashgraph`. Esto no debería ser un cambio de ruptura, ya que actualmente no publicamos esos paquetes en NPM. Se arreglaron varias APIs para asegurar que las listas fueran devueltas en un orden determinista de clasificación. Además, la especificación OpenAPI se ha arreglado para que refleje con precisión la API actual y pueda utilizarse para generar código de cliente. Por último, la API de los programas tuvo algunas mejoras de rendimiento.

En el lado de la vigilancia, hemos mejorado nuestros tableros de control de Grafana para hacerlos compatibles con Grafana Cloud mediante la adición de desplegables de bases de datos y cluster.

## [v0.39.0](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.39.0)

{% hint style="success" %}
**MAINNET ACTUALIZADO COMPLETEDO: AUGUSTO 31, 2021**
{% endhint %}

{% hint style="success" %}
**TESTNET ACTUALIZADO COMPLETEDO: AUGUST 30, 2021**
{% endhint %}

Esta versión proporciona compatibilidad con Hedera Services 0.17 incluyendo soporte para Tokens No Fungables (NFTs) y su mejora a tarifas personalizadas. Para estos últimos, un creador de NFT puede establecer una cuota de canje de canje cuando se intercambia un valor fungible por una de sus creaciones y el nodo espejo ha sido actualizado para rastrear este nuevo tipo de tarifas personalizadas. También se añadió apoyo para cuentas de pagadores efectivas en comisiones personalizadas evaluadas y para almacenar la red de transferencias en comisiones fraccionales.

Se eliminó el módulo generador de datos mayoritariamente no utilizado, resultando en un gran aumento de la cobertura de código. La cobertura ha aumentado de 84% a 92%.

Una buena cantidad de errores fueron arreglados incluyendo un fallo en el arranque de la API REST si la base de datos estaba caída, monitorear que tarda demasiado en iniciarse, correcciones de OpenAPI y más.

## [v0.38.1](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.38.1)

{% hint style="success" %}
**MAINNET ACTUALIZADO COMPLETEDO: AUGUSTO 17, 2021**
{% endhint %}

{% hint style="success" %}
**TESTNET ACTUALIZADO COMPLETEDO: AUGUSTO 17, 2021**
{% endhint %}

Esta versión es una pequeña versión de corrección de errores que contiene algunas correcciones importantes para nuestro componente de monitorización de nodos espejos. Hemos añadido un nuevo chequeo de salud de cluster al monitor que tiene en cuenta el estado de publicación. El balanceador de carga utiliza este chequeo de estado para determinar a qué cluster hay que enrutar el tráfico. El antiguo punto final del chequeo de estado no tuvo en cuenta si la publicación de la transacción estaba activa o tuvo éxito y por lo tanto no enrutaría el tráfico al nodo espejo público durante las actualizaciones de los nodos principales.

Además del nuevo chequeo de salud, el monitor tenía arreglos para su cálculo de velocidad en TPS bajos, no muestreo cuando está inactivo, validación de nodos, y las alertas que genera. La configuración de red de red principal del monitor ahora apunta al nodo espejo público y hemos añadido el nuevo nodo previewnet a la configuración de red previewnet.

También hubo varias otras correcciones para limpiar código y corregir pruebas. Hemos hecho un esfuerzo para reducir nuestro código huele como se ve en [SonarCloud](https://sonarcloud.io/dashboard?id=hedera-mirror-node).

## [v0.38.0](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.38.0)

{% hint style="success" %}
**MAINNET ACTUALIZADO COMPLETED: AUGUSTO 16, 2021**
{% endhint %}

{% hint style="success" %}
**TESTNET ACTUALIZADO COMPLETADO: AUGUSTO 13, 2021**
{% endhint %}

Esta versión completa NFT y soporte de tarifas personalizadas añadiendo cobertura de prueba adicional y corrigiendo cualquier error restante. Específicamente, el soporte NFT fue añadido a nuestra herramienta de monitoreo y a nuestras pruebas de aceptación. Las comisiones personalizadas también se añadieron a las pruebas de aceptación y tuvieron algunas correcciones de errores.

Mainnet public vio algunas mejoras de monitoreo, incluyendo la adición de soporte HTTPS a nuestro panel de control externo y la adición de una plataforma no activa que inhibe todas las demás alertas.

Hubo varias correcciones de errores en esta versión. El chequeo de estado del archivo stream que fue desactivado en la última versión debido a un error fue arreglado y rehabilitado. El flujo de actualización de la libreta de direcciones también vio un par de correcciones importantes.

#### Rompiendo Cambios

El ID de la cuenta de pagador en la respuesta de la API REST evaluada por transacción fue eliminado. Se trata de un cambio en los servicios 0. 6 por lo que ahora se cobran tasas de aduanas de la cuenta que envía los tokens de activación, no necesariamente el pagador de la transacción.

## [v0.37.2](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.37.2)

{% hint style="success" %}
**MAINNET ACTUALIZADO COMPLETADO: AUGUSTO 4, 2021**
{% endhint %}

{% hint style="success" %}
**TESTNET ACTUALIZADO COMPLETADO: AUGUSTO 5, 2021**
{% endhint %}

Una pequeña versión de corrección de errores que resuelve algunos problemas con nuestro soporte [HIP-18](https://github.com/hashgraph/hedera-improvement-proposal/blob/master/HIP/hip-18.md) .

## [v0.37.1](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.37.0)

{% hint style="success" %}
**MAINNET ACTUALIZADO COMPLETADO: JULY 29, 2021**
{% endhint %}

{% hint style="success" %}
**TESTNET ACTUALIZADO COMPLETADO: 15 de JULY, 2021**
{% endhint %}

Esta versión amplía nuestro soporte para [tokens](https://github.com/hashgraph/hedera-improvement-proposal/blob/master/HIP/hip-17.md) (NFTs) con nuevas APIs REST específicas de NFT. Una nueva API fue añadida para devolver una lista de NFTs para un identificador de token en particular. También añadimos una nueva API para devolver un solo NFT por su token ID y número de serie. Por último, hemos añadido una API para ver el historial de transacciones de un NFT en particular. En un esfuerzo por tener un código de API REST más manejable, ahora adoptamos un enfoque más orientado a objetos mediante la utilización de modelos, modelos de vista y servicios. A continuación se muestra un ejemplo de las tres nuevas APIs:

`GET /api/v1/tokens/0.0.1500/nfts`

```
{
  "nfts": [{
    "account_id": "0.0.1002",
    "created_timestamp": "1234567890. 00000010",
    "deleted": false,
    "metadata": "ahf=",
    "modified_timestamp": "1234567890. 00000010",
    "serial_number": 2,
    "token_id": "0. .1500"
  },{
    "account_id": "0. .1001",
    "created_timestamp": "1234567890. 00000009",
    "deleted": false,
    "metadata": "bTM=",
    "modified_timestamp": "1234567890. 00000008",
    "serial_number": 1,
    "token_id": "0.0. 500"
  }],
  "links": {
    "siguiente": null
  }
}
```

`GET /api/v1/tokens/0.0.1500/nfts/1`

```
{
  "account_id": "0.0.1001",
  "created_timestamp": "1234567890.000000008",
  "deleted": false,
  "metadata": "bTM=",
  "modified_timestamp": "1234567890.00000000009",
  "serial_number": 1,
  "token_id": "0.0.1500"
}
```

`GET /api/v1/tokens/0.0.1500/nfts/1/transactions`

```
{
  "transactions": [{
    "consensus_timestamp": "1234567890. 00000009",
    "transaction_id": "0.0.8-1234567890-000000009",
    "receiver_account_id": "0. .1001",
    "sender_account_id": "0.0. 001",
    "type": "CRYPTOTRANSFER"
  }, {
    "consensus_timestamp": "1234567890. 00000008",
    "transaction_id": "0.0.8-1234567890-000000008",
    "receiver_account_id": "0.0. 001",
    "sender_account_id": null,
    "type": "TOKENMINT"
  }],
  "links": {
    "next": null
  }
}
```

## [v0.36.0](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.36.0)

{% hint style="success" %}
**MAINNET ACTUALIZADO COMPLETADO: 19 de JULY 2021**
{% endhint %}

Estamos felices de [announce](https://hedera.com/blog/now-available-public-mainnet-mirror-node-managed-by-hedera) la disponibilidad de un Nodo de Mirror de acceso público, gratuito y gratuito, operado por el equipo de Hedera. Como parte de esto, dedicamos un gran esfuerzo a afinar nuestro despliegue de Kubernetes. migramos a [Flux 2](https://fluxcd. o/), una herramienta de despliegue basada en GitOps que nos permite especificar de forma declarativa el estado esperado del nodo Mirror en git y gestionar nuestros desplazamientos. Puedes navegar por nuestra [rama de deploy](https://github.com/hashgraph/hedera-mirror-node/tree/deploy) y ver la configuración exacta y las versiones desplegadas a varios clústeres y entornos. El gráfico del casco se actualizó para añadir `PodDisruptionBudgets`, ajustar las reglas de alerta y otras mejoras para facilitar la automatización del despliegue.

Esta versión es la primera versión del nodo Mirror con soporte preliminar para tokens no fungibles (NFTs). El soporte NFT está siendo añadido a los nodos Hedera como se describe en [HIP 17](https://github.com/hashgraph/hedera-improvement-proposal/blob/master/HIP/hip-17.md). Pasamos tiempo [designing](https://github.com/hashgraph/hedera-mirror-node/blob/v0.36.0/docs/design/nft.md) cómo se verá ese soporte NFT para el nodo Mirror. Se hicieron modificaciones al esquema para añadir nuevas tablas y campos y el importador se actualizó a las transacciones NFT ingestas. Las API REST existentes se actualizaron para añadir campos relacionados con NFT a la respuesta. Esto incluye añadir un campo `type` a las API relacionadas con token para indicar fungibilidad y un`nft_transfers` a `/api/v1/transactions/{id}`:

```
{
  "transactions": [{
      "consensus_timestamp": "1234567890. 00000001",
      "name": "CRYPTOTRANSFER",
      "nft_transfers": [
        {
          "receiver_account_id": "0. .121",
          "sender_account_id": "0.0. 22",
          "serial_number": 104,
          "token_id": "0. .14873"
        }
      ]
  }]
}
```

Una cosa a tener en cuenta es que no añadimos transferencias NFT al final de las transacciones de la lista en un esfuerzo por reducir el tamaño y mejorar el rendimiento de ese extremo. En la próxima versión, añadiremos nuevas APIs REST específicas de NFT.

Continuando con el tema de la última versión, hicimos cambios adicionales a la API de Rosetta para ponerla a la par con el resto de los componentes. Rosetta ahora incluye soporte para HTS a través de API de datos y construcción.

El importador se centró mucho en mejorar el desempeño y la resistencia. Ahora está muy disponible (HA) cuando se ejecuta dentro de Kubernetes. Esto permite que más de una instancia se ejecute a la vez y falle a la instancia secundaria cuando el primario se vuelve poco saludable. Un especial Kubernetes ConfigMap llamado `líderes` se utiliza para elegir atómicamente al líder.

Estamos mejorando drásticamente nuestro tiempo de ingestión para la creación de entidades. Anteriormente se trataba de hallazgos de base de datos seguidos de actualizaciones. Dado que las inserciones son siempre más rápidas que las actualizaciones y encontradas, hemos optimizado esto para insertar las actualizaciones en una tabla temporal y al final las actualizamos en la tabla final. Un archivo de registro con 6.000 nuevas entidades pasó de 21 segundos a 600 ms, lo que le mejoró 35 veces. El procesamiento de archivos de balance fue optimizado para reducir considerablemente la memoria al mantener un archivo en memoria a la vez.

### Rompiendo Cambios

En honor de [Juneteenth](https://es.wikipedia.org/wiki/Juneteenth) y como parte del movimiento general de la industria, renombramos nuestra rama `master` a `main`. Si tiene un clon o bifurcación del repositorio Git de la réplica de nodos, necesitarás tomar los siguientes pasos para actualizarlo para usar `main`:

```
git branch -m master main
git fetch origin
git branch -u origin/main
git remote set-head origin -a
```

Como parte de nuestra optimización para reducir el uso de memoria, ahora procesamos algunas cosas antes del ciclo de vida. Debido a esto hemos tenido que renombrar algunas propiedades para reflejar este cambio. También cambiamos la estructura de disco si estás usando las propiedades `keepFiles` (ahora renombrado a `writeFiles`) para escribir los archivos de flujo al disco después de la descarga. Ya no se archiva en carpetas por día. En su lugar, la estructura de carpetas coincidirá exactamente con la estructura del cubeta. Esto abre la posibilidad de que un nodo espejo descargue y réplica el mismo cubo usando una API compatible con S3 como [MinIO](https://min.io/). Debajo hay un resumen de las propiedades renombradas:

- Renombrado `hedera.mirror.importer.downloader.balance.keepSignatures` a `hedera.mirror.importer.downloader.balance.writeSignatures`
- Cambió el nombre de `hedera.mirror.importer.parser.balance.keepFiles` a `hedera.mirror.importer.downloader.balance.writeFiles`
- Renombrado `hedera.mirror.importer.parser.balance.persistBytes` a `hedera.mirror.importer.downloader.balance.persistBytes`
- Renombrado `hedera.mirror.importer.downloader.event.keepSignatures` a `hedera.mirror.importer.downloader.event.writeSignatures`
- Cambió el nombre de `hedera.mirror.importer.parser.event.keepFiles` a `hedera.mirror.importer.downloader.event.writeFiles`
- Renombrado `hedera.mirror.importer.parser.event.persistBytes` a `hedera.mirror.importer.downloader.event.persistBytes`
- Renombrado `hedera.mirror.importer.downloader.record.keepSignatures` a `hedera.mirror.importer.downloader.record.writeSignatures`
- Cambió el nombre de `hedera.mirror.importer.parser.record.keepFiles` a `hedera.mirror.importer.downloader.record.writeFiles`
- Renombrado `hedera.mirror.importer.parser.record.persistBytes` a `hedera.mirror.importer.downloader.record.persistBytes`

## [v0.35.0](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.35.0)

{% hint style="success" %}
**MAINNET ACTUALIZADO COMPLETADO: 8 JULY 2021**
{% endhint %}

{% hint style="success" %}
**TESTNET ACTUALIZADO COMPLETADO: 21 de JUNIO 2021**
{% endhint %}

La mayoría de los cambios en esta versión fueron mejoras operativas en nuestro despliegue de Kubernetes. Estos cambios fueron necesarios a medida que empezamos a convertir más entornos de máquinas virtuales a basados en Kubernetes. Hemos añadido nuestras pruebas de aceptación al gráfico del casco para que pueda activarse automáticamente durante las actualizaciones y verificar que el despliegue fue exitoso. En el importador, añadimos un nuevo chequeo de salud a las sondas que verifica que los archivos de flujo se están analizando con éxito. Y arreglamos el importador para que las sondas se iniciaran antes de las migraciones de bases de datos de larga duración, permitiéndonos finalmente habilitar su sonda de vida. Hubo muchas correcciones más pequeñas a los gráficos, así que por favor vea los PRs enlazados para más detalles.

El monitor vio una nueva API REST que lista las suscripciones activas. Esto se utiliza en nuestro clúster para determinar la salud general del clúster y el tráfico de rutas a través de nuestros balanceadores de carga. Hemos añadido una especificación OpenAPI y una interfaz de usuario Swagger para esta API también.

Agradecimientos especiales a [@si618](https://github.com/si618) por arreglar la compilación en Windows y añadir un workflow de GitHub para asegurarse de que permanece corregido.

### Cambios de ruptura

El máximo y límite predeterminado de la API REST fue bajado de 1000 a 500. Si envía explícitamente un número de más de 500, su solicitud fallará. Por favor, actualice el código de su cliente apropiadamente.

## [v0.34.0](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.34.0)

{% hint style="success" %}
**MAINNET ACTUALIZADO COMPLETADO: 16 de JUNO, 2021**
{% endhint %}

{% hint style="success" %}
**TESTNET ACTUALIZADO COMPLETADO: 11 de JUNIO de 2021**
{% endhint %}

En Hedera Mirror Node v0.34.0, comenzamos a trabajar en [designing](https://github.com/hashgraph/hedera-mirror-node/blob/master/docs/design/nft.md) soporte para [NFTs](https://github.com/hashgraph/hedera-improvement-proposal/blob/master/HIP/hip-17.md) que llegará en un futuro lanzamiento de Hedera Services.

Por defecto, el nodo réplica validará que al menos un tercio de todos los nodos de la libreta de direcciones hayan firmado un archivo de flujo antes de importarlo a su base de datos. Esto asegura que los nodos principales hayan alcanzado un consenso de dos tercios sobre las transacciones en el archivo. Por razones de rendimiento o verificación, puede que desee disminuir o aumentar este porcentaje por defecto. Para soportar este caso de uso, hemos añadido un `hedera.mirror.importer.downloader. la propiedad onsensusRatio` que controla la proporción de nodos verificados (nodos usados para llegar al consenso sobre el hash del archivo de firmas) al número total de nodos disponibles.

Nos tomamos el tiempo para emprender importantes mejoras de dependencias para la API de Rosetta. Esto incluía actualizaciones importantes para los SDK de Hedera y Rosetta que requerían una gran cantidad de refactorización. Se abordaron varios errores en Rosetta así como mejoras en el flujo de trabajo CI de Rosetta. Estos cambios sientan las bases para las mejoras adicionales de Rosetta en una futura versión.

Para evitar la duplicación, queríamos unificar nuestras pruebas de rendimiento de JMeter y Monitor. Para ello, necesitábamos la herramienta de monitor más reciente para tener paridad de características con nuestras pruebas de JMeter. Para lograr esto, hemos dividido la publicación en HAPI y suscribirnos a los flujos de los nodos espejo en el monitor para permitir la suscripción solamente. En esta iteración, sólo la API gRPC soporta suscripción. Con este cambio, pudimos eliminar nuestro código JMeter y optimizar la imagen `hedera-mirror-test` de 1.5G a 0.5G.

Hemos realizado algunas mejoras operativas en nuestro gráfico de timbre, incluyendo dependencias de alerta. Las dependencias de alertas ayudan a evitar un flujo de alertas que están todas relacionadas con la misma causa raíz. También hicimos algunas correcciones de errores en el gráfico que podrían ocurrir al activar o desactivar algunos componentes a favor de bases de datos externas o autobuses de mensajes.

## [v0.33.0](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.33.0)

{% hint style="success" %}
**MAINNET ACTUALIZADO COMPLETADO: 10 de JUNIO 2021**
{% endhint %}

{% hint style="success" %}
**TESTNET ACTUALIZADO COMPLETADO: MAY 21, 2021**
{% endhint %}

Esta versión añade soporte para HAPI 0.13.2. Esto trae consigo un nuevo formato de libreta de direcciones que es más compacto y no duplica la información de direcciones IP y puertos. Nos tomamos el tiempo de ajustar nuestra base de datos para reflejar el formato más nuevo manteniendo la compatibilidad con el formato antiguo.

Un gran foco de este lanzamiento fue la mejora de los gráficos del casco para su uso en implementaciones de producción. Ahora generamos contraseñas auto-generadas para los componentes que requieren uno y nos aseguramos de que permanezcan iguales en las actualizaciones usando la función [lookup]de Helm (https://helm.sh/docs/chart\\_template\\_guide/functions\\_and\\_pipelines/#using-the-lookup-function). Hemos añadido las propiedades `env`, `envFrom`, `volumes`, `volumeMounts` a todas las cartas para una configuración más flexible. Hemos añadido una propiedad de gráfico `global.image.tag` para facilitar la prueba de versiones personalizadas. Y hicimos más fácil el uso de dependencias que pueden estar fuera del clúster como Redis y PostgreSQL.

Algunas mejoras internas nos han llevado a automatizar nuestro proceso de lanzamiento para que los bumps de versiones y la generación de notas de lanzamiento puedan ser arrancados a través de GitHub. Esto ahora también incluye generar un CHANGELOG y mantenerlo actualizado con las notas de lanzamiento. Y finalmente actualizamos nuestras pruebas de aceptación para tirar y usar automáticamente la última libreta de direcciones junto con validar todos los nodos para asegurar sólo la última, nodos válidos se utilizan para la validación.

## [v0.32.0](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.32.0)

{% hint style="success" %}
**MAINNET ACTUALIZADO COMPLETADO: MAY 19, 2021**
{% endhint %}

{% hint style="success" %}
**TESTNET ACTUALIZADO COMPLETADO: MAY 11, 2021**
{% endhint %}

En esta versión nos tomamos el tiempo de hacer algunas optimizaciones de rendimiento tanto del importador como del monitor. Si está utilizando un nodo réplica contenedor, las aplicaciones Java ahora utilizan más de la memoria disponible que ya se le ha asignado. Optimizamos el tamaño de algunas colas internas para reducir la probabilidad de que no se produzcan errores de memoria. Y ahora usamos un método de streaming más eficiente para escribir entidades en la base de datos y evitar grandes asignaciones de memoria. Todo esto se combinan para reducir en gran medida el uso general de memoria y mejorar el rendimiento general del importador. El monitor también vio mejoras en el rendimiento para permitirle publicar transacciones a un ritmo de 10.000 TPS.

Esta versión actualiza más de nuestro sistema para manejar el diseño de transacciones programadas revisado que estará disponible pronto en mainnet. Tanto las pruebas de aceptación como el monitor se actualizaron para poder publicar las nuevas transacciones.

Ahora exponemos los bytes de transacción en bruto codificados en formato Base64 en la API REST. Persister en los bytes del protobuf `Transaction` en la base de datos es una opción que ha estado disponible durante un tiempo, pero hasta ahora no ha estado disponible a través de la API. Persister los datos está desactivado por defecto, al igual que aumenta el tamaño de la base de datos un poco. Los nodos de espejo gestionados por Hedera no tendrán esa funcionalidad activada para reducir el almacenamiento.

## [v0.31.0](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.31.0)

{% hint style="success" %}
**MAINNET ACTUALIZADO COMPLETADO: APRIL 30, 2021**
{% endhint %}

{% hint style="success" %}
**TESTNET ACTUALIZADO COMPLETADO: APRIL 26, 2021**
{% endhint %}

Después de que las transacciones programadas estuvieran disponibles en la vista previa, escuchamos los comentarios del usuario e iteramos sobre el diseño para facilitar su uso. Esta versión añade soporte para este diseño [revisado programado de transacciones](https://github.com/hashgraph/hedera-services/blob/master/docs/scheduled-transactions/revised-spec.md) planeado para ser lanzado en HAPI v0.13. No hubo ningún impacto en nuestro formato API REST, sólo el importador necesitaba ser actualizado para analizar e ingerir el nuevo formato proto. Nuestra API de monitor y las pruebas de aceptación se ajustarán en la próxima versión una vez que los SDKs añadan soporte para el nuevo diseño.

Esta versión también añade soporte para el formato de archivo de saldo de cuenta recientemente anunciado que fue lanzado en HAPI v0.12. El nuevo formato [protobuf](https://github.com/hashgraph/hedera-protobufs/blob/main/streams/account\_balance\_file.proto)basado finalmente reemplazará el formato CSV en julio de 2021. Hasta entonces, ambos formatos existirán simultáneamente en el cubo para que los usuarios puedan pasar a su gusto. Además de ser más eficiente de analizar, los nuevos archivos también se comprimen usando Gzip para reducir los costes de almacenamiento y descarga. También nos tomamos el tiempo para mejorar el rendimiento de análisis de archivos de balance independientemente del formato. Los tiempos promedio de análisis deberían disminuir en alrededor de un 27%.

Para nuestra API REST, ahora exponemos un campo `entity_id` en nuestras API relacionadas con transacciones. Este campo representa la entidad principal asociada a ese tipo de transacción. Por ejemplo, si se trataba de una transacción HCS sería el tema ID creado, actualizado o eliminado.

`GET /api/v1/transactions/0.0.1009-1234567890-999999998`

```
{
  "transactions": [{
    "consensus_timestamp": "1234567890. 99999999",
    "entity_id": "0.0.108763",
    "valid_start_timestamp": "1234567890. 999998",
    "charged_tx_fee": 0,
    "memo_base64": null,
    "result": "SUCCESS",
    "scheduled": false,
    "transaction_hash": "aGFzaA==",
    "name": "CRYPTOUPDATEACCOUNT",
    "node": "0. .3",
    "transaction_id": "0.0. 009-1234567890-9999998",
    "valid_duration_seconds": "11",
    "max_fee": "33",
    "transfers": []
  }]
}
```

Seguimos avanzando hacia nuestro objetivo de cambiar a TimescaleDB. Hemos solucionado los problemas de inicialización de usuarios y bases de datos y hemos probado una migración desde PostgreSQL. Hemos cambiado el gráfico de TimescaleDB a uno más estable y hemos explorado nuestras opciones de alojamiento para la producción. Finalmente, cambiamos a SCRAM-SHA-256 para mejorar la seguridad de la autenticación de usuarios de nuestra base de datos.

### Cambios de ruptura:

Ha habido una serie de cambios de ruptura que esta versión debe tener en cuenta. Si estás usando nuestro gráfico del casco, hemos cambiado el importador de un `StatefulSet` a un `Deployment` ya que ya no tiene la necesidad de un volumen persistente. También cambiamos la dependencia de Traefik de un `Desplegamiento` a un `DaemonSet`. Ambos requerirán una intervención manual para eliminar la carga de trabajo anterior antes de actualizar. El apoyo al Helm 2 fue eliminado ya que ya no es [supported](https://helm.sh/blog/helm-v2-deprecation-timeline/) por la comunidad después del 13 de noviembre de 2020. Si está leyendo directamente desde nuestra base de datos, un renombrado de la tabla `t_entities` y sus columnas pueden impactarle también.

## [v0.30.0](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.30.0)

El nodo Mirror v0.30 trae mejoras operativas con cambios en nuestros componentes de integración continua y monitoreo.

Con esta versión, hemos completado la migración de CircleCI a Acciones de GitHub. CircleCI tenía algunas limitaciones con nuestro uso de [Testcontainers](https://www.testcontainers.org/) para pruebas unitarias contra dependencias de terceros. Anteriormente teníamos una mezcla de Acciones de GitHub y CircleCI con este último usando comandos ligeramente diferentes que las pruebas locales. Consolidar a las Acciones de GitHub nos permitió reducir esta diferencia y paralelizar aún más nuestros controles.

Para mejorar nuestra cobertura de la observabilidad del tiempo de ejecución y las pruebas, hemos continuado invirtiendo en nuestra herramienta de monitoreo este ciclo. El soporte de transacciones programadas se ha añadido recientemente soportando tanto las operaciones `ScheduleCreate` como `ScheduleSign`. Hemos añadido los tres nuevos nodos mainnet de la configuración predeterminada del monitor. Se ha corregido un error con el monitor incapaz de llegar al TPS esperado con múltiples escenarios.

La API REST también vio algunas correcciones de errores, incluyendo una corrección de consultas con un parámetro de crédito/débito ahora capaz de recuperar transferencias de sólo token. La API de transacción ahora llena la lista de transferencias de tokens para todos los tipos de transacciones en lugar de limitarse a sólo las criptomonedas.

## [v0.29.1](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.29.0)

{% hint style="success" %}
**MAINNET ACTUALIZADO COMPLETADO: APRIL 5, 2021**
{% endhint %}

{% hint style="success" %}
**TESTNET ACTUALIZADO COMPLETADO: COMPLETADOS 26, 2021**
{% endhint %}

Esta versión trae una variedad de mejoras bajo el capuchón entre módulos y refinamientos de múltiples API REST.

Ya está disponible la información de entidad histórica previa a la OA. En esta versión hemos añadido una migración repetible de Java que importará información de entidad de una instantánea de red mainnet. Esto se ejecuta durante la actualización, es configurable (`hedera.mirror.importer.importHistoricalAccountInfo`) y funciona en combinación con la configuración `hedera.mirror.importer.startDate`.

La API REST ahora expande su soporte de opciones de filtrado específicamente alrededor de las transferencias y en relación con los tokens. Anteriormente las opciones de filtrado `account.id`y `credit/debit` soportadas sólo para transferencias HBAR, esta versión amplía ambos filtros para incluir tokens también.

También se ha mejorado el paquete REST API y `check-state-test`. La API ahora soporta filtrado para transacciones programadas a través de `/api/v1/transactions/:transactionId/statetest?scheduled=true` así como un formato de respuesta más compacto. Para secuencias de registros que utilizan la versión HAPI v5 mejorada más recientemente, la respuesta API a prueba de estado envía hash de vuelta en lugar de los bytes crudos completos. Con esto, la respuesta es más ligera.

```
 {
     "address_books": [
       "contenido de la libreta de direcciones"
     ],
     "record_file": {
       "head": "contenido de la cabeza",
       "start_running_hash_object": "contenido del principio que ejecuta el objeto hash",
       "hashes_before": [
         "hash del primer objeto de stream de registro",
         "hash del segundo objeto de flujo de registro",
         "hash del (m-1)th record stream object"
       ],
       "record_stream_object": "contenido del mth record stream object",
       "hashes_after": [
         "hash of the (m+1)th record stream object",
         "hash del (m+2)th record stream object",
         "hash del objeto nth record stream object"
       ],
       "end_running_hash_object": "contenido del final ejecutando objeto hash",
     },
     "signature_files": {
       "0. .3": "contenido del archivo de firmas del nodo 0.0.3",
       "0. .4": "contenido del archivo de firmas del nodo 0.0.4",
       "0. .n": "contenido del archivo de firma del nodo 0.0.n"
     },
     "version": 5
}
```

La API REST ahora también soporta «cuenta» repetible. d`parámetros de consulta al filtrar, con una configuración configurable para el número máximo de parámetros de consulta repetidos\`/api/v1/(cuentas|balances|transacciones)?account.id=:id&account.id=:id2...\`

`GET /api/v1/accounts?account.id=0.0.7&account.id=0.0.9`

```
{
     "accounts": [
       {
         "balance": {
           "timestamp": "0.000002345",
           "balance": 70,
           "tokens": [
             {
               "token_id": "0.0.100001",
               "balance": 7
             },
             {
               "token_id": "0.0.100002",
               "balance": 77
             }
           ]
         },
         "account": "0.0.7",
         "expiry_timestamp": null,
         "auto_renew_period": null,
         "key": null,
         "deleted": false
       },
       {
         "balance": {
           "timestamp": "0.000002345",
           "balance": 90,
           "tokens": []
         },
         "account": "0.0.9",
         "expiry_timestamp": null,
         "auto_renew_period": null,
         "key": null,
         "deleted": false
       }
     ],
     "links": {
       "next": null
     }
   }
```

Múltiples módulos también han visto mejoras de seguridad y estandarización mediante la adición de herramientas de análisis automatizados más robustas como 'gosec' así como la implementación de sugerencias de una auditoría de código de terceros.

Esta versión también vio un paso para soportar las nuevas y mejoradas ofertas de v2 de la versión (Java SDK)\[[https://github.com/hashgraph/hedera-sdk-java\\](https://github.com/hashgraph/hedera-sdk-java/)]. Tanto el módulo monitor como las pruebas de aceptación se actualizaron para utilizar el nuevo SDK y utilizar características como reintento integrado y soporte para transacciones programadas.

## [v0.28.2](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.28.2)

{% hint style="success" %}
**MAINNET ACTUALADO COMPLETADO: MARCH 17, 2021**
{% endhint %}

{% hint style="success" %}
**TESTNET ACTUALIZADO COMPLETADO: 10 ARCH 2021**
{% endhint %}

Esta versión finaliza el soporte para transacciones programadas y el protobuf v0.12 de HAPI. Se añadieron dos nuevas APIs específicas de programación REST incluyendo `/api/v1/schedules` y `/api/v1/schedules/:id`. El primero enumera todos los programas con varias opciones de filtrado disponibles y el segundo devuelve un programa específico por su ID.

`GET /api/v1/schedules?account.id=0.0.1024&schedule.id=gte:4000&order=desc&limit=10`

```
{
    "schedules": [
      {
        "admin_key": {
          "_type": "ProtobufEncoded",
          "key": "7b2233222c2233222c2233227d"
        },
        "consensus_timestamp": "1234567890. 00030003",
        "creator_account_id": "0.0. 024",
        "executed_timestamp": null,
        "memo": "Creado por decisión del Consejo de Gobierno con fecha 02/03/21",
        "payer_account_id": "0.0. 024",
        "schedule_id": "0.0. 000",
        "signatures": [
          {
            "consensus_timestamp": "1234567890. 00030001",
            "public_key_prefix": "CQkJ",
            "firma": "CQkJ"
          }
        ],
        "transaction_body": "AQECAgMD"
      }
    ],
    "links": {
      "next": null
    }
}
```

En HAPI v0.12, se añadieron nuevos campos de memo a todos los tipos de entidades, trayendo paridad a través de todos los servicios. El nodo Mirror ahora soporta los nuevos campos incluyendo para operaciones de actualización donde el campo memo puede establecerse en `null`, cadena vacía o una cadena no vacía para mantener, limpiar o reemplazar el memo existente, respectivamente.

Históricamente, la aplicación importadora siempre ha descargado archivos de flujo y guardado en el sistema de archivos en un hilo y luego los ha leído e ingerido en la base de datos en otro hilo. Esto a veces ha causado que la base de datos y el sistema de archivos no estén sincronizados y requieren una intervención manual para corregir. También hace que el importador tenga un estado y como resultado no pudo soportar la ejecución de múltiples instancias para una alta disponibilidad.

Con esta versión, hemos eliminado la necesidad de que el importador lea y escriba en el sistema de archivos. En su lugar, los hilos de descarga y analizador ahora se comunican a través de una cola en memoria. Para lograr esto, también tuvimos que eliminar la tabla `t_application_status` en favor de calcular el último archivo exitoso directamente de las tablas de archivos de flujo. Además de solucionar los problemas más importantes, la eliminación del sistema de archivos ha supuesto una mejora de latencia del 5%.

Otros cambios incluyen añadir un campo `index` a la tabla `record_file` para simular un índice de blockchain y actualizar nuestro [Google Marketplace](https://console.cloud.google.com/marketplace/details/mirror-node-public/hedera-mirror-node) a v0.27. Además, añadimos soporte para los archivos de flujo v5 a la aplicación cliente `check-state-test`.

## [0.27.0](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.27.0)

{% hint style="success" %}
**MAINNET ACTUALIZADO COMPLETEDO: FEBRUARY 22, 2021**
{% endhint %}

{% hint style="success" %}
**TESTNET ACTUALIZADO COMPLETEDO: FEBRUARY 11, 2021**
{% endhint %}

Esta versión añade un nuevo componente API REST que implementa la [API de Rosetta](https://www.rosetta-api.org/). La API de Rosetta es un estándar abierto para la integración con sistemas orientados a blockchain. Implementar la Rosetta AP proporciona una serie de ventajas. Reduce el tiempo y esfuerzo que se necesita para carteras, intercambios, etc. para integrarse con la red Hedera si se han integrado con Rosetta en el pasado. Incluso si el integrador de sistemas no ha usado Rosetta anteriormente, usando la API de Rosetta en lugar de nuestra [API REST](https://docs.hedera separada. om/guides/docs/mirror-node-api/cryptocurrency-api) podría ser útil para reducir la fricción usando una DLT que no sea blockchain como Hedera.

[Transacciones programadas](https://github.com/hashgraph/hedera-services/blob/master/docs/scheduled-transactions/spec.md) es una nueva característica que se añade a los nodos principales en una versión futura. Las transacciones programadas permiten que las transacciones sean enviadas sin todas las firmas necesarias y se ejecutarán una vez que todas las partes requeridas firmen. El nodo espejo ha sido actualizado para entender y almacenar estos nuevos tipos de transacciones. Adicionalmente, hemos actualizado nuestras APIs REST existentes para exponer esta información. La próxima versión añadirá API REST específicas de programación adicional.

Hicimos un esfuerzo concertado este lanzamiento para mejorar nuestras pruebas. La mayoría de nuestras pruebas defectuosas fueron arregladas para que nuestra integración continua funcionara mejor. También hemos mejorado la estabilidad de nuestras pruebas de aceptación. El monitor de la API REST también tenía algunas correcciones de registro y usabilidad para ayudar en la observabilidad de la producción.

## [v0.26.0](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.26.0)

{% hint style="success" %}
**MAINNET ACTUALIZADO COMPLETEDO: FEBRUARY 1, 2021**
{% endhint %}

{% hint style="success" %}
**TESTNET ACTUALIZADO COMPLETADO: 22 de enero de 2021**
{% endhint %}

Esta versión se centra principalmente en añadir soporte para las próximas características en los nodos principales. Hemos añadido soporte para el campo `newTotalSupply` al registro de transacciones en HAPI `0.10.0`. También documentamos nuestra [design](https://github.com/hashgraph/hedera-mirror-node/blob/master/docs/design/scheduled-transactions.md) para las próximas [transacciones programadas](https://github.com/hashgraph/hedera-services/blob/master/docs/scheduled-transactions/spec.md) servicios que vienen en una futura versión de HAPI. Nuestra próxima versión menor contará con el apoyo preliminar para ello.

Pero por lejos el cambio más grande es el soporte para el nuevo archivo de registro V5 y el formato de archivo de firma V5. Estos archivos se suben al almacenamiento en la nube y se extraen por los nodos de réplica para llenar su base de datos. Dado que es el formato de comunicación principal entre los nodos principales y los nodos espejo, se necesitó un poco de refactorización y código nuevo para soportar el nuevo formato mientras se conservaba la compatibilidad con archivos de flujo anteriores.

#### ¡Advertencia! Si no actualiza su nodo Mirror a v0.26.0 o posterior antes de HAPI v0.11. se publicó en unas semanas, su nodo espejo no podrá procesar nuevas transacciones.

Continuamos nuestro progreso en cambiar a TimescaleDB. Integramos un gráfico de casco TimescaleDB en nuestro despliegue de Kubernetes y añadimos scripts de migración para convertir de PostgreSQL a TimescaleDB. Todavía estamos en la fase de prueba, por lo que todavía se recomienda mantener el esquema v1 (el valor por defecto) por ahora.

## [v0.25.0](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.25.0)

{% hint style="success" %}
**MAINNET ACTUALIZADO COMPLETADO: 12 de enero de 2021**
{% endhint %}

{% hint style="success" %}
**TESTNET ACTUALIZADO COMPLETADO: 8 de Enero, 2021**
{% endhint %}

Esta versión vio una gran cantidad de mejoras en nuestro nuevo módulo de monitorización. La [monitor](https://github.com/hashgraph/hedera-mirror-node/blob/v0.25.0/docs/monitor.md) es un componente independiente que puede publicar y suscribirse a transacciones de varias APIs de Hedera para medir la salud del sistema. Nuevo en esta versión es la capacidad de crear automáticamente entidades al iniciar usando una nueva sintaxis de expresiones. Esto es útil para evitar la configuración de boilerplate y los pasos de creación de entidad manual que varían por entorno.

Se añadió una propiedad de porcentaje de muestra al monitor para controlar la frecuencia con la que se debería verificar la API REST. Nos hemos tomado el tiempo para documentar adecuadamente la herramienta de monitor detallando sus pasos de configuración y operaciones. Finalmente, añadimos un número de nuevas métricas y un Grafana [dashboard](https://github.com/hashgraph/hedera-mirror-node/blob/v0.25.0/docs/monitor.md#dashboard--metrics) para verlas.

Avanzamos hacia nuestro objetivo de reemplazar PostgreSQL con [TimescaleDB](https://www.timescale.com/). Esta versión contiene las migraciones iniciales de la base de datos para configurar el nodo réplica desde cero usando TimescaleDB. Estas migraciones están ocultas detrás de una bandera de características. En una próxima versión, añadiremos más funcionalidades incluyendo scripts de migración de datos y soporte de Helm.

## [v0.24.0](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.24.0)

{% hint style="success" %}
**MAINNET ACTUALIZADO COMPLETADO: DECEMBRE 28, 2020**
{% endhint %}

{% hint style="success" %}
**TESTNET ACTUALIZADO COMPLETADO: DECEMBRE 10, 2020**
{% endhint %}

Esta versión añade soporte de especificaciones [OpenAPI 3.0](https://swagger.io/specification) a nuestra API REST. La especificación OpenAPI está disponible en `/api/v1/openapi.yml`y sirve como una especificación formal de nuestra API. Los clientes pueden utilizar la especificación para acortar el tiempo que tarda en integrarse con nuestra API generando código o pruebas de duelo. También nos proporciona un nuevo sitio de documentación API auto-generado que se puede ver en `/api/v1/docs`.

Ahora tenemos soporte para [AWS Default Credential Provider Chain](https://docs.aws.amazon.com/sdk-for-java/v1/developer-guide/credentials.html). Ahora en lugar de ser capaz de proporcionar acceso estático y claves secretas en la configuración, puede confiar en la cadena de proveedores predeterminada para recuperar sus credenciales automáticamente del entorno (variables de entorno, `~/. ws/credenciales`, etc). Vea nuestra [documentation](https://github.com/hashgraph/hedera-mirror-node/blob/master/docs/configuration.md#connect-to-s3-with-the-default-credentials-provider) para más información.

Hemos mejorado nuestras herramientas de monitoreo para proporcionar una mayor observabilidad en el funcionamiento del nodo espejo. Además de publicar, nuestra herramienta de monitor ahora soporta suscribirse a las APIs GRPC y REST para verificar la funcionalidad final de Hedera. También generará métricas a partir de esta información. Aprovechamos la nueva capacidad de alerta de registro de Loki y ahora podemos alertar de cualquier error que veamos en los registros que podría ser motivo de preocupación.

## [v0.23.0](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.23.0)

{% hint style="success" %}
**MAINNET ACTUALIZADO COMPLETADO: DECEMBRE 2, 2020**
{% endhint %}

{% hint style="success" %}
**TESTNET ACTUALIZADO COMPLETADO: NOVEMBRE 20, 2020**
{% endhint %}

Esta versión se centra en finalizar nuestro soporte para el nuevo Servicio de Token de Hedera (HTS) proporcionado por la API de Hedera [v0.9.0](https://github.com/hashgraph/hedera-services/releases/tag/v0.9.0). No hay nuevas características HTS, sólo varias correcciones para hacerlo compatible con el último protobuf. HTS está actualmente habilitado en la previewnet y debería estar habilitado en la red de pruebas muy pronto, así que pruébalo y háganoslo saber si tiene algún comentario.

Un nuevo Helm [chart](https://github.com/hashgraph/hedera-mirror-node/tree/master/charts/hedera-mirror-monitor) fue añadido para ejecutar la aplicación de monitor. El monitor está todavía bajo un gran desarrollo, así que manténgase atento.

La mayoría de los otros cambios fueron correcciones de errores. Ahora usamos SonarCloud para buscar vulnerabilidades y errores y hemos abordado todos los elementos principales. Puedes ver nuestra SonarCloud [dashboard](https://sonarcloud.io/dashboard?id=hedera-mirror-node) para seguir nuestro progreso. Las entidades ahora sólo se insertan para transacciones exitosas y arreglamos que la libreta de direcciones equivocada se actualizara. Hubo varios problemas con la API alfa de prueba de estado que fueron resueltos. Para la API GRPC, hemos mejorado su rendimiento y reducido su uso de CPU. También en relación con gRPC, ahora habilitamos que el servidor enviado mantenga vivo los mensajes y permita que un menor cliente enviado mantenga vivos mensajes de 90 segundos, que debería abordar con suerte los problemas de tiempo de espera que algunos usuarios han reportado.

## [v0.22.0](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.22.0)

Esta versión continúa con nuestras mejoras en nuestro soporte de Kubernetes, así como monitoreo y mejoras de rendimiento en todos los módulos.

Hemos mejorado nuestras alertas personalizadas de PrometheusRule para los módulos API de importador, GRPC y REST, así como paneles añadidos para nuestros módulos API gRPC y REST. Además, incrementamos nuestros límites de recursos para optimizar la ingestión de importadores y el rendimiento de streaming de gRPC en un clúster de Kubernetes. Tanto nuestro monitor basado en js existentes como las pruebas de rendimiento REST fueron actualizadas para incluir soporte HTS.

También hemos mejorado nuestro módulo generador de datos con soporte para la mayoría de las transacciones HAPI que el importador de los nodos espejos. Además, añadimos un módulo de monitoreo basado en Java que soporta la generación y publicación de transacciones.

Esta versión también incluye mejoras para evitar el error de información de cuenta obsoleto que surge del equilibrio de archivos de flujo que se reciben a una frecuencia más lenta que los archivos de flujo de registro. Now account creations and account info changes will be reflected in REST API call even though the updated balance may not have been received.\
We also extended our REST API support to include case insensitive support query parameters. `/api/v1/transactions?transactionType=tokentransfers` y `/api/v1/transactions?transactiontype=tokentransfers` son ahora aceptables.

## [v0.21.0](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.21.0)

{% hint style="success" %}
**MAINNET ACTUALADO COMPLETADO: NOVEMBRE 24, 2020**
{% endhint %}

{% hint style="success" %}
**TESTNET ACTUALIZADO COMPLETADO: NOVEMBRE 13, 2020**
{% endhint %}

Este lanzamiento continúa centrándonos en el Servicio de Token de Hedera (HTS) añadiendo tres nuevas APIs REST. Un descubrimiento de token REST API `/api/v1/tokens` muestra tokens disponibles en la red. Un token REST API `/api/v1/tokens/${tokenId}` muestra los detalles de un token en la red. Una distribución de token REST API `/api/v1/tokens/${tokenId}/balances` muestra la distribución de token a través de las cuentas. Estas APIs ya han hecho su camino a la previewnet así que ¡échale un vistazo!

Continuando con nuestro tema HTS, hemos mejorado nuestros frameworks de pruebas con soporte de token. Nuestras pruebas de aceptación pueden enviar transacciones HTS a HAPI y esperar a que esas transacciones aparezcan a través del nodo réplica REST API. Además, nuestras pruebas de rendimiento pueden simular una carga HTS para probar cómo el sistema responde a las transacciones HTS.

Hemos mejorado nuestras API REST existentes añadiendo una forma de filtrar por tipo de transacción. Al buscar transacciones o mostrar las transacciones de una cuenta en particular, ahora puede filtrar a través de un parámetro opcional `transactionType`. Esta característica puede ser usada con la API de transacciones en el formato `/api/v1/transactions?transactionType=tokentransfers` mientras que el formato para la API de cuentas es `/api/v1/accounts/0.0.8?transactionType=TOKENTRANSFERS`.

Hemos mejorado nuestro soporte de Kubernetes con la integración de AlertManager. Ahora hay alertas personalizadas de `PrometheusRule` para cada componente que desencadena notificaciones basadas en métricas Prometeo. Se creó un tablero personalizado de Grafana que muestra alertas de disparo en ese momento.

## [v0.20.0](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.20.0)

{% hint style="success" %}
**MAINNET ACTUALADO COMPLETADO: NOVEMBRE 11, 2020**
{% endhint %}

{% hint style="success" %}
**TESTNET ACTUALIZADO COMPLETADO: NOVEMBRE 3, 2020**
{% endhint %}

Esta es una gran versión que contiene soporte para un nuevo servicio HAPI y un nuevo componente de tiempo de ejecución para mejorar dramáticamente el rendimiento. Debido a la magnitud de los cambios, nos tomó un poco más de tiempo para marcarlo tan generalmente disponible como queríamos asegurarnos de que se probara lo más posible de antemano.

En primer lugar está el soporte para el servicio Hedera Token (HTS) que fue recientemente [announced](https://hedera.com/blog/previewnet-hedera-token-service-hts-early-access) y se lanzó a la vista previa. Se ha dedicado mucho trabajo a apoyar los nuevos tipos de transacciones en el lado analizador, incluyendo mejorar el esquema con nuevas tablas e ingerirlos a través del flujo de registros. HTS también requiere una nueva versión del archivo de balance que agregue información de token al CSV. La información de token ahora es devuelta para nuestras APIs REST existentes mientras que la próxima versión contendrá APIs REST específicas para mayor granularidad. ¡Échale un vistazo en previewnet y haznos saber si tienes alguna opinión!

Hicimos muchos avances en la mejora del rendimiento de la ingestión en versiones anteriores, pero ya que también queríamos asegurar el extremo bajo a fin de latencia HCS a través de nuestra API GRPC tuvimos que sacrificar parte de esa velocidad. Como resultado, sólo pudimos ingerir alrededor de 3.000 transacciones por segundo (TPS) antes de que la latencia se disparara por encima de 10 segundos. Esto se debió por completo a nuestro uso de notificación/escucha de PostgreSQL para notificar a la API gRPC de nuevos datos.

En esta versión, añadimos un nuevo mecanismo de notificación sin sacrificar velocidad o latencia con nuestro apoyo a Redis pub/sub. Con Redis, el nodo espejo ahora puede ingerir al menos 10, 00 TPS mientras permanece por menos de 10 segundos de enviar el mensaje del tema y recibirlo de vuelta a través de la API de streaming del nodo espejo. Redis está activado por defecto, pero puede desactivarse si la latencia de HCS no es una preocupación y desea evitar otra dependencia de tiempo de ejecución.

También añadimos soporte para el protobuf HAPI [changes](https://hedera.com/blog/changes-to-hedera-api-hapi-for-v0-8-0-and-v0-9-0) que vienen en v0.9.0. El protobuf está eliminando dos campos obsoletos mientras se añade un nuevo campo `signedTransactionBytes`. Dado que el nodo réplica todavía necesita soportar transacciones históricas, mantenemos soporte para analizar transacciones que contienen el antiguo formato de carga útil.

## [v0.19.0](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.19.0)

{% hint style="success" %}
**MAINNET ACTUALADO COMPLETADO: OCTOBER 6, 2020**
{% endhint %}

{% hint style="success" %}
**TESTNET ACTUALIZADO COMPLETADO: SEPTEMBER 29, 2020**
{% endhint %}

Esta versión termina la API REST alfa de prueba de estado y la hace [generalmente disponible](http://www.hedera.com/blog/introducing-state-proof-alpha). Como parte de esto, hicimos muchas mejoras en la herramienta de línea de comandos a prueba de estado que consulta la API y valida los archivos localmente. Ahora también almacenamos la cuenta de nodo utilizada para verificar el archivo de registro, garantizando una mayor precisión en cuanto a la procedencia de la prueba de estado.

Ha habido algunos cambios en los entornos públicos de Hedera últimamente y hemos actualizado el nodo espejo para reflejar eso. Hemos añadido soporte para el nuevo entorno previewnet y hemos actualizado la configuración para apuntar al nuevo bucket testnet después de su reciente reinicio. Asegúrese de que su nodo espejo tiene todos los datos en el cubo anterior antes de actualizar a este lanzamiento, suponiendo que no esté especificando el nombre del cubo manualmente.

Añadimos los puntos finales adecuados de la sonda para todos nuestros componentes. Si no estás familiarizado con el concepto de sondas de vida y preparación, revisa el Kubernetes [documentation](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/) sobre el tema. Nuestro nuevo punto final de vida ahora no fallará si las dependencias externas están caídas como la base de datos, asegurándose de que la aplicación no se reinicie innecesariamente. Incluso si no está usando Kubernetes, valdría la pena examinar para asegurarse de que su nodo espejo esté utilizando el punto final apropiado para las revisiones de salud, en base a sus necesidades.

## [v0.18.2](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.18.2)

{% hint style="success" %}
**MAINNET ACTUALADO COMPLETADO: SEPTEMBER 22, 2020**
{% endhint %}

{% hint style="success" %}
**TESTNET ACTUALIZADO COMPLETADO: SEPTEMBER 15, 2020**
{% endhint %}

Corregir dos regresiones en el tren de liberación 0.18.

## [v0.18.1](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.18.1)

Contiene un pequeño cambio a la API REST de prueba de estado para devolver la libreta de direcciones actual por ahora.

## [v0.18.0](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.18.0)

Basándonos en la disponibilidad de la [API REST de la prueba del estado](https://github.com/hashgraph/hedera-mirror-node/blob/v0.18.0/docs/design/stateuebalpha.md) en la última versión, hemos añadido [código de muestra](https://github. om/hashgraph/hedera-mirror-node/blob/v0.18.0/hedera-mirror-rest/state-proof-demo) en JavaScript para recuperar la prueba de estado de un nodo espejo y verificarla localmente. Esto permite a los usuarios obtener pruebas criptográficas de que una transacción en particular tuvo lugar en Hedera. La validez de la prueba puede comprobarse de forma independiente para garantizar que la supermayoría de la participación de Hedera en la red principal haya alcanzado un consenso sobre esa transacción. Similar a la promesa de las pruebas de estado finales, el usuario puede confiar en este alfa de prueba de estado servido por los nodos espejo, incluso cuando el usuario no confía en los nodos espejos.

El importador ahora puede configurarse para conectarse a Amazon S3 usando credenciales de seguridad temporales a través de [AssumeRole](https://docs.aws.amazon.com/STS/latest/APIReference/API\_AssumeRole.html). Con esto, un usuario que no tiene permiso para acceder a un recurso AWS puede solicitar un rol temporal que le concederá ese permiso. Vea la [documentación de configuración](https://github.com/hashgraph/hedera-mirror-node/blob/v0.18.0/docs/configuration.md#connect-to-s3-with-ATIS) para más información.

El importador también añadió dos nuevas propiedades para controlar el subconjunto de datos que debería descargar y validar. La propiedad `hedera.mirror.importer.startDate` puede ser usada para excluir datos desde antes de esta fecha y "rápido" hasta el punto en el momento de interés. Por defecto, el `startDate` se ajustará a la hora actual para que los operadores de los nodos espejos puedan ponerse en marcha y ejecutarse más rápido con los datos más recientes y reducir los costes de recuperación de almacenamiento en la nube. Tenga en cuenta que esta propiedad sólo se aplica en el primer arranque del importador y no se puede cambiar después de eso. La propiedad `hedera.mirror.importer.endDate` puede ser usada para excluir datos después de esta fecha y detener el importador. Por defecto, se establece en una fecha lejana en el futuro, por lo que no se detendrá efectivamente.

### Rompiendo Cambios

La propiedad `startDate` más destacada sí cambia cómo los operadores del nodo espejo en el inicio de versiones anteriores. Por defecto hasta ahora, los usuarios que pongan en pie un nuevo nodo espejo ya no recuperarán todos los datos históricos y, en su lugar, sólo recuperarán los últimos datos. Los usuarios actuales que se actualicen a esta versión no se verán afectados incluso si su ingesta de datos no es completamente capturada, ya que esta propiedad sólo se aplica si la base de datos está vacía como si estuviera en el primer comienzo. Para revertir al comportamiento anterior, una fecha en el pasado puede ser especificada como el epoch de Unix `1970-01-01T00:00:00Z`.

## [v0.17.3](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.17.3)

{% hint style="success" %}
**MAINNET UPGRADE COMPLETADO: SEPTEMBRE 14. 2020**
{% endhint %}

{% hint style="success" %}
**TESTNET UPGRADE COMPLETADO: SEPTEMBER 3, 2020**
{% endhint %}

Esta versión contiene la adaptación de una corrección de errores para gestionar mejor el problema `VertxException: Thread blocked` visto en [#945](https://github.com/hashgraph/hedera-mirror-node/issues/945)

## [v0.17.2](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.17.2)

Una pequeña solución de error para soportar un mejor restablecimiento del nodo espejo cuando se reinicia un stream reset en el entorno de red

## [v0.17.1](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.17.1)

Una pequeña solución para corregir una regresión de rendimiento al no almacenar en caché correctamente una consulta muy usada.

## [v0.17.0](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.17.0)

This release adds support for the storage of the network address books from file `0.0.101` and `0.0.102`in the mirror node database.\
The mirror node will now retrieve file address book contents which include node identifiers and their public keys from the database instead of the file system at startup.

This sets the stage for an additional feature which is the State Proof alpha REST API at `/transactions/${transactionId}/stateproof`.\
With this release it is possible to request the address book, record file and signature files that contain the contents of a transaction and allow for cryptographic verification of the transaction. Los usuarios de nodo espejo ahora pueden verificar activamente las transacciones enviadas por sí mismos.

Otros cambios incluyen soporte para despliegue continuo (CD) usando [Github Actions](https://github.com/features/actions) que usan [FluxCD](https://fluxcd.io/) para desplegar versiones maestras en un clúster de Kubernetes. Además, esta versión incluye correcciones a la optimización de la operación de copia de la base de datos y un manejo mejorado del tamaño del búfer usado al copiar mensajes de temas grandes.

## [v0.16.0](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.16.0)

{% hint style="success" %}
**TESTNET UPGRADE COMPLETED: AUGUSTO 18, 2020**
{% endhint %}

Esta versión incluye la base para algunas características más grandes por venir. Los nombres de los cubos en la nube ahora se establecen basándose en las especificaciones de la red y los usuarios ya no necesitan especificar explícitamente los nombres de los cubos para la demostración, prueba y redes principales. Los contenidos de la tabla `record_file` también se expanden para incluir las marcas de tiempo de inicio y final de sus transacciones que contengan. La tabla `record_file` también vio una limpieza para eliminar la ruta del archivo.

Adicionalmente, esta versión transmite la arquitectura de cartas de timón con un gráfico común para recursos compartidos. También añade dependabot para facilitar la gestión de actualizaciones de dependencias. El analizador también se actualizó para manejar archivos de firma a través de múltiples grupos de cubo de tiempo para un mayor análisis de robustidad.

También se hicieron mejoras en la memoria en el analizador para mejorar el rendimiento de la ingestión. Debido al rendimiento de la notificación pg también se eliminó a favor de la notificación directa de psql para apoyar la transmisión más rápida de mensajes temáticos entrantes.

## [v0.15.3](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.15.3)

{% hint style="success" %}
**MAINNET UPGRADE COMPLETADO: JULY 29, 2020**
{% endhint %}

Funciona alrededor de un problema enviando cargas grandes JSON vía pg\_notify ignorándolas por ahora. Esto ocurre cuando se envía un mensaje de consenso con un mensaje que excede los 5824 bytes, lo que también está muy cerca del límite de protobuf.

## [v0.15.2](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.15.2)

{% hint style="success" %}
**MAINNET UPGRADE COMPLETADO: JULY 29, 2020**
{% endhint %}

{% hint style="success" %}
**TESTNET UPGRADE COMPLETADO: 20 JULY 2020**
{% endhint %}

Esta versión mejora la tasa de ingesta de mensajes temáticos que regresaron en la versión anterior. Esto no es más que una brecha de freno y las futuras liberaciones aumentarán aún más.

## [v0.15.1](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.15.1)

{% hint style="success" %}
**MAINNET UPGRADE COMPLETADO: JULY 29, 2020**
{% endhint %}

{% hint style="success" %}
**TESTNET UPGRADE COMPLETADO: 15 de JULY 2020**
{% endhint %}

Una versión de revisión caliente para abordar dos errores de análisis de alta prioridad con la nueva cabecera de mensaje de consenso.

## [v0.15.0](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.15.0)

{% hint style="success" %}
**MAINNET UPGRADE COMPLETADO: JULY 29, 2020**
{% endhint %}

{% hint style="success" %}
**TESTNET UPGRADE COMPLETADO: 15 de JULY 2020**
{% endhint %}

Esta versión añade soporte para la fragmentación del tema HCS que pronto se desplegará a los nodos principales en la versión 0.6.0. Para mensajes de consenso más grandes que no caben en el tamaño máximo de transacción de 6144 bytes, un encabezado de información de trozo estándar puede ser suministrado para indicar cómo ese mensaje debe dividirse en mensajes más pequeños. El nodo de rúbrica ahora entiende esta información de fragmentos y la almacena en la base de datos. Además, devolverá este [data](https://github.com/hashgraph/hedera-mirror-node/blob/a2f69ee1243fbbbfbc133549f9162bfc3a08f464/hedera-mirror-protobuf/src/main/proto/com/hedera/mirror/api/proto/ConsensusService.proto#L58) al suscribirse al tema a través de la API de gRPC. El SDK de Java se está actualizando para dividir y reconstruir automáticamente este mensaje según corresponda.

Otros cambios incluyen optimizaciones en torno a latencia final de la API GRPC. Esto fue conseguido principalmente añadiendo una nueva funcionalidad `NotifyingTopicListener` que usa la LISTEN/NOTIFY de PostgreSQL.

## [v0.14.1](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.14.1)

{% hint style="success" %}
**MAINNET UPGRADE COMPLETADO: JULY 29, 2020**
{% endhint %}

{% hint style="success" %}
**TESTNET UPGRADE COMPLETADO: 15 de JULY 2020**
{% endhint %}

Este lanzamiento optimiza aún más la tasa de ingestión. Las pruebas iniciales indican una mejora de 2x a 3x.

## [v0.14.0](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.14.0)

{% hint style="success" %}
**MAINNET UPGRADE COMPLETADO: JULY 29, 2020**
{% endhint %}

{% hint style="success" %}
**TESTNET UPGRADE COMPLETADO: 15 de JULY 2020**
{% endhint %}

Esta versión tiene todo que ver con optimizaciones de rendimiento. Hemos rediseñado algunas de las claves externas para mejorar el rendimiento de la ingestión por unos pocos miles de transacciones por segundo. También arreglamos un problema de memoria con la API gRPC e hicimos algunas optimizaciones en ese área.

Además del rendimiento, hemos hecho algunas pequeñas mejoras. Ahora establecemos `topicRunningHashV2AddedTimestamp` con un valor predeterminado para mainnet, haciendo que no falle al iniciar si no se proporciona un valor. Se añadieron pruebas de aceptación y rendimiento containerizadas, facilitando la prueba a escala.

#### Rompiendo Cambios

Hemos eliminado las propiedades `hedera.mirror.grpc.listener.bufferInitial` y `hedera.mirror.grpc.listener.bufferSize` ya que eliminamos el búfer del encuestador compartido.

También renombramos algunas tablas y columnas que le afectarían si utiliza directamente la estructura de la base de datos. Cambiamos el nombre de `t_transactions` a `transaction`, `t_cryptotransferlists` a `crypto_transfer` y `non_fee_transfers` a `non_fee_transfer`.

## [v0.13.2](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.13.2)

{% hint style="success" %}
**MAINNET UPGRADE COMPLETADO: 2, 2020**
{% endhint %}

{% hint style="success" %}
**TESTNET UPGRADE COMPLETADO: JUNE 23, 2020**
{% endhint %}

Error al reparar la liberación para solucionar un problema de memoria con la API de gRPC.

## [v0.13.1](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.13.1)

{% hint style="success" %}
**MAINNET UPGRADE COMPLETADO: 2, 2020**
{% endhint %}

{% hint style="success" %}
**TESTNET UPGRADE COMPLETADO: JUNE 23, 2020**
{% endhint %}

Pequeña versión de corrección de errores para abordar el problema de grpc NETTY bloqueando pruebas de aceptación

## [v0.13.0](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.13.0)

{% hint style="success" %}
**MAINNET UPGRADE COMPLETADO: 2, 2020**
{% endhint %}

{% hint style="success" %}
**TESTNET UPGRADE COMPLETADO: JUNE 23, 2020**
{% endhint %}

Esta versión es una versión más pequeña centrada principalmente en correcciones de errores con algunas mejoras menores. Hemos añadido una nueva propiedad `hedera.mirror.importer.downloader.endpointOverride` para pruebas. También añadimos `hedera.mirror.importer.downloader.gcpProjectId` para que se especifiquen las credenciales de pago con una cuenta personal. Por último, hemos mejorado nuestro apoyo a Marketplace para acercarnos a ponerlo a disposición.

## [v0.12.0](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.12.0)

{% hint style="success" %}
**TESTNET UPGRADE COMPLETADO: MAY 29, 2020**
{% endhint %}

Esta versión contiene algunas bonitas adiciones mientras se corrigen algunos errores críticos. Hemos avanzado mucho al añadir nuestra aplicación a [Google Cloud Platform Marketplace](https://console.cloud.google.com/marketplace). Esto debería estar listo pronto y habilitar un "clic para desplegar" del nodo espejo en la nube de Google. Adicionalmente, algunos campos extra fueron añadidos a nuestras APIs. Agregamos `runningHashVersion` a las APIs REST y GRPC. Finalmente, añadimos `transactionHash` a la transacción REST API.

Hemos mejorado la tasa de ingestión del importador de 3400 a 5600 transacciones por segundo en nuestro entorno de pruebas de rendimiento. Todavía hay margen para mejorar y planeamos hacer optimizaciones adicionales de rendimiento en una próxima versión.

### Rompiendo Cambios

Añadimos una opción para mantener los archivos de firma después de la verificación. De forma predeterminada, ya no almacenamos firmas en el sistema de archivos. Si quieres restaurar el comportamiento antiguo y guardar las firmas, puedes establecer `hedera.mirror.importer.downloader.record.keepSignatures=true` y `hedera.mirror.importer.downloader.balance.keepSignatures=true`.

Cambiamos el comportamiento de desajuste de hash de bypass en esta versión. Al pasar el desajuste de hash se podría utilizar en combinación con otros parámetros para avanzar rápidamente el nodo de réplica a los datos más recientes o para superar los reinicios de la secuencia. Anteriormente tenías que especificar esto a través de un valor de base de datos en `t_application_status`. Dado que estos datos no es el estado de la aplicación, pero se considera más un valor proporcionado por el usuario, añadimos una nueva propiedad `hedera.mirror.importer.verifyHashAfter=2020-06-05T17:16:00.384877454Z` para este propósito.

## [v0.11.0](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.11.0)

{% hint style="success" %}
**MAINNET UPGRADE COMPLETADO: 10 de JUNE 2020**
{% endhint %}

{% hint style="success" %}
**TESTNET UPGRADE COMPLETADO: MAY 29, 2020**
{% endhint %}

Esta versión se centró principalmente en la refactorización de código y propiedades como un paso necesario para futuras mejoras. También continuamos haciendo mejoras en nuestro soporte de Kubernetes. A tal fin, hemos añadido las métricas Prometheus REST, los tests de casco y el nodo de rótulos pueden funcionar ahora en GKE.

Hemos añadido un nuevo parámetro a todas las API REST relacionadas con el tema para devolver un mensaje de tema en texto plano en lugar de binario. Los mensajes enviados a HAPI se envían como binarios y se almacenan en el nodo Mirror de esa manera también. Si sabes que los mensajes son cadenas codificadas en UTF-8, entonces puedes establecer `encoding=utf-8`y la API REST hará un mejor esfuerzo de conversión a cadena. Por defecto o si pasa un parámetro de consulta de `encoding=base64`, devolverá el mensaje como binario codificado en base64.

**Cambios de ruptura**

Tenga en cuenta al actualizar que hicimos cambios importantes en el nombre de nuestras propiedades de configuración. Hemos renombrado todas las propiedades `hedera.mirror.api` a `hedera.mirror.rest`. También renombramos las propiedades `apiUsername` a `restUsername` y `apiPassword` a `restPassword` para reflejar eso también. Cualquier propiedad que fue usada por el módulo importador fue renombrada para ser anidada bajo `hedera.mirror.importer`. Nos disculpamos por cualquier inconveniente.

Hemos eliminado la propiedad `hedera.mirror.addressBookPath` a favor de una propiedad `hedera.mirror.importer.initialAddressBook`. La primera fue sobrecargada para ser tanto la libreta de direcciones inicial de arranque como la libreta de direcciones en vivo siendo actualizada por transacciones de archivos para `0.0.102`. La libreta de direcciones en vivo ahora está codificada en `${hedera.mirror.importer.dataPath}/addressbook.bin` y no puede ser cambiada.

La API REST para recuperar un mensaje de tema por su marca de tiempo de consenso ahora soporta tanto una ruta URI plural (`/topics/messages/:consensusTimestamp`) como singular (`/topic/message/:consensusTimestamp`). El formato singular está obsoleto y desaparecerá en un futuro próximo, así que por favor actualice al formato plural pronto.

Quitamos la forma singular de un tema alfa API REST. La API `/topic/:id/message` fue removida a favor de la forma plural `/topics/:id/messages`. Del mismo modo, el `/topic/:id/message/:sequencenumber`API fue eliminado a favor de su forma plural `/topics/:id/messages/:sequencenumber`. Por favor, actualice en consecuencia.

## [v0.10.1](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.10.1)

Versión pequeña de corrección de errores para abordar un problema de empaque de la API REST.

## [v0.10.0](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.10.0)

En preparación para la versión 0.5.0 del Nodo de Hedera, lanzamos v0.10.0 para soportar la última versión de [HAPI](https://docs.hedera.com/guides/docs/hedera-api). Los cambios incluyen renombrar Claims a LiveHash y nuevos códigos de respuesta. Un cambio importante de HAPI es la adición de un `topicRunningHashVersion` al registro de transacción. Este cambio era necesario ya que la forma en que el tema en ejecución del hash está cambiando con la publicación de 0.5.0. Como resultado, el nodo Hedera Mirror añadió este nuevo campo a su base de datos y una migración se ejecuta para poblarlo con la versión nueva o antigua dependiendo de la fecha de lanzamiento de 0. .0.

Desafortunadamente, esto necesario añadiendo un campo requerido `hedera.mirror.topicRunningHashV2AddedTimestamp` para controlar este comportamiento y fallará al arrancar si esto no está poblado. Se trata sólo de una medida temporal. Una vez que Hedera Node 0.5.0 sea liberado en testnet y mainnet lo actualizaremos automáticamente con la fecha conocida.

Otros cambios incluyen añadir soporte de Google PubSub para publicar JSON representando el protobuf `Transaction` y `TransactionRecord` a una cola de mensajes para consumo externo. También hemos añadido métricas de la API REST y hemos añadido Traefik como una pasarela API para nuestro gráfico de helm.

#### Cambios de ruptura

Hemos tenido que eliminar nuestro soporte para el flujo de eventos. Esta área del código nunca fue habilitada y no se puso a prueba y estaba incurriendo en deuda técnica sin proporcionar ningún beneficio. Si es necesario en el futuro, podemos volver a añadirlo dentro de nuestro marco refactorizado recientemente.

La nueva API alfa REST `/api/v1/topics/:id` que fue añadida en 0.9 ha sido cambiada a `/api/v1/topics/:id/messages`. Este cambio se hizo para alinear la API con el otro tema APIs de mensajería ya que se refiere a la entidad de mensajes y no a la entidad del tema.

## [v0.9.1](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.9.1)

Pequeña versión de corrección de errores para que la dirección no pueda manejar actualizaciones de libreta de direcciones que abarcan múltiples transacciones.

## [v0.9.0](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.9.0)

Esta versión contiene otra nueva API REST para nuestro [servicio de consenso](https://www.hedera.com/consensis) service/). Ahora puede recuperar todos los mensajes de temas en un tema en particular, con un filtrado adicional por número de secuencia y la marca de tiempo de consenso. Aquí hay un ejemplo:

`GET /api/v1/topic/7?sequencenumber=gt:2&timestamp=lte:1234567890.0000006&limit=2`

```
{
  "messages": [
    {
      "consensus_timestamp": "1234567890. 00000003",
      "topic_id": "0.0. ",
      "message": "bWVzc2FnZQ==",
      "running_hash": "cnVubmluZ19oYXNo",
      "número_secuencia": 3
    },
    {
      "consensus_timestamp": "1234567890. 00000004",
      "topic_id": "0. .7",
      "message": "bWVzc2FnZQ==",
      "running_hash": "cnVubmluZ19oYXNo",
      "secuencia_número": 4
    }
  ],
  "links": {
     "next": "/api/v1/topic/7? equencenumber=gt:2&timestamp=lte:1234567890. 00000006&timestamp=gt:1234567890.000000004&limit=2"
  }
}
```

La otra gran característica de esta versión es el soporte [Kubernetes](https://kubernetes.io/). Hemos creado un gráfico [Helm](https://helm.sh/) que puede utilizarse para desplegar un nodo de réplica altamente disponible con un solo comando. Esta característica se encuentra todavía en un gran desarrollo mientras trabajamos para convertir nuestros despliegues actuales en este nuevo enfoque.

## [v0.8.1](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.8.1)

Versión pequeña de corrección de errores para solucionar un problema de empaquetamiento.

## [v0.8.0](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.8.0)

¡El nodo de la rama v0.8.0 está aquí! Hemos dado grandes pasos para hacer que el nodo espejo sea más fácil de ejecutar y gestionar. En particular, hemos añadido soporte para [requester pays](https://docs.aws.amazon.com/AmazonS3/latest/dev/RequesterPaysBuckets.html). Esto permitirá a cualquiera ejecutar un nodo espejo siempre y cuando esté dispuesto a pagar el costo para recuperar los datos. Actualmente sólo Hedera y algunos socios tienen acceso al cubo, por lo que permitir esto abrirá esos datos a nuestra comunidad. Todavía estamos trabajando en una migración de los cubos a este modelo, así que manténgase atento.

También agregamos dos nuevas APIs REST experimentales para recuperar los datos de HCS. En primer lugar, añadimos `/api/v1/topic/message/${consensusTimestamp}` para recuperar un mensaje de tema por su marca de tiempo de consenso. En segundo lugar, añadimos `/api/v1/topic/${topic}/message/${seqNum}` para recuperar un mensaje de tema en particular por su número de secuencia de un tema. Estas APIs son consideradas alfa y pueden ser cambiadas o eliminadas en el futuro. También aumentamos drásticamente la cobertura de pruebas para las APIs REST y aplastamos algunos errores en el proceso.

Para nuestro GRPC API, tuvimos que cambiar de R2DBC a Hibernate para alcanzar la escala y estabilidad que necesitábamos. Al hacerlo, ahora podemos apoyar a muchos suscriptores más concurrentes en un trayecto mayor. También debería poner fin de una vez por todas a cualquier preocupación por la estabilidad del componente GPR.

Hay algunos cambios que hemos tenido que hacer. Ahora ya no escribimos y almacenamos archivos de registro o balance en el sistema de archivos después de que se inserten en la base de datos. Si todavía necesitas estos archivos, puedes establecer `hedera.mirror.parser.balance.keepFiles` y `hedera.mirror.parser.record.keepFiles` a verdadero.

También, movimos las propiedades persistentes a ser agrupadas bajo un nuevo camino. Eso es lo que movimos opciones como `hedera.mirror.parser.record.persistTransactionBytes` a `hedera.mirror.parser.record.persist.transactionBytes`. Por favor, actualice su configuración local en consecuencia.

## [v0.7.0](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.7.0)

0.7.0 se centra en refactorizar el análisis de archivos de registro para desacoplar el análisis de la persistencia de datos. Esta refactorización está sentando las bases para mejoras adicionales de rendimiento y permitiendo que los componentes adicionales de la corriente posterior se registren para la notificación de las transacciones.

## [v0.6.0](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.6.0)

- Release centrado en mejoras de estabilidad y rendimiento.
- Cobertura de prueba final a fin.

Esta versión se centró principalmente en mejorar la estabilidad y el rendimiento del nodo espejo. Hemos mejorado la velocidad de ingestión de las transacciones de 600 a 4000 transacciones por segundo. Al mismo tiempo, hemos mejorado enormemente la resistencia y el rendimiento del módulo GRPC. También añadimos pruebas de aceptación para probar HCS final a fin.

**Cambio de ruptura**

Por favor, ten en cuenta que un cambio potencial que rompe esta versión es rechazar las suscripciones a temas que no existen. Esto evita que el servidor tenga que sondear repetidamente hasta que se cree y tome recursos para un tema que nunca existe. Se espera que los clientes o la [SDK](https://github. om/hashgraph/hedera-sdk-java/issues/367) sondeará periódicamente después de crear un tema hasta que ese tema se acerque al nodo espejo. Esta funcionalidad está oculta detrás de una bandera de características, pero poco a poco se desplegará durante el próximo mes.

## v0.5.3

- Ahora soporta toda la funcionalidad de HCS incluyendo una API gRPC de streaming para la suscripción a temas de mensajes.
- Cambiada la forma en que el nodo réplica verifica el consensus mainnet. El nodo espejo ahora espera por lo menos el tercio de las firmas de los nodos en lugar de mayores de dos tercios para verificar el consenso.
- Se han añadido nuevos nodos mainnet a la libreta de direcciones del nodo réplica.
- Acceso todavía restringido a un conjunto blanco de direcciones IP. Solicitar acceso [here](https://learn.hedera.com/l/576593/2020-01-13/7z5jb).
- Por favor vea la página de lanzamientos de Mirror Node para la lista completa de cambios [here](https://github.com/hashgraph/hedera-mirror-node/releases).
- Ocasionalmente podemos encontrarnos con una situación en la que un retraso adicional de 15 segundos en el tiempo de viaje redondo del mensaje es experimentado y las conexiones de los suscriptores son eliminadas. No se pierden mensajes y el tiempo de consenso no se ve afectado. Se anima a los clientes a volver a conectarse. Este problema se corregirá en una versión posterior del nodo espejo de Hedera. Algunos nodos espejo de terceros no deberían verse afectados por esta cuestión. Tampoco esperamos que afecte a los intercambios usando el punto final REST para el nodo réplica.
