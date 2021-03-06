---
title: Conexión a una máquina virtual Linux con Azure Bastion | Microsoft Docs
description: En este artículo aprenderá a conectarse a una máquina virtual Linux mediante Azure Bastion.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: cherylmc
ms.openlocfilehash: b88327ea0b5d2958cc1c86fa317415f2441af894
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494484"
---
# <a name="connect-using-ssh-to-a-linux-virtual-machine-using-azure-bastion"></a>Conexión mediante SSH a una máquina virtual Linux con Azure Bastion

En este artículo se muestra cómo establecer una conexión SSH segura y sin problemas con sus máquinas virtuales Linux en una red virtual de Azure. Puede conectarse a una máquina virtual directamente desde Azure Portal. Cuando se utiliza Azure Bastion, las máquinas virtuales no requieren un cliente, un agente ni software adicional. Para más información sobre Azure Bastion, consulte la página de [información general](bastion-overview.md).

Puede usar Azure Bastion para conectar a una máquina virtual Linux mediante SSH. Puede utilizar tanto el nombre de usuario y la contraseña como las claves SSH para realizar la autenticación. Puede conectarse a la máquina virtual con claves SSH mediante una de estas opciones:

* Una clave privada que se escribe manualmente.
* Un archivo que contiene la información de la clave privada.

La clave privada SSH debe tener un formato que empieza con `"-----BEGIN RSA PRIVATE KEY-----"` y finaliza con `"-----END RSA PRIVATE KEY-----"`.

## <a name="before-you-begin"></a>Antes de empezar

Asegúrese de haber configurado un host de Azure Bastion para la red virtual donde reside la máquina virtual. Para más información, consulte [Create an Azure Bastion host](bastion-create-host-portal.md) (Creación de un host de Azure Bastion). Cuando el servicio Bastion se aprovisiona e implementa en la red virtual, puede usarlo para conectarse a cualquier máquina virtual de esta red virtual. Cuando se conecta mediante Bastion, se presupone que usa RDP para conectarse a una máquina virtual Windows y SSH para conectarse a las máquinas virtuales Linux.

Para crear una conexión, se requieren los siguientes roles:

* Rol de lector en la máquina virtual
* Rol de lector en la tarjeta de interfaz de red con la dirección IP privada de la máquina virtual
* Rol de lector en el recurso de Azure Bastion

## <a name="username"></a>Conexión: mediante un nombre de usuario y una contraseña

1.   Abra [Azure Portal](https://portal.azure.com). Vaya a la máquina virtual a la que quiere conectarse y, luego, haga clic en **Conectar**. La VM debe ser una máquina virtual Linux cuando se use una conexión SSH.
1. Después de hacer clic en Conectar, aparecerá una barra lateral con tres pestañas: RDP, SSH y Bastion. Si se aprovisionó Bastion para la red virtual, la pestaña Bastion aparece activa de manera predeterminada. Si no aprovisionó Bastion para la red virtual, consulte [Configure Bastion](bastion-create-host-portal.md) (Configuración de Bastion).

   ![Conexión de una máquina virtual](./media/bastion-connect-vm-ssh/bastion.png)
1. Escriba el nombre de usuario y la contraseña para establecer una conexión SSH con la máquina virtual.
1. Haga clic en el botón **Connect** (Conectar) después de escribir la clave.

## <a name="privatekey"></a>Conexión: con una clave privada escrita

1. Abra [Azure Portal](https://portal.azure.com). Vaya a la máquina virtual a la que quiere conectarse y, luego, haga clic en **Conectar**. La VM debe ser una máquina virtual Linux cuando se use una conexión SSH.
1. Después de hacer clic en Conectar, aparecerá una barra lateral con tres pestañas: RDP, SSH y Bastion. Si se aprovisionó Bastion para la red virtual, la pestaña Bastion aparece activa de manera predeterminada. Si no aprovisionó Bastion para la red virtual, consulte [Configure Bastion](bastion-create-host-portal.md) (Configuración de Bastion).

   ![Conexión de una máquina virtual](./media/bastion-connect-vm-ssh/bastion.png)
1. Escriba el nombre de usuario y seleccione **Clave privada SSH**.
1. Escriba la clave privada en el área de texto **Clave privada SSH** (o péguela directamente).
1. Haga clic en el botón **Connect** (Conectar) después de escribir la clave.

## <a name="ssh"></a>Conexión: con un archivo de clave privada

1. Abra [Azure Portal](https://portal.azure.com). Vaya a la máquina virtual a la que quiere conectarse y, luego, haga clic en **Conectar**. La VM debe ser una máquina virtual Linux cuando se use una conexión SSH.

   ![Conexión a la máquina virtual](./media/bastion-connect-vm-ssh/connect.png)
1. Después de hacer clic en Conectar, aparecerá una barra lateral con tres pestañas: RDP, SSH y Bastion. Si se aprovisionó Bastion para la red virtual, la pestaña Bastion aparece activa de manera predeterminada. Si no aprovisionó Bastion para la red virtual, consulte [Configure Bastion](bastion-create-host-portal.md) (Configuración de Bastion).

   ![Conexión de una máquina virtual](./media/bastion-connect-vm-ssh/bastion.png)
1. Escriba el nombre de usuario y seleccione **SSH Private Key from Local File** (Clave privada SSH desde archivo local).
1. Haga clic en el botón **Examinar** (el icono de carpeta en el archivo local).
1. Busque el archivo y haga clic en **Abrir**.
1. Haga clic en **Connect** (Conectar) para conectarse a la máquina virtual. Al hacer clic en Connect (Conectar), la conexión SSH con esta máquina virtual se abrirá directamente en Azure Portal. Esta conexión se realiza a través de HTML5 mediante el puerto 443 en el servicio Bastion a través de la dirección IP privada de la máquina virtual.

## <a name="next-steps"></a>Pasos siguientes

Lea el artículo sobre [preguntas frecuentes de Bastion](bastion-faq.md).