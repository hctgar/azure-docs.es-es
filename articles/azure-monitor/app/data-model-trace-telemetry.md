---
title: 'Modelo de datos de Azure Application Insights: telemetría de seguimiento'
description: Modelo de datos de Application Insights para la telemetría de seguimientos
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.openlocfilehash: 6e188039a86f4c655df3098be1d769668dcf3571
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/25/2019
ms.locfileid: "75407132"
---
# <a name="trace-telemetry-application-insights-data-model"></a>Telemetría de seguimiento: Modelo de datos de Application Insights

La telemetría de seguimientos (en [Application Insights](../../azure-monitor/app/app-insights-overview.md)) representa instrucciones de seguimiento de estilo `printf` en las que se pueden realizar búsquedas de texto. `Log4Net`, `NLog` y las demás entradas de archivo de registro basadas en texto se convierten a instancias de este tipo. El seguimiento no tiene medidas como una extensibilidad.

## <a name="message"></a>Message

Mensaje de seguimiento.

Longitud máxima: 32768 caracteres

## <a name="severity-level"></a>Nivel de gravedad

El nivel de gravedad del seguimiento. El valor puede ser `Verbose`, `Information`, `Warning`, `Error`, `Critical`.

## <a name="custom-properties"></a>Propiedades personalizadas

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="next-steps"></a>Pasos siguientes

- [Exploración de los registros de seguimiento de .NET en Application Insights](../../azure-monitor/app/asp-net-trace-logs.md).
- [Exploración de los registros de seguimiento de Java en Application Insights](../../azure-monitor/app/java-trace-logs.md).
- Consulte [modelo de datos](data-model.md) para los tipos y el modelo de datos de Application Insights.
- [Escritura de telemetría de seguimiento personalizada](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace)
- Consulte las [plataformas](../../azure-monitor/app/platforms.md) compatibles con Application Insights.
