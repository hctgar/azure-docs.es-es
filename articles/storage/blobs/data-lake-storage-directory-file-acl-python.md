---
title: SDK de Python para Azure Data Lake Storage Gen2 para archivos y ACL (versión preliminar)
description: Use Python para administrar directorios y listas de control de acceso (ACL) de archivos y directorios en cuentas de almacenamiento que tengan habilitado un espacio de nombres jerárquico (HNS).
author: normesta
ms.service: storage
ms.date: 11/24/2019
ms.author: normesta
ms.topic: article
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: e07bb6aa9d1fe22baaebb7bc7239ce03a728c6b9
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/25/2019
ms.locfileid: "75431807"
---
# <a name="use-python-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2-preview"></a>Uso de Python para administrar directorios, archivos y ACL en Azure Data Lake Storage Gen2 (versión preliminar)

En este artículo se muestra cómo usar Python para crear y administrar directorios, archivos y permisos en cuentas de almacenamiento que tengan habilitado un espacio de nombres jerárquico (HNS). 

> [!IMPORTANT]
> La biblioteca de cliente de Azure Data Lake Storage para Python se encuentra actualmente en versión preliminar pública.

[Paquete (índice de paquetes de Python)](https://pypi.org/project/azure-storage-file-datalake/) | [Muestras](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples) | [Referencia de API](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-storage-file-datalake/12.0.0b5/index.html) | [Asignación de Gen1 a Gen2](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md) | [Envíenos sus comentarios](https://github.com/Azure/azure-sdk-for-python/issues)

## <a name="prerequisites"></a>Prerequisites

> [!div class="checklist"]
> * Suscripción a Azure. Consulte [Obtención de una versión de evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/).
> * Una cuenta de almacenamiento que tenga habilitado el espacio de nombres jerárquico (HNS). Siga [estas](data-lake-storage-quickstart-create-account.md) instrucciones para crear uno.

## <a name="set-up-your-project"></a>Configurar su proyecto

Instale la biblioteca de cliente de Azure Data Lake Storage para Python con [PIP](https://pypi.org/project/pip/).

```
pip install azure-storage-file-datalake --pre
```

Agregue estas instrucciones import al inicio del archivo de código.

```python
import os, uuid, sys
from azure.storage.filedatalake import DataLakeServiceClient
```

## <a name="connect-to-the-account"></a>Conexión con la cuenta

Para usar los fragmentos de código de este artículo, tiene que crear una instancia de **DataLakeServiceClient** que represente la cuenta de almacenamiento. La forma más fácil de obtener una es usar una clave de cuenta. 

En este ejemplo se usa una clave de cuenta para crear una instancia de **DataLakeServiceClient** que represente la cuenta de almacenamiento. 

```python
try:  
    global service_client
        
    service_client = DataLakeServiceClient(account_url="{}://{}.dfs.core.windows.net".format(
        "https", storage_account_name), credential=storage_account_key)
    
except Exception as e:
    print(e)
```
 
- Reemplace el valor de marcador de posición `storage_account_name` por el nombre de la cuenta de almacenamiento.

- Reemplace el valor de marcador de posición `storage-account-key` por la clave de acceso de la cuenta de almacenamiento.

## <a name="create-a-file-system"></a>Creación de un sistema de archivos

Un sistema de archivos actúa como contenedor de los archivos. Puede crear uno llamando al método **FileSystemDataLakeServiceClient.create_file_system**.

En este ejemplo se crea un sistema de archivos llamado `my-file-system`.

```python
def create_file_system():
    try:
        global file_system_client

        file_system_client = service_client.create_file_system(file_system="my-file-system")
    
    except Exception as e:
        print(e) 
```


## <a name="create-a-directory"></a>Creación de un directorio

Cree una referencia de directorio llamando al método **FileSystemClient.create_directory**.

En este ejemplo se agrega un directorio denominado `my-directory` a un sistema de archivos. 

```python
def create_directory():
    try:
        file_system_client.create_directory("my-directory")
    
    except Exception as e:
     print(e) 
```

## <a name="rename-or-move-a-directory"></a>Cambio de nombre o traslado de un directorio

Cambie el nombre de un directorio o muévalo llamando al método **DataLakeDirectoryClient.rename_directory**. Pase la ruta de acceso del directorio que busca a un parámetro. 

En este ejemplo se cambia el nombre de un subdirectorio a `my-subdirectory-renamed`.

```python
def rename_directory():
    try:
       
       file_system_client = service_client.get_file_system_client(file_system="my-file-system")
       directory_client = file_system_client.get_directory_client("my-directory")
       
       new_dir_name = "my-directory-renamed"
       directory_client.rename_directory(rename_destination=directory_client.file_system_name + '/' + new_dir_name)

    except Exception as e:
     print(e) 
```

## <a name="delete-a-directory"></a>Eliminación de un directorio

Elimine un directorio o muévalo llamando al método **DataLakeDirectoryClient.rename_directory**.

En este ejemplo se elimina un directorio denominado `my-directory`.  

```python
def delete_directory():
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")
        directory_client = file_system_client.get_directory_client("my-directory")

        directory_client.delete_directory()
    except Exception as e:
     print(e) 
```

## <a name="manage-directory-permissions"></a>Administración de permisos de directorio

Obtenga la lista de control de acceso (ACL) de un directorio llamando al método **DataLakeDirectoryClient.get_access_control** y establezca dicha lista con una llamada al método **DataLakeDirectoryClient.set_access_control**.

En este ejemplo se obtiene y después se establece la ACL de un directorio denominado `my-directory`. La cadena `rwxr-xrw-` concede al usuario propietario permisos de lectura, escritura y ejecución, permisos de solo lectura y ejecución al grupo propietario, y permisos de lectura y escritura al resto.

```python
def manage_directory_permissions():
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        directory_client = file_system_client.get_directory_client("my-directory")
        
        acl_props = directory_client.get_access_control()
        
        print(acl_props['permissions'])
        
        new_dir_permissions = "rwxr-xrw-"
        
        directory_client.set_access_control(permissions=new_dir_permissions)
        
        acl_props = directory_client.get_access_control()
        
        print(acl_props['permissions'])
    
    except Exception as e:
     print(e) 
```

## <a name="upload-a-file-to-a-directory"></a>Carga de un archivo en un directorio 

En primer lugar, cree una referencia de archivo en el directorio de destino creando una instancia de la clase **DataLakeFileClient**. Cargue un archivo llamando al método **DataLakeFileClient.append_data**. Asegúrese de completar la carga llamando al método **DataLakeFileClient.flush_data**.

En este ejemplo se carga un archivo de texto en un directorio denominado `my-directory`.   

```python
def upload_file_to_directory():
    try:

        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        directory_client = file_system_client.get_directory_client("my-directory")
        
        file_client = directory_client.create_file("uploaded-file.txt")
        local_file = open("C:\\file-to-upload.txt",'r')

        file_contents = local_file.read()

        file_client.append_data(data=file_contents, offset=0, length=len(file_contents))

        file_client.flush_data(len(file_contents))

    except Exception as e:
      print(e) 
```

## <a name="manage-file-permissions"></a>Administración de permisos de archivo

Obtenga la lista de control de acceso (ACL) de un archivo llamando al método **DataLakeFileClient.get_access_control** y establezca dicha lista con una llamada al método **DataLakeFileClient.set_access_control**.

En este ejemplo se obtiene y después se establece la ACL de un archivo denominado `my-file.txt`. La cadena `rwxr-xrw-` concede al usuario propietario permisos de lectura, escritura y ejecución, permisos de solo lectura y ejecución al grupo propietario, y permisos de lectura y escritura al resto.

```python
def manage_file_permissions():
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        directory_client = file_system_client.get_directory_client("my-directory")
        
        file_client = directory_client.get_file_client("uploaded-file.txt")

        acl_props = file_client.get_access_control()
        
        print(acl_props['permissions'])
        
        new_file_permissions = "rwxr-xrw-"
        
        file_client.set_access_control(permissions=new_file_permissions)
        
        acl_props = file_client.get_access_control()
        
        print(acl_props['permissions'])

    except Exception as e:
     print(e) 
```

## <a name="download-from-a-directory"></a>Descarga de un directorio 

Abra un archivo local para escritura. Luego, cree una instancia de **DataLakeFileClient** que represente al archivo que quiere descargar. Llame a **DataLakeFileClient.read_file** para leer los bytes del archivo y, a continuación, escriba esos bytes en el archivo local. 

```python
def download_file_from_directory():
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        directory_client = file_system_client.get_directory_client("my-directory")
        
        local_file = open("C:\\file-to-download.txt",'wb')

        file_client = directory_client.get_file_client("uploaded-file.txt")

        downloaded_bytes = file_client.read_file()

        local_file.write(downloaded_bytes)

        local_file.close()

    except Exception as e:
     print(e)
```
## <a name="list-directory-contents"></a>Lista del contenido del directorio

Muestre el contenido del directorio llamando al método **FileSystemClient.get_paths**, que luego enumera los resultados.

En este ejemplo se imprime la ruta de acceso de cada uno de los subdirectorios y archivos que se encuentra en un directorio denominado `my-directory`.

```python
def list_directory_contents():
    try:
        
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        paths = file_system_client.get_paths(path="my-directory")

        for path in paths:
            print(path.name + '\n')

    except Exception as e:
     print(e) 
```

## <a name="see-also"></a>Consulte también

* [Documentación de referencia de API](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-storage-file-datalake/12.0.0b5/index.html)
* [Paquete (índice de paquetes de Python)](https://pypi.org/project/azure-storage-file-datalake/)
* [Muestras](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples)
* [Asignación de Gen1 a Gen2](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)
* [Problemas conocidos](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Envíenos sus comentarios](https://github.com/Azure/azure-sdk-for-python/issues)
