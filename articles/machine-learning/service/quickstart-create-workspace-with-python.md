---
title: 'Guía de inicio rápido:  Introducción a Python'
titleSuffix: Azure Machine Learning service
description: Introducción a Azure Machine Learning Service en Python. Use el SDK de Python para crear un área de trabajo, que es el bloque fundamental en la nube que se utiliza para experimentar, entrenar e implementar modelos de aprendizaje automático.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: quickstart
ms.reviewer: sgilley
author: hning86
ms.author: haining
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 3ab55cec4b8483cf254ec3d9fe68521baca9cdf5
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/12/2018
ms.locfileid: "53268524"
---
# <a name="quickstart-use-python-sdk-to-get-started-with-azure-machine-learning"></a>Guía de inicio rápido: Uso del SDK de Python para empezar a usar Azure Machine Learning

En esta guía de inicio rápido, usará el SDK de Azure Machine Learning para Python para crear y después usar un [área de trabajo](concept-azure-machine-learning-architecture.md) del servicio Machine Learning. Esta área de trabajo se encuentra en la nube y es el bloque fundamental que se utiliza para experimentar, entrenar e implementar modelos de aprendizaje automático con Machine Learning. En esta guía de inicio rápido, primero debe configurar su propio entorno de Python y el servidor de Jupyter Notebook. Para ejecutar sin necesidad de instalación, vea [Guía de inicio rápido: Uso de Azure Portal para empezar a trabajar con Azure Machine Learning](quickstart-get-started.md).

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2G9N6]

En este tutorial, instalará el SDK de Python y hará lo siguiente:

* Crear un área de trabajo en la suscripción de Azure.
* Crear un archivo de configuración para dicha área de trabajo para usarlo posteriormente en otros cuadernos y scripts.
* Escribir código que registra valores dentro del área de trabajo.
* Ver los valores registrados en el área de trabajo.

En esta guía de inicio rápido, va a crear un área de trabajo y un archivo de configuración. Pueden usarlos como requisitos previos para otros tutoriales y artículos de procedimientos de Machine Learning. Igual que en otros servicios de Azure, hay límites y cuotas asociados a Machine Learning. [Más información acerca de las cuotas y cómo solicitarlas.](how-to-manage-quotas.md)

Los siguientes recursos de Azure se agregan automáticamente al área de trabajo cuando estén disponibles por regiones:
 
- [Azure Container Registry](https://azure.microsoft.com/services/container-registry/)
- [Azure Storage](https://azure.microsoft.com/services/storage/)
- [Azure Application Insights](https://azure.microsoft.com/services/application-insights/) 
- [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)

Si no tiene una suscripción a Azure, cree una cuenta gratuita antes de empezar. Pruebe hoy mismo la [versión gratuita o de pago de Azure Machine Learning Service](http://aka.ms/AMLFree).

## <a name="install-the-sdk"></a>Instalación del SDK

>[!NOTE]
> El código de este artículo requiere el SDK de Azure Machine Learning, versión 1.0.2 o posterior. 

*Omita esta sección si utiliza una máquina virtual de ciencia de datos creada después del 27 de septiembre de 2018.* Estas máquinas virtuales de ciencia de datos vienen con el SDK de Python preinstalado.

Antes de instalar el SDK, se recomienda crear un entorno aislado de Python. Aunque en esta guía de inicio rápido se usa [Miniconda](https://conda.io/docs/user-guide/install/index.html), también puede usar la versión completa de [Anaconda](https://www.anaconda.com/) instalada o [Python virtualenv](https://virtualenv.pypa.io/en/stable/).

### <a name="install-miniconda"></a>Instalación de Miniconda


[Descargue](https://conda.io/miniconda.html) e instale Miniconda. Seleccione Python 3.7 o cualquier versión posterior. No seleccione la versión 2.x de Python.

### <a name="create-an-isolated-python-environment"></a>Creación de un entorno aislado de Python 

Abra una ventana de línea de comandos. Después, cree un nuevo entorno de conda denominado `myenv` con Python 3.6.

```shell
conda create -n myenv -y Python=3.6
```

Active el entorno.

  ```shell
  conda activate myenv
  ```

### <a name="install-the-sdk"></a>Instalación del SDK

En el entorno de conda activado, instale el SDK. Este código instala los componentes principales del SDK de Machine Learning. También instala un servidor de Jupyter Notebook en el entorno de conda. La instalación tarda unos minutos en completarse, según la configuración de la máquina.

```sh
# install Jupyter
conda install nb_conda

# install the base SDK and Jupyter Notebook
pip install azureml-sdk[notebooks]

```

También puede usar diferentes palabras clave "adicionales" para instalar componentes adicionales del SDK.

```sh
# install the base SDK and auto ml components
pip install azureml-sdk[automl]

# install the base SDK and model explainability component
pip install azureml-sdk[explain]

# install the base SDK and experimental components
pip install azureml-sdk[contrib]
```

Utilice esta instalación en lugar de un entorno de Databricks.

```
# install the base SDK and automl components in Azure Databricks environment
# read more at: https://github.com/Azure/MachineLearningNotebooks/tree/master/databricks
pip install azureml-sdk[databricks]
```


## <a name="create-a-workspace"></a>Crear un área de trabajo

Para iniciar Jupyter Notebook, escriba este comando.
```shell
jupyter notebook
```

En la ventana del explorador, cree un cuaderno nuevo mediante el kernel `Python 3` predeterminado. 

Para mostrar la versión de SDK, escriba el siguiente código de Python en una celda del cuaderno y ejecútelo.

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=import)]

Cree un nuevo grupo de recursos de Azure y un nuevo área de trabajo.

Busque un valor para `<azure-subscription-id>` en la [lista de suscripciones de Azure Portal](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Use cualquier suscripción en la que su rol sea propietario o colaborador.

```python
from azureml.core import Workspace
ws = Workspace.create(name='myworkspace',
                      subscription_id='<azure-subscription-id>',    
                      resource_group='myresourcegroup',
                      create_resource_group=True,
                      location='eastus2' # or other supported Azure region  
                     )
```

La ejecución del código anterior puede desencadenar una nueva ventana del explorador en la que puede iniciar sesión en su cuenta de Azure. Después de iniciar sesión, el token de autenticación se almacena en la caché local.

Para ver los detalles del área de trabajo, como el almacenamiento asociado, el registro de contenedor y el almacén de claves, escriba el código siguiente.

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=getDetails)]


## <a name="write-a-configuration-file"></a>Escritura de un archivo de configuración

Guarde los detalles del área de trabajo en un archivo de configuración del directorio actual. Dicho archivo se llama 'aml_config\config.json'.  

Este archivo de configuración del área de trabajo facilita la carga posterior de esta misma área de trabajo. Puede cargarlo con otros cuadernos y scripts en el mismo directorio o en un subdirectorio. 

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=writeConfig)]


La llamada API `write_config()` crea el archivo de configuración en el directorio actual. El archivo `config.json` contiene el script siguiente.

```json
{
    "subscription_id": "<azure-subscription-id>",
    "resource_group": "myresourcegroup",
    "workspace_name": "myworkspace"
}
```

## <a name="use-the-workspace"></a>Uso del área de trabajo

Escriba código que use las API básicas del SDK para realizar un seguimiento de las ejecuciones del experimento.

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=useWs)]


## <a name="view-logged-results"></a>Visualización de los resultados registrados
Cuando finaliza la ejecución, puede ver la ejecución del experimento en Azure Portal. Utilice el código siguiente para imprimir una dirección URL en los resultados de la última ejecución.

```python
print(run.get_portal_url())
```

Use el vínculo para ver los valores registrados en Azure Portal en el explorador.

![Valores registrados en el portal](./media/quickstart-create-workspace-with-python/logged-values.png)

## <a name="clean-up-resources"></a>Limpieza de recursos 
>[!IMPORTANT]
>Los recursos que creó pueden usarse como requisitos previos para otros tutoriales y artículos de procedimientos de Machine Learning.

Si no va a usar los recursos creados de aquí, elimínelos para no incurrir en cargos.

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=delete)]


## <a name="next-steps"></a>Pasos siguientes

Ha creado los recursos necesarios que necesita para experimentar con modelos e implementarlos. También ha ejecutado código en un cuaderno. Y ha explorado el historial de ejecución desde dicho código en el área de trabajo en la nube.

Necesita algunos paquetes más en su entorno para usarlos con los tutoriales de Machine Learning.

1. En el explorador, cierre el cuaderno.
1. En la ventana de línea de comandos, use `Ctrl`+`C` para detener el servidor del cuaderno.
1. Instale los paquetes adicionales.

    ```shell
    conda install -y cython matplotlib scikit-learn pandas numpy
    pip install azureml-sdk[automl]
    ```


Tras instalar dichos paquetes, siga los tutoriales para entrenar un modelo e implementarlo. 

> [!div class="nextstepaction"]
> [Tutorial: Entrenamiento de un modelo de clasificación de imágenes](tutorial-train-models-with-aml.md)

También puede explorar [ejemplos más avanzados en GitHub](https://aka.ms/aml-notebooks).
