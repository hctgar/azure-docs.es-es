---
title: Integración con identidades administradas de Azure
description: Aprenderá a usar identidades administradas de Azure para autenticarse en Azure App Configuration y acceder a este servicio
services: azure-app-configuration
author: yegu-ms
ms.author: yegu
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/24/2019
ms.openlocfilehash: 3af13a3009886c88f1a30eab2e6cc9f498ad6e45
ms.sourcegitcommit: 2c59a05cb3975bede8134bc23e27db5e1f4eaa45
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/05/2020
ms.locfileid: "75665252"
---
# <a name="integrate-with-azure-managed-identities"></a>Integración con identidades administradas de Azure

Las [identidades administradas](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) de Azure Active Directory ayudan a simplificar la administración de secretos de su aplicación de nube. Con una identidad administrada, puede configurar el código para usar la entidad de servicio que se creó para el servicio de Azure donde se ejecuta. Usar una identidad administrada en lugar de una credencial diferente que se almacenan en Azure Key Vault o una cadena de conexión local. 

Azure App Configuration y sus bibliotecas cliente .NET Core, .NET Framework y Java Spring incluyen compatibilidad integrada con la identidad administrada. Aunque su uso no es necesario, la identidad administrada elimina la necesidad de un token de acceso que contenga los secretos. El código solo puede acceder al almacén de App Configuration mediante el punto de conexión de servicio. Puede insertar esta dirección URL en el código directamente sin preocuparse de exponer ningún secreto.

En este tutorial se muestra cómo puede aprovechar la identidad administrada para acceder a App Configuration. Se basa en la aplicación web que se introdujo en los inicios rápidos. Antes de continuar, finalice primero el tutorial [Creación de una aplicación ASP.NET Core con Azure App Configuration](./quickstart-aspnet-core-app.md).

Además, este tutorial muestra también cómo puede usar la identidad administrada junto con las referencias a Key Vault de App Configuration. Esto le permite acceder sin problemas a los secretos almacenados en Key Vault, así como a los valores de configuración de App Configuration. Si desea explorar esta funcionalidad, termine primero [Uso de referencias de Key Vault con ASP.NET Core](./use-key-vault-references-dotnet-core.md).

Para realizar los pasos de este tutorial, puede usar cualquier editor de código. [Visual Studio Code](https://code.visualstudio.com/) es una excelente opción disponible en las plataformas Windows, macOS y Linux.

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Conceder a una identidad administrada acceso a App Configuration.
> * Configurar la aplicación para usar una identidad administrada al conectarse a App Configuration.
> * También puede configurar la aplicación para que use una identidad administrada cuando se conecte a Key Vault mediante una referencia de Key Vault de App Configuration.

## <a name="prerequisites"></a>Prerequisites

Para realizar este tutorial, necesitará lo siguiente:

* [SDK de .NET Core](https://www.microsoft.com/net/download/windows).
* [Azure Cloud Shell configurado](https://docs.microsoft.com/azure/cloud-shell/quickstart).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="add-a-managed-identity"></a>Agregar una identidad administrada

Para configurar una identidad administrada en el portal, primero crea una aplicación como lo hace normalmente y, a continuación, habilita la característica.

1. Cree una instancia de App Services en [Azure Portal](https://portal.azure.com) como lo haría normalmente. Vaya a ella en el portal.

2. Desplácese hacia abajo hasta el grupo **Configuración** en el panel de navegación izquierdo y seleccione **Identidad**.

3. En la pestaña **Asignado por el sistema**, cambie **Estado** a **Activado** y seleccione **Guardar**.

4. Responda **Sí** cuando se le pida que habilite la identidad administrada asignada por el sistema.

    ![Configurar la identidad administrada en App Service](./media/set-managed-identity-app-service.png)

## <a name="grant-access-to-app-configuration"></a>Conceder acceso a App Configuration

1. En [Azure Portal](https://portal.azure.com), seleccione **Todos los recursos** y elija el almacén de App Configuration que creó en el inicio rápido.

1. Seleccione **Access Control (IAM)** .

1. En la pestaña **Comprobar el acceso**, seleccione **Agregar** en la interfaz de usuario de la tarjeta **Agregar una asignación de roles**.

1. En **Rol**, seleccione **Colaborador**. En **Asignar acceso**, seleccione **App Service** en **Identidad administrada asignada por el sistema**.

1. En **Suscripción**, seleccione su suscripción de Azure. Seleccione el recurso de App Service para la aplicación.

1. Seleccione **Guardar**.

    ![Agregar una identidad administrada](./media/add-managed-identity.png)

1. Opcional: Si también desea conceder acceso a Key Vault, siga las instrucciones de [Autenticación de Key Vault con una identidad administrada](https://docs.microsoft.com/azure/key-vault/managed-identity).

## <a name="use-a-managed-identity"></a>Uso de una identidad administrada

1. Busque la dirección URL del almacén de App Configuration. Para ello, vaya a su pantalla de configuración en Azure Portal y haga clic en la pestaña **Claves de acceso**.

1. Abra *appsettings.json*y agregue el siguiente script. Reemplace *\<service_endpoint>* , incluidos los corchetes, por la dirección URL del almacén de App Configuration. 

    ```json
    "AppConfig": {
        "Endpoint": "<service_endpoint>"
    }
    ```

1. Si desea acceder solo a los valores almacenados directamente en App Configuration, abra *Program.cs* y actualice el método `CreateWebHostBuilder` reemplazando el método `config.AddAzureAppConfiguration()`.

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(options =>
                    options.ConnectWithManagedIdentity(settings["AppConfig:Endpoint"]));
            })
            .UseStartup<Startup>();
    ```

1. Si desea usar los valores de configuración de App Configuration, así como las referencias de Key Vault, abra *Program.cs* y actualice el método `CreateWebHostBuilder` tal como se muestra a continuación. Esto crea un nuevo objeto `KeyVaultClient` mediante `AzureServiceTokenProvider` y pasa esta referencia a una llamada al método `UseAzureKeyVault`.

    ```csharp
        public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
                .ConfigureAppConfiguration((hostingContext, config) =>
                {
                    var settings = config.Build();
                    AzureServiceTokenProvider azureServiceTokenProvider = new AzureServiceTokenProvider();
                    KeyVaultClient kvClient = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
                    
                    config.AddAzureAppConfiguration(options => options.ConnectWithManagedIdentity(settings["AppConfig:Endpoint"])).UseAzureKeyVault(kvClient));
                })
                .UseStartup<Startup>();
    ```

    Ya puede acceder a las referencias de Key Vault de igual forma que a cualquier otra clave de App Configuration. El proveedor de configuración usará el objeto `KeyVaultClient` que configuró para autenticarse en Key Vault y recuperará el valor.

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-from-local-git"></a>Implementación desde Git local

La manera más fácil de habilitar la implementación de Git local para la aplicación con el servidor de compilación Kudu es utilizar Azure Cloud Shell.

### <a name="configure-a-deployment-user"></a>Configuración de un usuario de implementación

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="enable-local-git-with-kudu"></a>Habilitación de GIT local con Kudu
Si no tiene un repositorio de Git local para la aplicación, deberá inicializar uno. Para ello, ejecute los comandos siguientes desde el directorio del proyecto de la aplicación:

```cmd
git init
git add .
git commit -m "Initial version"
```

Para habilitar la implementación de Git local para la aplicación con el servidor de compilación Kudu, ejecute [`az webapp deployment source config-local-git`](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-local-git) en Cloud Shell.

```azurecli-interactive
az webapp deployment source config-local-git --name <app_name> --resource-group <group_name>
```

Para crear en su lugar una aplicación habilitada para Git, ejecute [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) en Cloud Shell con el parámetro `--deployment-local-git`.

```azurecli-interactive
az webapp create --name <app_name> --resource-group <group_name> --plan <plan_name> --deployment-local-git
```

El comando `az webapp create` genera algo similar a la salida siguiente:

```json
Local git is configured with url of 'https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

### <a name="deploy-your-project"></a>Implementación del proyecto

En la _ventana del terminal local_, agregue una instancia remota de Azure al repositorio de Git local. Reemplace _\<url>_ con la dirección URL del Git remoto que obtuvo en [Habilitación de Git para la aplicación](#enable-local-git-with-kudu).

```bash
git remote add azure <url>
```

Inserte en la instancia remota de Azure para implementar la aplicación con el comando siguiente. Cuando se le pida una contraseña, escriba la contraseña que creó en [Configuración de un usuario de implementación](#configure-a-deployment-user). No use la misma contraseña que para iniciar sesión en Azure Portal.

```bash
git push azure master
```

Es posible que vea la automatización específica para el entorno de tiempo de ejecución en la salida, como MSBuild para ASP.NET, `npm install` para Node.js y `pip install` para Python.

### <a name="browse-to-the-azure-web-app"></a>Navegación hasta la aplicación web de Azure

Vaya a la aplicación web mediante un explorador para comprobar que el contenido se ha implementado.

```bash
http://<app_name>.azurewebsites.net
```

![Aplicación que se ejecuta en App Service](../app-service/media/app-service-web-tutorial-dotnetcore-sqldb/azure-app-in-browser.png)

## <a name="use-managed-identity-in-other-languages"></a>Usar identidades administradas en otros idiomas

Los proveedores de App Configuration para .NET Framework y Java Spring también incluyen compatibilidad integrada con identidades administradas. En estos casos, al configurar un proveedor, use el punto de conexión de dirección URL del almacén de App Configuration en lugar de su cadena de conexión completa. Por ejemplo, para la aplicación de consola de .NET Framework que se creó en el inicio rápido, especifique la siguiente configuración en el archivo *App.config*:

```xml
    <configSections>
        <section name="configBuilders" type="System.Configuration.ConfigurationBuildersSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" restartOnExternalChanges="false" requirePermission="false" />
    </configSections>

    <configBuilders>
        <builders>
            <add name="MyConfigStore" mode="Greedy" endpoint="${Endpoint}" type="Microsoft.Configuration.ConfigurationBuilders.AzureAppConfigurationBuilder, Microsoft.Configuration.ConfigurationBuilders.AzureAppConfiguration" />
            <add name="Environment" mode="Greedy" type="Microsoft.Configuration.ConfigurationBuilders.EnvironmentConfigBuilder, Microsoft.Configuration.ConfigurationBuilders.Environment" />
        </builders>
    </configBuilders>

    <appSettings configBuilders="Environment,MyConfigStore">
        <add key="AppName" value="Console App Demo" />
        <add key="Endpoint" value ="Set via an environment variable - for example, dev, test, staging, or production endpoint." />
    </appSettings>
```

## <a name="clean-up-resources"></a>Limpieza de recursos

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Pasos siguientes
En este tutorial, ha agregado una identidad administrada de Azure para optimizar el acceso a App Configuration y mejorar la administración de credenciales de su aplicación. Para más información sobre App Configuration, continúe con los ejemplos de la CLI de Azure.

> [!div class="nextstepaction"]
> [Ejemplos de CLI](./cli-samples.md)
