---
title: Cómo detener la supervisión del clúster de Azure Kubernetes Service | Microsoft Docs
description: En este artículo se describe cómo puede interrumpir la supervisión del clúster de Azure AKS con Azure Monitor para contenedores.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/04/2018
ms.author: magoedte
ms.openlocfilehash: 6865fac08b8c607f7053b334c648766cc1e7c291
ms.sourcegitcommit: a4e4e0236197544569a0a7e34c1c20d071774dd6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/15/2018
ms.locfileid: "51714748"
---
# <a name="how-to-stop-monitoring-your-azure-kubernetes-service-aks-with-azure-monitor-for-containers-preview"></a>Cómo detener la supervisión de Azure Kubernetes Service (AKS) con Azure Monitor para contenedores (versión preliminar)

Si después de habilitar la supervisión del clúster de AKS decide ya no desea supervisarla, puede *no participar*.  En este artículo se muestra cómo realizar esta tarea mediante la CLI de Azure o con las plantillas de Azure Resource Manager proporcionadas.  


## <a name="azure-cli"></a>Azure CLI
Use el comando [az aks disable-addons](https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-disable-addons) para deshabilitar Azure Monitor para contenedores. El comando quita el agente de los nodos del clúster, no la solución ni los datos ya recopilados y almacenados en el recurso de Log Analytics.  

```azurecli
az aks disable-addons -a monitoring -n MyExistingManagedCluster -g MyExistingManagedClusterRG
```

Para volver a habilitar la supervisión para el clúster, consulte [Habilitación de la supervisión mediante la CLI de Azure](container-insights-onboard.md#enable-monitoring-using-azure-cli).

## <a name="azure-resource-manager-template"></a>Plantilla del Administrador de recursos de Azure
Se proporcionan dos plantillas de Azure Resource Manager para permitir la eliminación de los recursos de la solución de manera coherente y repetida en el grupo de recursos. Una es una plantilla JSON que especifica la configuración para *no participar* y la otra contiene valores de parámetros que debe configurar para especificar el identificador de recurso del clúster de AKS y el grupo de recursos en el que se implementa el clúster. 

Si no conoce el concepto de implementación de recursos mediante una plantilla, consulte:
* [Implementación de recursos con las plantillas de Resource Manager y Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md)
* [Implementación de recursos con plantillas de Resource Manager y la CLI de Azure](../../azure-resource-manager/resource-group-template-deploy-cli.md)

>[!NOTE]
>La plantilla debe implementarse en el mismo grupo de recursos que el clúster.
>

Si decide usar la CLI de Azure, primero debe instalar y usar la CLI localmente. Debe ejecuta la versión 2.0.27 de la CLI de Azure, o cualquier versión posterior. Para identificar la versión, ejecute `az --version`. Si necesita instalar o actualizar la CLI de Azure, consulte [Instalación de la CLI de Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). 

### <a name="create-template"></a>Creación de una plantilla

1. Copie y pegue la siguiente sintaxis JSON en el archivo:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "aksResourceId": {
           "type": "string",
           "metadata": {
             "description": "AKS Cluster Resource ID"
           }
       },
      "aksResourceLocation": {
        "type": "string",
        "metadata": {
           "description": "Location of the AKS resource e.g. \"East US\""
         }
       }
    },
    "resources": [
      {
        "name": "[split(parameters('aksResourceId'),'/')[8]]",
        "type": "Microsoft.ContainerService/managedClusters",
        "location": "[parameters('aksResourceLocation')]",
        "apiVersion": "2018-03-31",
        "properties": {
          "mode": "Incremental",
          "id": "[parameters('aksResourceId')]",
          "addonProfiles": {
            "omsagent": {
              "enabled": false,
              "config": null
            }
           }
         }
       }
      ]
    }
    ```

2. Guarde este archivo como **OptOutTemplate.json** en una carpeta local.
3. Pegue la siguiente sintaxis de JSON en el archivo:

    ```json
    {
     "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
     "contentVersion": "1.0.0.0",
     "parameters": {
       "aksResourceId": {
         "value": "/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroup>/providers/Microsoft.ContainerService/managedClusters/<ResourceName>"
      },
      "aksResourceLocation": {
        "value": "<aksClusterRegion>"
        }
      }
    }
    ```

4. Edite los valores de **aksResourceId** y **aksResourceLocation** usando los valores del clúster de AKS, que puede encontrar en la página **Propiedades** del clúster seleccionado.

    ![Página de propiedades del contenedor](media/container-insights-optout/container-properties-page.png)

    Mientras está en la página de **propiedades**, copie también el **identificador de recurso del área de trabajo**. Este valor es necesario si decide que desea eliminar el área de trabajo de Log Analytics más adelante. La eliminación del área de trabajo de Log Analytics no se realiza como parte de este proceso. 

5. Guarde este archivo como **OptOutParam.json** en una carpeta local.
6. Está listo para implementar esta plantilla. 

### <a name="remove-the-solution-using-azure-cli"></a>Eliminación de la solución mediante la CLI de Azure
Ejecute el siguiente comando con la CLI de Azure en Linux para eliminar la solución y borrar la configuración en el clúster de AKS.

```azurecli
az login   
az account set --subscription "Subscription Name" 
az group deployment create --resource-group <ResourceGroupName> --template-file ./OptOutTemplate.json --parameters @./OptOutParam.json  
```

El cambio de configuración puede tardar unos minutos en completarse. Cuando termine, verá un mensaje similar al siguiente que incluye el resultado:

```azurecli
ProvisioningState       : Succeeded
```

### <a name="remove-the-solution-using-powershell"></a>Eliminación de la solución mediante PowerShell

Ejecute los siguientes comandos de PowerShell en la carpeta que contiene la plantilla para eliminar la solución y borrar la configuración del clúster de AKS.    

```powershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName <yourSubscriptionName>
New-AzureRmResourceGroupDeployment -Name opt-out -ResourceGroupName <ResourceGroupName> -TemplateFile .\OptOutTemplate.json -TemplateParameterFile .\OptOutParam.json
```

El cambio de configuración puede tardar unos minutos en completarse. Cuando termine, verá un mensaje similar al siguiente que incluye el resultado:

```powershell
ProvisioningState       : Succeeded
```

Si el área de trabajo se creó solamente para admitir la supervisión del clúster y ya no es necesario, tiene que eliminarlo manualmente. Si no está familiarizado con la eliminación de un área de trabajo, consulte [Eliminación de una área de trabajo de Azure Log Analytics con Azure Portal](../../log-analytics/log-analytics-manage-del-workspace.md). No se olvide del **identificador de recurso del área de trabajo** que copió anteriormente en el paso 4, lo necesitará. 

