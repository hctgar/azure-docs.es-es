---
title: Traslado de una dirección IP pública de Azure a otra región de Azure mediante Azure PowerShell
description: Use una plantilla de Azure Resource Manager para trasladar una dirección IP pública de Azure de una región de Azure a otra mediante Azure PowerShell.
author: asudbring
ms.service: virtual-network
ms.topic: article
ms.date: 08/29/2019
ms.author: allensu
ms.openlocfilehash: c55b6011381d385fed7c7b8175ff02ec9be66fdb
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/03/2020
ms.locfileid: "75641594"
---
# <a name="move-azure-public-ip-to-another-region-using-azure-powershell"></a>Traslado de una dirección IP pública de Azure a otra región mediante Azure PowerShell

Hay varios escenarios en los que puede que deba mover sus direcciones IP públicas de Azure actuales de una región a otra. Por ejemplo, quiere crear una dirección IP pública con la misma configuración y SKU para las pruebas. También quiere mover una dirección IP pública a otra región como parte del planeamiento de la recuperación ante desastres.

Las direcciones IP públicas de Azure son específicas de la región y no se pueden migrar de una región a otra. Sin embargo, puede usar una plantilla de Azure Resource Manager para exportar la configuración actual de una dirección IP pública.  Después, puede preparar el recurso en otra región exportando la dirección IP pública a una plantilla y modificando los parámetros para que coincidan con la región de destino, y luego implementar la plantilla en la nueva región.  Para más información sobre Resource Manager y sus plantillas, consulte [Exportación de grupos de recursos a plantillas](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates).


## <a name="prerequisites"></a>Prerequisites

- Asegúrese de que la dirección IP pública de Azure se encuentra en la región de Azure desde la que va a moverla.

- Las direcciones IP públicas de Azure no se pueden migrar entre regiones.  Tendrá que asociar la nueva dirección IP pública a los recursos de la región de destino.

- Para exportar una configuración de IP pública e implementar una plantilla para crear una dirección IP pública en otra región, necesitará el rol de colaborador de red u otro superior.
   
- Identifique el diseño de red de origen y todos los recursos que está usando actualmente. Este diseño incluye, pero no se limita a, los equilibradores de carga, los grupos de seguridad de red (NSG) y las redes virtuales.

- Compruebe que su suscripción de Azure permite crear direcciones IP públicas en la región de destino que se usa. Para habilitar la cuota necesaria, póngase en contacto con el soporte técnico.

- Asegúrese de que la suscripción tiene suficientes recursos para admitir la adición de direcciones IP públicas para este proceso.  Vea [Límites, cuotas y restricciones de suscripción y servicios de Microsoft Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits).


## <a name="prepare-and-move"></a>Preparación y traslado
En los pasos siguientes se muestra cómo preparar la dirección IP pública para trasladar la configuración mediante una plantilla de Resource Manager y cómo trasladar la configuración de IP pública a la región de destino mediante Azure PowerShell.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-template-and-deploy-from-a-script"></a>Exportación de la plantilla e implementación desde un script

1. Inicie sesión en la suscripción a Azure con el comando [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) y siga las instrucciones de la pantalla:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. Obtenga el Id. de recurso de la dirección IP pública que quiere trasladar a la región de destino y colóquelo en una variable mediante [Get-AzPublicIPAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=azps-2.6.0):

    ```azurepowershell-interactive
    $sourcePubIPID = (Get-AzPublicIPaddress -Name <source-public-ip-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. Exporte la red virtual de origen a un archivo .json en el directorio donde se ejecuta el comando [Export-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0):
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceVNETID -IncludeParameterDefaultValue
   ```

4. El nombre del archivo descargado se asignará en función del grupo de recursos desde el que se exportó el recurso.  Busque el archivo que se exportó desde el comando denominado **\<nombre-del-grupo-de-recursos>.json** y ábralo en el editor que prefiera:
   
   ```azurepowershell
   notepad <source-resource-group-name>.json
   ```

5. Para editar el parámetro del nombre de la dirección IP pública, cambie el valor **defaultValue** de la propiedad del nombre de la IP pública de origen por el nombre de la dirección IP pública de destino. Asegúrese de que el nombre se encuentra entre comillas:
    
    ```json
        {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "publicIPAddresses_myVM1pubIP_name": {
        "defaultValue": "<target-publicip-name>",
        "type": "String"
        }
    }

    ```

6. Para editar la región de destino a la que se va a trasladar la dirección IP pública, cambie la propiedad **location** en resources:

    ```json
            "resources": [
            {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2019-06-01",
            "name": "[parameters('publicIPAddresses_myPubIP_name')]",
            "location": "<target-region>",
            "sku": {
                "name": "Basic",
                "tier": "Regional"
            },
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "7549a8f1-80c2-481a-a073-018f5b0b69be",
                "ipAddress": "52.177.6.204",
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Dynamic",
                "idleTimeoutInMinutes": 4,
                "ipTags": []
               }
               }
             ]             
    ```
  
7. Para obtener los códigos de ubicación de la región, puede usar el cmdlet Azure PowerShell [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) al ejecutar el siguiente comando:

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
8. También puede cambiar otros parámetros de la plantilla si así lo desea; son opcionales según sus requisitos:

    * **SKU**: puede cambiar la SKU de la dirección IP pública en la configuración del nivel estándar al básico o viceversa si modifica la propiedad **sku** > **name** en el archivo **\<resource-group-name>.json**:

         ```json
            "resources": [
                   {
                    "type": "Microsoft.Network/publicIPAddresses",
                    "apiVersion": "2019-06-01",
                    "name": "[parameters('publicIPAddresses_myPubIP_name')]",
                    "location": "<target-region>",
                    "sku": {
                        "name": "Basic",
                        "tier": "Regional"
                    },
         ```

         Para más información sobre las diferencias entre las IP públicas de la SKU básica y estándar, consulte [Creación, modificación o eliminación de una dirección IP pública](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address).

    * **Método de asignación de IP pública** y **tiempo de espera de inactividad**: puede cambiar estas dos opciones en la plantilla si cambia la propiedad **publicIPAllocationMethod** de **Dynamic** a **Static** o bien de **Static** a **Dynamic**. El tiempo de espera de inactividad se puede cambiar modificando la propiedad **idleTimeoutInMinutes** con la cantidad deseada.  El valor predeterminado es **4**:

         ```json
         "resources": [
                {
                "type": "Microsoft.Network/publicIPAddresses",
                "apiVersion": "2019-06-01",
                "name": "[parameters('publicIPAddresses_myPubIP_name')]",
                "location": "<target-region>",
                  "sku": {
                  "name": "Basic",
                  "tier": "Regional"
                 },
                "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "7549a8f1-80c2-481a-a073-018f5b0b69be",
                "ipAddress": "52.177.6.204",
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Dynamic",
                "idleTimeoutInMinutes": 4,
                "ipTags": []
                   }
                }            
         ```

        Para más información sobre los métodos de asignación y los valores de tiempo de espera de inactividad, consulte [Creación, modificación o eliminación de una dirección IP pública](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address).


9. Guarde el archivo **\<nombre-del-grupo-de-recursos>.json**.

10. Cree un grupo de recursos en la región de destino para la dirección IP pública de destino que se va a implementar mediante [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0).
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
11. Implemente el archivo **\<nombre-del-grupo-de-recursos>.json** editado en el grupo de recursos que creó en el paso anterior mediante [New-AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment?view=azps-2.6.0):

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```

12. Para comprobar que los recursos se crearon en la región de destino, use los comandos [Get-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup?view=azps-2.6.0) y [Get-AzPublicIPAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=azps-2.6.0):
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzPublicIPAddress -Name <target-publicip-name> -ResourceGroupName <target-resource-group-name>

    ```
## <a name="discard"></a>Discard (Descartar) 

Después de la implementación, si quiere empezar de nuevo o descartar la dirección IP pública en el destino, elimine el grupo de recursos que se creó en el destino y se eliminará la dirección IP pública trasladada.  Para quitar el grupo de recursos, use [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0):

```azurepowershell-interactive

Remove-AzResourceGroup -Name <target-resource-group-name>

```

## <a name="clean-up"></a>Limpieza

Para confirmar los cambios y completar el traslado de la red virtual, elimine la red virtual o el grupo de recursos de origen mediante [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0) o [Remove-AzPublicIPAddress](https://docs.microsoft.com/powershell/module/az.network/remove-azpublicipaddress?view=azps-2.6.0):

```azurepowershell-interactive

Remove-AzResourceGroup -Name <source-resource-group-name>

```

``` azurepowershell-interactive

Remove-AzPublicIpAddress -Name <source-publicip-name> -ResourceGroupName <resource-group-name>

```

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha movido una dirección IP pública de Azure de una región a otra y ha limpiado los recursos de origen.  Para obtener más información sobre cómo trasladar recursos entre regiones y la recuperación ante desastres en Azure, consulte:


- [Traslado de los recursos a un nuevo grupo de recursos o a una nueva suscripción](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Traslado de máquinas virtuales de Azure a otra región](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
