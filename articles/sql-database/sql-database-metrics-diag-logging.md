---
title: Métricas y registros de diagnóstico de Azure SQL Database | Microsoft Docs
description: Aprenda a configurar Azure SQL Database para almacenar estadísticas de uso de recursos, conectividad y ejecución de consultas.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: v-daljep
ms.reviewer: carlrab
manager: craigg
ms.date: 09/20/2018
ms.openlocfilehash: b903d0ddbccac8fe4fa8b251d409bd8addebb435
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/27/2018
ms.locfileid: "52426006"
---
# <a name="azure-sql-database-metrics-and-diagnostics-logging"></a>Métricas y registros de diagnóstico de Azure SQL Database 

Azure SQL Database, los grupos elásticos, Instancia administrada y las bases de datos de Instancia administrada pueden emitir métricas y registros de diagnóstico para facilitar la supervisión del rendimiento. Puede configurar una base de datos para optimizar el uso de recursos, los trabajos y sesiones, y la conectividad en uno de estos recursos de Azure:

* **Azure SQL Analytics**: se utiliza como solución de supervisión del rendimiento inteligente de bases de datos de Azure, con funcionalidades de informes, alertas y mitigación.
* **Azure Event Hubs**: se utiliza para integrar la telemetría de SQL Database con una solución de supervisión personalizada o canalizaciones activas.
* **Azure Storage**: se utiliza para archivar grandes cantidades de telemetría por una fracción del precio.

    ![Arquitectura](./media/sql-database-metrics-diag-logging/architecture.png)

Para conocer las métricas y las categorías de registro compatibles con los diferentes servicios de Azure, tal vez desee leer lo siguiente:

* [Información general sobre las métricas en Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md)
* [Información general de los registros de diagnóstico de Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) 

## <a name="enable-logging-of-diagnostics-telemetry"></a>Habilitación del registro de datos de telemetría de diagnóstico

Utilice la primera sección de este documento para habilitar los datos de telemetría de diagnóstico en bases de datos y la segunda parte para habilitar los datos de telemetría de diagnóstico en grupos elásticos o instancias administradas. Utilice las últimas secciones de este documento para configurar Azure SQL Analytics como una herramienta de supervisión que permita visualizar los datos de telemetría de diagnóstico de las bases de datos que se han transmitido.

> [!NOTE]
> Si utiliza grupos elásticos o instancias administradas, además de habilitar los datos de telemetría de diagnóstico en las bases de datos, es conveniente que los habilite también en estos recursos. El motivo es que los grupos elásticos y las instancias administradas del rol de contenedores de bases de datos tienen sus propios datos de telemetría, y estos son diferentes de los datos de telemetría de diagnóstico de una base de datos específica. 
>

Puede habilitar y administrar las métricas y los registros de datos de telemetría de diagnóstico utilizando uno de los métodos siguientes:

- Azure Portal
- PowerShell
- Azure CLI
- API de REST de Azure Monitor 
- Plantilla del Administrador de recursos de Azure

Al habilitar las métricas y los registros de diagnóstico, debe especificar el destino de recursos de Azure donde se recopilarán los datos seleccionados. Las opciones disponibles incluyen:

- Azure SQL Analytics
- Azure Event Hubs
- Azure Storage

Puede aprovisionar un nuevo recurso de Azure o seleccionar uno existente. Después de seleccionar un recurso, deberá especificar qué datos se deben recopilar mediante la opción de configuración de diagnóstico.

## <a name="enable-logging-for-azure-sql-database-or-databases-in-managed-instance"></a>Habilitación del registro en Azure SQL Database o en bases de datos de Instancia Administrada

De forma predeterminada, las métricas y los registros de diagnóstico de SQL Database y las bases de datos de Instancia Administrada no están habilitados.

La siguiente telemetría de diagnóstico está disponible para la colección de instancias de Azure SQL Database y bases de datos de Instancia administrada:

| Telemetría de supervisión de bases de datos | Compatibilidad con Azure SQL Database | Compatibilidad con base de datos de Instancia administrada |
| :------------------- | ------------------- | ------------------- |
| [Todas las métricas](sql-database-metrics-diag-logging.md#all-metrics): contiene el porcentaje de DTU/CPU, el límite de DTU/CPU, el porcentaje de lectura de datos físicos, el porcentaje de escritura en registro, las conexiones correctas, erróneas o bloqueadas por el firewall, el porcentaje de sesiones, el porcentaje de trabajo, el almacenamiento, el porcentaje de almacenamiento y el porcentaje de almacenamiento de XTP. | SÍ | Sin  |
| [QueryStoreRuntimeStatistics](sql-database-metrics-diag-logging.md#query-store-runtime-statistics): contiene la información sobre las estadísticas de tiempo de ejecución de consulta, como estadísticas del uso de CPU y duración de la consulta. | SÍ | SÍ |
| [QueryStoreWaitStatistics](sql-database-metrics-diag-logging.md#query-store-wait-statistics): contiene la información sobre las estadísticas de espera de las consultas, que le indica el motivo de la espera de sus consultas, como la CPU, el registro y el bloqueo. | SÍ | SÍ |
| [Errores](sql-database-metrics-diag-logging.md#errors-dataset): contiene la información sobre los errores de SQL producidos en esta base de datos. | SÍ | Sin  |
| [DatabaseWaitStatistics](sql-database-metrics-diag-logging.md#database-wait-statistics-dataset): contiene la información sobre cuánto tiempo ha dedicado una base de datos a esperar distintos tipos de espera. | SÍ | Sin  |
| [Tiempos de espera](sql-database-metrics-diag-logging.md#time-outs-dataset): contiene información sobre los tiempos de espera que se produjeron en una base de datos. | SÍ | Sin  |
| [Bloqueos](sql-database-metrics-diag-logging.md#blockings-dataset): contiene la información acerca de los eventos de bloqueo que se produjeron en una base de datos. | SÍ | Sin  |
| [SQLInsights](sql-database-metrics-diag-logging.md#intelligent-insights-dataset): contiene Intelligent Insights sobre el rendimiento. [Más información sobre Intelligent Insights](sql-database-intelligent-insights.md). | SÍ | SÍ |

### <a name="azure-portal"></a>Azure Portal

El streaming de telemetría de diagnóstico de Azure SQL Database y bases de datos de Instancia administrada a destinos de Azure Storage, centros de eventos o Log Analytics se configura a través del menú de configuración de diagnóstico para cada una de las bases de datos en Azure Portal.

### <a name="configure-streaming-of-diagnostics-telemetry-for-azure-sql-database"></a>Configuración del streaming de telemetría de diagnóstico para Azure SQL Database

   ![Icono de SQL Database](./media/sql-database-metrics-diag-logging/icon-sql-database-text.png)

Para habilitar el streaming de telemetría de diagnóstico para **Azure SQL Database**, siga estos pasos:

1. Vaya al recurso de Azure SQL Database.
2. Seleccione **Configuración de diagnóstico**.
3. Seleccione **Activar diagnóstico** si no existe ninguna configuración anterior o **Editar configuración** para editar una configuración anterior.
- Se pueden crear hasta tres (3) conexiones paralelas para hacer streaming de la telemetría de diagnóstico. Para configurar el streaming múltiple en paralelo de datos de diagnóstico a varios recursos, seleccione **+Agregar configuración de diagnóstico** para crear una configuración adicional.

   ![Habilitación de diagnósticos en SQL Database](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-sql-enable.png)

4. Escriba el nombre de la configuración (para su propia referencia).
5. Seleccione el recurso al que los datos de diagnóstico se transmiten en secuencia desde la base de datos: **Archivar en una cuenta de almacenamiento**, **Transmitir a un centro de eventos** o **Enviar a Log Analytics**.
6. En la experiencia de supervisión estándar, active las casillas de telemetría de registro de diagnóstico de bases de datos: **SQLInsights**, **AutomaticTuning**, **QueryStoreRuntimeStatistics**, **QueryStoreWaitStatistics**, **Errors**, **DatabaseWaitStatistics**, **Timeouts**, **Blocks** y **Deadlocks**. Esta telemetría se basa en eventos y ofrece la experiencia de supervisión estándar.
7. En la experiencia de supervisión avanzada, active la casilla **AllMetrics**. Se trata de una telemetría minuto a minuto para la telemetría de diagnóstico de bases de datos, tal y como se describió anteriormente. 
8. Haga clic en **Guardar**

   ![Configuración de diagnósticos en SQL Database](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-sql-selection.png)

> [!NOTE]
> Los registros de auditoría de seguridad no se pueden habilitar en la configuración de diagnóstico de la base de datos. Para habilitar el streaming de registros de auditoría, consulte [Configuración de la auditoría para su base de datos](sql-database-auditing.md#subheading-2) y [SQL Audit logs in Azure Log Analytics and Azure Event Hubs](https://blogs.msdn.microsoft.com/sqlsecurity/2018/09/13/sql-audit-logs-in-azure-log-analytics-and-azure-event-hubs/) (Registros de auditoría de SQL en Azure Log Analytics y Azure Event Hubs).
>

> [!TIP]
> Repita los pasos anteriores para cada instancia de Azure SQL Database que quiera supervisar. 
>

### <a name="configure-streaming-of-diagnostics-telemetry-for-databases-in-managed-instance"></a>Configuración del streaming de telemetría de diagnóstico para bases de datos de Instancia administrada

   ![Icono de bases de datos de Instancia Administrada](./media/sql-database-metrics-diag-logging/icon-mi-database-text.png)

Para habilitar el streaming de telemetría de diagnóstico para **bases de datos de Instancia administrada**, siga estos pasos:

1. Vaya a la base de datos de Instancia administrada.
2. Seleccione **Configuración de diagnóstico**.
3. Seleccione **Activar diagnóstico** si no existe ninguna configuración anterior o **Editar configuración** para editar una configuración anterior.
- Se pueden crear hasta tres (3) conexiones paralelas para hacer streaming de la telemetría de diagnóstico. Para configurar el streaming múltiple en paralelo de datos de diagnóstico a varios recursos, seleccione **+Agregar configuración de diagnóstico** para crear una configuración adicional.

   ![Habilitación de diagnósticos en una base de datos de Instancia Administrada](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-mi-enable.png)

4. Escriba el nombre de la configuración (para su propia referencia).
5. Seleccione el recurso al que los datos de diagnóstico se transmiten en secuencia desde la base de datos: **Archivar en una cuenta de almacenamiento**, **Transmitir a un centro de eventos** o **Enviar a Log Analytics**.
6. Active las casillas de telemetría de diagnóstico de bases de datos: **SQLInsights**, **QueryStoreRuntimeStatistics**, **QueryStoreWaitStatistics** y **Errors**.
7. Haga clic en **Guardar**

   ![Configuración de diagnósticos en una base de datos de Instancia Administrada](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-mi-selection.png)

> [!TIP]
> Repita los pasos anteriores para cada base de datos de Instancia administrada que quiera supervisar.
>

## <a name="enable-logging-for-elastic-pools-or-managed-instance"></a>Habilitación del registro para grupos elásticos o Instancia administrada

Los grupos elásticos y las instancias administradas como contenedores de bases de datos tienen sus propios datos de telemetría de diagnóstico, que son diferentes a los de las bases de datos. Estos datos de telemetría de diagnóstico no está habilitados de forma predeterminada. 

### <a name="configure-streaming-of-diagnostics-telemetry-for-elastic-pools"></a>Configuración del streaming de telemetría de diagnóstico para grupos elásticos

   ![Icono de grupo elástico](./media/sql-database-metrics-diag-logging/icon-elastic-pool-text.png)

La siguiente telemetría de diagnóstico está disponible para la colección del recurso de grupos elásticos:

| Recurso | Telemetría de supervisión |
| :------------------- | ------------------- |
| **Grupo elástico** | [Todas las métricas](sql-database-metrics-diag-logging.md#all-metrics) contiene el porcentaje de eDTU/CPU, el límite de eDTU/CPU, el porcentaje de lectura de datos físicos, el porcentaje de escritura en registro, el porcentaje de sesiones, el porcentaje de trabajos, el almacenamiento, el porcentaje de almacenamiento, el límite de almacenamiento y el porcentaje de almacenamiento de XTP. |

Para habilitar el streaming de telemetría de diagnóstico del **recurso de grupos elásticos**, siga estos pasos:

1. Vaya al recurso de grupos elásticos de Azure Portal
2. Seleccione **Configuración de diagnóstico**.
3. Seleccione **Activar diagnóstico** si no existe ninguna configuración anterior o **Editar configuración** para editar una configuración anterior.

   ![Habilitación de diagnósticos en grupos elásticos](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-elasticpool-enable.png)

4. Escriba el nombre de la configuración (para su propia referencia).
5. Seleccione el recurso al que los datos de diagnóstico se transmiten en secuencia desde el grupo elástico: **Archivar en una cuenta de almacenamiento**, **Transmitir a un centro de eventos** o **Enviar a Log Analytics**.
6. En caso de que Log Analytics este seleccionada, elija **Configurar** y cree un área de trabajo seleccionando **+Crear nueva área de trabajo** o bien seleccione un área de trabajo.
7. Active la casilla **AllMetrics** para la telemetría de diagnóstico de grupos elásticos.
8. Haga clic en **Guardar**

   ![Configuración de diagnósticos en grupos elásticos](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-elasticpool-selection.png)

> [!TIP]
> Repita los pasos anteriores para cada grupo elástico que quiera supervisar.
>

### <a name="configure-streaming-of-diagnostics-telemetry-for-managed-instance"></a>Configuración del streaming de telemetría de diagnóstico para Instancia administrada

   ![Icono de Instancia administrada](./media/sql-database-metrics-diag-logging/icon-managed-instance-text.png)

La siguiente telemetría de diagnóstico está disponible para la colección del recurso de Instancia administrada:

| Recurso | Telemetría de supervisión |
| :------------------- | ------------------- |
| **Instancia administrada** | [ResourceUsageStats](sql-database-metrics-diag-logging.md#resource-usage-stats): contiene el número de núcleos virtuales, el porcentaje medio de CPU, las solicitudes de E/S, los bytes leídos y escritos, el espacio de almacenamiento reservado y el espacio de almacenamiento utilizado. |

Para habilitar el streaming de telemetría de diagnóstico del **recurso de Instancia administrada**, siga estos pasos:

1. Vaya al recurso de Instancia administrada en Azure Portal.
2. Seleccione **Configuración de diagnóstico**.
3. Seleccione **Activar diagnóstico** si no existe ninguna configuración anterior o **Editar configuración** para editar una configuración anterior.

   ![Habilitación de diagnósticos en Instancia administrada](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-mi-enable.png)

4. Escriba el nombre de la configuración (para su propia referencia).
5. Seleccione el recurso al que los datos de diagnóstico se transmiten en secuencia desde el grupo elástico: **Archivar en una cuenta de almacenamiento**, **Transmitir a un centro de eventos** o **Enviar a Log Analytics**.
6. En caso de que Log Analytics esté seleccionada, cree o utilice un área de trabajo.
7. Active la casilla **ResourceUsageStats** para la telemetría de diagnóstico de instancias.
8. Haga clic en **Guardar**

   ![Configuración de diagnósticos en Instancia administrada](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-mi-selection.png)

> [!TIP]
> Repita los pasos anteriores para cada Instancia administrada que quiera supervisar.
>

### <a name="powershell"></a>PowerShell

Para habilitar las métricas y los registros de diagnóstico con PowerShell, use los siguientes comandos:

- Para habilitar el almacenamiento de registros de diagnóstico en una cuenta de almacenamiento, use este comando:

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
   ```

   El identificador de la cuenta de almacenamiento es el identificador de recurso para la cuenta de almacenamiento a la que desea enviar los registros.

- Para habilitar la transmisión de registros de diagnóstico a un centro de eventos, use este comando:

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
   ```

   El identificador de regla de Azure Service Bus es una cadena con este formato:

   ```powershell
   {service bus resource ID}/authorizationrules/{key name}
   ``` 

- Para habilitar el envío de registros de diagnóstico a un área de trabajo de Log Analytics, use este comando:

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
   ```

- Puede obtener el identificador de recurso de su área de trabajo de Log Analytics con el comando siguiente:

   ```powershell
   (Get-AzureRmOperationalInsightsWorkspace).ResourceId
   ```

Puede combinar estos parámetros para habilitar varias opciones de salida.

### <a name="to-configure-multiple-azure-resources"></a>Para configurar varios recursos de Azure

Para admitir varias suscripciones, use el script de PowerShell de [Enable Azure resource metrics logging using PowerShell](https://blogs.technet.microsoft.com/msoms/2017/01/17/enable-azure-resource-metrics-logging-using-powershell/) (Habilitar registro de métricas de recursos de Azure mediante PowerShell).

Proporcione el identificador de recurso del área de trabajo &lt;$WSID&gt; como un parámetro al ejecutar el script (Enable-AzureRMDiagnostics.ps1) para enviar los datos de diagnóstico de varios recursos a una área de trabajo. Para obtener el identificador de área de trabajo &lt;$WSID&gt; al que desea enviar datos de diagnóstico, reemplace &lt;subID&gt; por el identificador de suscripción, reemplace &lt;RG_NAME&gt; por el nombre del grupo de recursos y reemplace &lt;WS_NAME&gt; por el nombre del área de trabajo en el script siguiente.

- Para configurar varios recursos de Azure, use los siguientes comandos:

    ```powershell
    PS C:\> $WSID = "/subscriptions/<subID>/resourcegroups/<RG_NAME>/providers/microsoft.operationalinsights/workspaces/<WS_NAME>"
    PS C:\> .\Enable-AzureRMDiagnostics.ps1 -WSID $WSID
    ```

### <a name="azure-cli"></a>Azure CLI

Para habilitar las métricas y los registros de diagnóstico con la CLI de Azure, use los siguientes comandos:

- Para habilitar el almacenamiento de registros de diagnóstico en una cuenta de almacenamiento, use este comando:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true
   ```

   El identificador de la cuenta de almacenamiento es el identificador de recurso para la cuenta de almacenamiento a la que desea enviar los registros.

- Para habilitar la transmisión de registros de diagnóstico a un centro de eventos, use este comando:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
   ```

   El identificador de regla de Service Bus es una cadena con este formato:

   ```azurecli-interactive
   {service bus resource ID}/authorizationrules/{key name}
   ```

- Para habilitar el envío de registros de diagnóstico a un área de trabajo de Log Analytics, use este comando:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --workspaceId <resource id of the log analytics workspace> --enabled true
   ```

Puede combinar estos parámetros para habilitar varias opciones de salida.

### <a name="rest-api"></a>API DE REST

Lea sobre cómo [cambiar la configuración de diagnóstico con la API de REST de Azure Monitor](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings). 

### <a name="resource-manager-template"></a>Plantilla de Resource Manager

Lea sobre cómo [habilitar la configuración de diagnóstico al crear recursos con la plantilla de Resource Manager](../monitoring-and-diagnostics/monitoring-enable-diagnostic-logs-using-template.md). 

## <a name="stream-into-azure-sql-analytics"></a>Transmisión en Azure SQL Analytics 

Azure SQL Analytics es una solución en la nube que permite supervisar desde un panel único el rendimiento de bases de datos, grupos elásticos e instancias administradas de Azure SQL a escala y entre varias suscripciones. Recopila y visualiza métricas importantes del rendimiento de Azure SQL Database con inteligencia integrada para solucionar problemas de rendimiento.

![Información general de Azure SQL Analytics](../log-analytics/media/log-analytics-azure-sql/azure-sql-sol-overview.png)

Las métricas y los registros de diagnóstico de SQL Database se pueden transmitir a Azure SQL Analytics mediante la opción **Enviar a Log Analytics** integrada en la hoja de configuración de diagnósticos del portal. También puede habilitar Log Analytics mediante una configuración de diagnóstico a través de los cmdlets de PowerShell, la CLI de Azure o la API de REST de Azure Monitor.

### <a name="installation-overview"></a>Introducción a la instalación

Supervisar una flota de SQL Database resulta sencillo con Azure SQL Analytics. Se necesitan tres pasos:

1. Creación de una solución de Azure SQL Analytics en Azure Marketplace
2. Creación de un área de trabajo de supervisión en la solución
3. Configuración de bases de datos para transmitir datos de telemetría de diagnóstico al área de trabajo que ha creado

Si utiliza grupos elásticos o instancias administradas, además de configurar los datos de telemetría de diagnóstico de la base de datos, tendrá que configurar también la transmisión de telemetría de diagnóstico de estos recursos.

### <a name="create-azure-sql-analytics-resource"></a>Creación de un recurso de Azure SQL Analytics

1. Busque Azure SQL Analytics en Azure Marketplace y selecciónelo.

   ![Busque Azure SQL Analytics en el portal.](./media/sql-database-metrics-diag-logging/sql-analytics-in-marketplace.png)
   
2. Seleccione **Crear** en la pantalla de información general de la solución.

3. Rellene el formulario de Azure SQL Analytics con la información adicional necesaria: nombre del área de trabajo, suscripción, grupo de recursos, ubicación y plan de tarifa.
 
   ![Configuración de Azure SQL Analytics en el portal](./media/sql-database-metrics-diag-logging/sql-analytics-configuration-blade.png)

4. Seleccione **Aceptar** para confirmar y **Crear** para terminar.

### <a name="configure-databases-to-record-metrics-and-diagnostics-logs"></a>Configuración de bases de datos para registrar las métricas y los registros de diagnóstico

La forma más sencilla de configurar la ubicación en la que las bases de datos van a registrar sus métricas es hacerlo mediante Azure Portal, tal y como se indicó anteriormente. En el portal, vaya al recurso de SQL Database y seleccione **Configuración de diagnóstico**.

Si utiliza grupos elásticos o instancias administradas, también tendrá que configurar los valores de diagnóstico de estos recursos, así como transmitir sus propios datos de telemetría de diagnóstico al área de trabajo que ha creado.

### <a name="use-the-sql-analytics-solution"></a>Uso de la solución SQL Analytics

SQL Analytics es un panel jerárquico que le permite moverse por la jerarquía de recursos de SQL Database. Para obtener información sobre cómo utilizar la solución SQL Analytics, consulte [Supervisión de Azure SQL Database mediante Azure SQL Analytics](../log-analytics/log-analytics-azure-sql.md).

## <a name="stream-into-event-hubs"></a>Transmisión a Event Hubs

Las métricas y los registros de diagnóstico de SQL Database se pueden transmitir a Event Hubs mediante el uso de la opción **Transmitir a un centro de eventos** integrada en el portal. También puede habilitar el identificador de regla de Service Bus mediante una configuración de diagnóstico a través de los cmdlets de PowerShell, la CLI de Azure o la API de REST de Azure Monitor. 

### <a name="what-to-do-with-metrics-and-diagnostics-logs-in-event-hubs"></a>Utilidad de las métricas y los registros de diagnóstico de Event Hubs
Una vez que los datos seleccionados se transmiten a los Event Hubs, está un paso más cerca de habilitar escenarios de supervisión avanzados. Los centros de Event Hubs actúan como la puerta de entrada de una canalización de eventos. Una vez que los datos se recopilan en un centro de eventos, se pueden transformar y almacenar con cualquier proveedor de análisis en tiempo real o adaptadores de procesamiento por lotes y almacenamiento. Los centros de Event Hubs desacoplan la producción de un flujo de eventos del consumo de esos eventos. De esta manera, los consumidores de eventos pueden tener acceso a los eventos en su propia programación. Para obtener más información sobre Event Hubs, consulte:

- [¿Qué es Azure Event Hubs?](../event-hubs/event-hubs-what-is-event-hubs.md)
- [Introducción a Event Hubs](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

Estas son algunas formas en que se podría usar la funcionalidad de transmisión:

* **Visualización del estado del servicio mediante la transmisión de datos de ruta de acceso activa a Power BI**. Con Event Hubs, Stream Analytics y Power BI, puede transformar fácilmente las métricas y los datos de diagnóstico en información prácticamente en tiempo real sobre los servicios de Azure. Para obtener información general sobre cómo configurar un centro de Event Hubs, procesar datos con Stream Analytics y usar Power BI como salida, vea [Stream Analytics y Power BI](../stream-analytics/stream-analytics-power-bi-dashboard.md).

* **Transmisión de registros a registros de terceros y flujos de telemetría**. Con la transmisión de Event Hubs puede enviar las métricas y los registros de diagnóstico a distintas soluciones de supervisión y análisis de registros de terceros. 

* **Creación de una plataforma personalizada de registro y telemetría**. Si ya tiene una plataforma de telemetría personalizada o está pensando en crear una, la gran escalabilidad en cuanto a la suscripción y la publicación de Event Hubs permite introducir registros de diagnóstico de manera flexible. Vea la [guía de Dan Rosanova para usar Event Hubs en una plataforma de telemetría de escala global](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/).

## <a name="stream-into-storage"></a>Transmisión a Storage

Las métricas y los registros de diagnóstico de SQL Database se pueden guardar en Storage mediante la opción **Archivar en una cuenta de almacenamiento** integrada en el portal. También puede habilitar Storage mediante una configuración de diagnóstico a través de los cmdlets de PowerShell, la CLI de Azure o la API de REST de Azure Monitor.

### <a name="schema-of-metrics-and-diagnostics-logs-in-the-storage-account"></a>Esquema de métricas y registros de diagnóstico en la cuenta de almacenamiento

Una vez que ha configurado la recopilación de métricas y los registros de diagnóstico, se crea un contenedor de almacenamiento en la cuenta de almacenamiento seleccionada cuando las primeras filas de datos están disponibles. La estructura de estos blobs es:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ databases/{database_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```
    
O, sencillamente:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/{resource Id}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Por ejemplo, un nombre de blob para todas las métricas podría ser:

```powershell
insights-metrics-minute/resourceId=/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.SQL/ servers/Server1/databases/database1/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

Si quiere registrar los datos desde el grupo elástico, el nombre de blob es un poco distinto:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ elasticPools/{elastic_pool_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

### <a name="download-metrics-and-logs-from-storage"></a>Descarga de métricas y registros desde Storage

Aprenda a [descargar métricas y registros de diagnóstico desde Storage](../storage/blobs/storage-quickstart-blobs-dotnet.md#download-the-sample-application).

## <a name="data-retention-policy-and-pricing"></a>Directiva de retención de datos y precios

Si selecciona Event Hubs o una cuenta de almacenamiento, puede especificar una directiva de retención. Esta directiva elimina los datos anteriores a un período de tiempo seleccionado. Si especifica Log Analytics, la directiva de retención depende del plan de tarifa seleccionado. Se aplica el consumo de datos de telemetría de diagnóstico por encima de las unidades gratuitas de ingesta de datos asignadas al mes. Las unidades gratuitas de ingesta de datos especificadas habilitan la supervisión gratuita de varias bases de datos al mes. Tenga en cuenta que las bases de datos más activas con cargas de trabajo más pesadas ingerirán más datos que las bases de datos inactivas. Para obtener más información, consulte [Precios de Log Analytics](https://azure.microsoft.com/pricing/details/monitor/). 

Si usa Azure SQL Analytics, puede supervisar fácilmente el consumo de ingesta de datos en la solución. Para ello, seleccione el área de trabajo de OMS en el menú de navegación de Azure SQL Analytics y luego elija Uso y costos estimados.

## <a name="metrics-and-logs-available"></a>Métricas y registros disponibles

Los datos de telemetría de supervisión recopilados pueden utilizarse para realizar su propio **análisis personalizado** y su propio **desarrollo de aplicaciones** utilizando el [lenguaje de SQL Analytics](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries). A continuación, se muestra la estructura de los datos recopilados, las métricas y los registros.

## <a name="all-metrics"></a>Todas las métricas

### <a name="all-metrics-for-elastic-pools"></a>Todas las métricas para grupos elásticos

|**Recurso**|**Métricas**|
|---|---|
|Grupo elástico|Porcentaje de eDTU; eDTU usada; límite de eDTU; porcentaje de CPU; porcentaje de lectura de datos físicos; porcentaje de escritura en registro; porcentaje de sesiones; porcentaje de trabajos; almacenamiento; porcentaje de almacenamiento; límite de almacenamiento y porcentaje de almacenamiento de XTP |

### <a name="all-metrics-for-azure-sql-database"></a>Todas las métricas para Azure SQL Database

|**Recurso**|**Métricas**|
|---|---|
|Azure SQL Database|Porcentaje de DTU; DTU usada; límite de DTU; porcentaje de CPU; porcentaje de lectura de datos físicos; porcentaje de escritura en registro; conexiones correctas, erróneas o bloqueadas por el firewall; porcentaje de sesiones; porcentaje de trabajos; almacenamiento; porcentaje de almacenamiento; porcentaje de almacenamiento de XTP e interbloqueos |

## <a name="logs"></a>Registros

### <a name="logs-for-managed-instance"></a>Registros para Instancia administrada

### <a name="resource-usage-stats"></a>Estadísticas de uso de recursos

|Propiedad|Descripción|
|---|---|
|TenantId|El identificador de inquilino.|
|SourceSystem|Siempre: Azure|
|TimeGenerated [UTC]|Marca de tiempo de cuándo se registró el registro.|
|type|Siempre: AzureDiagnostics|
|ResourceProvider|Nombre del proveedor de recursos Siempre: MICROSOFT.SQL|
|Categoría|Nombre de la categoría Siempre: ResourceUsageStats|
|Recurso|Nombre del recurso.|
|ResourceType|Nombre del tipo de recurso Siempre: MANAGEDINSTANCES|
|SubscriptionId|GUID de la suscripción al que pertenece la base de datos.|
|ResourceGroup|Nombre del grupo de recursos al que pertenece la base de datos.|
|LogicalServerName_s|Nombre de la instancia administrada.|
|ResourceId|URI de recurso.|
|SKU_s|SKU del producto de Instancia administrada|
|virtual_core_count_s|Número de núcleos virtuales disponibles|
|avg_cpu_percent_s|Porcentaje de CPU medio|
|reserved_storage_mb_s|Capacidad de almacenamiento reservado en Instancia administrada|
|storage_space_used_mb_s|Almacenamiento utilizado en Instancia administrada|
|io_requests_s|Recuento de IOPS|
|io_bytes_read_s|Bytes de IOPS leídos|
|io_bytes_written_s|Bytes de IOPS escritos|

### <a name="logs-for-azure-sql-database-and-managed-instance-database"></a>Registros para Azure SQL Database y bases de datos de Instancia administrada

### <a name="query-store-runtime-statistics"></a>Estadísticas de tiempo de ejecución del Almacén de consultas

|Propiedad|Descripción|
|---|---|
|TenantId|El identificador de inquilino.|
|SourceSystem|Siempre: Azure|
|TimeGenerated [UTC]|Marca de tiempo de cuándo se registró el registro.|
|type|Siempre: AzureDiagnostics|
|ResourceProvider|Nombre del proveedor de recursos Siempre: MICROSOFT.SQL|
|Categoría|Nombre de la categoría Siempre: QueryStoreRuntimeStatistics|
|nombreOperación|Nombre de la operación. Siempre: QueryStoreRuntimeStatisticsEvent|
|Recurso|Nombre del recurso.|
|ResourceType|Nombre del tipo de recurso Siempre: SERVERS/DATABASES|
|SubscriptionId|GUID de la suscripción al que pertenece la base de datos.|
|ResourceGroup|Nombre del grupo de recursos al que pertenece la base de datos.|
|LogicalServerName_s|Nombre del servidor al que pertenece la base de datos.|
|ElasticPoolName_s|Nombre del grupo elástico al que pertenece la base de datos, si pertenece a alguno.|
|DatabaseName_s|Nombre de la base de datos.|
|ResourceId|URI de recurso.|
|query_hash_s|Hash de consulta.|
|query_plan_hash_s|Hash del plan de consulta.|
|statement_sql_handle_s|Controlador de SQL de instrucción.|
|interval_start_time_d|Inicio datetimeoffset del intervalo en número de tics de 1900-1-1|
|interval_end_time_d|Fin datetimeoffset del intervalo en número de tics de 1900-1-1|
|logical_io_writes_d|Número total de escrituras de E/S lógicas|
|max_logical_io_writes_d|Número máximo de escrituras de E/S lógicas por ejecución|
|physical_io_reads_d|Número total de lecturas de E/S físicas|
|max_physical_io_reads_d|Número máximo de escrituras de E/S lógicas por ejecución|
|logical_io_reads_d|Número total de lecturas de E/S lógicas|
|max_logical_io_reads_d|Número máximo de escrituras de E/S lógicas por ejecución|
|execution_type_d|Tipo de ejecución.|
|count_executions_d|Número de ejecuciones de la consulta|
|cpu_time_d|Tiempo total de la CPU usado por la consulta en microsegundos|
|max_cpu_time_d|Consumidor de tiempo máximo de la CPU por una sola ejecución en microsegundos|
|dop_d|Suma de los grados de paralelismo|
|max_dop_d|Grado máximo de paralelismo que se usa para una sola ejecución|
|rowcount_d|Número total de filas devueltas|
|max_rowcount_d|Número máximo de filas devueltas en una sola ejecución|
|query_max_used_memory_d|Cantidad total de memoria usada en KB|
|max_query_max_used_memory_d|Cantidad máxima de memoria usada por una sola ejecución en KB|
|duration_d|Tiempo de ejecución total en milisegundos|
|max_duration_d|Tiempo de ejecución máximo de una sola ejecución|
|num_physical_io_reads_d|Número total de lecturas físicas|
|max_num_physical_io_reads_d|Número máximo de lecturas físicas por ejecución|
|log_bytes_used_d|Cantidad total de bytes de registro usados|
|max_log_bytes_used_d|Cantidad máxima de bytes de registro usados por ejecución|
|query_id_d|Identificador de la consulta en el Almacén de consultas.|
|plan_id_d|Identificador del plan en el Almacén de consultas.|

Obtenga más información sobre los [datos de estadísticas de tiempo de ejecución del Almacén de consultas](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-runtime-stats-transact-sql).

### <a name="query-store-wait-statistics"></a>Estadísticas de espera del Almacén de consultas

|Propiedad|Descripción|
|---|---|
|TenantId|El identificador de inquilino.|
|SourceSystem|Siempre: Azure|
|TimeGenerated [UTC]|Marca de tiempo de cuándo se registró el registro.|
|type|Siempre: AzureDiagnostics|
|ResourceProvider|Nombre del proveedor de recursos Siempre: MICROSOFT.SQL|
|Categoría|Nombre de la categoría Siempre: QueryStoreWaitStatistics|
|nombreOperación|Nombre de la operación. Siempre: QueryStoreWaitStatisticsEvent|
|Recurso|Nombre del recurso|
|ResourceType|Nombre del tipo de recurso Siempre: SERVERS/DATABASES|
|SubscriptionId|GUID de la suscripción al que pertenece la base de datos.|
|ResourceGroup|Nombre del grupo de recursos al que pertenece la base de datos.|
|LogicalServerName_s|Nombre del servidor al que pertenece la base de datos.|
|ElasticPoolName_s|Nombre del grupo elástico al que pertenece la base de datos, si pertenece a alguno.|
|DatabaseName_s|Nombre de la base de datos.|
|ResourceId|URI de recurso.|
|wait_category_s|Categoría de la espera|
|is_parameterizable_s|La consulta se puede parametrizar.|
|statement_type_s|Tipo de la instrucción|
|statement_key_hash_s|Hash de clave de instrucción|
|exec_type_d|Tipo de ejecución.|
|total_query_wait_time_ms_d|Tiempo total de espera de la consulta en la categoría de espera específica|
|max_query_wait_time_ms_d|Tiempo de espera máximo de la consulta en ejecución individual en la categoría de espera específica.|
|query_param_type_d|0|
|query_hash_s|Hash de consulta del Almacén de consultas|
|query_plan_hash_s|Hash de plan de consulta en el Almacén de consultas.|
|statement_sql_handle_s|Controlador de instrucción en el Almacén de consultas.|
|interval_start_time_d|Inicio datetimeoffset del intervalo en número de tics de 1900-1-1|
|interval_end_time_d|Fin datetimeoffset del intervalo en número de tics de 1900-1-1|
|count_executions_d|Número de ejecuciones de la consulta.|
|query_id_d|Identificador de la consulta en el Almacén de consultas.|
|plan_id_d|Identificador del plan en el Almacén de consultas.|

Obtenga más información sobre los [datos de estadísticas de espera del Almacén de consultas](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql).

### <a name="errors-dataset"></a>Conjunto de datos de errores

|Propiedad|Descripción|
|---|---|
|TenantId|El identificador de inquilino.|
|SourceSystem|Siempre: Azure|
|TimeGenerated [UTC]|Marca de tiempo de cuándo se registró el registro.|
|type|Siempre: AzureDiagnostics|
|ResourceProvider|Nombre del proveedor de recursos Siempre: MICROSOFT.SQL|
|Categoría|Nombre de la categoría Siempre: Errors|
|nombreOperación|Nombre de la operación. Siempre: ErrorEvent|
|Recurso|Nombre del recurso|
|ResourceType|Nombre del tipo de recurso Siempre: SERVERS/DATABASES|
|SubscriptionId|GUID de la suscripción al que pertenece la base de datos.|
|ResourceGroup|Nombre del grupo de recursos al que pertenece la base de datos.|
|LogicalServerName_s|Nombre del servidor al que pertenece la base de datos.|
|ElasticPoolName_s|Nombre del grupo elástico al que pertenece la base de datos, si pertenece a alguno.|
|DatabaseName_s|Nombre de la base de datos.|
|ResourceId|URI de recurso.|
|Message|Mensaje de error en texto sin formato.|
|user_defined_b|Es el bit de error definido por el usuario.|
|error_number_d|Código de error.|
|Gravedad|Gravedad del error.|
|state_d|Estado del error.|
|query_hash_s|Hash de consulta de la consulta errónea, si está disponible.|
|query_plan_hash_s|Hash de plan de consulta de la consulta errónea, si está disponible.|

Obtenga más información sobre [mensajes de error de SQL Server](https://msdn.microsoft.com/library/cc645603.aspx).

### <a name="database-wait-statistics-dataset"></a>Conjunto de datos de estadísticas de espera de base de datos

|Propiedad|Descripción|
|---|---|
|TenantId|El identificador de inquilino.|
|SourceSystem|Siempre: Azure|
|TimeGenerated [UTC]|Marca de tiempo de cuándo se registró el registro.|
|type|Siempre: AzureDiagnostics|
|ResourceProvider|Nombre del proveedor de recursos Siempre: MICROSOFT.SQL|
|Categoría|Nombre de la categoría Siempre: DatabaseWaitStatistics|
|nombreOperación|Nombre de la operación. Siempre: DatabaseWaitStatisticsEvent|
|Recurso|Nombre del recurso|
|ResourceType|Nombre del tipo de recurso Siempre: SERVERS/DATABASES|
|SubscriptionId|GUID de la suscripción al que pertenece la base de datos.|
|ResourceGroup|Nombre del grupo de recursos al que pertenece la base de datos.|
|LogicalServerName_s|Nombre del servidor al que pertenece la base de datos.|
|ElasticPoolName_s|Nombre del grupo elástico al que pertenece la base de datos, si pertenece a alguno.|
|DatabaseName_s|Nombre de la base de datos.|
|ResourceId|URI de recurso.|
|wait_type_s|Nombre del tipo de espera.|
|start_utc_date_t [UTC]|Hora de inicio del período medido|
|end_utc_date_t [UTC]|Hora de finalización del período medido|
|delta_max_wait_time_ms_d|Tiempo máximo esperado por ejecución|
|delta_signal_wait_time_ms_d|Tiempo de espera de señal total.|
|delta_wait_time_ms_d|Tiempo total de espera en el período.|
|delta_waiting_tasks_count_d|Número de tareas en espera.|

Obtenga más información sobre las [estadísticas de espera de la base de datos](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql).

### <a name="time-outs-dataset"></a>Conjunto de datos de los tiempos de espera

|Propiedad|Descripción|
|---|---|
|TenantId|El identificador de inquilino.|
|SourceSystem|Siempre: Azure|
|TimeGenerated [UTC]|Marca de tiempo de cuándo se registró el registro.|
|type|Siempre: AzureDiagnostics|
|ResourceProvider|Nombre del proveedor de recursos Siempre: MICROSOFT.SQL|
|Categoría|Nombre de la categoría Siempre: Timeouts|
|nombreOperación|Nombre de la operación. Siempre: TimeoutEvent|
|Recurso|Nombre del recurso|
|ResourceType|Nombre del tipo de recurso Siempre: SERVERS/DATABASES|
|SubscriptionId|GUID de la suscripción al que pertenece la base de datos.|
|ResourceGroup|Nombre del grupo de recursos al que pertenece la base de datos.|
|LogicalServerName_s|Nombre del servidor al que pertenece la base de datos.|
|ElasticPoolName_s|Nombre del grupo elástico al que pertenece la base de datos, si pertenece a alguno.|
|DatabaseName_s|Nombre de la base de datos.|
|ResourceId|URI de recurso.|
|error_state_d|Código de estado de error.|
|query_hash_s|Hash de consulta si está disponible.|
|query_plan_hash_s|Hash de plan de consulta si está disponible.|

### <a name="blockings-dataset"></a>Conjunto de datos de bloqueos

|Propiedad|Descripción|
|---|---|
|TenantId|El identificador de inquilino.|
|SourceSystem|Siempre: Azure|
|TimeGenerated [UTC]|Marca de tiempo de cuándo se registró el registro.|
|type|Siempre: AzureDiagnostics|
|ResourceProvider|Nombre del proveedor de recursos Siempre: MICROSOFT.SQL|
|Categoría|Nombre de la categoría Siempre: Blocks|
|nombreOperación|Nombre de la operación. Siempre: BlockEvent|
|Recurso|Nombre del recurso|
|ResourceType|Nombre del tipo de recurso Siempre: SERVERS/DATABASES|
|SubscriptionId|GUID de la suscripción al que pertenece la base de datos.|
|ResourceGroup|Nombre del grupo de recursos al que pertenece la base de datos.|
|LogicalServerName_s|Nombre del servidor al que pertenece la base de datos.|
|ElasticPoolName_s|Nombre del grupo elástico al que pertenece la base de datos, si pertenece a alguno.|
|DatabaseName_s|Nombre de la base de datos.|
|ResourceId|URI de recurso.|
|lock_mode_s|Modo de bloqueo usado por la consulta.|
|resource_owner_type_s|Propietario del bloqueo.|
|blocked_process_filtered_s|Archivo XML de informe del proceso bloqueado|
|duration_d|Duración del bloqueo en microsegundos.|

### <a name="deadlocks-dataset"></a>Conjunto de datos de interbloqueos

|Propiedad|Descripción|
|---|---|
|TenantId|El identificador de inquilino.|
|SourceSystem|Siempre: Azure|
|TimeGenerated [UTC] |Marca de tiempo de cuándo se registró el registro.|
|type|Siempre: AzureDiagnostics|
|ResourceProvider|Nombre del proveedor de recursos Siempre: MICROSOFT.SQL|
|Categoría|Nombre de la categoría Siempre: Deadlocks|
|nombreOperación|Nombre de la operación. Siempre: DeadlockEvent|
|Recurso|Nombre del recurso.|
|ResourceType|Nombre del tipo de recurso Siempre: SERVERS/DATABASES|
|SubscriptionId|GUID de la suscripción al que pertenece la base de datos.|
|ResourceGroup|Nombre del grupo de recursos al que pertenece la base de datos.|
|LogicalServerName_s|Nombre del servidor al que pertenece la base de datos.|
|ElasticPoolName_s|Nombre del grupo elástico al que pertenece la base de datos, si pertenece a alguno.|
|DatabaseName_s|Nombre de la base de datos. |
|ResourceId|URI de recurso.|
|deadlock_xml_s|Informe XML de interbloqueo.|

### <a name="automatic-tuning-dataset"></a>Conjunto de datos de ajuste automático

|Propiedad|Descripción|
|---|---|
|TenantId|El identificador de inquilino.|
|SourceSystem|Siempre: Azure|
|TimeGenerated [UTC]|Marca de tiempo de cuándo se registró el registro.|
|type|Siempre: AzureDiagnostics|
|ResourceProvider|Nombre del proveedor de recursos Siempre: MICROSOFT.SQL|
|Categoría|Nombre de la categoría Siempre: AutomaticTuning|
|Recurso|Nombre del recurso.|
|ResourceType|Nombre del tipo de recurso Siempre: SERVERS/DATABASES|
|SubscriptionId|GUID de la suscripción al que pertenece la base de datos.|
|ResourceGroup|Nombre del grupo de recursos al que pertenece la base de datos.|
|LogicalServerName_s|Nombre del servidor al que pertenece la base de datos.|
|LogicalDatabaseName_s|Nombre de la base de datos.|
|ElasticPoolName_s|Nombre del grupo elástico al que pertenece la base de datos, si pertenece a alguno.|
|DatabaseName_s|Nombre de la base de datos.|
|ResourceId|URI de recurso.|
|RecommendationHash_s|Código hash único de la recomendación de ajuste automático.|
|OptionName_s|Operación de ajuste automático.|
|Schema_s|Esquema de la base de datos.|
|Table_s|Tabla afectada.|
|IndexName_s|Nombre del índice.|
|IndexColumns_s|Nombre de la columna.|
|IncludedColumns_s|Columnas incluidas.|
|EstimatedImpact_s|Impacto estimado del formato JSON de la recomendación de ajuste automático.|
|Event_s|Tipo de evento de ajuste automático.|
|Timestamp_t|Última marca de tiempo actualizada.|

### <a name="intelligent-insights-dataset"></a>Conjunto de datos de Intelligent Insights
Obtenga más información sobre el [formato de registro de Intelligent Insights](sql-database-intelligent-insights-use-diagnostics-log.md).

## <a name="next-steps"></a>Pasos siguientes

Para aprender a habilitar el registro y comprender las métricas y las categorías de registro admitidas por los diferentes servicios de Azure, lea lo siguiente:

 * [Información general sobre las métricas en Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md)
 * [Información general de los registros de diagnóstico de Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)

Para obtener información sobre Event Hubs, lea lo siguiente:

* [¿Qué es Azure Event Hubs?](../event-hubs/event-hubs-what-is-event-hubs.md)
* [Introducción a Event Hubs](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

Para obtener más información acerca de Storage, vea cómo [descargar métricas y registros de diagnóstico desde Storage](../storage/blobs/storage-quickstart-blobs-dotnet.md#download-the-sample-application).
