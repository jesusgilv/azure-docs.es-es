---
title: 'Guía de inicio rápido: Compilación, implementación y uso de un modelo personalizado con Custom Translator'
titleSuffix: Azure Cognitive Services
description: En esta guía de inicio rápido recorrerá el proceso paso a paso para compilar un sistema de traducción mediante Custom Translator.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.component: custom-translator
ms.date: 11/13/2018
ms.author: v-rada
ms.topic: quickstart
ms.openlocfilehash: cb713ac53b399d8ba79d003ee6d948bb5af5220b
ms.sourcegitcommit: 8314421d78cd83b2e7d86f128bde94857134d8e1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/19/2018
ms.locfileid: "51975779"
---
# <a name="quickstart-build-deploy-and-use-a-custom-model-for-translation"></a>Guía de inicio rápido: Compilación, implementación y uso de un modelo personalizado para la traducción

En este artículo se proporcionan instrucciones detalladas para compilar un sistema de traducción con Custom Translator.

## <a name="prerequisites"></a>Requisitos previos

1. Para usar el portal de [Custom Translator](https://portal.customtranslator.azure.ai), necesitará una [cuenta Microsoft](https://signup.live.com) o [cuenta de Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) (cuenta de organización hospedada en Azure) para iniciar sesión.

2. Una suscripción a Translator Text API a través de Azure Portal. Necesitará la clave de suscripción de Translator Text API para asociarla con el área de trabajo de Custom Translator. Consulte [Cómo suscribirse a Translator Text API](https://docs.microsoft.com/azure/cognitive-services/translator/translator-text-how-to-signup).

3. Cuando disponga de todo lo anterior, inicie sesión en el portal de [Custom Translator](https://portal.customtranslator.azure.ai). Una vez allí, vaya a la página Configuración en la que podrá asociar la clave de suscripción de Microsoft Translator Text API con el área de trabajo.

## <a name="create-a-project"></a>Crear un proyecto

En la página de aterrizaje del portal de Custom Translator, haga clic en Nuevo proyecto. En el cuadro de diálogo puede especificar el nombre de proyecto deseado, el par de idiomas y la categoría, así como otros campos importantes. Después, guarde el proyecto. Para más información, visite [Creación de proyectos](how-to-create-project.md).

![Crear proyecto](media/quickstart/ct-how-to-create-project.png)


## <a name="upload-documents"></a>Cargar documentos

A continuación, cargue los conjuntos de documentos de [aprendizaje](training-and-model.md#training-dataset-for-custom-translator), [optimización](training-and-model.md#tuning-dataset-for-custom-translator) y [pruebas](training-and-model.md#testing-dataset-for-custom-translator). Puede cargar documentos [paralelos](what-are-parallel-documents.md) y documentos de cuadro combinado. También puede cargar un [diccionario](what-is-dictionary.md).

Puede cargar documentos desde la pestaña de documentos o desde la página de un proyecto concreto.

![Cargar documentos](media/quickstart/ct-how-to-upload.png)

Al cargar los documentos, elija el tipo de documento (aprendizaje, optimización o pruebas) y el par de idiomas. Si carga documentos paralelos tendrá que especificar además un nombre de documento. Para más información, visite [Carga de documentos](how-to-upload-document.md).

## <a name="create-a-model"></a>Crear un modelo

Una vez cargados todos los documentos necesarios el siguiente paso es crear el modelo.

Seleccione el proyecto que ha creado. Verá todos los documentos que ha cargado que compartan el par de idiomas con este proyecto. Seleccione los documentos que desea incluir en el modelo. Puede seleccionar datos de [aprendizaje](training-and-model.md#training-dataset-for-custom-translator), [optimización](training-and-model.md#tuning-dataset-for-custom-translator) y [prueba](training-and-model.md#testing-dataset-for-custom-translator) o solo seleccionar datos de aprendizaje y dejar que Custom Translator compile automáticamente los conjuntos de datos de optimización y prueba del modelo.

![Crear un modelo](media/quickstart/ct-how-to-train.png)

Cuando haya acabado de seleccionar los documentos que desee, haga clic en el botón Crear modelo para crear el modelo e iniciar el aprendizaje. Puede ver el estado del aprendizaje y los detalles de todos los modelos entrenados, en la pestaña Modelos.

Para más información, visite [Creación de un modelo](how-to-train-model.md).

## <a name="analyze-your-model"></a>Análisis del modelo

Una vez que el aprendizaje se ha completado correctamente, inspeccione los resultados. La puntuación BLEU es una métrica que indica la calidad de la traducción. También puede comparar manualmente las traducciones realizadas con el modelo personalizado con las traducciones proporcionadas en el conjunto de pruebas. Para ello, vaya a la pestaña "Prueba" y haga clic en "Resultados del sistema". Si inspecciona manualmente varias de estas traducciones podrá hacerse una buena idea de la calidad de la traducción que su sistema ha producido. Para más información, visite la página de [resultados de pruebas del sistema](how-to-view-system-test-results.md).

## <a name="deploy-a-trained-model"></a>Implementación de un modelo entrenado

Cuando esté listo para implementar el modelo entrenado, haga clic en el botón "Implementar". Puede tener un modelo implementado por proyecto y puede ver el estado de la implementación en la columna Estado. Para más información, visite [Model Deployment](how-to-view-system-test-results.md#deploy-a-model) (Implementación de un modelo)

![Implementación de un modelo entrenado](media/quickstart/ct-how-to-deploy.png)

## <a name="use-a-deployed-model"></a>Uso de un modelo implementado

Se puede acceder a los modelos implementados a través de Microsoft Translator [Text API V3 especificando CategoryID](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl). Puede encontrar más información sobre Translator Text API en la página web de [referencia de API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference).

## <a name="next-steps"></a>Pasos siguientes

- Vaya a [Custom Translator workspace and manage your projects](workspace-and-project.md) (Área de trabajo de Custom Translator y administración de proyectos).
