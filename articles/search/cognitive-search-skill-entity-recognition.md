---
title: 'Aptitud de búsqueda cognitiva de reconocimiento de entidades: Azure Search'
description: Extraiga diferentes tipos de entidades del texto de una canalización de búsqueda cognitiva de Azure Search.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 9745934891cd7ba99fa821377318e38134b7d2a5
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/12/2018
ms.locfileid: "53311871"
---
#    <a name="entity-recognition-cognitive-skill"></a>Aptitud cognitiva de reconocimiento de entidades

La aptitud **Reconocimiento de entidades** extrae entidades de distintos tipos del texto. 

> [!NOTE]
> A partir del 21 de diciembre de 2018, podrá asociar un recurso de Cognitive Services con un conjunto de aptitudes de Azure Search. Esto nos permitirá empezar a cobrar por la ejecución del conjunto de aptitudes. En esta fecha, también empezaremos a cobrar por la extracción de imágenes como parte de la fase de descifrado de documentos. La extracción de texto de documentos continuará ofreciéndose sin costo adicional.
>
> La ejecución de aptitudes integradas se cobrará a los [precios de pago por uso existentes de Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/). La extracción de imágenes se cobrará al precio de la versión preliminar, tal y como se describe en la [página de precios de Azure Search](https://go.microsoft.com/fwlink/?linkid=2042400). [Más información](cognitive-search-attach-cognitive-services.md).


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.EntityRecognitionSkill

## <a name="data-limits"></a>Límites de datos
El tamaño máximo de un registro debe tener 50 000 caracteres según lo que mida `String.Length`. Si tiene que dividir los datos antes de enviarlos al extractor de frases clave, puede usar la [aptitud de división de texto](cognitive-search-skill-textsplit.md).

## <a name="skill-parameters"></a>Parámetros de la aptitud

Los parámetros distinguen mayúsculas de minúsculas y son opcionales.

| Nombre de parámetro     | DESCRIPCIÓN |
|--------------------|-------------|
| Categorías    | Matriz de categorías que se deben extraer.  Tipos de categorías posibles: `"Person"`, `"Location"`, `"Organization"`, `"Quantity"`, `"Datetime"`, `"URL"`, `"Email"`. Si no se proporciona ninguna categoría, se devuelven todos los tipos.|
|defaultLanguageCode |  Código de idioma del texto de entrada. Se admiten los siguientes idiomas: `de, en, es, fr, it`|
|minimumPrecision | Sin usar. Reservado para uso futuro. |
|includeTypelessEntites | Cuando se establece en true, si el texto contiene una entidad conocida, pero no se puede clasificar en una de las categorías admitidas, se devolverá como parte del campo de salida compleja `"entities"`. El valor predeterminado es `false` |


## <a name="skill-inputs"></a>Entradas de la aptitud

| Nombre de entrada      | DESCRIPCIÓN                   |
|---------------|-------------------------------|
| languageCode  | Opcional. El valor predeterminado es `"en"`.  |
| text          | Texto que se analizará.          |

## <a name="skill-outputs"></a>Salidas de la aptitud

**NOTA**: No todas las categorías de entidad son compatibles con todos los idiomas.
Solo _en_, _es_ admiten la extracción de los tipos `"Quantity"`, `"Datetime"`, `"URL"` y `"Email"`.

| Nombre de salida     | DESCRIPCIÓN                   |
|---------------|-------------------------------|
| persons      | Una matriz de cadenas donde cada cadena representa el nombre de una persona. |
| Ubicaciones  | Una matriz de cadenas donde cada cadena representa una ubicación. |
| organizations  | Una matriz de cadenas donde cada cadena representa una organización. |
| quantities  | Una matriz de cadenas donde cada cadena representa una cantidad. |
| dateTimes  | Una matriz de cadenas donde cada cadena representa un valor de fecha y hora (como aparece en el texto). |
| urls | Una matriz de cadenas donde cada cadena representa una dirección URL. |
| emails | Una matriz de cadenas donde cada cadena representa un correo electrónico. |
| namedEntities | Una matriz de tipos complejos, que contienen los siguientes campos: <ul><li>categoría</li> <li>valor (el nombre de entidad real)</li><li>desplazamiento (la ubicación donde se encontró en el texto)</li><li>confidence (no se usa por ahora. Se establecerá en un valor de -1)</li></ul> |
| entities | Una matriz de tipos complejos, que contiene información valiosa acerca de las entidades extraídas del texto, con los siguientes campos: <ul><li> name (el nombre real de la entidad. Esto representa una forma "normalizada")</li><li> wikipediaId</li><li>wikipediaLanguage</li><li>wikipediaUrl (un vínculo a la página de Wikipedia de la entidad)</li><li>bingId</li><li>type (la categoría de la entidad reconocida)</li><li>subType (solo disponible para determinadas categorías; proporciona una vista más pormenorizada del tipo de entidad)</li><li> matches (una colección compleja que contiene)<ul><li>text (el texto sin formato de la entidad)</li><li>offset (la ubicación donde se encontró)</li><li>length (la longitud del texto de la entidad sin formato)</li></ul></li></ul> |

##  <a name="sample-definition"></a>Definición de ejemplo

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
    "categories": [ "Person", "Email"],
    "defaultLanguageCode": "en",
    "includeTypelessEntities": true,
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
            "confidence": -1
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

+ [Aptitudes predefinidas](cognitive-search-predefined-skills.md)
+ [Definición de un conjunto de aptitudes](cognitive-search-defining-skillset.md)