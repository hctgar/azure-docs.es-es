---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 06/07/2019
ms.author: rogarana
ms.openlocfilehash: b28427b3ede0cfaeb9e08d3c73b15ea7f2961f1b
ms.sourcegitcommit: 83df2aed7cafb493b36d93b1699d24f36c1daa45
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/22/2019
ms.locfileid: "71180073"
---
#### <a name="additional-premium-file-share-level-limits"></a>Límites del nivel de recurso compartido de archivos prémium adicionales

|Ámbito  |Destino  |
|---------|---------|
|Aumento o disminución del tamaño mínimo    |1 GiB      |
|IOPS base    |1 IOPS por GiB hasta 100 000|
|Ampliación de IOPS    |3 x IOPS por GiB hasta 100 000|
|Velocidad de salida         |60 MiB/s + 0,06 * GiB aprovisionado        |
|Velocidad de entrada| 40 MiB/s + 0,04 * GiB aprovisionado |

#### <a name="file-level-limits"></a>Límites de nivel de archivo

|Ámbito  |Archivo prémium  |Archivo estándar |
|---------|---------|---------|
|Size                  |1 TiB         |1 TiB|
|IOPS máximas por archivo     |5\.000         |1000|
|Identificadores simultáneos    |2\.000         |2\.000|
|Salida  |300 MiB/s|      Consulte los valores de rendimiento de archivo estándar|
|Entrada  |200 MiB/s| Consulte los valores de rendimiento de archivo estándar|
|Throughput| Consulte los valores de entrada y salida de archivos prémium| Hasta 60 MiB/s|
