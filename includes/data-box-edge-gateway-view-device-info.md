---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/04/2019
ms.author: alkohli
ms.openlocfilehash: d5af557a62f4bd35c242d334c28a38c3d632f7cf
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/18/2019
ms.locfileid: "67186775"
---
1. [Conéctese a la interfaz de PowerShell](#connect-to-the-powershell-interface).
2. Use `Get-HcsApplianceInfo` para obtener la información del dispositivo.

    En el ejemplo siguiente se muestra el uso de este cmdlet:

    ```
    [10.100.10.10]: PS>Get-HcsApplianceInfo
    
    Id                            : b2044bdb-56fd-4561-a90b-407b2a67bdfc
    FriendlyName                  : DBE-NBSVFQR94S6
    Name                          : DBE-NBSVFQR94S6
    SerialNumber                  : HCS-NBSVFQR94S6
    DeviceId                      : 40d7288d-cd28-481d-a1ea-87ba9e71ca6b
    Model                         : Virtual
    FriendlySoftwareVersion       : Data Box Gateway 1902
    HcsVersion                    : 1.4.771.324
    IsClustered                   : False
    IsVirtual                     : True
    LocalCapacityInMb             : 1964992
    SystemState                   : Initialized
    SystemStatus                  : Normal
    Type                          : DataBoxGateway
    CloudReadRateBytesPerSec      : 0
    CloudWriteRateBytesPerSec     : 0
    IsInitialPasswordSet          : True
    FriendlySoftwareVersionNumber : 1902
    UploadPolicy                  : All
    DataDiskResiliencySettingName : Simple
    ApplianceTypeFriendlyName     : Data Box Gateway
    IsRegistered                  : False
    ```

    Esta es una tabla que resume alguna de la información importante del dispositivo:
    
    | Parámetro                             | DESCRIPCIÓN                                                                                                                                                  |   |
    |--------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------|---|
    | FriendlyName                   | El nombre descriptivo del dispositivo, tal y como se configuró mediante la interfaz de usuario web local durante la implementación del dispositivo. El nombre descriptivo predeterminado es el número de serie del dispositivo.  |   |
    | SerialNumber                   | El número de serie del dispositivo es un número único asignado en la fábrica.                                                                             |   |
    | Modelo                          | El modelo del dispositivo de Data Box Edge o Data Box Gateway. El modelo es virtual para Data Box Gateway y físico para Data Box Edge.                   |   |
    | FriendlySoftwareVersion        | La cadena descriptiva que corresponde a la versión de software del dispositivo. Para un sistema que ejecuta la versión preliminar, la versión de software descriptiva sería Data Box Edge 1902. |   |
    | HcsVersion                     | La versión de software HCS que se ejecuta en el dispositivo. Por ejemplo, la versión de software HCS correspondiente a Data Box Edge 1902 es 1.4.771.324.            |   |
    | LocalCapacityInMb              | La capacidad total local del dispositivo en megabits.                                                                                                        |   |
    | IsRegistered                   | Este valor indica si el dispositivo se activa con el servicio.                                                                                         |   |


