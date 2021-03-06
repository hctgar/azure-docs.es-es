---
title: Protección de API mediante la autenticación de certificados de cliente en API Management
titleSuffix: Azure API Management
description: Obtenga información acerca de cómo proteger el acceso a las API mediante certificados de cliente.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 05/30/2019
ms.author: apimpm
ms.openlocfilehash: 85eeaaa052604c3198ca2ab8988f9e7a77e2a63d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/25/2019
ms.locfileid: "75430648"
---
# <a name="how-to-secure-apis-using-client-certificate-authentication-in-api-management"></a>Protección de API mediante la autenticación de certificados de cliente en API Management

API Management proporciona la capacidad de proteger el acceso a las API (es decir, de cliente a API Management) mediante certificados de cliente. Puede validar el certificado entrante y comprobar las propiedades del certificado con los valores deseados mediante expresiones de directiva.

Para información acerca de cómo proteger el acceso al servicio de back-end de una API mediante certificados de cliente (por ejemplo, de API Management para back-end), consulte [Cómo asegurar servicios back-end con la autenticación de certificados de cliente en Azure API Management](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)

> [!IMPORTANT]
> Para recibir y comprobar los certificados de cliente en el nivel Consumo, debe activar la opción "Solicitar certificado de cliente" en la hoja "Dominios personalizados", tal como se muestra a continuación.

![Solicitar certificado de cliente](./media/api-management-howto-mutual-certificates-for-clients/request-client-certificate.png)

## <a name="checking-the-issuer-and-subject"></a>Comprobación del emisor y el asunto

Es posible configurar las directivas siguientes para comprobar el emisor y el firmante de un certificado de cliente:

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Request.Certificate.Verify() || context.Request.Certificate.Issuer != "trusted-issuer" || context.Request.Certificate.SubjectName.Name != "expected-subject-name")" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

> [!NOTE]
> Para deshabilitar la comprobación de la lista de revocación de certificados, use `context.Request.Certificate.VerifyNoRevocation()` en lugar de `context.Request.Certificate.Verify()`.
> Si el certificado de cliente es un certificado autofirmado, los certificados CA raíz (o intermedios) deben [cargarse](api-management-howto-ca-certificates.md) en API Management para que `context.Request.Certificate.Verify()` y `context.Request.Certificate.VerifyNoRevocation()` funcionen.

## <a name="checking-the-thumbprint"></a>Comprobación de la huella digital

Es posible configurar las directivas siguientes para comprobar la huella digital de un certificado de cliente:

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Request.Certificate.Verify() || context.Request.Certificate.Thumbprint != "desired-thumbprint")" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

> [!NOTE]
> Para deshabilitar la comprobación de la lista de revocación de certificados, use `context.Request.Certificate.VerifyNoRevocation()` en lugar de `context.Request.Certificate.Verify()`.
> Si el certificado de cliente es un certificado autofirmado, los certificados CA raíz (o intermedios) deben [cargarse](api-management-howto-ca-certificates.md) en API Management para que `context.Request.Certificate.Verify()` y `context.Request.Certificate.VerifyNoRevocation()` funcionen.

## <a name="checking-a-thumbprint-against-certificates-uploaded-to-api-management"></a>Comprobación de una huella digital en relación con certificados cargados en API Management

En el ejemplo siguiente se muestra cómo comprobar la huella digital de un certificado de cliente en relación con los certificados cargados en API Management:

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Request.Certificate.Verify()  || !context.Deployment.Certificates.Any(c => c.Value.Thumbprint == context.Request.Certificate.Thumbprint))" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>

```

> [!NOTE]
> Para deshabilitar la comprobación de la lista de revocación de certificados, use `context.Request.Certificate.VerifyNoRevocation()` en lugar de `context.Request.Certificate.Verify()`.
> Si el certificado de cliente es un certificado autofirmado, los certificados CA raíz (o intermedios) deben [cargarse](api-management-howto-ca-certificates.md) en API Management para que `context.Request.Certificate.Verify()` y `context.Request.Certificate.VerifyNoRevocation()` funcionen.

> [!TIP]
> El problema de interbloqueo de certificados de cliente descrito en este [artículo](https://techcommunity.microsoft.com/t5/Networking-Blog/HTTPS-Client-Certificate-Request-freezes-when-the-Server-is/ba-p/339672) puede manifestarse de varias maneras, por ejemplo, las solicitudes se inmovilizan, las solicitudes resultan en un código de estado `403 Forbidden` después de agotar el tiempo, `context.Request.Certificate` es `null`. Este problema afecta normalmente a las solicitudes `POST` y `PUT` con la longitud del contenido de aproximadamente 60 KB o más.
> Para evitar que este problema se produzca, active "Negociar certificado de cliente" para los nombres de host deseados en la hoja "Dominios personalizados", tal como se muestra a continuación. Esta característica no está disponible en el nivel Consumo.

![Negociación del certificado de cliente](./media/api-management-howto-mutual-certificates-for-clients/negotiate-client-certificate.png)

## <a name="next-steps"></a>Pasos siguientes

-   [Cómo asegurar servicios back-end con la autenticación de certificados de cliente](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)
-   [¿Cómo se cargan certificados?](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)
