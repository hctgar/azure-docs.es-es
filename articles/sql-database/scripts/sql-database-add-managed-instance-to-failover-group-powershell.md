---
title: 'Ejemplo de PowerShell: grupo de conmutación por error en una instancia administrada de Azure SQL Database'
description: Script de ejemplo de Azure PowerShell para crear una instancia administrada de Azure SQL Database, agregarla a un grupo de conmutación por error y probar la conmutación por error.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 07/16/2019
ms.openlocfilehash: e50877f6f3194885b139683fe865144384716b48
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/06/2019
ms.locfileid: "73691764"
---
# <a name="use-powershell-to-add-an-azure-sql-database-managed-instance-to-a-failover-group"></a>Uso de PowerShell para agregar una instancia administrada de Azure SQL Database a un grupo de conmutación por error 

Este script de ejemplo de PowerShell crea dos instancias administradas, las agrega a un grupo de conmutación por error y, a continuación, prueba la conmutación por error de la instancia administrada principal en la instancia administrada secundaria. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Si decide instalar y usar PowerShell de manera local, en este tutorial se requiere la versión 1.4.0 de AZ PowerShell o posterior. Si necesita actualizarla, consulte [Instalación del módulo de Azure PowerShell](/powershell/azure/install-az-ps). Si PowerShell se ejecuta localmente, también debe ejecutar `Connect-AzAccount` para crear una conexión con Azure.

## <a name="sample-scripts"></a>Scripts de ejemplo

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/failover-groups/add-managed-instance-to-failover-group-az-ps.ps1 "Add managed instance to a failover group")]

## <a name="clean-up-deployment"></a>Limpieza de la implementación

Use el siguiente comando para quitar el grupo de recursos y todos los recursos que tenga asociados. Tendrá que quitar el grupo de recursos dos veces. Al quitar el grupo de recursos la primera vez, se quitarán la instancia administrada y los clústeres virtuales, pero se producirá el mensaje de error `Remove-AzResourceGroup : Long running operation failed with status 'Conflict'.`. Ejecute el comando Remove-AzResourceGroup una segunda vez para quitar los recursos residuales y el grupo de recursos.

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>Explicación del script

Este script usa los siguientes comandos. Cada comando de la tabla crea un vínculo a documentación específica del comando.

| Get-Help | Notas |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Crea un grupo de recursos de Azure.  |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Crea una red virtual.  |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Agrega una configuración de subred a una red virtual. | 
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Obtiene una red virtual en un grupo de recursos. | 
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Obtiene una subred en una red virtual. | 
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | Crea un grupo de seguridad de red. | 
| [New-AzRouteTable](/powershell/module/az.network/new-azroutetable) | Crea una tabla de rutas. |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | Actualiza una configuración de subred para una red virtual.  |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Actualiza una red virtual.  |
| [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) | Obtiene un grupo de seguridad de red. |
| [Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| Agrega una configuración de regla de seguridad de red a un grupo de seguridad de red. |
| [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) | Actualiza un grupo de seguridad de red.  | 
| [Add-AzRouteConfig](/powershell/module/az.network/add-azrouteconfig) | Agrega una ruta a una tabla de rutas. |
| [Set-AzRouteTable](/powershell/module/az.network/set-azroutetable) | Actualiza una tabla de rutas.  |
| [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) | Crea una instancia administrada de Azure SQL Database.  |
| [Get-AzSqlInstance](/powershell/module/az.sql/get-azsqlinstance)| Devuelve información sobre la instancia de base de datos administrados de Azure SQL. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Crea una dirección IP pública.  | 
| [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | Crea una configuración de IP para una puerta de enlace de Virtual Network. |
| [New-AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | Crea una puerta de enlace de Virtual Network. |
| [New-AzVirtualNetworkGatewayConnection](/powershell/module/az.network/new-azvirtualnetworkgatewayconnection) | Crea una conexión entre las dos puertas de enlace de redes virtuales.   |
| [New-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup)| Crea un nuevo grupo de conmutación por error de la instancia administrada de Azure SQL Database.  |
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) | Obtiene o enumera los grupos de conmutación por error de la instancia administrada.| 
| [Switch-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) | Ejecuta una conmutación por error de un grupo de conmutación por error de la instancia administrada. | 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Elimina un grupo de recursos. | 

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre Azure PowerShell, consulte la [documentación de Azure PowerShell](/powershell/azure/overview).

Encontrará más ejemplos de scripts de PowerShell de SQL Database en los [scripts de PowerShell de Azure SQL Database](../sql-database-powershell-samples.md).
