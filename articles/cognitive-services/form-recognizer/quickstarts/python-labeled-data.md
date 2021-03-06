---
title: 'Inicio rápido: Entrenamiento con etiquetas mediante la API REST y Python: Form Recognizer'
titleSuffix: Azure Cognitive Services
description: Aprenda a usar la característica de datos etiquetados Form Recognizer con la API REST y Python para entrenar un modelo personalizado.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 10/16/2019
ms.author: pafarley
ms.openlocfilehash: 9b9600c1ccb9fb9a87d4c98e4e9042ed71029057
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/25/2019
ms.locfileid: "75450933"
---
# <a name="train-a-form-recognizer-model-with-labels-using-rest-api-and-python"></a>Entrenamiento de un modelo de Form Recognizer con etiquetas mediante la API REST y Python

En este inicio rápido, usará la API REST de Form Recognizer con Python para entrenar un modelo personalizado con datos etiquetados manualmente. Consulte la sección [Entrenamiento con etiquetas](../overview.md#train-with-labels) de la introducción para más información acerca de esta característica.

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

## <a name="prerequisites"></a>Prerequisites

Para completar este inicio rápido, debe cumplir los siguientes requisitos:
- Tener acceso a la versión preliminar de acceso limitado de Form Recognizer. Para acceder a la versión preliminar, rellene y envíe el formulario [Solicitud de acceso de Form Recognizer](https://aka.ms/FormRecognizerRequestAccess).
- [Python](https://www.python.org/downloads/) instalado (si desea ejecutar el ejemplo localmente).
- Un conjunto de al menos seis formularios del mismo tipo. Usará estos datos para entrenar el modelo y probar un formulario. En este inicio rápido puede usar un [conjunto de datos de ejemplo](https://go.microsoft.com/fwlink/?linkid=2090451). Cargue los archivos de entrenamiento en la raíz de un contenedor de almacenamiento de blobs de una cuenta de Azure Storage.

## <a name="set-up-training-data"></a>Configuración de los datos de entrenamiento

En primer lugar, deberá configurar los datos de entrada necesarios. La característica de datos etiquetados tiene requisitos de entrada especiales que van más allá de los que se necesitan para entrenar un modelo personalizado. 

Asegúrese de que todos los documentos de entrenamiento tienen el mismo formato. Si tiene formularios en varios formatos, organícelos en subcarpetas basadas en un formato común. Al entrenar, deberá dirigir la API a una subcarpeta.

Para entrenar un modelo con datos etiquetados, necesitará los siguientes archivos como entradas en la subcarpeta. A continuación, aprenderá a crear estos archivos.

* **Formularios de origen**: los formularios de los que se van a extraer datos. Los tipos admitidos son JPEG, PNG, BMP, PDF o TIFF.
* **Archivos de diseño OCR**: archivos JSON que describen los tamaños y posiciones de todo el texto legible de cada formulario de origen. Usará la API de diseño de Form Recognizer para generar estos datos. 
* **Archivos de etiqueta**: archivos JSON que describen las etiquetas de datos que un usuario ha escrito manualmente.

Todos estos archivos deben ocupar la misma subcarpeta y tener el siguiente formato:

* input_file1.pdf 
* input_file1.pdf.ocr.json
* input_file1.pdf.labels.json 
* input_file2.pdf 
* input_file2.pdf.ocr.json
* input_file2.pdf.labels.json
* …

> [!TIP]
> Cuando etiqueta formularios mediante la [herramienta de etiquetado de ejemplo](./label-tool.md) Form Recognizer, dicha herramienta crea estas etiquetas y los archivos de diseño OCR automáticamente.

### <a name="create-the-ocr-output-files"></a>Creación de los archivos de salida de OCR

Necesita archivos de resultados de OCR para que el servicio tenga en cuenta los archivos de entrada correspondientes para el entrenamiento etiquetado. Para obtener los resultados de OCR de un formulario de origen determinado, siga estos pasos:

1. Llame a la API **[Analyze Layout](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeLayoutAsync)** en el contenedor de diseño de lectura con el archivo de entrada como parte del cuerpo de la solicitud. Guarde el identificador que se encontró en el encabezado **Operation-Location** de la respuesta.
1. Llame a la API **[Get Analyze Layout Result](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/GetAnalyzeLayoutResult)** mediante el identificador de la operación del paso anterior.
1. Obtenga la respuesta y escriba el contenido en un archivo. Para cada formulario de origen, el archivo de OCR correspondiente debe tener el nombre del archivo original anexado a `.ocr.json`. La salida JSON del OCR debe tener el siguiente formato. Para ver un ejemplo completo, consulte el [archivo OCR de ejemplo](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/Invoice_1.pdf.ocr.json). 

    ```json
    {
    "status": "succeeded",
    "createdDateTime": "2019-11-12T21:18:12Z",
    "lastUpdatedDateTime": "2019-11-12T21:18:17Z",
    "analyzeResult": {
        "version": "2.0.0",
        "readResults": [
            {
                "page": 1,
                "language": "en",
                "angle": 0,
                "width": 8.5,
                "height": 11,
                "unit": "inch",
                "lines": [
                    {
                        "language": "en",
                        "boundingBox": [
                            0.5384,
                            1.1583,
                            1.4466,
                            1.1583,
                            1.4466,
                            1.3534,
                            0.5384,
                            1.3534
                        ],
                        "text": "Contoso",
                        "words": [
                            {
                                "boundingBox": [
                                    0.5384,
                                    1.1583,
                                    1.4466,
                                    1.1583,
                                    1.4466,
                                    1.3534,
                                    0.5384,
                                    1.3534
                                ],
                                "text": "Contoso",
                                "confidence": 1
                            }
                        ]
                    },
                    ...
    ```

### <a name="create-the-label-files"></a>Creación de archivos de etiqueta

Los archivos de etiqueta contienen asociaciones de clave-valor que un usuario ha especificado manualmente. Son necesarios para el entrenamiento con datos etiquetados, pero no todos los archivos de origen deben tener un archivo de etiqueta correspondiente. Los archivos de origen sin etiquetas se tratarán como documentos de entrenamiento ordinarios. Se recomiendan cinco o más archivos etiquetados para un entrenamiento confiable.

Al crear un archivo de etiquetas, puede especificar de forma opcional las posiciones de regiones exactas de los valores del documento. Esto proporcionará al entrenamiento aún más precisión. Las regiones tienen un formato de conjunto de ocho valores que se corresponden con las cuatro coordenadas X, Y: superior izquierda, superior derecha, inferior derecha e inferior izquierda. Los valores de las coordenadas están entre cero y uno, escalados a las dimensiones de la página.

Para cada formulario de origen, el archivo de etiquetas correspondiente debe tener el nombre del archivo original anexado a `.labels.json`. El archivo de etiquetas debe tener el siguiente formato. Para ver un ejemplo completo, consulte el [archivo de etiquetas de ejemplo](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/Invoice_1.pdf.labels.json).

```json
{
    "document": "Invoice_1.pdf",
    "labels": [
        {
            "label": "Provider",
            "key": null,
            "value": [
                {
                    "page": 1,
                    "text": "Contoso",
                    "boundingBoxes": [
                        [
                            0.06334117647058823,
                            0.1053,
                            0.17018823529411767,
                            0.1053,
                            0.17018823529411767,
                            0.12303636363636362,
                            0.06334117647058823,
                            0.12303636363636362
                        ]
                    ]
                }
            ]
        },
        {
            "label": "For",
            "key": null,
            "value": [
                {
                    "page": 1,
                    "text": "Microsoft",
                    "boundingBoxes": [
                        [
                            0.6122941176470589,
                            0.1374,
                            0.6841764705882353,
                            0.1374,
                            0.6841764705882353,
                            0.14682727272727272,
                            0.6122941176470589,
                            0.14682727272727272
                        ]
                    ]
                },
                {
                    "page": 1,
                    "text": "1020",
                    "boundingBoxes": [
                        [
                            0.6121882352941176,
                            0.156,
                            0.6462941176470588,
                            0.156,
                            0.6462941176470588,
                            0.1653181818181818,
                            0.6121882352941176,
                            0.1653181818181818
                        ]
                    ]
                },
                ...
```

> [!NOTE]
> Solo se puede aplicar una etiqueta a cada elemento de texto y cada etiqueta solo se puede aplicar una vez por página. Actualmente no se puede aplicar una etiqueta a varias páginas.


## <a name="train-a-model-using-labeled-data"></a>Entrenamiento de un modelo con datos etiquetados

Para entrenar un modelo con datos etiquetados, llame a la API **[Train Custom Model](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/TrainCustomModelAsync)** ejecutando el siguiente código de Python. Antes de ejecutar el código, realice estos cambios:

1. Reemplace `<Endpoint>` por la dirección URL del punto de conexión del recurso de Form Recognizer.
1. Reemplace `<SAS URL>` por la dirección URL de la firma de acceso compartido (SAS) del contenedor de almacenamiento de blobs de Azure. Para recuperar la dirección URL de SAS, abra el Explorador de Microsoft Azure Storage, haga clic con el botón derecho en el contenedor y seleccione **Get shared access signature** (Obtener firma de acceso compartido). Asegúrese de que los permisos de **lectura** y **enumeración** están marcados y haga clic en **Create** (Crear). A continuación, copie el valor en la sección **URL**. Debe tener el formato `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.
1. Reemplace `<Blob folder name>` por el nombre de la carpeta del contenedor de blobs donde se encuentran los datos de entrada. O bien, si los datos están en la raíz, déjelo en blanco y quite el campo `"prefix"` del cuerpo de la solicitud HTTP.

```python
########### Python Form Recognizer Labeled Async Train #############
import json
import time
from requests import get, post

# Endpoint URL
endpoint = r"<Endpoint>"
post_url = endpoint + r"/formrecognizer/v2.0-preview/custom/models"
source = r"<SAS URL>"
prefix = "<Blob folder name>"
includeSubFolders = False
useLabelFile = True

headers = {
    # Request headers
    'Content-Type': 'application/json',
    'Ocp-Apim-Subscription-Key': '<subsription key>',
}

body =  {
    "source": source,
    "sourceFilter": {
        "prefix": prefix,
        "includeSubFolders": includeSubFolders
    },
    "useLabelFile": useLabelFile
}

try:
    resp = post(url = post_url, json = body, headers = headers)
    if resp.status_code != 201:
        print("POST model failed (%s):\n%s" % (resp.status_code, json.dumps(resp.json())))
        quit()
    print("POST model succeeded:\n%s" % resp.headers)
    get_url = resp.headers["location"]
except Exception as e:
    print("POST model failed:\n%s" % str(e))
    quit() 
```

## <a name="get-training-results"></a>Obtención de los resultados del entrenamiento

Una vez que haya iniciado la operación de entrenamiento, use el identificador devuelto para obtener el estado de esta. Agregue el código siguiente al final del script de Python. Este utilizará el valor del identificador de la llamada de entrenamiento en una nueva llamada API. La operación de entrenamiento es asincrónica, así que este script llama a la API a intervalos regulares hasta que el estado del entrenamiento es "Completado". Se recomienda un intervalo de uno o varios segundos.

```python 
n_tries = 15
n_try = 0
wait_sec = 5
max_wait_sec = 60
while n_try < n_tries:
    try:
        resp = get(url = get_url, headers = headers)
        resp_json = resp.json()
        if resp.status_code != 200:
            print("GET model failed (%s):\n%s" % (resp.status_code, json.dumps(resp_json)))
            quit()
        model_status = resp_json["modelInfo"]["status"]
        if model_status == "ready":
            print("Training succeeded:\n%s" % json.dumps(resp_json))
            quit()
        if model_status == "invalid":
            print("Training failed. Model is invalid:\n%s" % json.dumps(resp_json))
            quit()
        # Training still running. Wait and retry.
        time.sleep(wait_sec)
        n_try += 1
        wait_sec = min(2*wait_sec, max_wait_sec)     
    except Exception as e:
        msg = "GET model failed:\n%s" % str(e)
        print(msg)
        quit()
print("Train operation did not complete within the allocated time.")
```

Cuando se complete el proceso de entrenamiento, recibirá una respuesta `201 (Success)` con contenido JSON similar al siguiente. La respuesta se ha abreviado por motivos de simplicidad.

```json
{ 
  "modelInfo":{ 
    "status":"ready",
    "createdDateTime":"2019-10-08T10:20:31.957784",
    "lastUpdatedDateTime":"2019-10-08T14:20:41+00:00",
    "modelId":"1cfb372bab404ba3aa59481ab2c63da5"
  },
  "trainResult":{ 
    "trainingDocuments":[ 
      { 
        "documentName":"invoices\\Invoice_1.pdf",
        "pages":1,
        "errors":[ 

        ],
        "status":"succeeded"
      },
      { 
        "documentName":"invoices\\Invoice_2.pdf",
        "pages":1,
        "errors":[ 

        ],
        "status":"succeeded"
      },
      { 
        "documentName":"invoices\\Invoice_3.pdf",
        "pages":1,
        "errors":[ 

        ],
        "status":"succeeded"
      },
      { 
        "documentName":"invoices\\Invoice_4.pdf",
        "pages":1,
        "errors":[ 

        ],
        "status":"succeeded"
      },
      { 
        "documentName":"invoices\\Invoice_5.pdf",
        "pages":1,
        "errors":[ 

        ],
        "status":"succeeded"
      }
    ],
    "errors":[ 

    ]
  },
  "keys":{ 
    "0":[ 
      "Address:",
      "Invoice For:",
      "Microsoft",
      "Page"
    ]
  }
}
```

Copie el valor `"modelId"` para usarlo en los pasos siguientes.

[!INCLUDE [analyze forms](../includes/python-custom-analyze.md)]

Cuando se complete el proceso, recibirá una respuesta `202 (Success)` con contenido JSON con el siguiente formato. La respuesta se ha abreviado por motivos de simplicidad. Las asociaciones de clave-valor principales se encuentran en el nodo `"documentResults"`. Los resultados de la API de diseño (el contenido y las posiciones de todo el texto del documento) se encuentran en el nodo `"readResults"`.

```json
{ 
    "analyzeResult":{ 
      "version":"2.0.0",
      "readResults":[ 
        { 
          "page":1,
          "language":"en",
          "angle":0,
          "width":8.5,
          "height":11,
          "unit":"inch",
          "lines":[ 
            { 
              "language":"en",
              "boundingBox":[ 
                0.5375,
                1.1349,
                2.6064,
                1.1349,
                2.6064,
                1.354,
                0.5375,
                1.354
              ],
              "text":"Contoso Suites",
              "words":[ 
                { 
                  "boundingBox":[ 
                    0.5375,
                    1.1402,
                    1.6595,
                    1.1402,
                    1.6595,
                    1.354,
                    0.5375,
                    1.354
                  ],
                  "text":"Contoso",
                  "confidence":1
                },
                { 
                  "boundingBox":[ 
                    1.758,
                    1.1349,
                    2.6064,
                    1.1349,
                    2.6064,
                    1.3534,
                    1.758,
                    1.3534
                  ],
                  "text":"Suites",
                  "confidence":1
                }
              ]
            },
            ...
          ]
        }
      ],
      "pageResults":[ 
        { 
          "page":1,
          "tables":[ 
            { 
              "rows":2,
              "columns":6,
              "cells":[ 
                { 
                  "rowIndex":0,
                  "columnIndex":0,
                  "text":"Invoice Number",
                  "boundingBox":[ 
                    0.5075,
                    2.8088,
                    1.9061,
                    2.8088,
                    1.9061,
                    3.3219,
                    0.5075,
                    3.3219
                  ],
                  "elements":[ 
                    "#/readResults/0/lines/7/words/0",
                    "#/readResults/0/lines/7/words/1"
                  ]
                },
                { 
                  "rowIndex":0,
                  "columnIndex":1,
                  "text":"Invoice Date",
                  "boundingBox":[ 
                    1.9061,
                    2.8088,
                    3.3074,
                    2.8088,
                    3.3074,
                    3.3219,
                    1.9061,
                    3.3219
                  ],
                  "elements":[ 
                    "#/readResults/0/lines/8/words/0",
                    "#/readResults/0/lines/8/words/1"
                  ]
                },
                ...     
              ]
            }
          ]
        }
      ],
      "documentResults":[ 
        { 
          "docType":"Analyze",
          "pageRange":[ 
            1,
            1
          ],
          "fields":{ 
            "total":{ 
              "type":"string",
              "valueString":"$22,123.24",
              "text":"$22,123.24",
              "boundingBox":[ 
                5.29,
                3.41,
                5.9750000000000009,
                3.41,
                5.9750000000000009,
                3.54,
                5.29,
                3.54
              ],
              "page":1,
              "confidence":1,
              "elements":[ 
                { 
                  "$ref":"#/analyzeResult/readResults/0/lines/15/words/0"
                }
              ],
              "fieldName":"total"
            },
            "invoice #":{ 
              "type":"string",
              "valueString":"7689302",
              "text":"7689302",
              "boundingBox":[ 
                0.54,
                3.41,
                1.065,
                3.41,
                1.065,
                3.515,
                0.54,
                3.515
              ],
              "page":1,
              "confidence":1,
              "elements":[ 
                { 
                  "$ref":"#/analyzeResult/readResults/0/lines/12/words/0"
                }
              ],
              "fieldName":"invoice #"
            },
            "vat":{ 
              "type":"string",
              "valueString":"QR",
              "text":"QR",
              "boundingBox":[ 
                6.2250000000000009,
                3.41,
                6.425,
                3.41,
                6.425,
                3.52,
                6.2250000000000009,
                3.52
              ],
              "page":1,
              "confidence":0.9839357733726502,
              "elements":[ 
                { 
                  "$ref":"#/analyzeResult/readResults/0/lines/16/words/0"
                }
              ],
              "fieldName":"vat"
            },
            ...
          }
        }
      ]
    },
    "status":"succeeded",
    "createdDateTime":"2019-11-12T21:26:19+00:00",
    "lastUpdatedDateTime":"2019-11-12T21:27:27.0488571+00:00"
}
```

## <a name="improve-results"></a>Mejora de los resultados

Examine los valores de `"confidence"` de cada resultado de clave-valor en el nodo `"documentResults"`. También debe examinar las puntuaciones de confianza del nodo `"readResults"`, que se corresponden a la operación de diseño. La confianza de los resultados del diseño no afecta a la confianza de los resultados de la extracción de los pares clave-valor, por lo que debe comprobar ambos.
* Si las puntuaciones de confianza de la operación de diseño son bajas, intente mejorar la calidad de los documentos de entrada (consulte [Requisitos de entrada](../overview.md#input-requirements)).
* Si las puntuaciones de confianza de la operación de extracción de pares clave-valor son bajas, asegúrese de que los documentos que se están analizando sean del mismo tipo que los documentos usados en el conjunto de entrenamiento. Si los documentos del conjunto de entrenamiento tienen variantes de apariencia, considere la posibilidad de dividirlos en carpetas diferentes y entrenar un modelo para cada variante.

### <a name="avoid-cluttered-labels"></a>Evitar etiquetas desordenadas

A veces, cuando se aplican etiquetas diferentes en la misma línea de texto, el servicio puede combinar esas etiquetas en un campo. Por ejemplo, en una dirección, podría etiquetar la ciudad, el estado y el código postal como campos diferentes, pero durante la predicción esos campos no se reconocerían por separado.

Somos conscientes de que esta posibilidad es fundamental para nuestros clientes y estamos trabajando para mejorarla en el futuro. Actualmente, recomendamos que los usuarios etiqueten varios campos desordenados como un solo campo y, a continuación, separe los términos en un procesamiento posterior de los resultados de la extracción.

## <a name="next-steps"></a>Pasos siguientes

En este inicio rápido, ha aprendido a usar la API REST de Form Recognizer con Python para entrenar un modelo con datos etiquetados manualmente. A continuación, consulte la [documentación de referencia](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeWithCustomForm) para explorar la API de Form Recognizer con mayor profundidad.
