---
title: Aplicación de transformaciones de SQL
titleSuffix: Azure Machine Learning
description: Obtenga información sobre cómo usar el módulo Aplicar la transformación de SQL en Azure Machine Learning para ejecutar una consulta SQLite en conjuntos de datos de entrada para transformar los datos.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/09/2019
ms.openlocfilehash: 2e44a4861e2522b766aab9c7151d76c471dd2d8c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/25/2019
ms.locfileid: "75456518"
---
# <a name="apply-sql-transformation"></a>Aplicación de transformaciones de SQL

En este artículo se describe un módulo del diseñador de Azure Machine Learning (versión preliminar).

Con el módulo Apply SQL Transformation (Aplicar transformación SQL), puede:
  
-   Crear tablas para los resultados y guarde los conjuntos de datos en una base de datos portátil.  
  
-   Realizar transformaciones personalizadas en tipos de datos o crear agregados.  
  
-   Ejecutar instrucciones de consulta SQL para filtrar o modificar datos y devolver los resultados de la consulta como una tabla de datos.  

> [!IMPORTANT]
> El motor de SQL que se usa en este módulo es **SQLite**. Para obtener más información sobre la sintaxis de SQLite, consulte [SQL como lo entiende SQLite](https://www.sqlite.org/index.html).  

## <a name="how-to-configure-apply-sql-transformation"></a>Configuración del módulo de aplicación de transformaciones de SQL  

El módulo puede tardar hasta tres conjuntos de datos como entradas. Al hacer referencia a los conjuntos de datos conectados a cada puerto de entrada, debe utilizar los nombres `t1`, `t2` y `t3`. El número de la tabla indica el índice del puerto de entrada.  
  
El parámetro restante es una consulta SQL, que utiliza la sintaxis de SQLite. Al escribir varias líneas en el cuadro de texto **SQL Script** (Script de SQL), utilice un punto y coma para terminar cada instrucción. De lo contrario, los saltos de línea se convierten en espacios.  

Este módulo admite todas las instrucciones estándar de la sintaxis de SQLite. Para obtener una lista de instrucciones no admitidas, consulte la sección [Notas técnicas](#technical-notes).

##  <a name="technical-notes"></a>Notas técnicas  

Esta sección contiene detalles de implementación, sugerencias y respuestas a las preguntas más frecuentes.

-   Siempre se requiere una entrada en el puerto 1.  
  
-   En el caso de los identificadores de columna que contienen un espacio u otros caracteres especiales, incluya siempre el identificador de columna entre corchetes o comillas dobles al hacer referencia a la columna en las cláusulas `SELECT` o `WHERE`.  
  
### <a name="unsupported-statements"></a>Instrucciones no admitidas  

Aunque SQLite admite gran parte del estándar ANSI SQL, no incluye muchas características compatibles con los sistemas de bases de datos relacionales comerciales. Para más información, consulte [SQL como lo entiende SQLite](http://www.sqlite.org/lang.html). Además, tenga en cuenta las siguientes restricciones al crear instrucciones SQL:  
  
- SQLite usa la escritura dinámica para los valores, en lugar de asignar un tipo a una columna como en la mayoría de los sistemas de bases de datos relacionales. Tiene un establecimiento flexible de tipos y permite la conversión implícita de tipos.  
  
- `LEFT OUTER JOIN` está implementado, pero no `RIGHT OUTER JOIN` ni `FULL OUTER JOIN`.  

- Puede utilizar las instrucciones `RENAME TABLE` y `ADD COLUMN` con el comando `ALTER TABLE`, pero no se admiten otras cláusulas, como `DROP COLUMN`, `ALTER COLUMN` y `ADD CONSTRAINT`.  
  
- Puede crear una vista dentro de SQLite, pero las vistas posteriores son de solo lectura. No se puede ejecutar una instrucción `DELETE`, `INSERT` o `UPDATE` en una vista. Sin embargo, puede crear un desencadenador que se active al intentar `DELETE`, `INSERT` o `UPDATE` en una vista y realizar otras operaciones en el cuerpo del desencadenador.  
  

Además de la lista de funciones no admitidas que se proporcionan en el sitio oficial de SQLite, la siguiente wiki proporciona una lista de otras características no admitidas: [SQLite: SQL no admitido](http://www2.sqlite.org/cvstrac/wiki?p=UnsupportedSql)  
    
## <a name="next-steps"></a>Pasos siguientes

Consulte el [conjunto de módulos disponibles](module-reference.md) para Azure Machine Learning. 
