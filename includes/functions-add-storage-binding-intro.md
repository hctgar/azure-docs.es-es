---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/29/2019
ms.author: glenga
ms.openlocfilehash: f16852cdc18053a3a8e375dc6f14e39bb069afef
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/15/2019
ms.locfileid: "72329611"
---
Azure Functions le permite conectar servicios de Azure y otros recursos a funciones sin tener que escribir su propio código de integración. Estos *enlaces*, que representan la entrada y la salida, se declaran dentro de la definición de función. Los datos de los enlaces se proporcionan a la función como parámetros. Un *desencadenador* es un tipo especial de enlace de entrada. Si bien una función tiene un único desencadenador, puede tener varios enlaces de entrada y salida. Para más información, consulte [Conceptos básicos sobre los enlaces y desencadenadores de Azure Functions](../articles/azure-functions/functions-triggers-bindings.md).