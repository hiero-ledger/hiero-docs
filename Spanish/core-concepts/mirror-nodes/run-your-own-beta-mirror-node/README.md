# Ejecuta tu propio nodo de espejo

## Resumen

Un nodo Hedera Mirror es un nodo que recibe archivos preconstruidos de la red Hedera. Estos archivos preconstruidos incluyen **registros de transacción** y **archivos de saldo de cuentas**. Los registros de la transacción incluyen información sobre una transacción, como el ID de la transacción, el hash de la transacción, la cuenta, etc. Los archivos de saldo de la cuenta le dan una instantánea de los saldos de todas las cuentas en una marca de tiempo determinada.

En este tutorial, ejecutarás tu propio nodo Hedera Mirror. Necesitará crear una cuenta de Google Cloud Platform (GCP) o una cuenta de Amazon Web Services (AWS) si aún no la tiene. El cubo de almacenamiento de objetos del nodo giratorio de Hedera, desde donde se extraerá el saldo de la cuenta y los datos de la transacción, se almacena en el cubo GCP o AWS y está configurado para [requester pays](https://cloud. oogle.com/storage/docs/requester-pays). Esto significa que el operador del nodo espejo es responsable de los costes operativos de lectura y recuperación de datos del GCP/AWS. Una cuenta de GCP/AWS proporcionará la información de facturación necesaria para solicitar los datos.

{% content-ref url="run-your-own-mirror-node-gcs.md" %}
[run-your-own-mirror-node-gcs.md](run-your-n-mirror-node-gcs.md)
{% endcontent-ref %}

{% content-ref url="run-your-own-mirror-node-s3.md" %}
[run-your-own-mirror-node-s3.md](ejecute-tu-de-de-de-de-nodo-md)
{% endcontent-ref %}

## Requisitos mínimos de hardware y costos asociados

Para ejecutar un nodo Hedera Mirror, necesitará hardware y recursos específicos. A continuación se detallan los requisitos mínimos recomendados para ejecutar un nodo de espejo, junto con los costos asociados.

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-cover data-type="files"></th></tr></thead><tbody><tr><td><h4><mark style="background-color:yellow;">Motor de Calcular</mark></h4><ul><li><strong>Región:</strong> Iowa</li><li><strong>Modelo de protección:</strong> regular</li><li><strong>Tipo de Instancia:</strong> n1-standard-4</li><li><strong>Sistema Funcional / Software:</strong> Gratis</li><li><strong>Horas totales al mes:</strong> 1.460</li><li><strong>Descuento sostenido en el uso:</strong> aplicado (30%)</li></ul></td><td>El costo estimado para el motor de cálculo por mes es <strong>USD 194.18</strong>.</td><td></td></tr><tr><td><h4><mark style="background-color:yellow;">Cloud SQL para PostgreSQL</mark></h4><ul><li><strong>Tipo de Instancia:</strong> db-highmem-4</li><li><strong>Ubicación:</strong> Iowa</li><li><strong>Número de Instancias:</strong> 1</li><li><strong>Horas totales al mes:</strong> 730,0</li><li><strong>Almacenamiento SSD:</strong> 250.0 GiB</li><li><strong>Copia de seguridad:</strong> 250.0 GiB</li></ul></td><td><p>El costo estimado para Cloud SQL para PostgreSQL por mes es <strong>USD 303.46</strong>. </p><p></p><p><em><strong>*</strong>The Mainnet full node needs a much bigger database (50TB) for the complete transaction history.</em></p></td><td></td></tr><tr><td><h4><mark style="background-color:yellow;">Disco persistente (Acompañado)</mark></h4><ul><li><strong>Product accompanying:</strong> GKE Standard</li><li><strong>Zonal SSD PD:</strong> 50 GiB (2 x disco de arranque)</li></ul></td><td><p>El costo estimado para el disco persistente por mes es <strong>USD 17,00</strong>.</p><p></p><p>Con base en estas especificaciones, el costo mensual estimado total para ejecutar un nodo Hedera Mirror es <strong>514,64</strong>.</p></td><td></td></tr></tbody></table>

{% hint style="warning" %}
**NOTA:** Estos son costos estimados. y los costes reales variarán en función del uso y de cualquier cambio en el precio de los recursos utilizados. Consulte siempre las listas de precios más recientes de los respectivos servicios para obtener unos costes precisos.
{% endhint %}
