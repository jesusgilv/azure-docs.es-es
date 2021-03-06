---
title: Guía de migración de API de v1 a v2
titleSuffix: Azure Cognitive Services
description: Obtenga información sobre cómo migrar al conjunto de API más reciente.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/06/2018
ms.author: diberry
ms.openlocfilehash: 57742d72c8be0dcd999bc8b73f6d598263429c12
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/23/2018
ms.locfileid: "49646582"
---
# <a name="api-v2-migration-guide"></a>Guía para la migración a la v2 de la API
La versión 1 de las API de [punto de conexión](https://aka.ms/v1-endpoint-api-docs) y [creación](https://aka.ms/v1-authoring-api-docs) estará en desuso. Use esta guía para comprender cómo migrar a la versión 2 de las API de [punto de conexión](https://aka.ms/luis-endpoint-apis) y [creación](https://aka.ms/luis-authoring-apis). 

## <a name="new-azure-regions"></a>Nuevas regiones de Azure
LUIS tiene nuevas [regiones](https://aka.ms/LUIS-regions) que proporciona para las API de LUIS. LUIS proporciona otro sitio web para grupos de regiones. La aplicación se debe crear en la misma región que espera consultar. Las aplicaciones no migran las regiones de forma automática. Exporte la aplicación de una región y, luego, impórtela en otra para que esté disponible en una nueva región.

## <a name="authoring-route-changes"></a>Cambios de la ruta de creación
La ruta de API de creación ha pasado de usar la ruta **prog** a usar la ruta **api**.


| version | ruta |
|--|--|
|1|/luis/v1.0/**prog**/apps|
|2|/luis/**api**/v2.0/apps|


## <a name="endpoint-route-changes"></a>Cambios de la ruta de punto de conexión
La API de punto de conexión tiene nuevos parámetros de cadena de consulta, así como otra respuesta. Si la marca detallada es true, todas las intenciones, independientemente de la puntuación, se devuelven en una matriz con el nombre de las intenciones, además del valor de topScoringIntent.

| version | Ruta de GET |
|--|--|
|1|/luis/v1/application?ID={appId}&q={q}|
|2|/luis/v2.0/apps/{appId}?q={q}[&timezoneOffset][&verbose][&spellCheck][&staging][&bing-spell-check-subscription-key][&log]|


Respuesta correcta de punto de conexión de v1:
```JSON
{
  "odata.metadata":"https://dialogice.cloudapp.net/odata/$metadata#domain","value":[
    {
      "id":"bccb84ee-4bd6-4460-a340-0595b12db294","q":"turn on the camera","response":"[{\"intent\":\"OpenCamera\",\"score\":0.976928055},{\"intent\":\"None\",\"score\":0.0230718572}]"
    }
  ]
}
```

Respuesta correcta de punto de conexión de v2:
```JSON
{
  "query": "forward to frank 30 dollars through HSBC",
  "topScoringIntent": {
    "intent": "give",
    "score": 0.3964121
  },
  "entities": [
    {
      "entity": "30",
      "type": "builtin.number",
      "startIndex": 17,
      "endIndex": 18,
      "resolution": {
        "value": "30"
      }
    },
    {
      "entity": "frank",
      "type": "frank",
      "startIndex": 11,
      "endIndex": 15,
      "score": 0.935219169
    },
    {
      "entity": "30 dollars",
      "type": "builtin.currency",
      "startIndex": 17,
      "endIndex": 26,
      "resolution": {
        "unit": "Dollar",
        "value": "30"
      }
    },
    {
      "entity": "hsbc",
      "type": "Bank",
      "startIndex": 36,
      "endIndex": 39,
      "resolution": {
        "values": [
          "BankeName"
        ]
      }
    }
  ]
}
```

## <a name="key-management-no-longer-in-api"></a>Administración de claves no incluida en la API
Las API de clave de punto de conexión de suscripción están en desuso y devuelven el error 410 "Desaparecido".

| version | ruta |
|--|--|
|1|/luis/v1.0/prog/subscriptions|
|1|/luis/v1.0/prog/subscriptions/{subscriptionKey}|

Las [claves de punto de conexión](luis-how-to-azure-subscription.md) de Azure se generan en Azure Portal. Asigne la clave a una aplicación de LUIS en la página **[Publicar](luis-how-to-manage-keys.md)**. No tiene que conocer el valor real de la clave. LUIS usa el nombre de la suscripción para realizar la asignación. 

## <a name="new-versioning-route"></a>Nueva ruta de control de versiones
El modelo de v2 se encuentra ahora en una [versión](luis-how-to-manage-versions.md). Un nombre de versión tiene 10 caracteres en la ruta. La versión predeterminada es "0.1".

| version | ruta |
|--|--|
|1|/luis/v1.0/**prog**/apps/{appId}/entities|
|2|/luis/**api**/v2.0/apps/{appId}/**versions**/{versionId}/entities|

## <a name="metadata-renamed"></a>Cambio en el nombre de los metadatos
Varias API que devuelven metadatos de LUIS tienen nombres nuevos.

| Nombre de ruta de la v1 | Nombre de ruta de la v2 |
|--|--|
|PersonalAssistantApps |assistants|
|applicationcultures|cultures|
|applicationdomains|domains|
|applicationusagescenarios|usagescenarios|


## <a name="sample-renamed-to-suggest"></a>"Sample" cambia de nombre a "suggest".
LUIS sugiere expresiones a partir de [expresiones de punto de conexión](luis-how-to-review-endoint-utt.md) existentes que podrían mejorar el modelo. En la versión anterior, esto se llamaba **sample**. En la nueva versión, el nombre ha cambiado de sample a **suggest**. Esto se denomina **[Review endpoint utterances](luis-how-to-review-endoint-utt.md)** (Revisar las expresiones de punto de conexión) en el sitio web de LUIS.

| version | ruta |
|--|--|
|1|/luis/v1.0/**prog**/apps/{appId}/entities/{entityId}/**sample**|
|1|/luis/v1.0/**prog**/apps/{appId}/intents/{intentId}/**sample**|
|2|/luis/**api**/v2.0/apps/{appId}/**versions**/{versionId}/entities/{entityId}/**suggest**|
|2|/luis/**api**/v2.0/apps/{appId}/**versions**/{versionId}/intents/{intentId}/**suggest**|


## <a name="create-app-from-prebuilt-domains"></a>Crear la aplicación a partir de dominios creados previamente
Los [dominios creados previamente](luis-how-to-use-prebuilt-domains.md) proporcionan un modelo de dominio predefinido. Los dominios creados previamente permiten desarrollar rápidamente la aplicación de LUIS para dominios comunes. Esta API permite crear una aplicación basada en un dominio creado previamente. La respuesta es el nuevo valor de appID.

|Ruta de la v2|Verbo|
|--|--|
|/luis/api/v2.0/apps/customprebuiltdomains  |get, post|
|/luis/api/v2.0/apps/customprebuiltdomains/{culture}  |get|

## <a name="importing-1x-app-into-2x"></a>Importación de una aplicación de 1.x a 2.x
El JSON de la aplicación 1.x exportado tiene algunas áreas que debe cambiar antes de importarla en [LUIS][LUIS] 2.0. 

### <a name="prebuilt-entities"></a>Entidades creadas previamente 
Las [entidades creadas previamente](luis-prebuilt-entities.md) han cambiado. Asegúrese de que está usando las entidades creadas previamente de la v2. Esto incluye el uso de [datetimeV2](luis-reference-prebuilt-datetimev2.md), en lugar de datetime. 

### <a name="actions"></a>Acciones
La propiedad actions ya no es válida. Debería estar vacía. 

### <a name="labeled-utterances"></a>Expresiones con etiqueta
En la v1, se permitía que las expresiones con etiqueta incluyeran espacios al principio o final de la palabra o frase. Se han quitado los espacios. 

## <a name="common-reasons-for-http-response-status-codes"></a>Motivos comunes de los códigos de estado de respuesta HTTP
Vea [LUIS API response codes](luis-reference-response-codes.md) (Códigos de respuesta de la API de LUIS).

## <a name="next-steps"></a>Pasos siguientes

Use la documentación de la v2 de la API para actualizar las llamadas de REST existentes a las API de [punto de conexión](https://aka.ms/luis-endpoint-apis) y [creación](https://aka.ms/luis-authoring-apis) de LUIS. 

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions