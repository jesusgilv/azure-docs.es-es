---
title: Incorporación de Azure Monitor para VM (versión preliminar) | Microsoft Docs
description: En este artículo se describe cómo incorporar y configurar Azure Monitor para VM para poder empezar a comprender el rendimiento de la aplicación distribuida y los problemas de estado identificados.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/13/2018
ms.author: magoedte
ms.openlocfilehash: 93cbb9a891f83eed623f095f208319c9071e287e
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/30/2018
ms.locfileid: "52635550"
---
# <a name="how-to-onboard-the-azure-monitor-for-vms-preview"></a>Cómo incorporar Azure Monitor para VM (versión preliminar)
En este artículo se describe cómo configurar Azure Monitor para VM con el fin de supervisar el estado del sistema operativo de las máquinas virtuales de Azure, los conjuntos de escalado de máquinas virtuales y las máquinas virtuales en el entorno, y de detectar y asignar las dependencias de aplicación que pueden hospedar.  

La habilitación de Azure Monitor para VM se logra mediante uno de los métodos siguientes y se proporcionan detalles sobre el uso de cada método más adelante en el artículo.  

* Una sola máquina virtual de Azure, seleccionando **Insights (versión preliminar)** directamente desde la VM.
* Varias VM de Azure con Azure Policy para asegurarse de que las VM nuevas y existentes que se evalúan tienen las dependencias necesarias instaladas y se han configurado correctamente.  Las VM no compatibles se notifican para que pueda decidir a partir de lo que no cumple los requisitos y cómo quiere corregirlo.  
* Varias VM o conjuntos de escalado de máquinas virtuales de Azure en una suscripción o grupo de recursos concreto mediante PowerShell.

## <a name="prerequisites"></a>Requisitos previos
Antes de empezar, asegúrese de conocer los elementos que se describen en las subsecciones siguientes.

### <a name="log-analytics"></a>Log Analytics 

Actualmente, se admite un área de trabajo de Log Analytics en las siguientes regiones:

  - Centro occidental de EE.UU.  
  - Este de EE. UU  
  - Europa occidental  
  - Sudeste Asiático<sup>1</sup>  

<sup>1</sup> Actualmente, esta región no admite la característica de estado de Azure Monitor para VM   

>[!NOTE]
>Se pueden incorporar máquinas virtuales de Azure desde cualquier región y desaparece la limitación de las regiones admitidas en el área de trabajo de Log Analytics.
>

Si no tiene ninguna área de trabajo, puede crearla a través de la [CLI de Azure](../../log-analytics/log-analytics-quick-create-workspace-cli.md), de [PowerShell](../../log-analytics/log-analytics-quick-create-workspace-posh.md) en [Azure Portal](../../log-analytics/log-analytics-quick-create-workspace.md) o con [Azure Resource Manager](../../log-analytics/log-analytics-template-workspace-configuration.md).  Si va a habilitar la supervisión en una sola máquina virtual de Azure desde Azure Portal, tiene la opción de crear un área de trabajo durante el proceso.  

La habilitación de la solución del escenario a escala requiere, en primer lugar, la configuración de lo siguiente en el área de trabajo de Log Analytics:

* Instalación de las soluciones **ServiceMap** e **InfrastructureInsights**. Solo puede realizarse mediante una plantilla de Azure Resource Manager proporcionada en este artículo.   
* Configure el área de trabajo de Log Analytics para que recopile contadores de rendimiento.

Para configurar el área de trabajo para el escenario a escala, consulte [Configuración del área de trabajo de Log Analytics para la implementación a escala](#setup-log-analytics-workspace).

### <a name="supported-operating-systems"></a>Sistemas operativos compatibles

La siguiente es una lista de los sistemas operativos Windows y Linux que son compatibles oficialmente con Azure Monitor para máquinas virtuales.  Más adelante en esta sección, encontrará una lista completa que detalla las versiones de kernel y las versiones de sistema operativo Linux principales y secundarias admitidas.

|Versión del SO |Rendimiento |Mapas |Health |  
|-----------|------------|-----|-------|  
|Windows Server 2016 1803 | X | X | X |
|Windows Server 2016 | X | X | X |  
|Windows Server 2012 R2 | X | X | |  
|Windows Server 2012 | X | X | |  
|Windows Server 2008 R2 | X | X| |  
|RHEL 7, 6| X | X| X |  
|Ubuntu 18.04, 16.04, 14.04 | X | X | X |  
|Cent OS Linux 7, 6 | X | X | X |  
|SLES 12 | X | X | X |  
|Oracle Linux 7 | X<sup>1</sup> | | X |  
|Oracle Linux 6 | X | X | X |  
|Debian 9.4, 8 | X<sup>1</sup> | | X | 

<sup>1</sup> La característica de rendimiento de Azure Monitor para VM sólo está disponible en Azure Monitor; no está disponible cuando se accede a esta desde el panel izquierdo de la VM de Azure directamente.  

>[!NOTE]
>La siguiente información se aplica a la compatibilidad del sistema operativo Linux:  
> - Se admiten solo versiones de kernel SMP Linux y predeterminados.  
> - Las versiones de kernel no estándar, como PAE y Xen, no son compatibles con ninguna distribución de Linux. Por ejemplo, un sistema con la cadena de versión de "2.6.16.21-0.8-xen" no es compatible.  
> - No se admiten los kernel personalizados, incluidas las recompilaciones de kernels estándar.  
> - No se admite el kernel de CentOSPlus.  


#### <a name="red-hat-linux-7"></a>Red Hat Linux 7

| Versión del SO | Versión del kernel |
|:--|:--|
| 7.0 | 3.10.0-123 |
| 7.1 | 3.10.0-229 |
| 7,2 | 3.10.0-327 |
| 7.3 | 3.10.0-514 |
| 7.4 | 3.10.0-693 |
| 7.5 | 3.10.0-862 |

#### <a name="red-hat-linux-6"></a>Red Hat Linux 6

| Versión del SO | Versión del kernel |
|:--|:--|
| 6.0 | 2.6.32-71 |
| 6.1 | 2.6.32-131 |
| 6.2 | 2.6.32-220 |
| 6.3 | 2.6.32-279 |
| 6.4. | 2.6.32-358 |
| 6.5 | 2.6.32-431 |
| 6.6 | 2.6.32-504 |
| 6.7 | 2.6.32-573 |
| 6,8 | 2.6.32-642 |
| 6.9 | 2.6.32-696 |

#### <a name="ubuntu-server"></a>Ubuntu Server

| Versión del SO | Versión del kernel |
|:--|:--|
| Ubuntu 18.04 | kernel 4.15.* |
| Ubuntu 16.04.3 | kernel 4.15.* |
| 16.04 | 4.4.\*<br>4.8.\*<br>4.10.\*<br>4.11.\*<br>4.13.\* |
| 14.04 | 3.13.\*<br>4.4.\* |

#### <a name="oracle-enterprise-linux-6-with-unbreakable-enterprise-kernel"></a>Oracle Enterprise Linux 6 con Unbreakable Enterprise Kernel
| Versión del SO | Versión del kernel
|:--|:--|
| 6.2 | Oracle 2.6.32-300 (UEK R1) |
| 6.3 | Oracle 2.6.39-200 (UEK R2) |
| 6.4. | Oracle 2.6.39-400 (UEK R2) |
| 6.5 | Oracle 2.6.39-400 (UEK R2 i386) |
| 6.6 | Oracle 2.6.39-400 (UEK R2 i386) |

#### <a name="oracle-enterprise-linux-5-with-unbreakable-enterprise-kernel"></a>Oracle Enterprise Linux 5 con Unbreakable Enterprise Kernel

| Versión del SO | Versión del kernel
|:--|:--|
| 5.10 | Oracle 2.6.39-400 (UEK R2) |
| 5.11 | Oracle 2.6.39-400 (UEK R2) |

#### <a name="suse-linux-12-enterprise-server"></a>SUSE Linux 12 Enterprise Server

| Versión del SO | Versión del kernel
|:--|:--|
|12 SP2 | 4.4.* |
|12 SP3 | 4.4.* |

### <a name="microsoft-dependency-agent"></a>Microsoft Dependency Agent
La asignación de Azure Monitor para VM obtiene sus datos de Microsoft Dependency Agent. Dependency Agent usa el agente de Log Analytics para su conexión a Log Analytics y, por lo tanto, un sistema debe tenerlo instalado y configurado con Dependency Agent. Cuando se habilita Azure Monitor para VM para una sola máquina virtual de Azure o cuando se usan los métodos de implementación a escala, la extensión de Dependency Agent de VM de Azure se usa para instalar el agente como parte de la experiencia de incorporación. Con un entorno híbrido, Dependency Agent puede descargarse e instalarse manualmente o mediante un método de implementación automatizada en las máquinas virtuales hospedadas fuera de Azure.  

En la tabla siguiente se describen los orígenes conectados que son compatibles con la característica Asignación en un entorno híbrido.

| Origen conectado | Compatible | DESCRIPCIÓN |
|:--|:--|:--|
| Agentes de Windows | SÍ | Además del [agente de Log Analytics para Windows](../../azure-monitor/platform/log-analytics-agent.md), los agentes de Windows requieren Microsoft Dependency Agent. Consulte los [sistemas operativos compatibles](#supported-operating-systems) para obtener una lista completa de las versiones de sistema operativo. |
| Agentes de Linux | SÍ | Además del [agente de Log Analytics para Linux](../../azure-monitor/platform/log-analytics-agent.md), los agentes de Linux requieren Microsoft Dependency Agent. Consulte los [sistemas operativos compatibles](#supported-operating-systems) para obtener una lista completa de las versiones de sistema operativo. |
| Grupo de administración de System Center Operations Manager | Sin  | |  

Dependency Agent se puede descargar desde la ubicación siguiente.

| Archivo | SO | Versión | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) |  Windows | 9.7.1 | 55030ABF553693D8B5112569FB2F97D7C54B66E9990014FC8CC43EFB70DE56C6 |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.7.1 | 43C75EF0D34471A0CBCE5E396FFEEF4329C9B5517266108FA5D6131A353D29FE |

## <a name="role-based-access-control"></a>Control de acceso basado en rol
El siguiente acceso se debe conceder a los usuarios con el fin de habilitar y acceder a funciones en Azure Monitor para VM.  
  
- Para habilitar la solución, debe agregarse como miembro del rol de colaborador de Log Analytics.  

- Para ver los datos de rendimiento, estado de mantenimiento y asignación, deberá agregarse como miembro del rol de lector de supervisión para la VM de Azure y para el área de trabajo de Log Analytics configurada con Azure Monitor para VM.   

Para más información acerca de cómo controlar el acceso a un área de trabajo de Log Analytics, consulte [Administración de áreas de trabajo](../../log-analytics/log-analytics-manage-access.md).

## <a name="enable-from-the-azure-portal"></a>Habilitar desde Azure Portal
Para habilitar la supervisión de la VM de Azure en Azure Portal, siga estos pasos:

1. Inicie sesión en Azure Portal en [https://portal.azure.com](https://portal.azure.com). 
2. En Azure Portal, seleccione **Virtual Machines**. 
3. En la lista, seleccione una máquina virtual. 
4. En la página de la VM, en la sección **Supervisión**, seleccione **Insights (versión preliminar)**.
5. En la página **Insights (versión preliminar)**, seleccione **Probar ahora**.

    ![Habilitar Azure Monitor para VM para una máquina virtual](./media/vminsights-onboard/enable-vminsights-vm-portal-01.png)

5. En la página **Azure Monitor Insights Onboarding** (Incorporación a Insights de Azure Monitor), si tiene un área de trabajo de Log Analytics existente en la misma suscripción, selecciónela en la lista desplegable.  La lista preselecciona el área de trabajo y la ubicación predeterminadas en las que se implementa la máquina virtual en la suscripción. 

    >[!NOTE]
    >Si quiere crear una área de trabajo de Log Analytics nueva para almacenar los datos de supervisión de la VM, siga las instrucciones de [Creación de una área de trabajo de Log Analytics en Azure Portal](../../log-analytics/log-analytics-quick-create-workspace.md) en una de las regiones admitidas indicadas anteriormente.   

Después de habilitar la supervisión, pueden pasar unos 10 minutos hasta que pueda ver la métrica de estado de la máquina virtual. 

![Habilitar el procesamiento de implementación de supervisión de Azure Monitor para VM](./media/vminsights-onboard/onboard-vminsights-vm-portal-status.png)


## <a name="on-boarding-at-scale"></a>Incorporación a escala
En esta sección se ofrecen las instrucciones necesarias para realizar la implementación a escala de Azure Monitor para VM mediante Azure Policy o con Azure PowerShell.  

Se resumen los pasos que debe realizar para configurar previamente el área de trabajo de Log Analytics antes de continuar con la incorporación de sus máquinas virtuales.

1. Cree una nueva área de trabajo si todavía no existe una que sea compatible con Azure Monitor para VM. Revise cómo [administrar áreas de trabajo](../../log-analytics/log-analytics-manage-access.md?toc=/azure/azure-monitor/toc.json) antes de crear una nueva área de trabajo para conocer las consideraciones de costos, administración y cumplimiento antes de continuar.       
2. Habilite los contadores de rendimiento en el área de trabajo para la recopilación en máquinas virtuales de Windows y Linux.
3. Instale y habilite las soluciones **ServiceMap** e **InfrastructureInsights** en el área de trabajo.  

### <a name="setup-log-analytics-workspace"></a>Configuración del área de trabajo de Log Analytics
Si no tiene un área de trabajo de Log Analytics, revise los métodos disponibles que se sugieren en la sección [Requisitos previos](#log-analytics) para crearlo.  

#### <a name="enable-performance-counters"></a>Activar los contadores de rendimiento
Si el área de trabajo de Log Analytics a la que hace referencia la solución no está configurada para recopilar los contadores de rendimiento que requiere la solución, deben habilitarse. Se puede hacer manualmente, como se describe [aquí](../../azure-monitor/platform/data-sources-performance-counters.md), o mediante la descarga y ejecución de un script de PowerShell disponible en la [galería de Powershell de Azure](https://www.powershellgallery.com/packages/Enable-VMInsightsPerfCounters/1.1).
 
#### <a name="install-the-servicemap-and-infrastructureinsights-solutions"></a>Instalación de las soluciones ServiceMap e InfrastructureInsights
Este método incluye una plantilla JSON que especifica la configuración para habilitar los componentes de la solución en el área de trabajo de Log Analytics.  

Si no conoce el concepto de implementación de recursos mediante una plantilla, consulte:
* [Implementación de recursos con las plantillas de Resource Manager y Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md)
* [Implementación de recursos con plantillas de Resource Manager y la CLI de Azure](../../azure-resource-manager/resource-group-template-deploy-cli.md) 

Si decide usar la CLI de Azure, primero debe instalar y usar la CLI localmente. Debe ejecuta la versión 2.0.27 de la CLI de Azure, o cualquier versión posterior. Para identificar la versión, ejecute `az --version`. Si necesita instalar o actualizar la CLI de Azure, consulte [Instalación de la CLI de Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). 

1. Copie y pegue la siguiente sintaxis JSON en el archivo:

    ```json
    {

    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "WorkspaceName": {
            "type": "string"
        },
        "WorkspaceLocation": {
            "type": "string"
        }
    },
    "resources": [
        {
            "apiVersion": "2017-03-15-preview",
            "type": "Microsoft.OperationalInsights/workspaces",
            "name": "[parameters('WorkspaceName')]",
            "location": "[parameters('WorkspaceLocation')]",
            "resources": [
                {
                    "apiVersion": "2015-11-01-preview",
                    "location": "[parameters('WorkspaceLocation')]",
                    "name": "[concat('ServiceMap', '(', parameters('WorkspaceName'),')')]",
                    "type": "Microsoft.OperationsManagement/solutions",
                    "dependsOn": [
                        "[concat('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                    ],
                    "properties": {
                        "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                    },

                    "plan": {
                        "name": "[concat('ServiceMap', '(', parameters('WorkspaceName'),')')]",
                        "publisher": "Microsoft",
                        "product": "[Concat('OMSGallery/', 'ServiceMap')]",
                        "promotionCode": ""
                    }
                },
                {
                    "apiVersion": "2015-11-01-preview",
                    "location": "[parameters('WorkspaceLocation')]",
                    "name": "[concat('InfrastructureInsights', '(', parameters('WorkspaceName'),')')]",
                    "type": "Microsoft.OperationsManagement/solutions",
                    "dependsOn": [
                        "[concat('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                    ],
                    "properties": {
                        "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                    },
                    "plan": {
                        "name": "[concat('InfrastructureInsights', '(', parameters('WorkspaceName'),')')]",
                        "publisher": "Microsoft",
                        "product": "[Concat('OMSGallery/', 'InfrastructureInsights')]",
                        "promotionCode": ""
                    }
                }
            ]
        }
    ]
    ```

2. Guarde este archivo como **installsolutionsforvminsights.json** en una carpeta local.
3. Edite los valores de **WorkspaceName**, **ResourceGroupName** y **WorkspaceLocation**.  El valor de **WorkspaceName** es el identificador de recurso completo de su área de trabajo de Log Analytics, que incluye el nombre del área de trabajo, y el valor de **WorkspaceLocation** es la región en que se define el área de trabajo.
4. Puede implementar una plantilla mediante el siguiente comando de PowerShell:

    ```powershell
    New-AzureRmResourceGroupDeployment -Name DeploySolutions -TemplateFile InstallSolutionsForVMInsights.json -ResourceGroupName ResourceGroupName> -WorkspaceName <WorkspaceName> -WorkspaceLocation <WorkspaceLocation - example: eastus>
    ```

    El cambio de configuración puede tardar unos minutos en completarse. Cuando se completa, se muestra un mensaje que incluye el resultado y que es similar al siguiente:

    ```powershell
    provisioningState       : Succeeded
    ```

### <a name="enable-using-azure-policy"></a>Habilitar el uso de Azure Policy
Para habilitar Azure Monitor para VM a una escala que garantice el cumplimiento consistente y la habilitación automática de las máquinas virtuales aprovisionadas, se recomienda [Azure Policy](../../azure-policy/azure-policy-introduction.md). Estas directivas:

* Implementan el agente de Log Analytics y Dependency Agent 
* Informar sobre los resultados de cumplimiento 
* Corrigen las máquinas virtuales no compatibles

Habilite Azure Monitor para VM mediante la directiva que requiere el inquilino: 

- Asigne la iniciativa a un ámbito (grupo de administración, suscripción o grupo de recursos) 
- Revisión y corrección de los resultados del cumplimiento  

Para más información acerca de la asignación de Azure Policy, consulte [Introducción a Azure Policy](../../governance/policy/overview.md#policy-assignment) y consulte la [introducción a los grupos de administración](../../governance/management-groups/index.md) antes de continuar.  

En la tabla siguiente se enumeran las definiciones de directiva proporcionadas.  

|NOMBRE |DESCRIPCIÓN |Escriba |  
|-----|------------|-----|  
|[Versión preliminar]: Habilitar Azure Monitor para VM |Permitir habilitar Azure Monitor para las máquinas virtuales (VM) en el ámbito especificado (grupo de administración, suscripción o grupo de recursos). Usa el área de trabajo de Log Analytics como parámetro. |Iniciativa |  
|[Versión preliminar]: Auditar implementación de Dependency Agent: imagen de la VM (SO) no está en la lista |Notifica que las máquinas virtuales no son compatibles si la imagen de la máquina virtual (SO) no está en la lista definida y el agente no está instalado. |Directiva |  
|[Versión preliminar]: Auditar la implementación del agente de Log Analytics: imagen de la VM (SO) no está en la lista |Notifica que las máquinas virtuales no son compatibles si la imagen de la máquina virtual (SO) no está en la lista definida y el agente no está instalado. |Directiva |  
|[Versión preliminar]: Implementar Dependency Agent en máquinas virtuales Linux |Implementar Dependency Agent en máquinas virtuales Linux si la imagen de la máquina virtual (SO) está en la lista definida y el agente no está instalado. |Directiva |  
|[Versión preliminar]: Implementar Dependency Agent en máquinas virtuales Windows |Implemente Dependency Agent en máquinas virtuales Windows si la imagen de la máquina virtual (SO) está en la lista definida y el agente no está instalado. |Directiva |  
|[Versión preliminar]: Implementar el agente de Log Analytics en máquinas virtuales Linux |Implementar el agente de Log Analytics en máquinas virtuales Linux si la imagen de la máquina virtual (SO) está en la lista definida y el agente no está instalado. |Directiva |  
|[Versión preliminar]: Implementar el agente de Log Analytics en máquinas virtuales Windows |Implementar el agente de Log Analytics en máquinas virtuales Windows si la imagen de la máquina virtual (SO) está en la lista definida y el agente no está instalado. |Directiva |  

Directiva independiente (no se incluye con la iniciativa) 

|NOMBRE |DESCRIPCIÓN |Escriba |  
|-----|------------|-----|  
|[Versión preliminar]: auditar área de trabajo de Log Analytics en la máquina virtual: error de coincidencia del informe |Notificar que las máquinas virtuales no son compatibles si no se registran en el área de trabajo de Log Analytics especificada en la asignación de la directiva o iniciativa. |Directiva |

#### <a name="assign-azure-monitor-initiative"></a>Asignación de una iniciativa de Azure Monitor
Con esta versión inicial, solo se puede crear la asignación de directiva desde Azure Portal. Para saber cómo completar estos pasos, consulte  [Create a policy assignment from the Azure portal](../../governance/policy/assign-policy-portal.md) (Creación de una asignación de directiva desde Azure Portal). 

1. Inicie el servicio Azure Policy en Azure Portal. Para ello, haga clic en **Todos los servicios** y, a continuación, busque y seleccione **Directiva**. 
2. Seleccione **Asignaciones** en el panel izquierdo de la página de Azure Policy. Una asignación es una directiva que se asignó para que se lleve a cabo dentro de un ámbito específico.
3. Seleccione **Asignar directiva** en la parte superior de la página **Policy - Asignaciones** (Directiva: asignaciones).
4. En la página **Asignar iniciativa**, haga clic en los puntos suspensivos para seleccionar una opción de **Ámbito** y seleccione una suscripción o un grupo de administración y, opcionalmente, un grupo de recursos. Un ámbito limita la asignación de directiva en nuestro caso a una agrupación de máquinas virtuales para su aplicación. Para guardar los cambios, haga clic en **Seleccionar** en la parte inferior de la página **Ámbito**.
5. Las **exclusiones** permiten omitir uno o varios recursos del ámbito, que es opcional. 
6. Seleccione los puntos suspensivos de **Definición de iniciativa** para abrir la lista de definiciones disponibles, seleccione  **[Versión preliminar] Habilitar Azure Monitor para VM** en la lista y, después, haga clic en **Seleccionar**.
7. **Nombre de asignación** se ha rellenado automáticamente con el nombre de la iniciativa que ha seleccionado, pero puede cambiarlo. También puede agregar una **Descripción** opcional. **Asignado por** se rellena automáticamente en función de quién haya iniciado sesión y este campo es opcional.
8. Seleccione un **área de trabajo de Log Analytics** en la lista desplegable que está disponible en la región admitida.

    >[!NOTE]
    >Si el área de trabajo está fuera del ámbito de la asignación, debe conceder los permisos de **colaborador de Log Analytics** al identificador de la entidad de seguridad. de la asignación de la directiva. Si no lo hace, es posible que vea un error de implementación, como: `The client '343de0fe-e724-46b8-b1fb-97090f7054ed' with object id '343de0fe-e724-46b8-b1fb-97090f7054ed' does not have authorization to perform action 'microsoft.operationalinsights/workspaces/read' over scope ... ` Consulte [cómo configurar manualmente la identidad administrada](../../governance/policy/how-to/remediate-resources.md#manually-configure-the-managed-identity) para conceder acceso.
    >

9. Observe que la opción **Identidad administrada** está seleccionada. Se selecciona cuando la iniciativa que se asigna incluye una directiva con el efecto deployIfNotExists. En la lista desplegable **Ubicación de la identidad administrada**, seleccione la región adecuada.  
10. Haga clic en **Asignar**.

#### <a name="review-and-remediate-the-compliance-results"></a>Revisión y corrección de los resultados del cumplimiento 

Puede aprender a revisar los resultados de cumplimiento mediante la lectura de [identificación de los resultados de no cumplimiento](../../governance/policy/assign-policy-portal.md#identify-non-compliant-resources). Seleccione **Cumplimiento** en el lado izquierdo de la página y busque la iniciativa **[Versión preliminar] Habilitar Azure Monitor para VM** que no es compatible por la asignación que ha creado.

![Cumplimiento de directivas en máquinas virtuales de Azure](./media/vminsights-onboard/policy-view-compliance-01.png)

En función de los resultados de las directivas incluidas con la iniciativa, las máquinas virtuales se notifican como no compatibles en los siguientes escenarios:  
  
1. Log Analytics o Dependency Agent no están implementados.  
   Esto es típico de un ámbito con las máquinas virtuales existentes. Para mitigarlo, [cree tareas de corrección](../../governance/policy/how-to/remediate-resources.md) en una directiva no compatible para implementar los agentes necesarios.    
 
    - [Versión preliminar]: Deploy Dependency Agent for Linux VMs   
    - [Versión preliminar]: Deploy Dependency Agent for Windows VMs  
    - [Versión preliminar]: Deploy Log Analytics Agent for Linux VMs  
    - [Versión preliminar]: Deploy Log Analytics Agent for Windows VMs  

2. La imagen de la máquina virtual (SO) no está en la lista identificada en la definición de la directiva.  
   Los criterios de la directiva de implementación solo incluyen las máquinas virtuales que se implementan a partir de imágenes de máquina virtual de Azure conocidas. Consulte en la documentación si el sistema operativo de VM es compatible o no. Si no lo es, tiene que duplicar la directiva de implementación y actualizarla o modificarla para que la imagen sea compatible. 
  
    - [Versión preliminar]: Auditar implementación de Dependency Agent: imagen de la VM (SO) no está en la lista  
    - [Versión preliminar]: Auditar la implementación del agente de Log Analytics: imagen de la VM (SO) no está en la lista

3. Las máquinas virtuales no se registran en el área de trabajo de Log Analytics especificada.  
Es posible que algunas máquinas virtuales del ámbito de la iniciativa se registren en un área de trabajo de Log Analytics distinta de la que una se especificó en la asignación de la directiva. Esta directiva es una herramienta para identificar qué máquinas virtuales notifican a un área de trabajo no compatible.  
 
    - [Versión preliminar]: Audit Log Analytics Workspace for VM - Report Mismatch  

### <a name="enable-with-powershell"></a>Habilitar con PowerShell
Para habilitar Azure Monitor para VM en varias máquinas virtuales o conjuntos de escalado de máquinas virtuales, puede usar un script de PowerShell proporcionado: [Install VMInsights.ps1](https://www.powershellgallery.com/packages/Install-VMInsights/1.0) disponible en la Galería de Azure PowerShell para completar esta tarea.  Este script se repetirá en todas las máquinas virtuales y conjuntos de escalado de máquinas virtuales de la suscripción, en el grupo de recursos con ámbito que especifica *ResourceGroup* o en una sola máquina virtual o en un conjunto de escalado de máquinas virtuales especificado por *Nombre*.  En cada máquina virtual o conjunto de escalado de máquinas virtuales, el script comprueba si la extensión de la máquina virtual ya está instalada y, si no lo está, intenta reinstalarla.  En caso contrario, procede a instalar las extensiones de VM de Log Analytics y Dependency Agent.   

Este script requiere la versión 5.7.0 o posterior del módulo de Azure PowerShell. Ejecute `Get-Module -ListAvailable AzureRM` para encontrar la versión. Si necesita actualizarla, consulte [Instalación del módulo de Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps). Si PowerShell se ejecuta localmente, también debe ejecutar `Connect-AzureRmAccount` para crear una conexión con Azure.

Para obtener ayuda acerca del script, puede ejecutar `Get-Help` para obtener una lista de detalles del argumento y un ejemplo de uso.   

```powershell
Get-Help .\Install-VMInsights.ps1 -Detailed

SYNOPSIS
    This script installs VM extensions for Log Analytics and Dependency Agent as needed for VM Insights.


SYNTAX
    .\Install-VMInsights.ps1 [-WorkspaceId] <String> [-WorkspaceKey] <String> [-SubscriptionId] <String> [[-ResourceGroup]
    <String>] [[-Name] <String>] [[-PolicyAssignmentName] <String>] [-ReInstall] [-TriggerVmssManualVMUpdate] [-Approve] [-WorkspaceRegion] <String>
    [-WhatIf] [-Confirm] [<CommonParameters>]


DESCRIPTION
    This script installs or re-configures following on VM's and VM Scale Sets:
    - Log Analytics VM Extension configured to supplied Log Analytics Workspace
    - Dependency Agent VM Extension

    Can be applied to:
    - Subscription
    - Resource Group in a Subscription
    - Specific VM/VM Scale Set
    - Compliance results of a policy for a VM or VM Extension

    Script will show you list of VM's/VM Scale Sets that will apply to and let you confirm to continue.
    Use -Approve switch to run without prompting, if all required parameters are provided.

    If the extensions are already installed will not install again.
    Use -ReInstall switch if you need to for example update the workspace.

    Use -WhatIf if you would like to see what would happen in terms of installs, what workspace configured to, and status of the extension.


PARAMETERS
    -WorkspaceId <String>
        Log Analytics WorkspaceID (GUID) for the data to be sent to

    -WorkspaceKey <String>
        Log Analytics Workspace primary or secondary key

    -SubscriptionId <String>
        SubscriptionId for the VMs/VM Scale Sets
        If using PolicyAssignmentName parameter, subscription that VM's are in

    -ResourceGroup <String>
        <Optional> Resource Group to which the VMs or VM Scale Sets belong to

    -Name <String>
        <Optional> To install to a single VM/VM Scale Set

    -PolicyAssignmentName <String>
        <Optional> Take the input VM's to operate on as the Compliance results from this Assignment
        If specified will only take from this source.

    -ReInstall [<SwitchParameter>]
        <Optional> If VM/VM Scale Set is already configured for a different workspace, set this to change to the new workspace

    -TriggerVmssManualVMUpdate [<SwitchParameter>]
        <Optional> Set this flag to trigger update of VM instances in a scale set whose upgrade policy is set to Manual

    -Approve [<SwitchParameter>]
        <Optional> Gives the approval for the installation to start with no confirmation prompt for the listed VM's/VM Scale Sets

    -WorkspaceRegion <String>
        Region the Log Analytics Workspace is in
        Suported values: "East US","eastus","Southeast Asia","southeastasia","West Central US","westcentralus","West Europe","westeurope"
        For Health supported is: "East US","eastus","West Central US","westcentralus"

    -WhatIf [<SwitchParameter>]
        <Optional> See what would happen in terms of installs.
        If extension is already installed will show what workspace is currently configured, and status of the VM extension

    -Confirm [<SwitchParameter>]
        <Optional> Confirm every action

    <CommonParameters>
        This cmdlet supports the common parameters: Verbose, Debug,
        ErrorAction, ErrorVariable, WarningAction, WarningVariable,
        OutBuffer, PipelineVariable, and OutVariable. For more information, see
        about_CommonParameters (https:/go.microsoft.com/fwlink/?LinkID=113216).

    -------------------------- EXAMPLE 1 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -ResourceGroup <ResourceGroup>

    Install for all VM's in a Resource Group in a subscription

    -------------------------- EXAMPLE 2 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -ResourceGroup <ResourceGroup> -ReInstall

    Specify to ReInstall extensions even if already installed, for example to update to a different workspace

    -------------------------- EXAMPLE 3 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -PolicyAssignmentName a4f79f8ce891455198c08736 -ReInstall

    Specify to use a PolicyAssignmentName for source, and to ReInstall (move to a new workspace)
```

En el ejemplo siguiente se muestra cómo utilizar los comandos de PowerShell en la carpeta para habilitar Azure Monitor para VM y comprender el resultado esperado:

```powershell
$WorkspaceId = "<GUID>"
$WorkspaceKey = "<Key>"
$SubscriptionId = "<GUID>"
.\Install-VMInsights.ps1 -WorkspaceId $WorkspaceId -WorkspaceKey $WorkspaceKey -SubscriptionId $SubscriptionId -WorkspaceRegion eastus

Getting list of VM's or VM ScaleSets matching criteria specified

VM's or VM ScaleSets matching criteria:

db-ws-1 VM running
db-ws2012 VM running

This operation will install the Log Analytics and Dependency Agent extensions on above 2 VM's or VM Scale Sets.
VM's in a non-running state will be skipped.
Extension will not be re-installed if already installed. Use -ReInstall if desired, for example to update workspace

Confirm
Continue?
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): y

db-ws-1 : Deploying DependencyAgentWindows with name DAExtension
db-ws-1 : Successfully deployed DependencyAgentWindows
db-ws-1 : Deploying MicrosoftMonitoringAgent with name MMAExtension
db-ws-1 : Successfully deployed MicrosoftMonitoringAgent
db-ws2012 : Deploying DependencyAgentWindows with name DAExtension
db-ws2012 : Successfully deployed DependencyAgentWindows
db-ws2012 : Deploying MicrosoftMonitoringAgent with name MMAExtension
db-ws2012 : Successfully deployed MicrosoftMonitoringAgent

Summary:

Already Onboarded: (0)

Succeeded: (4)
db-ws-1 : Successfully deployed DependencyAgentWindows
db-ws-1 : Successfully deployed MicrosoftMonitoringAgent
db-ws2012 : Successfully deployed DependencyAgentWindows
db-ws2012 : Successfully deployed MicrosoftMonitoringAgent

Connected to different workspace: (0)

Not running - start VM to configure: (0)

Failed: (0)
```

## <a name="enable-for-hybrid-environment"></a>Habilitar para el entorno híbrido
En esta sección se explica cómo incorporar máquinas virtuales o equipos físicos hospedados en el centro de datos o en otro entorno en la nube para que los supervise Azure Monitor para VM.  

Dependency Agent de asignación de Azure Monitor para VM no transmite ningún dato y no requiere ningún cambio en firewalls o puertos. La asignación de datos siempre se transmite del agente de Log Analytics al servicio de Azure Monitor, ya sea directamente o a través de la [Puerta de enlace de OMS](../../azure-monitor/platform/gateway.md) si las directivas de seguridad de TI no permiten que los equipos de la red se conecten a Internet.

Revise los requisitos y los métodos de implementación para el [Agente Linux y Windows de Log Analytics](../../azure-monitor/platform/log-analytics-agent.md).  

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

Pasos resumidos:

1. Instalar el agente de Log Analytics para Windows o Linux
2. Descargue e instale Dependency Agent para las asignaciones de Azure Monitor para VM en [Windows](https://aka.ms/dependencyagentwindows) o [Linux](https://aka.ms/dependencyagentlinux).
3. Habilitar la recopilación de contadores de rendimiento
4. Incorporar Azure Monitor para VM

### <a name="install-the-dependency-agent-on-windows"></a>Instalar Dependency Agent en Windows 
Dependency Agent puede instalarse manualmente en equipos Windows ejecutando `InstallDependencyAgent-Windows.exe`. Si ejecuta este archivo ejecutable sin opciones, se inicia un asistente para instalación que puede seguir para realizar la instalación de forma interactiva.  

>[!NOTE]
>Se requieren privilegios de administrador para instalar o desinstalar al agente.

En la tabla siguiente se destacan los parámetros específicos que admite el programa de instalación para el agente desde la línea de comandos.  

| Parámetro | DESCRIPCIÓN |
|:--|:--|
| /? | Devuelve una lista de las opciones de la línea de comandos. |
| /S | Realice una instalación silenciosa sin interacción del usuario. |

Por ejemplo, para ejecutar el programa de instalación con el parámetro `/?`, escriba `InstallDependencyAgent-Windows.exe /?`

De forma predeterminada, los archivos de Windows Dependency Agent se instalan en `C:\Program Files\Microsoft Dependency Agent`.  Si Dependency Agent no se inicia una vez completada la instalación, compruebe los registros para obtener información detallada del error. El directorio de registro es `%Programfiles%\Microsoft Dependency Agent\logs`. 

### <a name="install-the-dependency-agent-on-linux"></a>Instalación de Dependency Agent en Linux
Dependency Agent se instala en servidores Linux desde `InstallDependencyAgent-Linux64.bin`, un script de shell con un archivo binario autoextraíble. Puede ejecutar el archivo mediante `sh` o agregar permisos de ejecución al propio archivo.

>[!NOTE]
> Es necesario el acceso raíz para instalar o configurar el agente.
> 

| Parámetro | DESCRIPCIÓN |
|:--|:--|
| -help | Obtenga una lista de las opciones de la línea de comandos. |
| -s | Realice una instalación silenciosa sin preguntas. |
| --check | Compruebe los permisos y el sistema operativo, pero no instale el agente. |

Por ejemplo, para ejecutar el programa de instalación con el parámetro `-help`, escriba `InstallDependencyAgent-Linux64.bin -help`

Instale Dependency Agent de Linux como raíz ejecutando el comando siguiente: `sh InstallDependencyAgent-Linux64.bin`
    
Si Dependency Agent no se inicia, compruebe los registros para obtener información detallada del error. En los agentes de Linux, el directorio de registro es `/var/opt/microsoft/dependency-agent/log`.

Los archivos de Dependency Agent se colocan en los directorios siguientes:

| Archivos | Ubicación |
|:--|:--|
| Archivos principales | /opt/microsoft/dependency-agent |
| Archivos de registro | /var/opt/microsoft/dependency-agent/log |
| Archivos de configuración | /etc/opt/microsoft/dependency-agent/config |
| Archivos ejecutables de servicio | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| Archivos binarios de almacenamiento | /var/opt/microsoft/dependency-agent/storage |

### <a name="enable-performance-counters"></a>Activar los contadores de rendimiento
Si el área de trabajo de Log Analytics a la que hace referencia la solución no está configurada para recopilar los contadores de rendimiento que requiere la solución, deben habilitarse. Se puede hacer manualmente, como se describe [aquí](../../azure-monitor/platform/data-sources-performance-counters.md), o mediante la descarga y ejecución de un script de PowerShell disponible en la [galería de Powershell de Azure](https://www.powershellgallery.com/packages/Enable-VMInsightsPerfCounters/1.1).
 
### <a name="onboard-azure-monitor-for-vms"></a>Incorporar Azure Monitor para VM
Este método incluye una plantilla JSON que especifica la configuración para habilitar los componentes de la solución en el área de trabajo de Log Analytics.  

Si no conoce el concepto de implementación de recursos mediante una plantilla, consulte:
* [Implementación de recursos con las plantillas de Resource Manager y Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md)
* [Implementación de recursos con plantillas de Resource Manager y la CLI de Azure](../../azure-resource-manager/resource-group-template-deploy-cli.md) 

Si decide usar la CLI de Azure, primero debe instalar y usar la CLI localmente. Debe ejecuta la versión 2.0.27 de la CLI de Azure, o cualquier versión posterior. Para identificar la versión, ejecute `az --version`. Si necesita instalar o actualizar la CLI de Azure, consulte [Instalación de la CLI de Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). 

#### <a name="create-and-execute-a-template"></a>Creación y ejecución de una plantilla

1. Copie y pegue la siguiente sintaxis JSON en el archivo:

    ```json
    {

    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "WorkspaceName": {
            "type": "string"
        },
        "WorkspaceLocation": {
            "type": "string"
        }
    },
    "resources": [
        {
            "apiVersion": "2017-03-15-preview",
            "type": "Microsoft.OperationalInsights/workspaces",
            "name": "[parameters('WorkspaceName')]",
            "location": "[parameters('WorkspaceLocation')]",
            "resources": [
                {
                    "apiVersion": "2015-11-01-preview",
                    "location": "[parameters('WorkspaceLocation')]",
                    "name": "[concat('ServiceMap', '(', parameters('WorkspaceName'),')')]",
                    "type": "Microsoft.OperationsManagement/solutions",
                    "dependsOn": [
                        "[concat('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                    ],
                    "properties": {
                        "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                    },

                    "plan": {
                        "name": "[concat('ServiceMap', '(', parameters('WorkspaceName'),')')]",
                        "publisher": "Microsoft",
                        "product": "[Concat('OMSGallery/', 'ServiceMap')]",
                        "promotionCode": ""
                    }
                },
                {
                    "apiVersion": "2015-11-01-preview",
                    "location": "[parameters('WorkspaceLocation')]",
                    "name": "[concat('InfrastructureInsights', '(', parameters('WorkspaceName'),')')]",
                    "type": "Microsoft.OperationsManagement/solutions",
                    "dependsOn": [
                        "[concat('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                    ],
                    "properties": {
                        "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                    },
                    "plan": {
                        "name": "[concat('InfrastructureInsights', '(', parameters('WorkspaceName'),')')]",
                        "publisher": "Microsoft",
                        "product": "[Concat('OMSGallery/', 'InfrastructureInsights')]",
                        "promotionCode": ""
                    }
                }
            ]
        }
    ]
    ```

2. Guarde este archivo como **installsolutionsforvminsights.json** en una carpeta local.
3. Edite los valores de **WorkspaceName**, **ResourceGroupName** y **WorkspaceLocation**.  El valor de **WorkspaceName** es el identificador de recurso completo de su área de trabajo de Log Analytics, que incluye el nombre del área de trabajo, y el valor de **WorkspaceLocation** es la región en que se define el área de trabajo.
4. Puede implementar una plantilla mediante el siguiente comando de PowerShell:

    ```powershell
    New-AzureRmResourceGroupDeployment -Name DeploySolutions -TemplateFile InstallSolutionsForVMInsights.json -ResourceGroupName ResourceGroupName> -WorkspaceName <WorkspaceName> -WorkspaceLocation <WorkspaceLocation - example: eastus>
    ```

    El cambio de configuración puede tardar unos minutos en completarse. Cuando se completa, se muestra un mensaje que incluye el resultado y que es similar al siguiente:

    ```powershell
    provisioningState       : Succeeded
    ```
Después de habilitar la supervisión, pueden pasar unos 10 minutos hasta que pueda ver la métrica y el estado del equipo híbrido. 

## <a name="performance-counters-enabled"></a>Contadores de rendimiento habilitados
Azure Monitor para VM configura un área de trabajo de Log Analytics para recopilar contadores de rendimiento que usa la solución.  En la tabla siguiente se enumeran los objetos y contadores que configura la solución que se recopilan cada 60 segundos.

### <a name="windows-performance-counters"></a>Contadores de rendimiento de Windows

|Nombre del objeto |Nombre del contador |  
|------------|-------------|  
|LogicalDisk |% de espacio libre |  
|LogicalDisk |Prom. Segundos de disco/lecturas |  
|LogicalDisk |Prom. Segundos de disco/transferencias |  
|LogicalDisk |Prom. Segundos de disco/escrituras |  
|LogicalDisk |Bytes de disco/s |  
|LogicalDisk |Bytes de lectura de disco/s  |  
|LogicalDisk |Lecturas de disco/s  |  
|LogicalDisk |Transferencias de disco/s |  
|LogicalDisk |  Bytes de escritura en disco/s |  
|LogicalDisk | Escrituras en disco/s |  
|LogicalDisk |Megabytes libres |  
|Memoria |MB disponibles |  
|Adaptador de red |Bytes recibidos por segundo |  
|Adaptador de red |Bytes enviados por segundo |  
|Procesador |% de tiempo de procesador |  

### <a name="linux-performance-counters"></a>Contadores de rendimiento de Linux

|Nombre del objeto |Nombre del contador |  
|------------|-------------|  
|Disco lógico |% espacio usado |  
|Disco lógico |Bytes de lectura de disco/s  |  
|Disco lógico |Lecturas de disco/s  |  
|Disco lógico |Transferencias de disco/s |  
|Disco lógico |  Bytes de escritura en disco/s |  
|Disco lógico | Escrituras en disco/s |  
|Disco lógico |Megabytes libres |  
|Disco lógico |Bytes de disco lógico/s |  
|Memoria |MB de memoria disponibles |  
|Red |Número total de bytes recibidos |  
|Red |Número total de bytes transmitidos |  
|Procesador |% de tiempo de procesador |  

## <a name="diagnostic-and-usage-data"></a>Datos de diagnóstico y uso
Microsoft recopila automáticamente datos de uso y rendimiento a través del servicio Azure Monitor. Microsoft usa estos datos para proporcionar y mejorar la calidad, la seguridad y la integridad del servicio. Con el fin de proporcionar funcionalidades de solución de problemas precisas y eficientes, los datos de la característica de asignación incluyen información sobre la configuración del software, como sistema operativo y versión, dirección IP, nombre DNS y nombre de la estación de trabajo. Microsoft no recopila nombres, direcciones ni otra información de contacto.

Para más información sobre el uso y la recopilación de datos, vea la [Declaración de privacidad de Microsoft Online Services](https://go.microsoft.com/fwlink/?LinkId=512132).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]
## <a name="next-steps"></a>Pasos siguientes

Con la supervisión habilitada para la máquina virtual, esta información está disponible para su análisis con Azure Monitor para VM.  Para obtener información sobre cómo usar la característica de mantenimiento, consulte [Ver el estado de Azure Monitor para VM](vminsights-health.md) o, para ver las dependencias de aplicaciones detectadas, consulte [Ver la asignación de Azure Monitor para VM](vminsights-maps.md).  