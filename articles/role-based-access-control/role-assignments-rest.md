---
title: Incorporación o eliminación de asignaciones de roles con RBAC de Azure y la API REST
description: Aprenda a conceder acceso a recursos de Azure para usuarios, grupos, entidades de servicio e identidades administradas mediante el control de acceso basado en rol (RBAC) y la API REST.
services: active-directory
documentationcenter: na
author: rolyon
manager: mtillman
editor: ''
ms.assetid: 1f90228a-7aac-4ea7-ad82-b57d222ab128
ms.service: role-based-access-control
ms.workload: multiple
ms.tgt_pltfrm: rest-api
ms.devlang: na
ms.topic: conceptual
ms.date: 11/25/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 3c7b7dac649548b8b21cc13761009c11609c8904
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/15/2020
ms.locfileid: "75981032"
---
# <a name="add-or-remove-role-assignments-using-azure-rbac-and-the-rest-api"></a>Incorporación o eliminación de asignaciones de roles con RBAC de Azure y la API REST

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)] En este artículo se describe cómo asignar roles con la API REST.

## <a name="prerequisites"></a>Prerequisites

Para agregar o quitar asignaciones de roles, debe tener:

- Permisos `Microsoft.Authorization/roleAssignments/write` y `Microsoft.Authorization/roleAssignments/delete`, como [Administrador de acceso de usuarios](built-in-roles.md#user-access-administrator) o [propietario](built-in-roles.md#owner)

## <a name="add-a-role-assignment"></a>Adición de una asignación de roles

En RBAC, para conceder acceso es preciso agregar una asignación de roles. Para agregar una asignación de roles, use la API REST [Asignaciones de roles - Crear](/rest/api/authorization/roleassignments/create) y especifique la entidad de seguridad, la definición de roles y el ámbito. Para llamar a esta API, debe tener acceso a la operación `Microsoft.Authorization/roleAssignments/write`. Entre los roles integrados, solo se concede acceso a esta operación a [Propietario](built-in-roles.md#owner) y [Administrador de acceso de usuario](built-in-roles.md#user-access-administrator).

1. Use la API de REST [Definiciones de roles - Lista](/rest/api/authorization/roledefinitions/list) o consulte [Roles integrados](built-in-roles.md) para obtener el identificador de la definición de roles que quiere asignar.

1. Use una herramienta de GUID para generar un identificador único que se use para el identificador de asignación de roles. El identificador tiene el formato: `00000000-0000-0000-0000-000000000000`

1. Empiece con la solicitud y el cuerpo siguientes:

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentName}?api-version=2015-07-01
    ```

    ```json
    {
      "properties": {
        "roleDefinitionId": "/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}",
        "principalId": "{principalId}"
      }
    }
    ```

1. En el identificador URI, reemplace *{scope}* por el ámbito de la asignación de roles.

    | Ámbito | Tipo |
    | --- | --- |
    | `providers/Microsoft.Management/managementGroups/{groupId1}` | Grupo de administración |
    | `subscriptions/{subscriptionId1}` | Subscription |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resource group |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/ providers/microsoft.web/sites/mysite1` | Resource |

1. Reemplace *{roleAssignmentName}* por el identificador GUID de la asignación de roles.

1. En el cuerpo de la solicitud, reemplace *{scope}* por el ámbito de la asignación de roles.

    | Ámbito | Tipo |
    | --- | --- |
    | `providers/Microsoft.Management/managementGroups/{groupId1}` | Grupo de administración |
    | `subscriptions/{subscriptionId1}` | Subscription |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resource group |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/ providers/microsoft.web/sites/mysite1` | Resource |

1. Reemplace *{roleDefinitionId}* por el identificador de la definición de roles.

1. Reemplace *{principalId}* por el identificador de objeto del usuario, grupo o entidad de servicio al que se asignará el rol.

## <a name="remove-a-role-assignment"></a>Eliminación de una asignación de rol

En RBAC, para quitar el acceso hay que quitar una asignación de roles. Para quitar una asignación de roles, use la API de REST [Asignaciones de roles - Eliminar](/rest/api/authorization/roleassignments/delete). Para llamar a esta API, debe tener acceso a la operación `Microsoft.Authorization/roleAssignments/delete`. Entre los roles integrados, solo se concede acceso a esta operación a [Propietario](built-in-roles.md#owner) y [Administrador de acceso de usuario](built-in-roles.md#user-access-administrator).

1. Obtenga el identificador de asignación de roles (GUID). Este identificador se devuelve cuando se crea por primera vez la asignación de roles o puede obtenerlo enumerando las asignaciones de roles.

1. Empiece con la solicitud siguiente:

    ```http
    DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentName}?api-version=2015-07-01
    ```

1. En el identificador URI, reemplace *{scope}* por el ámbito para eliminar la asignación de roles.

    | Ámbito | Tipo |
    | --- | --- |
    | `providers/Microsoft.Management/managementGroups/{groupId1}` | Grupo de administración |
    | `subscriptions/{subscriptionId1}` | Subscription |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resource group |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/ providers/microsoft.web/sites/mysite1` | Resource |

1. Reemplace *{roleAssignmentName}* por el identificador GUID de la asignación de roles.

## <a name="next-steps"></a>Pasos siguientes

- [Lista de asignaciones de roles con RBAC de Azure y la API REST](role-assignments-list-rest.md)
- [Implementación de recursos con las plantillas de Resource Manager y la API de REST de Resource Manager](../azure-resource-manager/templates/deploy-rest.md)
- [Azure REST API Reference](/rest/api/azure/) (Referencia de API de REST en Azure)
- [Creación de roles personalizados para los recursos de Azure con la API de REST](custom-roles-rest.md)
