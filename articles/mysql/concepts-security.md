---
title: Seguridad de Azure Database for MySQL
description: Información general sobre las características de seguridad de Azure Database for MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 421604bcec5277d337b7e7f73a869f40fa73158a
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/03/2019
ms.locfileid: "74764974"
---
# <a name="security-in-azure-database-for-mysql"></a>Seguridad en Azure Database for MySQL

Existen varios niveles de seguridad disponibles para proteger los datos en el servidor de Azure Database for MySQL. En este artículo se describen esas opciones de seguridad.

## <a name="information-protection-and-encryption"></a>Protección y cifrado de información

### <a name="in-transit"></a>En tránsito
Azure Database for MySQL protege los datos mediante el cifrado de datos en tránsito con Seguridad de la capa de transporte. El cifrado (SSL/TLS) se aplica de forma predeterminada.

### <a name="at-rest"></a>En reposo
El servicio Azure Database for MySQL usa el módulo criptográfico con validación FIPS 140-2 para el cifrado del almacenamiento de los datos en reposo. Los datos (copias de seguridad incluidas) se cifran en el disco, a excepción de los archivos temporales creados por el motor durante la ejecución de consultas. El servicio usa el cifrado AES de 256 bits que se incluye en el cifrado de almacenamiento de Azure y las claves las administra el sistema. El cifrado de almacenamiento siempre está activado y no se puede deshabilitar.


## <a name="network-security"></a>Seguridad de las redes
Las conexiones a un servidor de Azure Database for MySQL se enrutan primero a través de una puerta de enlace regional. La puerta de enlace tiene una dirección IP accesible públicamente, mientras que las direcciones IP del servidor están protegidas. Para más información sobre la puerta de enlace, vea el [artículo sobre arquitectura de conectividad](concepts-connectivity-architecture.md).  

Un servidor de Azure Database for MySQL recién creado tiene un firewall que bloquea todas las conexiones externas. Aunque lleguen a la puerta de enlace, no tienen permiso para conectarse al servidor. 

### <a name="ip-firewall-rules"></a>Reglas de firewall de IP
Las reglas de firewall de IP otorgan acceso a los servidores según la dirección IP de origen de cada solicitud. Consulte la [información general sobre las reglas de firewall](concepts-firewall-rules.md) para obtener más información.

### <a name="virtual-network-firewall-rules"></a>Reglas de firewall de red virtual
Los puntos de conexión de servicio de red virtual amplían la conectividad de la red virtual a través de la red troncal de Azure. Cuando se usan reglas de red virtual, el servidor de Azure Database for MySQL se puede habilitar para permitir conexiones desde subredes seleccionadas en una red virtual. Para más información, vea la [información general sobre los puntos de conexión de servicio de red virtual](concepts-data-access-and-security-vnet.md).


## <a name="access-management"></a>administración de acceso

Al crear el servidor de Azure Database for MySQL, se deben proporcionar las credenciales de un usuario administrador. Este administrador se puede usar para crear más usuarios de MySQL.


## <a name="threat-protection"></a>Protección contra amenazas

Puede optar por usar [Advanced Threat Protection](concepts-data-access-and-security-threat-protection.md), que detecta actividades anómalas que indican intentos poco habituales y posiblemente dañinos de acceder a sus servidores o de aprovechar sus vulnerabilidades.

Existe un [registro de auditoría](concepts-audit-logs.md) disponible para realizar un seguimiento de las actividades en las bases de datos. 


## <a name="next-steps"></a>Pasos siguientes
- Habilite las reglas de firewall de [direcciones IP](concepts-firewall-rules.md) o de [redes virtuales](concepts-data-access-and-security-vnet.md).