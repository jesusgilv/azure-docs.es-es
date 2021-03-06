---
title: 'Streaming de archivos de vídeo con Azure Media Services: .NET | Microsoft Docs'
description: Siga los pasos de este inicio rápido para crear una nueva cuenta de Azure Media Services, codificar un archivo y hacer streaming a Azure Media Player.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
keywords: azure media services, streaming
ms.service: media-services
ms.workload: media
ms.topic: quickstart
ms.custom: mvc
ms.date: 11/11/2018
ms.author: juliako
ms.openlocfilehash: fc8fc1af51332df032e864c84791791a38bc8601
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/13/2018
ms.locfileid: "51612227"
---
# <a name="quickstart-stream-video-files---net"></a>Inicio rápido: streaming de archivos de vídeo: .NET

Este inicio rápido muestra lo fácil que es codificar e iniciar el streaming de vídeos en una amplia variedad de navegadores y dispositivos con Azure Media Services. Se puede especificar contenido de entrada con direcciones URL de HTTPS, direcciones URL de SAS o rutas de acceso a archivos ubicados en Azure Blob Storage.
El ejemplo de este tema permite codificar contenido que se hace accesible a través de una dirección URL HTTPS. Tenga en cuenta que, actualmente, AMS v3 no admite la codificación de transferencia fragmentada a través de direcciones URL de HTTPS.

Al final del inicio rápido, podrá hacer streaming de un vídeo.  

![Reproducción del vídeo](./media/stream-files-dotnet-quickstart/final-video.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Requisitos previos

- Si no tiene Visual Studio instalado, puede obtener [Visual Studio Community 2017](https://www.visualstudio.com/thank-you-downloading-visual-studio/?sku=Community&rel=15).
- Instale y use la CLI localmente, para este artículo es preciso usar la CLI de Azure versión 2.0 o posterior. Ejecute `az --version` para encontrar la versión que tiene. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure](/cli/azure/install-azure-cli). 

    Actualmente no todos los comandos de la [CLI de Media Services v3](https://aka.ms/ams-v3-cli-ref) funcionan en Azure Cloud Shell. Se recomienda usar la CLI localmente.

- [Cree una cuenta de Media Services](create-account-cli-how-to.md).

## <a name="download-the-sample"></a>Descarga del ejemplo

Clone un repositorio GitHub que contenga el ejemplo de .NET de streaming en la máquina con el siguiente comando:  

 ```bash
 git clone http://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts.git
 ```

El ejemplo se encuentra en la carpeta [EncodeAndStreamFiles](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/tree/master/AMSV3Quickstarts/EncodeAndStreamFiles).

Este ejemplo realiza las acciones siguientes:

1. Crea una transformación (en primer lugar, comprueba si existe la transformación especificada). 
2. Crea un recurso de salida que se utiliza como la salida del trabajo de codificación.
3. Crea la entrada del trabajo que se basa en una dirección URL HTTPS.
4. Envía al trabajo de codificación con la entrada y salida que se han creado anteriormente.
5. Comprueba el estado del trabajo.
6. Crea un objeto StreamingLocator.
7. Crea direcciones URL de streaming.

Para obtener una explicación sobre qué hace cada función en el ejemplo, examine el código y observe los comentarios en [este archivo de origen](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs).

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

## <a name="run-the-sample-app"></a>Ejecutar la aplicación de ejemplo

Al ejecutar la aplicación, se muestran las direcciones URL que se pueden usar para reproducir el vídeo con diferentes protocolos. 

1. Presione Control + F5 para ejecutar la aplicación *EncodeAndStreamFiles*.
2. Elija el protocolo **HLS** de Apple (termina por *manifest(format=m3u8-aapl)*) y copie la dirección URL del streaming desde la consola.

![Salida](./media/stream-files-tutorial-with-api/output.png)

En el [código fuente](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs) del ejemplo, puede ver cómo se compila la dirección URL. Para compilarla, debe concatenar el nombre de host del punto de conexión del streaming y la ruta de acceso del localizador del streaming.  

## <a name="test-with-azure-media-player"></a>Prueba con Azure Media Player

Para probar el streaming, este artículo usa Azure Media Player. 

> [!NOTE]
> Si el reproductor está hospedado en un sitio https, asegúrese de actualizar la dirección URL a "https".

1. Abra un explorador web y vaya a [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/).
2. En el cuadro **Dirección URL:**, pegue uno de los valores de la dirección URL de streaming que se obtuvo al ejecutar la aplicación. 
3. Presione **Actualizar Player**.

Azure Media Player puede usarse para realizar pruebas, pero no debe usarse en un entorno de producción. 

## <a name="clean-up-resources"></a>Limpieza de recursos

Si ya no necesita ninguno de los recursos del grupo de recursos, incluida las cuentas de almacenamiento y de Media Services que ha creado en este inicio rápido, elimine el grupo de recursos.

Ejecute el siguiente comando de la CLI:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="examine-the-code"></a>Examen del código

Para obtener una explicación sobre qué hace cada función en el ejemplo, examine el código y observe los comentarios en [este archivo de origen](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs).

El tutorial [Carga, codificación y streaming de archivos](stream-files-tutorial-with-api.md) proporciona un ejemplo de streaming más avanzado con explicaciones detalladas. 

## <a name="multithreading"></a>Multithreading

Los SDK de Azure Media Services v3 no son seguros para subprocesos. Al trabajar con una aplicación que admite multithreading, debe generar un nuevo objeto AzureMediaServicesClient por subproceso.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Tutorial: Carga, codificación y streaming de archivos](stream-files-tutorial-with-api.md)
