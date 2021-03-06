---
title: Aptitud cognitiva de reconocimiento de entidades
titleSuffix: Azure Cognitive Search
description: Extraiga diferentes tipos de entidades del texto de una canalización de enriquecimiento de Búsqueda cognitiva de Azure.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 559d8cb25624c1d8bebb2969fbeeb80bdcc020e6
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2019
ms.locfileid: "73479739"
---
#   <a name="entity-recognition-cognitive-skill"></a>Aptitud cognitiva de reconocimiento de entidades

La aptitud **Reconocimiento de entidades** extrae entidades de distintos tipos del texto. Esta aptitud utiliza los modelos de aprendizaje automático proporcionados por [Text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) en Cognitive Services.

> [!NOTE]
> A medida que expanda el ámbito aumentando la frecuencia de procesamiento, agregando más documentos o agregando más algoritmos de IA, tendrá que [asociar un recurso facturable de Cognitive Services](cognitive-search-attach-cognitive-services.md). Los cargos se acumulan cuando se llama a las API de Cognitive Services y por la extracción de imágenes como parte de la fase de descifrado de documentos de Azure Cognitive Search. No hay ningún cargo por la extracción de texto de documentos.
>
> La ejecución de aptitudes integradas se cobra según los [precios de pago por uso de Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/) existentes. Los precios de la extracción de imágenes se describen en la [página de precios de Búsqueda cognitiva de Azure](https://go.microsoft.com/fwlink/?linkid=2042400).


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.EntityRecognitionSkill

## <a name="data-limits"></a>Límites de datos
El tamaño máximo de un registro debe ser de 50 000 caracteres, medidos por [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length). Si tiene que dividir los datos antes de enviarlos al extractor de frases clave, puede usar la [aptitud de división de texto](cognitive-search-skill-textsplit.md).

## <a name="skill-parameters"></a>Parámetros de la aptitud

Los parámetros distinguen mayúsculas de minúsculas y son opcionales.

| Nombre de parámetro     | DESCRIPCIÓN |
|--------------------|-------------|
| categories    | Matriz de categorías que se deben extraer.  Tipos de categorías posibles: `"Person"`, `"Location"`, `"Organization"`, `"Quantity"`, `"Datetime"`, `"URL"`, `"Email"`. Si no se proporciona ninguna categoría, se devuelven todos los tipos.|
|defaultLanguageCode |  Código de idioma del texto de entrada. Se admiten los siguientes idiomas: `de, en, es, fr, it`|
|minimumPrecision | Un valor entre 0 y 1. Si la puntuación de confianza (en el resultado `namedEntities`) es inferior a este valor, no se devuelve la entidad. El valor predeterminado es 0. |
|includeTypelessEntities | Establézcalo en `true` si desea reconocer entidades conocidas que no se ajustan a las categorías actuales. Las entidades reconocidas se devuelven en el campo de salida complejo `entities`. Por ejemplo, "Windows 10" es una entidad conocida (un producto), pero como "Productos" no es una categoría admitida, esta entidad se incluiría en el campo de salida de las entidades. El valor predeterminado es `false` |


## <a name="skill-inputs"></a>Entradas de la aptitud

| Nombre de entrada      | DESCRIPCIÓN                   |
|---------------|-------------------------------|
| languageCode  | Opcional. El valor predeterminado es `"en"`.  |
| text          | Texto que se analizará.          |

## <a name="skill-outputs"></a>Salidas de la aptitud

> [!NOTE]
> No todas las categorías de entidad son compatibles con todos los idiomas. Solo _en_, _es_ admiten la extracción de los tipos `"Quantity"`, `"Datetime"`, `"URL"` y `"Email"`.

| Nombre de salida     | DESCRIPCIÓN                   |
|---------------|-------------------------------|
| persons      | Una matriz de cadenas donde cada cadena representa el nombre de una persona. |
| locations  | Una matriz de cadenas donde cada cadena representa una ubicación. |
| organizations  | Una matriz de cadenas donde cada cadena representa una organización. |
| quantities  | Una matriz de cadenas donde cada cadena representa una cantidad. |
| dateTimes  | Una matriz de cadenas donde cada cadena representa un valor de fecha y hora (como aparece en el texto). |
| urls | Una matriz de cadenas donde cada cadena representa una dirección URL. |
| emails | Una matriz de cadenas donde cada cadena representa un correo electrónico. |
| namedEntities | Una matriz de tipos complejos, que contiene los siguientes campos: <ul><li>category</li> <li>valor (el nombre de entidad real)</li><li>desplazamiento (la ubicación donde se encontró en el texto)</li><li>confianza (cuanto más alto sea el valor, más real será una entidad)</li></ul> |
| entities | Una matriz de tipos complejos que contiene información valiosa acerca de las entidades extraídas del texto, con los siguientes campos: <ul><li> name (el nombre real de la entidad. Esto representa una forma "normalizada")</li><li> wikipediaId</li><li>wikipediaLanguage</li><li>wikipediaUrl (un vínculo a la página de Wikipedia de la entidad)</li><li>bingId</li><li>type (la categoría de la entidad reconocida)</li><li>subType (solo disponible para determinadas categorías; proporciona una vista más pormenorizada del tipo de entidad)</li><li> matches (una colección compleja que contiene)<ul><li>text (el texto sin formato de la entidad)</li><li>offset (la ubicación donde se encontró)</li><li>length (la longitud del texto de la entidad sin formato)</li></ul></li></ul> |

##  <a name="sample-definition"></a>Definición de ejemplo

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
    "categories": [ "Person", "Email"],
    "defaultLanguageCode": "en",
    "includeTypelessEntities": true,
    "minimumPrecision": 0.5,
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "persons",
        "targetName": "people"
      },
      {
        "name": "emails",
        "targetName": "contact"
      },
      {
        "name": "entities"
      }
    ]
  }
```
##  <a name="sample-input"></a>Entrada de ejemplo

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
             "text": "Contoso corporation was founded by John Smith. They can be reached at contact@contoso.com",
             "languageCode": "en"
           }
      }
    ]
}
```

##  <a name="sample-output"></a>Salida de ejemplo

```json
{
  "values": [
    {
      "recordId": "1",
      "data" : 
      {
        "persons": [ "John Smith"],
        "emails":["contact@contoso.com"],
        "namedEntities": 
        [
          {
            "category":"Person",
            "value": "John Smith",
            "offset": 35,
            "confidence": 0.98
          }
        ],
        "entities":  
        [
          {
            "name":"John Smith",
            "wikipediaId": null,
            "wikipediaLanguage": null,
            "wikipediaUrl": null,
            "bingId": null,
            "type": "Person",
            "subType": null,
            "matches": [{
                "text": "John Smith",
                "offset": 35,
                "length": 10
            }]
          },
          {
            "name": "contact@contoso.com",
            "wikipediaId": null,
            "wikipediaLanguage": null,
            "wikipediaUrl": null,
            "bingId": null,
            "type": "Email",
            "subType": null,
            "matches": [
            {
                "text": "contact@contoso.com",
                "offset": 70,
                "length": 19
            }]
          },
          {
            "name": "Contoso",
            "wikipediaId": "Contoso",
            "wikipediaLanguage": "en",
            "wikipediaUrl": "https://en.wikipedia.org/wiki/Contoso",
            "bingId": "349f014e-7a37-e619-0374-787ebb288113",
            "type": null,
            "subType": null,
            "matches": [
            {
                "text": "Contoso",
                "offset": 0,
                "length": 7
            }]
          }
        ]
      }
    }
  ]
}
```


## <a name="error-cases"></a>Casos de error
Si no se admite el código de idioma del documento, se devuelve un error y no se extrae ninguna entidad.

## <a name="see-also"></a>Otras referencias

+ [Aptitudes integradas](cognitive-search-predefined-skills.md)
+ [Definición de un conjunto de aptitudes](cognitive-search-defining-skillset.md)
