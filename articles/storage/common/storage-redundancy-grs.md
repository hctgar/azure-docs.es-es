---
title: Almacenamiento con redundancia geográfica (GRS) para la durabilidad interregional
titleSuffix: Azure Storage
description: El almacenamiento con redundancia geográfica (GRS) replica sus datos entre dos regiones que se encuentran a cientos de millas la una de la otra. GRS protege frente a errores de hardware en el centro de datos, así como frente a desastres regionales.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 01/02/2020
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: 6bb93c3fb6599a05978e11ef5fbc179ccfaa9ec2
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/02/2020
ms.locfileid: "75614900"
---
# <a name="geo-redundant-storage-grs-cross-regional-replication-for-azure-storage"></a>Almacenamiento con redundancia geográfica (GRS): replicación entre regiones para Azure Storage

[!INCLUDE [storage-common-redundancy-GRS](../../../includes/storage-common-redundancy-grs.md)]

## <a name="read-access-geo-redundant-storage"></a>Almacenamiento con redundancia geográfica con acceso de lectura

El almacenamiento con redundancia geográfica con acceso de lectura (RA-GRS) maximiza la disponibilidad de la cuenta de almacenamiento. RA-GRS proporciona acceso de solo lectura a los datos de la ubicación secundaria, además de ofrecer replicación geográfica entre dos regiones.

Cuando habilita el acceso de solo lectura para los datos de la región secundaria, los datos quedan disponibles en un punto de conexión secundario, además del punto de conexión principal de la cuenta de almacenamiento. El punto de conexión secundario es similar al punto de conexión principal, pero anexa el sufijo `–secondary` al nombre de la cuenta. Por ejemplo, si el punto de conexión principal de Blob service es `myaccount.blob.core.windows.net`, el punto de conexión secundario es `myaccount-secondary.blob.core.windows.net`. Las claves de acceso de la cuenta de almacenamiento son iguales para los extremos principal y secundario.

Tenga en cuenta lo siguiente cuando use RA-GRS:

- La aplicación tiene que administrar el punto de conexión con el que interactúa al usar RA-GRS.
- Como la replicación asincrónica implica un retraso, los cambios que todavía no se hayan replicado a la región secundaria pueden perderse si los datos no se pueden recuperar desde la región principal.
- Puede comprobar la propiedad **Hora de la última sincronización** de la cuenta de almacenamiento. La **Hora de la última sincronización** es un valor de fecha y hora GMT. Todas las escrituras primarias realizadas antes de la **hora de la última sincronización** se han escrito correctamente en la ubicación secundaria, lo que significa que están disponibles para leerse desde la ubicación secundaria. Las escrituras primarias posteriores a la **hora de la última sincronización** pueden o no estar todavía disponibles para las lecturas. Puede consultar este valor utilizando PowerShell, la CLI de Azure o una de las bibliotecas de cliente de Azure Storage. Para obtener más información, vea **Obtener la hora de última sincronización** en [Diseño de aplicaciones de alta disponibilidad mediante almacenamiento con redundancia geográfica con acceso de lectura](storage-designing-ha-apps-with-ragrs.md#getting-the-last-sync-time).
- Si inicia una conmutación por error (versión preliminar) de una cuenta GRS o RA-GRS que tenga como destino la región secundaria, el acceso de escritura a esa cuenta se restaurará una vez que se complete la conmutación por error. Para más información, consulte [Recuperación ante desastres y conmutación por error de la cuenta de almacenamiento (versión preliminar)](storage-disaster-recovery-guidance.md).
- RA-GRS está pensado para fines de alta disponibilidad. Para obtener instrucciones sobre escalabilidad, revise la [lista de comprobación de rendimiento](storage-performance-checklist.md).
- Para sugerencias sobre cómo diseñar la alta disponibilidad con RA-GRS, consulte [Diseño de aplicaciones de alta disponibilidad mediante RA-GRS](storage-designing-ha-apps-with-ragrs.md).

## <a name="what-is-the-rpo-and-rto-with-grs"></a>¿Qué es el RPO y el RTO en GRS?

**Objetivo de punto de recuperación (RPO):** En GRS y RA-GRS, el servicio de almacenamiento realiza un replicación geográfica asincrónica de los datos, desde la ubicación primaria a la secundaria. Si la región primaria deja de estar disponible, puede ejecutar una conmutación por error (versión preliminar) en la cuenta que tenga como destino la región secundaria. Al iniciar una conmutación por error, podrían perderse los cambios recientes que aún no se hayan replicado geográficamente. El número de minutos de posibles pérdidas de datos se conoce como el objetivo de punto de recuperación (RPO). El RPO indica el punto en el tiempo al que se pueden recuperar los datos. Normalmente, Azure Storage tiene un RPO inferior a 15 minutos, aunque actualmente no hay ningún contrato de nivel de servicio sobre cuánto tiempo tarda la replicación geográfica.

**Objetivo de tiempo de recuperación (RTO):** Se trata de una medida que indica cuánto tiempo tarda en realizarse la conmutación por error y tener la cuenta de almacenamiento de nuevo en línea. El tiempo para realizar la conmutación por error incluye lo siguiente:

- El tiempo que transcurre hasta que el cliente inicia la conmutación por error de la cuenta de almacenamiento desde la región primaria a la región secundaria.
- El tiempo necesario para que Azure pueda realizar la conmutación por error y cambiar las entradas DNS principales para que apunten a la ubicación secundaria.

## <a name="paired-regions"></a>Regiones emparejadas

Cuando crea una cuenta de almacenamiento, selecciona la región principal de la cuenta. La región secundaria emparejada se determina según la región primaria y no es posible cambiarla. Para información actualizada sobre las regiones que admite Azure, consulte [Continuidad empresarial y recuperación ante desastres (BCDR): regiones emparejadas de Azure](../../best-practices-availability-paired-regions.md).

## <a name="see-also"></a>Consulte también

- [Replicación de Azure Storage](storage-redundancy.md)
- [Almacenamiento con redundancia local (LRS): redundancia de datos de bajo costo para Azure Storage](storage-redundancy-lrs.md)
- [Almacenamiento con redundancia de zona (ZRS): aplicaciones de Azure Storage de alta disponibilidad](storage-redundancy-zrs.md)
