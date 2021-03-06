---
title: Supervisión de Azure Cosmos DB | Microsoft Docs
description: Obtenga información sobre cómo supervisar el rendimiento y la disponibilidad de Azure Cosmos DB.
author: bwren
services: cosmos-db
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: bwren
ms.custom: subject-monitoring
ms.openlocfilehash: c166811bbfd27691f9a01a944d304d06560b0232
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/25/2019
ms.locfileid: "75445173"
---
# <a name="monitoring-azure-cosmos-db"></a>Supervisión de Azure Cosmos DB
Si tiene aplicaciones y procesos empresariales críticos que dependen de recursos de Azure, querrá supervisar esos recursos para su disponibilidad, rendimiento y funcionamiento. En este artículo se describen los datos de supervisión generados por las bases de datos de Azure Cosmos y cómo puede usar las características de Azure Monitor para analizar y alertar sobre estos datos.

## <a name="what-is-azure-monitor"></a>¿Qué es Azure Monitor?
Azure Cosmos DB crea datos de supervisión mediante [Azure Monitor](../azure-monitor/overview.md), que es un servicio de supervisión de pila completo de Azure, que proporciona un conjunto completo de características para supervisar los recursos de Azure, además de los recursos locales y en otras nubes. 

Si no está familiarizado con la supervisión de los servicios de Azure, comience con el artículo [Supervisión de recursos de Azure con Azure Monitor](../azure-monitor/insights/monitor-azure-resource.md) que describe lo siguiente:

- ¿Qué es Azure Monitor?
- Costos asociados con la supervisión
- Datos de supervisión recopilados en Azure
- Configuración de la recolección de datos
- Herramientas estándar en Azure para analizar datos de supervisión y alertar sobre ellos

Las secciones siguientes se basan en este artículo, donde se describen los datos específicos recopilados de Azure Cosmos DB y se proporcionan ejemplos para configurar la recopilación de datos y el análisis de estos datos con herramientas de Azure.

## <a name="azure-monitor-for-cosmos-db-preview"></a>Azure Monitor para Cosmos DB (versión preliminar)
[Azure Monitor para Azure Cosmos DB](../azure-monitor/insights/cosmosdb-insights-overview.md) se basa en la [característica de libros de Azure Monitor](../azure-monitor/app/usage-workbooks.md) y utiliza los mismos datos de supervisión recopilados para Cosmos DB que se describen en las secciones siguientes. Use esta herramienta para una vista del rendimiento general, los errores, la capacidad y el estado operativo de todos los recursos de Azure Cosmos DB en una experiencia interactiva unificada y aproveche las otras características de Azure Monitor para el análisis detallado y las alertas. 

![Azure Monitor para Cosmos DB](media/monitor-cosmos-db/azure-monitor-cosmos-db.png)

## <a name="monitoring-data-collected-from-azure-cosmos-db"></a>Supervisión de datos recopilados desde Azure Cosmos DB

Azure Cosmos DB recopila los mismos tipos de datos de supervisión que otros recursos de Azure, que se describen en [Supervisión de datos de recursos de Azure](../azure-monitor/insights/monitor-azure-resource.md#monitoring-data). Consulte [Referencia de datos de supervisión de Azure Cosmos DB](monitor-cosmos-db-reference.md) para una referencia detallada de los registros y las métricas creados por Azure Cosmos DB.

La página **Información general** de Azure Portal para cada base de datos de Azure Cosmos incluye una breve vista del uso de la base de datos, incluidos su solicitud y el uso de facturación por hora. Esta información es útil, pero solo una pequeña cantidad de datos de supervisión está disponible. Algunos de estos datos se recopilan automáticamente y están disponibles para su análisis en cuanto se crea la base de datos, mientras que se puede habilitar la recopilación de datos adicional con alguna configuración.

![Página de información general](media/monitor-cosmos-db/overview-page.png)

## <a name="analyzing-metric-data"></a>Analizar datos de métricas

Azure Cosmos DB proporciona una experiencia personalizada para trabajar con métricas. Consulte [Supervisión y depuración de métricas de Azure Cosmos DB de Azure Monitor](cosmos-db-azure-monitor-metrics.md) para obtener información detallada sobre el uso de esta experiencia y para analizar diferentes escenarios de Azure Cosmos DB.

Puede analizar las métricas de Azure Cosmos DB con métricas de otros servicios de Azure mediante el explorador de métricas abriendo **Métricas** en el menú de **Azure Monitor**. Consulte [Introducción al explorador de métricas de Azure](../azure-monitor/platform/metrics-getting-started.md) para más información sobre esta herramienta. Todas las métricas de Azure Cosmos DB se encuentran en el espacio de nombres de las **métricas estándar de Cosmos DB**. Puede usar las siguientes dimensiones con estas métricas cuando agregue un filtro a un gráfico:

- CollectionName
- DatabaseName
- OperationType
- Region
- StatusCode


## <a name="analyzing-log-data"></a>Analizar datos de registro
Los datos de los registros de Azure Monitor se almacenan en tablas; cada tabla tiene su propio conjunto de propiedades únicas. Azure Cosmos DB almacena los datos en las tablas siguientes.

| Tabla | Descripción |
|:---|:---|
| AzureDiagnostics | Tabla común que utilizan varios servicios para almacenar registros de recursos. Los registros de recursos de Azure Cosmos DB se pueden identificar con `MICROSOFT.DOCUMENTDB`.   |
| AzureActivity    | Tabla común que almacena todos los registros del registro de actividad. 


> [!IMPORTANT]
> Al seleccionar **Registros** en el menú de Azure Machine Learning, Log Analytics se abre con el ámbito de la consulta establecido en la base de datos de Azure Cosmos actual. Esto significa que las consultas de registro solo incluirán datos de ese recurso. Si desea ejecutar una consulta que incluya datos de otras bases de datos o de otros servicios de Azure, elija **Registros** en el menú **Azure Monitor**. Consulte [Ámbito e intervalo de tiempo de una consulta de registro en Log Analytics de Azure Monitor](../azure-monitor/log-query/scope.md) para obtener más información.

### <a name="azure-cosmos-db-log-analytics-queries-in-azure-monitor"></a>Consultas de Log Analytics de Azure Cosmos DB en Azure Monitor

Estas son algunas consultas que puede escribir en la barra de búsqueda **Búsqueda de registros** para ayudarle a supervisar los contenedores de Azure Cosmos. Estas consultas funcionan con el [nuevo lenguaje](../log-analytics/log-analytics-log-search-upgrade.md).

A continuación se muestran las consultas que puede usar para supervisar las bases de datos de Azure Cosmos.

* Para consultar todos los registros de diagnóstico de Azure Cosmos DB durante el período de tiempo especificado:

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"

    ```

* Para consultar los últimos 10 eventos registrados:

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | limit 10
    ```

* Para consultar todas las operaciones, agrupadas por tipo de operación:

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | summarize count() by OperationName
    ```

* Para consultar todas las operaciones, agrupadas por recurso:

    ```Kusto
    AzureActivity 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | summarize count() by Resource

    ```

* Para consultar toda la actividad de los usuarios, agrupada por recurso:

    ```Kusto
    AzureActivity 
    | where Caller == "test@company.com" and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | summarize count() by Resource
    ```
* Para obtener todas las consultas mayores que cien RU unidas con datos de **DataPlaneRequests** y **QueryRunTimeStatistics**.

    ```Kusto
    AzureDiagnostics
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" and todouble(requestCharge_s) > 100.0
    | project activityId_g, requestCharge_s
    | join kind= inner (
           AzureDiagnostics
           | where ResourceProvider =="MICROSOFT.DOCUMENTDB" and Category == "QueryRuntimeStatistics"
           | project activityId_g, querytext_s
    ) on $left.activityId_g == $right.activityId_g
    | order by requestCharge_s desc
    | limit 100
    ```

* Para consultar qué operaciones tardan más de tres milisegundos:

    ```Kusto
    AzureDiagnostics 
    | where toint(duration_s) > 3 and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | summarize count() by clientIpAddress_s, TimeGenerated
    ```

* Para consultar qué agente está ejecutando las operaciones:

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | summarize count() by OperationName, userAgent_s
    ```

* Para consultar cuándo se realizaron las operaciones de larga duración:

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | project TimeGenerated , duration_s 
    | summarize count() by bin(TimeGenerated, 5s)
    | render timechart
    ```
    
* Para obtener estadísticas de clave de partición para evaluar el sesgo entre las tres primeras particiones para la cuenta de base de datos:

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="PartitionKeyStatistics" 
    | project SubscriptionId, regionName_s, databaseName_s, collectionname_s, partitionkey_s, sizeKb_s, ResourceId 
    ```

## <a name="monitor-azure-cosmos-db-programmatically"></a>Supervisión de Azure Cosmos DB mediante programación
Las métricas de nivel de cuenta disponibles en el portal, como el uso de almacenamiento de cuenta y el total de solicitudes, no están disponibles mediante las API de SQL. Sin embargo, puede recuperar datos de uso en el nivel de colección mediante las API de SQL. Para recuperar datos de nivel de colección, haga lo siguiente:

* Para usar la API de REST, [ejecute una operación GET en la colección](https://msdn.microsoft.com/library/mt489073.aspx). La información de cuota y uso de la colección se devuelve en los encabezados x-ms-resource-quota y x-ms-resource-usage de la respuesta.
* Para usar el SDK de .NET, use el método [DocumentClient.ReadDocumentCollectionAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.readdocumentcollectionasync.aspx), que devuelve un objeto [ResourceResponse](https://msdn.microsoft.com/library/dn799209.aspx) que contiene varias propiedades de uso, como **CollectionSizeUsage**, **DatabaseUsage**, **DocumentUsage** y otras más.

Para acceder a métricas adicionales, use el [SDK de Azure Monitor](https://www.nuget.org/packages/Microsoft.Azure.Insights). Se pueden recuperar definiciones de métricas mediante la llamada a:

    https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/metricDefinitions?api-version=2015-04-08

Las consultas para recuperar métricas individuales utilizan el siguiente formato:

    https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/metrics?api-version=2015-04-08&$filter=%28name.value%20eq%20%27Total%20Requests%27%29%20and%20timeGrain%20eq%20duration%27PT5M%27%20and%20startTime%20eq%202016-06-03T03%3A26%3A00.0000000Z%20and%20endTime%20eq%202016-06-10T03%3A26%3A00.0000000Z



## <a name="next-steps"></a>Pasos siguientes

- Consulte [Referencia de datos de supervisión de Azure Cosmos DB](monitor-cosmos-db-reference.md) para una referencia de los registros y las métricas creados por Azure Cosmos DB.
- Para más información sobre la supervisión de recursos de Azure, consulte [Supervisión de recursos de Azure con Azure Monitor](../azure-monitor/insights/monitor-azure-resource.md).
