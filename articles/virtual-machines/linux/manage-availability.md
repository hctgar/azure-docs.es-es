---
title: Administración de la disponibilidad de máquinas virtuales Linux en Azure
description: Aprenda a utilizar varias máquinas virtuales para garantizar la alta disponibilidad de su aplicación Linux en Azure.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: 891c852a-84c0-4940-a61e-ada6e185bf37
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 03/27/2018
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5742ed346c6761dd443d6252e5c9e457fa952b87
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/13/2019
ms.locfileid: "74035899"
---
# <a name="manage-the-availability-of-linux-virtual-machines"></a>Manage the availability of Linux virtual machines (Administración de la disponibilidad de las máquinas virtuales con Linux)

Aprenda a configurar y administrar varias máquinas virtuales para garantizar una alta disponibilidad de la aplicación Linux en Azure. También puede [administrar la disponibilidad de máquinas virtuales Windows](../windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Para obtener instrucciones sobre cómo crear un conjunto de disponibilidad mediante la CLI en el modelo de implementación de Resource Manager, consulte [azure availset: comandos para administrar los conjuntos de disponibilidad](../azure-cli-arm-commands.md#azure-availset-commands-to-manage-your-availability-sets).

[!INCLUDE [virtual-machines-common-manage-availability](../../../includes/virtual-machines-common-manage-availability.md)]

## <a name="next-steps"></a>Pasos siguientes
Para más información sobre el equilibrio de carga de las máquinas virtuales, consulte [Equilibrio de carga para servicios de infraestructura de Azure](../virtual-machines-linux-load-balance.md).

