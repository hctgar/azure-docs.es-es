---
title: 'Actualización de la versión de IoT Edge en los dispositivos: Azure IoT Edge | Microsoft Docs'
description: Cómo actualizar los dispositivos IoT Edge para ejecutar las versiones más recientes del demonio de seguridad y el entorno de ejecución de IoT Edge
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/19/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 1e29420b8e029a7e1723f969753eb2081381a8e8
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/25/2019
ms.locfileid: "75434144"
---
# <a name="update-the-iot-edge-security-daemon-and-runtime"></a>Actualice el archivo de configuración del demonio de seguridad y el entorno de ejecución de IoT Edge.

Dado que el servicio de IoT Edge lanza versiones nuevas, querrá actualizar los dispositivos IoT Edge para tener las últimas características y mejoras de seguridad. En este artículo se proporciona información sobre cómo actualizar los dispositivos IoT Edge cuando hay una versión nueva disponible.

Es necesario actualizar dos componentes de un dispositivo IoT Edge si quiere pasar a una versión más reciente. El primero es el demonio de seguridad, que se ejecuta en el dispositivo e inicia los módulos en tiempo de ejecución cuando se inicia el dispositivo. Actualmente, el demonio de seguridad solo puede actualizarse desde el propio dispositivo. El segundo componente es el entorno de ejecución, formado por los módulos Centro de IoT Edge y Agente de IoT Edge. En función del modo de estructurar la implementación, el entorno de ejecución puede actualizarse desde el dispositivo o de forma remota.

Para obtener la versión más reciente de Azure IoT Edge, consulte [Versiones de Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases).

## <a name="update-the-security-daemon"></a>Actualización del demonio de seguridad

El demonio de seguridad de IoT Edge es un componente nativo que debe actualizarse mediante el administrador de paquetes en el dispositivo IoT Edge.

Compruebe la versión del demonio de seguridad que se ejecuta en el dispositivo mediante el comando `iotedge version`.

### <a name="linux-devices"></a>Dispositivos Linux

En los dispositivos Linux x64, use apt-get o el administrador de paquetes adecuado para actualizar el demonio de seguridad.

```bash
apt-get update
apt-get install libiothsm iotedge
```

En los dispositivos Linux ARM32, siga los pasos de [Instalación del entorno de ejecución de Azure IoT Edge en Linux (ARM32v7/armhf)](how-to-install-iot-edge-linux-arm.md) para instalar la versión más reciente del demonio de seguridad.

### <a name="windows-devices"></a>Dispositivos Windows

En los dispositivos Windows, use el script de PowerShell para actualizar el demonio de seguridad. El script extrae automáticamente la versión más reciente del demonio de seguridad.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Update-IoTEdge -ContainerOs <Windows or Linux>
```

Al ejecutar el comando Update-IoTEdge se quita y actualiza el demonio de seguridad del dispositivo, junto con las dos imágenes de contenedor del entorno de ejecución. El archivo config.yaml se mantiene en el dispositivo, así como los datos del motor de contenedor Moby (si usa contenedores de Windows). Si conserva la información de configuración, no tendrá que volver a proporcionar la cadena de conexión o información de Device Provisioning Service para el dispositivo durante el proceso de actualización.

Si quiere instalar una versión específica del demonio de seguridad, descargue el archivo adecuado Microsoft-Azure-IoTEdge.cab de las [versiones de IoT Edge](https://github.com/Azure/azure-iotedge/releases). A continuación, use el parámetro `-OfflineInstallationPath` para que apunte a la ubicación del archivo. Para más información, consulte la [Instalación sin conexión](how-to-install-iot-edge-windows.md#offline-installation).

## <a name="update-the-runtime-containers"></a>Actualización de los contenedores del entorno de ejecución

El modo de actualizar los contenedores del Centro de IoT Edge y del Agente de IoT Edge depende de si usa etiquetas graduales (por ejemplo, 1.0) o etiquetas específicas (por ejemplo, 1.0.7) en la implementación.

Compruebe la versión de los módulos Agente de IoT Edge y Centro de IoT Edge instalada actualmente en el dispositivo mediante los comandos `iotedge logs edgeAgent` o `iotedge logs edgeHub`.

  ![Búsqueda de la versión del contenedor en los registros](./media/how-to-update-iot-edge/container-version.png)

### <a name="understand-iot-edge-tags"></a>Información sobre las etiquetas de IoT Edge

Las imágenes del Agente de IoT Edge y del centro de IoT Edge se etiquetan con la versión de IoT Edge a la que están asociadas. Hay dos maneras diferentes de usar etiquetas con las imágenes del entorno de ejecución:

* **Etiquetas graduales**: use solo los dos primeros valores del número de versión para obtener la imagen más reciente que coincida con esos dígitos. Por ejemplo, 1.0 se actualiza cada vez que hay una nueva versión para que apunte a la versión 1.0.x más reciente. Si el entorno de ejecución del contenedor en el dispositivo IoT Edge extrae la imagen de nuevo, se actualizan los módulos del entorno de ejecución a la versión más reciente. Este enfoque se recomienda para fines de desarrollo. Las implementaciones de Azure Portal tienen como valor predeterminado las etiquetas graduales.

* **Etiquetas específicas**: use los tres valores del número de versión para establecer explícitamente la versión de la imagen. Por ejemplo, la versión 1.0.7 no cambiará después de su lanzamiento inicial. Puede declarar un nuevo número de versión del manifiesto de implementación cuando esté listo para actualizar. Este enfoque se recomienda para fines de producción.

### <a name="update-a-rolling-tag-image"></a>Actualización de una imagen de etiqueta gradual

Si usa etiquetas graduales en la implementación (por ejemplo, mcr.microsoft.com/azureiotedge-hub:**1.0**), deberá forzar el entorno de ejecución del contenedor en el dispositivo para extraer la versión más reciente de la imagen.

Elimine la versión local de la imagen del dispositivo IoT Edge. En equipos con Windows, al desinstalar el demonio de seguridad también se quitan las imágenes en tiempo de ejecución, por lo que no es necesario volver a realizar este paso.

```bash
docker rmi mcr.microsoft.com/azureiotedge-hub:1.0
docker rmi mcr.microsoft.com/azureiotedge-agent:1.0
```

Es posible que tenga que usar la marca `-f` de fuerza para eliminar las imágenes.

El servicio de IoT Edge extraerá las versiones más recientes de las imágenes del entorno de ejecución y volverá a iniciarlas automáticamente en el dispositivo.

### <a name="update-a-specific-tag-image"></a>Actualización de una etiqueta específica

Si usa etiquetas específicas en la implementación (por ejemplo, mcr.microsoft.com/azureiotedge-hub:**1.0.8**), solo tiene que actualizar la etiqueta en el manifiesto de implementación y aplicar los cambios en el dispositivo.

1. En IoT Hub en Azure Portal, seleccione el dispositivo IoT Edge y luego **Establecer módulos**.

1. En la sección **Módulos de IoT Edge**, seleccione **Configuración del entorno de ejecución**.

   ![Configuración de los parámetros de ejecución](./media/how-to-update-iot-edge/configure-runtime.png)

1. En **Configuración del entorno de ejecución**, actualice el valor **Image** del **Centro de Microsoft Edge** con la versión deseada. No seleccione **Guardar** todavía.

   ![Actualización de la versión de la imagen del Centro de Microsoft Edge](./media/how-to-update-iot-edge/runtime-settings-edgehub.png)

1. Contraiga la configuración del **Centro de Microsoft Edge**, o desplácese hacia abajo, y actualice el valor de **Image** para el **Agente de Edge** con la misma versión deseada.

   ![Actualización de la versión del agente del Centro de Microsoft Edge](./media/how-to-update-iot-edge/runtime-settings-edgeagent.png)

1. Seleccione **Guardar**.

1. Seleccione **Revisar y crear**, revise la implementación y, después, seleccione **Crear**.

## <a name="update-to-a-release-candidate-version"></a>Actualización a una versión candidata para lanzamiento

Azure IoT Edge lanza periódicamente nuevas versiones del servicio IoT Edge. Antes de cada versión estable, se publican una o varias versiones candidatas para lanzamiento (RC). Las versiones RC incluyen todas las características planeadas para la versión, pero aún se están sometiendo a los procesos de pruebas y validación necesarios para una versión estable. Si quiere probar una nueva característica de manera anticipada, puede instalar la versión RC y proporcionar comentarios a través de GitHub.

Las versiones candidatas para lanzamiento siguen la misma convención de numeración que las versiones, pero tienen **-rc** más un número incremental anexados al final. Puede ver las versiones candidatas para lanzamiento en la misma lista de [versiones de Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases) que las versiones estables. Por ejemplo, busque **1.0.7-rc1** y **1.0.7-rc2**, las dos versiones candidatas para lanzamiento anteriores a la versión **1.0.7**. También puede ver que las versiones RC se marcan con etiquetas de **versión preliminar**.

Igual que las versiones preliminares, las versiones candidatas para lanzamiento no se incluyen como la versión más reciente que utilizan los instaladores habituales. En su lugar, debe especificar manualmente los recursos para la versión RC que quiere probar. Según el sistema operativo del dispositivo IoT Edge, use las secciones siguientes para actualizar IoT Edge a una versión específica:

* [Linux](how-to-install-iot-edge-linux.md#install-a-specific-runtime-version)
* [Windows](how-to-install-iot-edge-windows.md#offline-installation)

## <a name="next-steps"></a>Pasos siguientes

Consulte las últimas [versiones de Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases).

Permanezca actualizado con los anuncios y actualizaciones recientes del [blog Internet of Things](https://azure.microsoft.com/blog/topics/internet-of-things/) (Internet de las cosas)