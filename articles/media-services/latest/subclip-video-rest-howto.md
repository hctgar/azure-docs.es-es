---
title: Creación de un subclip de vídeo al codificar con la API de REST de Azure Media Services
description: En este tema se describe cómo crear un subclip de vídeo al codificar mediante Azure Media Services con REST
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 06/10/2019
ms.author: juliako
ms.openlocfilehash: 3557aef6213955ef77542bffafe0a2b0c374ed68
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704445"
---
# <a name="subclip-a-video-when-encoding-with-media-services---rest"></a>Creación de un subclip de vídeo al codificar mediante Azure Media Services: REST

Puede recortar un vídeo, o crear un subclip de este al codificarlo mediante la opción [Trabajo](https://docs.microsoft.com/rest/api/media/jobs). Esta funcionalidad se puede usar con cualquier elemento [Transformación](https://docs.microsoft.com/rest/api/media/transforms) que se compila mediante los valores preestablecidos [BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) o [StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset). 

El ejemplo de REST de este tema crea un trabajo que recorta un vídeo cuando envía un trabajo de codificación. 

## <a name="prerequisites"></a>Requisitos previos

Para completar los pasos descritos en este tema, ha de:

- [Crear una cuenta de Azure Media Services](create-account-cli-how-to.md).
- [Configuración de Postman para llamadas API REST de Azure Media Services](media-rest-apis-with-postman.md).
    
    Asegúrese de seguir el último paso en el tema [Obtención del token de Azure AD](media-rest-apis-with-postman.md#get-azure-ad-token). 
- Crear una transformación y un recurso de salida. Puede ver cómo crear una transformación y un recurso de salida en el tutorial [Codificación de un archivo remoto según una dirección URL y transmisión del vídeo: REST](stream-files-tutorial-with-rest.md).
- Revisar el tema [Concepto de codificación](encoding-concept.md).

## <a name="create-a-subclipping-job"></a>Creación de un trabajo de creación de subclips

1. En la colección de Postman que descargó, seleccione **Transforms and jobs (Transformaciones y trabajos)**  -> **Create Job with Sub Clipping** (Crear un trabajo con creación de subclips).
    
    La solicitud **PUT** tiene este aspecto:
    
    ```
    https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/transforms/:transformName/jobs/:jobName?api-version={{api-version}}
    ```
1. Actualice el valor de la variable de entorno "transformName" con el nombre de la transformación. 
1. Seleccione la pestaña **Cuerpo** y actualice el objeto "myOutputAsset" con el nombre del recurso de salida.

    ```
    {
      "properties": {
        "description": "A Job with transform cb9599fb-03b3-40eb-a2ff-7ea909f53735 and single clip.",
       
        "input": {
          "@odata.type": "#Microsoft.Media.JobInputHttp",
          "baseUri": "https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/",
          "files": [
                "Ignite-short.mp4"
            ],
          "start": {
            "@odata.type": "#Microsoft.Media.AbsoluteClipTime",
            "time": "PT10S"
          },
          "end": {
            "@odata.type": "#Microsoft.Media.AbsoluteClipTime",
            "time": "PT40S"
          }
        },
      
        "outputs": [
          {
            "@odata.type": "#Microsoft.Media.JobOutputAsset",
            "assetName": "myOutputAsset"
          }
        ],
        "priority": "Normal"
      }
    }
    ```
1. Presione **Enviar**.

    En la sección **Respuesta**, verá la información sobre el trabajo que se creó y envía, así como el estado del trabajo. 

## <a name="next-steps"></a>Pasos siguientes

[Procedimiento de codificación con una transformación personalizada](custom-preset-rest-howto.md) 
