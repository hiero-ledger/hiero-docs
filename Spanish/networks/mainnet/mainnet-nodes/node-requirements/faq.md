---
description: Preguntas comunes para los actuales nodos de consenso de Hedera Mainnet
---

# FAQ

## ¿Qué protocolos de seguridad y tamaños de clave son usados por hashgraph?

Los nodos utilizan claves 3k TLS 1.2 DH RSA y SHA384 para asegurar las comunicaciones entre nodos. Nuestro objetivo es satisfacer la suite CNSA 1 especificada por el gobierno de los Estados Unidos. TLS 1.2 utiliza efemeral AES 256 para un perfecto secreto hacia delante. Se utilizan diferentes claves para el intercambio de claves TLS y una diferente para las firmas. Los clientes utilizan ED25519 para firmar transacciones.

## ¿Un nodo de consenso apoya el tráfico de unión o división del progreso y el éxito?

Hashgraph no apoya la unificación o división del tráfico de ingles y egresos.

## ¿Necesita el nodo de consenso acceso a nuestra red interna?

El nodo de consenso de Hedera no necesita acceso a ningún recurso interno y debe separarse del resto de la red corporativa, idealmente en su propia zona ilitarizada DMZ).

## ¿Hay que respaldar el nodo de consenso?

No se requieren copias de seguridad específicas de la aplicación. Dado que la red Hedera continúa procesando transacciones mientras el nodo fallido está caído, las copias de seguridad restauradas estarán desactualizadas en el momento en que sean recuperadas. La redundancia viene de los otros nodos y el nodo recuperado será resincronizado por el software hashgraph .

Se espera que haya procedimientos normales de copia de seguridad para el nivel del sistema operativo que permitan una recuperación rápida y consistente para situaciones de desastre incluyendo fallos de hardware y situaciones similares.

## ¿Cuáles son los requisitos operacionales y del nodo de consenso?

El acuerdo de la BLC especifica que "mientras que el objetivo inicial deseado debe ser al menos el 99,5% de disponibilidad, incluyendo las ventanas de mantenimiento técnicas/operacionales".

La vigilancia externa estará disponible en Hedera para notificar el fracaso.
