---
title: 'Ejemplo de PowerShell: aplicaciones de proxy de aplicación con dominios personalizados'
description: Ejemplo de PowerShell en el que se enumeran todas las aplicaciones de proxy de aplicación de Azure Active Directory (Azure AD) que usan dominios personalizados e información de certificados.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 12/05/2019
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9722178011c3c0411f2e0e888dd9ab5962b933b9
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/25/2019
ms.locfileid: "75475622"
---
# <a name="get-all-application-proxy-apps-using-custom-domains-and-certificate-information"></a>Obtención de todas las aplicaciones de Application Proxy con dominios personalizados e información de certificados

En este script de PowerShell de ejemplo se enumeran todas las aplicaciones de proxy de aplicación de Azure Active Directory (Azure AD) que usan dominios personalizados y la información de certificados asociada a los dominios personalizados.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Este ejemplo requiere el [módulo AzureAD V2 PowerShell para Graph](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0) (AzureAD) o la [versión preliminar del módulo AzureAD V2 PowerShell para Graph](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) (AzureADPreview).

## <a name="sample-script"></a>Script de ejemplo

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-all-custom-domains-and-certs.ps1 "Get all Application Proxy apps using custom domains and certificate information")]

## <a name="script-explanation"></a>Explicación del script

| Get-Help | Notas |
|---|---|
|[Get-AzureADServicePrincipal](https://docs.microsoft.com/powershell/module/azuread/get-azureadserviceprincipal?view=azureadps-2.0) | Obtiene una entidad de servicio. |
|[Get-AzureADApplication](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplication?view=azureadps-2.0) | Obtiene una aplicación de Azure AD. |
|[Get-AzureADApplicationProxyApplication](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplicationproxyapplication?view=azureadps-2.0) | Recupera una aplicación configurada para el proxy de aplicación en Azure AD. |

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre el módulo de Azure AD PowerShell, vea [Información general del módulo de Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0).

Para obtener otros ejemplos de PowerShell para Application Proxy, vea [Ejemplos de Azure AD PowerShell para Azure AD Application Proxy](../application-proxy-powershell-samples.md).