---
title: 'Ejecución de Azure Container Instances: servicio de voz'
titleSuffix: Azure Cognitive Services
description: Implemente el contenedor de servicio de voz en una instancia de Azure Container Instances y pruébelo en un explorador web.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 7/5/2019
ms.author: dapine
ms.openlocfilehash: 690a45ad7f009a65686e27eba5095a5a2870039c
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/04/2019
ms.locfileid: "74806276"
---
# <a name="deploy-the-speech-service-container-to-azure-container-instances"></a>Implementación del contenedor del servicio de voz en Azure Container Instances

Aprenda a implementar el contenedor del [servicio de voz](speech-container-howto.md) de Cognitive Services en una instancia de [Azure Container Instances](https://docs.microsoft.com/azure/container-instances/). Este procedimiento muestra la creación de un recurso del servicio de voz de Azure. Luego se trata la extracción de la imagen de contenedor asociada. Por último, se resalta la posibilidad de aprovechar la orquestación de los dos desde un explorador. El uso de contenedores puede desviar la atención de los desarrolladores de la administración de la infraestructura y centrarla en el desarrollo de aplicaciones.

[!INCLUDE [Prerequisites](../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-container-registry"></a>Solicitud de acceso al registro de contenedor

Primero debe completar y enviar el [formulario de solicitud de contenedores del servicio Voz de Cognitive Services](https://aka.ms/speechcontainerspreview/) para solicitar acceso al contenedor. 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Create a Cognitive Services Speech service resource](includes/create-speech-resource.md)]

[!INCLUDE [Create a Speech service container on Azure Container Instances](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../containers/includes/containers-next-steps.md)]
