---
title: Transferencia de datos con Azure Files como origen o destino mediante AzCopy v10 | Microsoft Docs
description: Transferencia de datos con AzCopy y File Storage.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: fe2a6cb7943875732e6ce2fad630b2af69e6197b
ms.sourcegitcommit: b5d59c6710046cf105236a6bb88954033bd9111b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/27/2019
ms.locfileid: "74559140"
---
# <a name="transfer-data-with-azcopy-and-file-storage"></a>Transferencia de datos con AzCopy y File Storage 

AzCopy es una utilidad de línea de comandos que puede usar para copiar blobs o archivos a una cuenta de almacenamiento o desde una cuenta de almacenamiento. Este artículo contiene comandos de ejemplo que funcionan con Azure Files.

Antes de comenzar, consulte el artículo [Introducción a AzCopy](storage-use-azcopy-v10.md) para descargar AzCopy y familiarizarse con la herramienta.

## <a name="create-file-shares"></a>Creación de recursos compartidos de archivos

Puede usar el comando [azcopy make](storage-ref-azcopy-make.md) para crear un recurso compartido de archivos. El ejemplo de esta sección crea un recurso compartido de archivos denominado `myfileshare`.

> [!TIP]
> Los ejemplos de esta sección delimitan los argumentos de ruta de acceso con comillas simples (''). Use comillas simples en todos los shells de comandos excepto en el shell de comandos de Windows (cmd.exe). Si utiliza un shell de comandos de Windows (cmd.exe), incluya los argumentos de la ruta de acceso entre comillas dobles ("") en lugar de comillas simples ('').

|    |     |
|--------|-----------|
| **Sintaxis** | `azcopy make 'https://<storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>'` |
| **Ejemplo** | `azcopy make 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'` |

Para obtener documentos de referencia detallados, consulte [azcopy make](storage-ref-azcopy-make.md).

## <a name="upload-files"></a>Carga de archivos

Puede usar el comando [azcopy copy](storage-ref-azcopy-copy.md) para cargar archivos y directorios desde el equipo local.

En esta sección se incluyen los ejemplos siguientes:

> [!div class="checklist"]
> * Cargar un archivo
> * Subir un directorio
> * Subir el contenido de un directorio
> * Cargar un archivo específico

> [!NOTE]
> AzCopy no calcula de forma automática ni almacena el código hash md5 del archivo. Si quiere que AzCopy haga eso, anexe la marca `--put-md5` a cada comando de copia. De ese modo, cuando se descarga el archivo, AzCopy calcula un hash MD5 para los datos descargados y comprueba que el hash MD5 almacenado en la propiedad `Content-md5` del archivo coincide con el hash calculado.

Para obtener documentos de referencia detallados, consulte [azcopy copy](storage-ref-azcopy-copy.md).

> [!TIP]
> Los ejemplos de esta sección delimitan los argumentos de ruta de acceso con comillas simples (''). Use comillas simples en todos los shells de comandos excepto en el shell de comandos de Windows (cmd.exe). Si usa un shell de comandos de Windows (cmd.exe), incluya los argumentos de la ruta de acceso entre comillas dobles ("") en lugar de comillas simples ('').

### <a name="upload-a-file"></a>Cargar un archivo

|    |     |
|--------|-----------|
| **Sintaxis** | `azcopy copy '<local-file-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-name>?<SAS-token>'` |
| **Ejemplo** | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'` |

También puede cargar un archivo mediante un símbolo comodín (*) en cualquier lugar de la ruta de acceso o del nombre de archivo. Por ejemplo: `'C:\myDirectory\*.txt'` o `C:\my*\*.txt`.

### <a name="upload-a-directory"></a>Subir un directorio

En este ejemplo se copia un directorio (y todos sus archivos) en un recurso compartido de archivos. El resultado es un directorio en el recurso compartido de archivos con el mismo nombre.

|    |     |
|--------|-----------|
| **Sintaxis** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>' --recursive` |
| **Ejemplo** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' --recursive` |

Para copiar un directorio dentro del recurso compartido de archivos, simplemente especifique el nombre de ese directorio en la cadena de comandos.

|    |     |
|--------|-----------|
| **Ejemplo** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' --recursive` |

Si especifica el nombre de un directorio que no existe en el recurso compartido de archivos, AzCopy crea un directorio con ese nombre.

### <a name="upload-the-contents-of-a-directory"></a>Subir el contenido de un directorio

Puede cargar el contenido de un directorio sin copiar el propio directorio contenedor mediante el carácter comodín (*).

|    |     |
|--------|-----------|
| **Sintaxis** | `azcopy copy '<local-directory-path>/*' 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path>?<SAS-token>` |
| **Ejemplo** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

> [!NOTE]
> Anexe la marca `--recursive` para cargar archivos en todos los subdirectorios.

### <a name="upload-specific-files"></a>Carga de archivos específicos

Puede especificar nombres de archivo completos o usar nombres parciales con caracteres comodín (*).

#### <a name="specify-multiple-complete-file-names"></a>Especificación de varios nombres de archivo completos

Use el comando [azcopy copy](storage-ref-azcopy-copy.md) con la opción `--include-path`. Separe los nombres de archivo individuales mediante punto y coma (`;`).

|    |     |
|--------|-----------|
| **Sintaxis** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name>?<SAS-token>' --include-path <semicolon-separated-file-list>` |
| **Ejemplo** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --include-path 'photos;documents\myFile.txt'` |

En este ejemplo, AzCopy transfiere el directorio `C:\myDirectory\photos` y el archivo `C:\myDirectory\documents\myFile.txt`. Debe incluir la opción `--recursive` para transferir todos los archivos del directorio `C:\myDirectory\photos`.

También puede excluir archivos mediante la opción `--exclude-path`. Para más información, consulte los documentos de referencia de [azcopy copy ](storage-ref-azcopy-copy.md).

#### <a name="use-wildcard-characters"></a>Uso de caracteres comodín

Use el comando [azcopy copy](storage-ref-azcopy-copy.md) con la opción `--include-pattern`. Especifique nombres parciales que incluyan los caracteres comodín. Separe los nombres con punto y coma (`;`).

|    |     |
|--------|-----------|
| **Sintaxis** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name>?<SAS-token>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Ejemplo** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --include-pattern 'myFile*.txt;*.pdf*'` |

También puede excluir archivos mediante la opción `--exclude-pattern`. Para más información, consulte los documentos de referencia de [azcopy copy ](storage-ref-azcopy-copy.md).

Las opciones `--include-pattern` y `--exclude-pattern` solo se aplican a los nombres de archivo, no a la ruta de acceso.  Si quiere copiar todos los archivos de texto que existen en un árbol de directorios, use la opción `–recursive` para obtener todo el árbol de directorios y, a continuación, use el `–include-pattern` y especifique `*.txt` para obtener todos los archivos de texto.

## <a name="download-files"></a>Descarga de archivos

Puede usar el comando [azcopy copy](storage-ref-azcopy-copy.md) para descargar archivos, directorios y recursos compartidos de archivos en la máquina local.

En esta sección se incluyen los ejemplos siguientes:

> [!div class="checklist"]
> * Descarga de un archivo
> * Descargar un directorio
> * Descargar el contenido de un directorio
> * Descarga de archivos específicos

> [!NOTE]
> Si el valor de la propiedad `Content-md5` de un archivo contiene un hash, AzCopy calcula un hash MD5 para los datos descargados y comprueba que el hash MD5 almacenado en la propiedad `Content-md5` del archivo coincide con el hash calculado. Si estos valores no coinciden, se produce un error en la descarga a menos que invalide este comportamiento mediante la anexión de `--check-md5=NoCheck` o `--check-md5=LogOnly` al comando de copia.

Para obtener documentos de referencia detallados, consulte [azcopy copy](storage-ref-azcopy-copy.md).

> [!TIP]
> Los ejemplos de esta sección delimitan los argumentos de ruta de acceso con comillas simples (''). Use comillas simples en todos los shells de comandos excepto en el shell de comandos de Windows (cmd.exe). Si usa un shell de comandos de Windows (cmd.exe), incluya los argumentos de la ruta de acceso entre comillas dobles ("") en lugar de comillas simples ('').

### <a name="download-a-file"></a>Descarga de un archivo

|    |     |
|--------|-----------|
| **Sintaxis** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-path>?<SAS-token>' '<local-file-path>'` |
| **Ejemplo** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory\myTextFile.txt'` |

### <a name="download-a-directory"></a>Descargar un directorio

|    |     |
|--------|-----------|
| **Sintaxis** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path>?<SAS-token>' '<local-directory-path>' --recursive` |
| **Ejemplo** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory'  --recursive` |

En este ejemplo se crea un directorio denominado `C:\myDirectory\myFileShareDirectory` que contiene todos los archivos descargados.

### <a name="download-the-contents-of-a-directory"></a>Descargar el contenido de un directorio

Puede descargar el contenido de un directorio sin copiar el propio directorio contenedor mediante el carácter comodín (*).

|    |     |
|--------|-----------|
| **Sintaxis** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/*?<SAS-token>' '<local-directory-path>/'` |
| **Ejemplo** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory/*?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory'` |

> [!NOTE]
> Anexe la marca `--recursive` para descargar archivos en todos los subdirectorios.

### <a name="download-specific-files"></a>Descarga de archivos específicos

Puede especificar nombres de archivo completos o usar nombres parciales con caracteres comodín (*).

#### <a name="specify-multiple-complete-file-names"></a>Especificación de varios nombres de archivo completos

Use el comando [azcopy copy](storage-ref-azcopy-copy.md) con la opción `--include-path`. Separe los nombres de archivo individuales mediante un punto y coma (`;`).

|    |     |
|--------|-----------|
| **Sintaxis** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name>?<SAS-token>' '<local-directory-path>'  --include-path <semicolon-separated-file-list>` |
| **Ejemplo** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt' --recursive` |

En este ejemplo, AzCopy transfiere el directorio `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/photos` y el archivo `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/documents/myFile.txt`. Debe incluir la opción `--recursive` para transferir todos los archivos del directorio `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/photos`.

También puede excluir archivos mediante la opción `--exclude-path`. Para más información, consulte los documentos de referencia de [azcopy copy ](storage-ref-azcopy-copy.md).

#### <a name="use-wildcard-characters"></a>Uso de caracteres comodín

Use el comando [azcopy copy](storage-ref-azcopy-copy.md) con la opción `--include-pattern`. Especifique nombres parciales que incluyan los caracteres comodín. Separe los nombres con punto y coma (`;`).

|    |     |
|--------|-----------|
| **Sintaxis** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>?<SAS-token>' '<local-directory-path>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Ejemplo** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |

También puede excluir archivos mediante la opción `--exclude-pattern`. Para más información, consulte los documentos de referencia de [azcopy copy ](storage-ref-azcopy-copy.md).

Las opciones `--include-pattern` y `--exclude-pattern` solo se aplican a los nombres de archivo, no a la ruta de acceso.  Si quiere copiar todos los archivos de texto que existen en un árbol de directorios, use la opción `–recursive` para obtener todo el árbol de directorios y, a continuación, use el `–include-pattern` y especifique `*.txt` para obtener todos los archivos de texto.

## <a name="copy-files-between-storage-accounts"></a>Copia de archivos entre cuentas de almacenamiento

Puede usar AzCopy para copiar archivos a otras cuentas de almacenamiento. La operación de copia es sincrónica, por lo que cuando el comando devuelve un resultado, eso indica que se han copiado todos los archivos.

AzCopy usa [API](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url) [de servidor a servidor](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url), por lo que los datos se copian directamente entre servidores de almacenamiento. En estas operaciones de copia no se usa el ancho de banda de red del equipo. Para aumentar el rendimiento de estas operaciones puede establecer el valor de la variable de entorno `AZCOPY_CONCURRENCY_VALUE`. Para obtener más información, consulte [Optimización del rendimiento](storage-use-azcopy-configure.md#optimize-throughput).

En esta sección se incluyen los ejemplos siguientes:

> [!div class="checklist"]
> * Copia de un archivo a otra cuenta de almacenamiento
> * Copia de un directorio a otra cuenta de almacenamiento
> * Copia de un recurso compartido de archivos a otra cuenta de almacenamiento
> * Copia de todos los recurso compartido de archivos, directorios y archivos a otra cuenta de almacenamiento

Para obtener documentos de referencia detallados, consulte [azcopy copy](storage-ref-azcopy-copy.md).

> [!TIP]
> En los ejemplos de esta sección se delimitan los argumentos de ruta de acceso con comillas simples (''). Use comillas simples en todos los shells de comandos excepto en el shell de comandos de Windows (cmd.exe). Si utiliza un shell de comandos de Windows (cmd.exe), incluya los argumentos de la ruta de acceso entre comillas dobles ("") en lugar de comillas simples ('').

### <a name="copy-a-file-to-another-storage-account"></a>Copia de un archivo a otra cuenta de almacenamiento

|    |     |
|--------|-----------|
| **Sintaxis** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<file-path>?<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name>/<file-path>?<SAS-token>'` |
| **Ejemplo** | `azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D'` |

### <a name="copy-a-directory-to-another-storage-account"></a>Copia de un directorio a otra cuenta de almacenamiento

|    |     |
|--------|-----------|
| **Sintaxis** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path>?<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>' --recursive` |
| **Ejemplo** | `azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

### <a name="copy-a-file-share-to-another-storage-account"></a>Copia de un recurso compartido de archivos a otra cuenta de almacenamiento

|    |     |
|--------|-----------|
| **Sintaxis** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>' --recursive` |
| **Ejemplo** | `azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

### <a name="copy-all-file-shares-directories-and-files-to-another-storage-account"></a>Copia de todos los recurso compartido de archivos, directorios y archivos a otra cuenta de almacenamiento

|    |     |
|--------|-----------|
| **Sintaxis** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/?<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/?<SAS-token>' --recursive'` |
| **Ejemplo** | `azcopy copy 'https://mysourceaccount.file.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

## <a name="synchronize-files"></a>Sincronización de archivos

Puede sincronizar el contenido de un recurso compartido de archivos con otro recurso compartido de archivos. También puede sincronizar el contenido de un directorio de un recurso compartido de archivos con el contenido de un directorio que se encuentra en otro recurso compartido de archivos. La sincronización es unidireccional. En otras palabras, tendrá que elegir cuál de estos dos puntos de conexión es el origen y cuál es el destino. La sincronización también usa las API de servidor a servidor.

> [!NOTE]
> En la actualidad, este escenario solo se admite para las cuentas que no tienen un espacio de nombres jerárquico. La versión actual de AzCopy no se sincroniza entre Azure Files y Blob Storage.

El comando [sync](storage-ref-azcopy-sync.md) compara nombres de archivo y las marcas de tiempo de la última modificación. Establezca la marca opcional `--delete-destination` en un valor de `true` o `prompt` para eliminar archivos en el directorio de destino si esos archivos ya no existen en el directorio de origen.

Si establece la marca `--delete-destination` en `true`, AzCopy elimina los archivos sin proporcionar un mensaje. Si quiere que aparezca un mensaje antes de que AzCopy elimine un archivo, establezca la marca `--delete-destination` en `prompt`.

Para obtener documentos de referencia detallados, consulte [azcopy sync](storage-ref-azcopy-sync.md).

> [!TIP]
> En los ejemplos de esta sección se delimitan los argumentos de ruta de acceso con comillas simples (''). Use comillas simples en todos los shells de comandos excepto en el shell de comandos de Windows (cmd.exe). Si utiliza un shell de comandos de Windows (cmd.exe), incluya los argumentos de la ruta de acceso entre comillas dobles ("") en lugar de comillas simples ('').

### <a name="update-a-file-share-with-changes-to-another-file-share"></a>Actualización de un recurso compartido de archivos con los cambios realizados en otro recurso compartido de archivos

El primer recurso compartido de archivos que aparece en este comando es el origen. El segundo es el destino.

|    |     |
|--------|-----------|
| **Sintaxis** | `azcopy sync 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>' --recursive` |
| **Ejemplo** | `azcopy sync 'https://mysourceaccount.file.core.windows.net/myfileShare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

### <a name="update-a-directory-with-changes-to-a-directory-in-another-file-share"></a>Actualización de un directorio con cambios en un directorio de otro recurso compartido de archivos

El primer directorio que aparece en este comando es el origen. El segundo es el destino.

|    |     |
|--------|-----------|
| **Sintaxis** | `azcopy sync 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-name>?<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-name>?<SAS-token>' --recursive` |
| **Ejemplo** | `azcopy sync 'https://mysourceaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

## <a name="next-steps"></a>Pasos siguientes

Puede encontrar más ejemplos en cualquiera de estos artículos:

- [Introducción a AzCopy](storage-use-azcopy-v10.md)

- [Transferencia de datos con AzCopy y Blob Storage](storage-use-azcopy-blobs.md)

- [Transferencia de datos con AzCopy y cubos de Amazon S3](storage-use-azcopy-s3.md)

- [Configurar, optimizar y solucionar problemas de AzCopy](storage-use-azcopy-configure.md)
