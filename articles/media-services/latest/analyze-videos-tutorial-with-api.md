---
title: Análisis de vídeos con Media Services (Azure) | Microsoft Docs
description: Siga los pasos de este tutorial para analizar los vídeos con Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: tutorial
ms.date: 12/08/2018
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 42ffecec896265f99a8f1f0b43b47c1988a493d6
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/10/2018
ms.locfileid: "53133900"
---
# <a name="tutorial-analyze-videos-with-media-services-v3-using-apis"></a>Tutorial: Análisis de vídeos con Media Services v3 mediante API

En este tutorial se muestra cómo analizar vídeos con Azure Media Services. Hay muchos escenarios en los que puede desear obtener información detallada sobre los vídeos grabados o el contenido de audio. Por ejemplo, para lograr una mayor satisfacción del cliente, las organizaciones pueden ejecutar el procesamiento de voz a texto para convertir las grabaciones de soporte técnico al cliente en un catálogo que permite búsquedas, con índices y paneles. A continuación, los clientes pueden obtener información detallada sobre sus empresas, como una lista de las quejas más habituales, orígenes de dichas quejas, y más información útil.

En este tutorial se muestra cómo realizar las siguientes acciones:    

> [!div class="checklist"]
> * Creación de una cuenta de Media Services
> * Acceso a la API de Media Services
> * Configuración de la aplicación de ejemplo
> * Examen del código que analiza el vídeo especificado
> * Ejecución de la aplicación
> * Examen de la salida
> * Limpieza de recursos

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Requisitos previos

- Si no tiene Visual Studio instalado, puede obtener [Visual Studio Community 2017](https://www.visualstudio.com/thank-you-downloading-visual-studio/?sku=Community&rel=15).
- Instale y use la CLI localmente, para este artículo es preciso usar la CLI de Azure versión 2.0 o posterior. Ejecute `az --version` para encontrar la versión que tiene. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure](/cli/azure/install-azure-cli). 

    Actualmente no todos los comandos de la [CLI de Media Services v3](https://aka.ms/ams-v3-cli-ref) funcionan en Azure Cloud Shell. Se recomienda usar la CLI localmente.

- [Cree una cuenta de Media Services](create-account-cli-how-to.md).

    Asegúrese de recordar los valores que usó para el nombre de la cuenta de Media Services y el nombre del grupo de recursos.

## <a name="download-the-sample"></a>Descarga del ejemplo

Clone un repositorio GitHub que contenga el ejemplo de .NET en la máquina mediante el siguiente comando:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```

El ejemplo se encuentra en la carpeta [AnalyzeVideos](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/AnalyzeVideos).

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

## <a name="examine-the-code-that-analyzes-the-specified-video"></a>Examen del código que analiza el vídeo especificado

En esta sección se examinan las funciones definidas en el archivo [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/AnalyzeVideos/Program.cs) del proyecto *AnalyzeVideos*.

Este ejemplo realiza las acciones siguientes:

1. Crea una transformación y un trabajo que analiza el vídeo.
2. Crea un recurso de entrada y carga el vídeo en él. El recurso se usa como entrada del trabajo.
3. Crea un recurso de salida que almacena la salida del trabajo. 
4. Envía el trabajo.
5. Comprueba el estado del trabajo.
6. Descarga los archivos resultantes de la ejecución del trabajo. 

> [!NOTE]
> Si usa los valores preestablecidos de un analizador de audio o de vídeo, emplee Azure Portal para establecer que la cuenta tenga diez unidades reservadas de multimedia S3. Para más información, consulte [Información general del escalado de procesamiento de medios](../previous/media-services-scale-media-processing-overview.md).

### <a name="start-using-media-services-apis-with-net-sdk"></a>Uso de las API de Media Services con SDK de .NET

Para empezar a usar las API de Media Services con. NET, debe crear un objeto **AzureMediaServicesClient**. Para crear el objeto, debe proporcionar las credenciales necesarias para que el cliente se conecte a Azure mediante Azure AD. En el código que se clonó al principio del artículo, la función **GetCredentialsAsync** crea el objeto ServiceClientCredentials basándose en las credenciales proporcionadas en el archivo de configuración local. 

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CreateMediaServicesClient)]

### <a name="create-an-input-asset-and-upload-a-local-file-into-it"></a>Creación de un recurso de entrada y carga de un archivo local en él 

La función **CreateInputAsset** crea un nuevo [recurso](https://docs.microsoft.com/rest/api/media/assets) de entrada y carga en él el archivo de vídeo local especificado. Este recurso se utiliza como entrada para el trabajo de codificación. En Media Services v3, la entrada a un trabajo puede ser un recurso, o puede ser contenido que se pone a disposición de la cuenta de Media Services a través de direcciones URL HTTPS. Para más información sobre cómo codificar desde una dirección URL HTTPS, consulte [este](job-input-from-http-how-to.md) artículo.  

En Media Services v3, se utilizan las API de Azure Storage para cargar archivos. En el siguiente fragmento de código de .NET se muestra cómo hacerlo.

La función siguiente realiza estas acciones:

* Crea un recurso. 
* Obtiene una [dirección URL de SAS](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) que se puede escribir en el [contenedor de almacenamiento](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet?tabs=windows#upload-blobs-to-the-container) del recurso.
* Carga el archivo en el contenedor de almacenamiento mediante la dirección URL de SAS

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CreateInputAsset)]

### <a name="create-an-output-asset-to-store-the-result-of-the-job"></a>Creación de un recurso de salida para almacenar el resultado del trabajo 

El [recurso](https://docs.microsoft.com/rest/api/media/assets) de salida almacena el resultado del trabajo. El proyecto define la función **DownloadResults** que descarga los resultados de este recurso de salida en la carpeta "output", para que se pueda ver lo que tiene.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CreateOutputAsset)]

### <a name="create-a-transform-and-a-job-that-analyzes-videos"></a>Creación de una transformación y de un trabajo que analiza vídeos

Cuando se codifica o procesa contenido en Media Services, es un patrón común configurar los ajustes de codificación como una receta. Después, podría enviar un **trabajo** para aplicar esa receta a un vídeo. Al enviar nuevos trabajos a cada nuevo vídeo, está aplicando esa receta a todos los vídeos de la biblioteca. Una receta en Media Services se llama **transformación**. Para más información, consulte [Transformaciones y trabajos](transform-concept.md). En el ejemplo descrito en este tutorial se define una receta que analiza el vídeo especificado. 

#### <a name="transform"></a>Transformación

Al crear una nueva instancia de la [transformación](https://docs.microsoft.com/rest/api/media/transforms), debe especificar qué desea originar como salida. El parámetro requerido es un objeto **TransformOutput**, como se muestra en el código anterior. Cada objeto **TransformOutput** contiene un **valor preestablecido**. El **valor preestablecido** describe las instrucciones paso a paso de las operaciones de procesamiento de vídeo o audio que se utilizarán para generar el objeto **TransformOutput** deseado. En este ejemplo, se utiliza el valor preestablecido de **VideoAnalyzerPreset**  y el idioma ("en-US") se pasa al constructor. Este valor preestablecido permite extraer diversa información de audio y vídeo desde un vídeo. Puede usar el valor preestablecido de **AudioAnalyzerPreset** si necesita extraer diversa información de audio desde un vídeo. 

Al crear una **transformación**, debe comprobar primero si ya existe una con el método **Get**, tal como se muestra en el código siguiente.  En Media Services v3, los métodos **Get** en las entidades devuelven **null** si la entidad no existe (una comprobación sin distinción entre mayúsculas y minúsculas en el nombre).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#EnsureTransformExists)]

#### <a name="job"></a>Trabajo

Como se mencionó anteriormente, el objeto [Transform](https://docs.microsoft.com/rest/api/media/transforms) es la receta y un [trabajo](https://docs.microsoft.com/rest/api/media/jobs) es la solicitud real a Media Services para aplicar que dicho objeto **Transform** a un determinado contenido de audio o vídeo de entrada. El **trabajo** especifica información como la ubicación del vídeo de entrada y la ubicación de la salida. Puede especificar la ubicación del vídeo mediante: direcciones URL HTTPS, direcciones URL de SAS o recursos que se encuentran en su cuenta de Media Services. 

En este ejemplo, la entrada de trabajo es un vídeo local.  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#SubmitJob)]

### <a name="wait-for-the-job-to-complete"></a>Espere a que el trabajo se complete

El trabajo tarda algún tiempo en completarse y cuando lo hace querrá recibir una notificación. Hay distintas opciones para obtener notificaciones sobre la realización del [trabajo](https://docs.microsoft.com/rest/api/media/jobs). La opción más sencilla (que se muestra aquí) es para utilizar el sondeo. 

El sondeo no es un procedimiento recomendado para aplicaciones de producción debido a la posible latencia. El sondeo se puede limitar si se sobreutiliza en una cuenta. Los desarrolladores deben utilizar en su lugar Event Grid.

Event Grid está diseñado para una alta disponibilidad, un rendimiento consistente y una escala dinámica. Con Event Grid, sus aplicaciones pueden escuchar y reaccionar a eventos de casi todos los servicios de Azure y de orígenes personalizados. El control de eventos sencillo y reactivo basado en HTTP le ayuda a crear soluciones eficaces con filtrado y enrutamiento de eventos inteligente. Consulte [Enrutamiento de eventos a un punto de conexión web personalizado](job-state-events-cli-how-to.md).

El **trabajo** pasa normalmente por los siguientes estados: **Programado**, **En cola**, **Procesando**, **Finalizado** (el estado final). Si el trabajo ha encontrado un error, obtendrá el estado **Error**. Si el trabajo está en proceso de cancelación, obtendrá **Cancelando** y **Cancelado** cuando haya terminado.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#WaitForJobToFinish)]

### <a name="download-the-result-of-the-job"></a>Descarga del resultado del trabajo

La siguiente función descarga los resultados del [recurso](https://docs.microsoft.com/rest/api/media/assets) de salida en la carpeta "output", para que pueda examinar los resultados del trabajo. 

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#DownloadResults)]

### <a name="clean-up-resource-in-your-media-services-account"></a>Limpieza de recursos en la cuenta de Media Services

Por lo general, debe limpiar todo excepto los objetos que piensa reutilizar (normalmente, reutilizará transformaciones y conservará objetos StreamingLocators). Si desea que la cuenta esté limpia después de la experimentación, debe eliminar los recursos que no piensa volver a usar. Por ejemplo, el código siguiente elimina los trabajos.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CleanUp)]

## <a name="run-the-sample-app"></a>Ejecutar la aplicación de ejemplo

Presione Ctrl+F5 para ejecutar la aplicación *AnalyzeVideos*.

Cuando se ejecuta el programa, el trabajo produce miniaturas para cada rostro que encuentra en el vídeo. También produce el archivo insights.json.

## <a name="examine-the-output"></a>Examen de la salida

El archivo de salida de análisis de vídeos se denomina insights.json. Este archivo contiene información detallada sobre el vídeo. Puede encontrar la descripción de los elementos que se encuentran en el archivo json en el artículo [Media intelligence](intelligence-concept.md) (Inteligencia multimedia).

## <a name="clean-up-resources"></a>Limpieza de recursos

Si ya no necesita ninguno de los recursos del grupo de recursos, incluida las cuentas de almacenamiento y de Media Services que ha creado en este tutorial, elimine el grupo de recursos que ha creado antes. 

Ejecute el siguiente comando de la CLI:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="multithreading"></a>Multithreading

Los SDK de Azure Media Services v3 no son seguros para subprocesos. Al trabajar con una aplicación que admite multithreading, debe generar un nuevo objeto AzureMediaServicesClient por subproceso.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Tutorial: Carga, codificación y streaming de archivos](stream-files-tutorial-with-api.md)
