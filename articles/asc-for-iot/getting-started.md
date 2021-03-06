---
title: Introducción al uso de Azure Security Center para IoT | Microsoft Docs
description: Primeros pasos para entender el flujo de trabajo básico de las características y el servicio Azure Security Center for IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 55c8d3b6-3126-4246-8d07-ef88fe5ea84f
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 86c6c0bac5caae0873d0067c6abcb5a8ac864c88
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/29/2019
ms.locfileid: "68596408"
---
# <a name="get-started-with-azure-security-center-for-iot"></a>Introducción a Azure Security Center for IoT

En este artículo se explican los diferentes bloques de creación del servicio Azure Security Center para IoT. También se explica cómo empezar a usar el servicio con dos posibles opciones de implementación.  

## <a name="deployment-options"></a>Opciones de implementación

Elija el escenario del servicio que mejor encaje con sus requisitos de entorno y de dispositivo IoT. 

### <a name="built-in-deployment"></a>Implementación integrada
Con la opción de la implementación integrada y directa, Azure Security Center para IoT se puede integrar rápidamente en IoT Hub y proporcionar análisis de seguridad de la configuración de IoT Hub, la identidad y administración de los dispositivos y los patrones de comunicación entre el centro y el dispositivo.

Inicie una [implementación integrada](iot-hub-integration.md) que ofrezca recomendaciones y supervisión de IoT Hub. 
    <br>

### <a name="enhanced-deployment"></a>Implementación mejorada
Para mejorar las funcionalidades de seguridad, la implementación de los agentes de Azure Security Center para IoT además de habilitar la seguridad de IoT ofrece una recopilación de eventos basada en el agente, análisis y detección de amenazas de los principales datos de seguridad de sus dispositivos IoT y funcionalidades de administración de la posición completa de seguridad.

Inicie una [implementación mejorada](security-agents.md) que incluya una solución de administración de la posición de seguridad y la protección total de amenazas basada en un agente.
   

## <a name="next-steps"></a>Pasos siguientes

- Habilitar [Azure Security Center para IoT](quickstart-onboard-iot-hub.md)
- Configurar la [solución](quickstart-configure-your-solution.md)
- [Creación de módulos de seguridad](quickstart-create-security-twin.md)
- Configurar [alertas personalizadas](quickstart-create-custom-alerts.md)
- [Implementación de un agente de seguridad](how-to-deploy-agent.md)
