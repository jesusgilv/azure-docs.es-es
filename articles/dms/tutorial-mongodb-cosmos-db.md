---
title: 'Tutorial: Uso de Azure Database Migration Service para migrar MongoDB a la API de Mongo de Azure Cosmos DB sin conexión | Microsoft Docs'
description: Obtenga información sobre cómo migrar desde bases de datos locales de MongoDB a la API de Mongo de Azure Cosmos DB sin conexión mediante Azure Database Migration Service.
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: ''
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 12/11/2018
ms.openlocfilehash: 46e8ca97411e9dce77c0c82c4e0f5dd164bce01b
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/13/2018
ms.locfileid: "53320741"
---
# <a name="tutorial-migrate-mongodb-to-azure-cosmos-db-mongo-api-offline-using-dms"></a>Tutorial: Migración de MongoDB a la API de Mongo de Azure Cosmos DB sin conexión mediante DMS
Puede usar Azure Database Migration Service para realizar una migración sin conexión (de una sola vez) de las bases de datos desde una instancia local o en la nube de MongoDB a la API de Mongo de Azure Cosmos DB.

En este tutorial, aprenderá a:
> [!div class="checklist"]
> * Crear una instancia de Azure Database Migration Service.
> * Crear un proyecto de migración mediante Azure Database Migration Service.
> * Ejecutar la migración.
> * Supervisar la migración

En este tutorial, migrará el conjunto de datos **Wingtips** de MongoDB hospedado en una máquina virtual de Azure a la API de Mongo de Cosmos DB mediante Azure Database Migration Service. Si no tiene un origen de MongoDB ya configurado, vea el artículo [Instalación y configuración de MongoDB en una máquina virtual Windows en Azure](https://docs.microsoft.com/azure/virtual-machines/windows/install-mongodb).

## <a name="prerequisites"></a>Requisitos previos
Para completar este tutorial, necesita:
- [Crear una cuenta de API de MongoDB de Azure Cosmos DB](https://ms.portal.azure.com/#create/Microsoft.DocumentDB).
- Crear una red virtual para Azure Database Migration Service mediante el modelo de implementación de Azure Resource Manager, que proporciona conectividad de sitio a sitio a los servidores de origen local utilizando [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) o [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
- Asegúrese de que las reglas del grupo de seguridad de red de Azure Virtual Network (VNET) no bloquean los puertos de comunicación 443, 53, 9354, 445 y 12000. Para obtener información más detallada sobre el filtrado de tráfico con NSG de Azure VNET, vea el artículo [Filtrado del tráfico de red con grupos de seguridad de red](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
- Abra el Firewall de Windows para permitir que Azure Database Migration Service acceda al servidor de MongoDB de origen que, de manera predeterminada, es el puerto TCP 27017.
- Cuando se usa un dispositivo de firewall frente a las bases de datos de origen, puede que sea necesario agregar reglas de firewall para permitir que Azure Database Migration Service acceda a las bases de datos de origen para realizar la migración.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registro del proveedor de recursos Microsoft.DataMigration
1. Inicie sesión en Azure Portal, seleccione **Todos los servicios** y, después, **Suscripciones**.

   ![Mostrar suscripciones en el portal](media/tutorial-mongodb-to-cosmosdb/portal-select-subscription1.png)
       
2. Seleccione la suscripción en la que quiere crear la instancia de Azure Database Migration Service y después seleccione **Proveedores de recursos**.
 
    ![Mostrar los proveedores de recursos](media/tutorial-mongodb-to-cosmosdb/portal-select-resource-provider.png)
    
3.  Busque la migración y después, a la derecha de **Microsoft.DataMigration**, seleccione **Registrar**.
 
    ![Registro del proveedor de recursos](media/tutorial-mongodb-to-cosmosdb/portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>Creación de una instancia
1.  En Azure Portal, seleccione + **Crear un recurso**, busque Azure Database Migration Service y, después, seleccione **Azure Database Migration Service** en la lista desplegable.

    ![Azure Marketplace](media/tutorial-mongodb-to-cosmosdb/portal-marketplace.png)

2.  En la pantalla **Azure Database Migration Service**, seleccione **Crear**.
 
    ![Creación de una instancia de Azure Database Migration Service](media/tutorial-mongodb-to-cosmosdb/dms-create1.png)
  
3.  En la pantalla **Crear el servicio de migración**, especifique un nombre para el servicio, la suscripción y un grupo de recursos nuevo o existente.

4. Seleccione la ubicación en la que quiere crear la instancia de Azure Database Migration Service. 

5. Seleccione una red virtual (VNET) existente o cree una nueva.

    La VNET proporciona a Azure Database Migration Service acceso a la instancia de SQL Server de origen y a la instancia de destino de Azure SQL Database.

    Para más información sobre cómo crear una VNET en Azure Portal, vea el artículo [Create a virtual network using the Azure portal](https://aka.ms/DMSVnet) (Creación de una red virtual con Azure Portal).

6. Seleccione un plan de tarifa.

    Para más información sobre los costos y planes de tarifa, vea la [página de precios](https://aka.ms/dms-pricing).

    Si necesita ayuda para elegir el nivel adecuado de Azure Database Migration Service, consulte las recomendaciones descritas en [esta entrada de blog](https://go.microsoft.com/fwlink/?linkid=861067).  

     ![Configuración de la instancia de Azure Database Migration Service](media/tutorial-mongodb-to-cosmosdb/dms-settings2.png)

7.  Seleccione **Crear** para crear el servicio.

## <a name="create-a-migration-project"></a>Creación de un proyecto de migración
Después de crear el servicio, búsquelo en Azure Portal, ábralo y cree un proyecto de migración.

1. En Azure Portal, seleccione **Todos los servicios**, busque Azure Database Migration Service y, luego, elija **Azure Database Migration Services**.
 
      ![Búsqueda de todas las instancias de Azure Database Migration Service](media/tutorial-mongodb-to-cosmosdb/dms-search.png)

2. En la pantalla **Azure Database Migration Services**, busque el nombre de la instancia de Azure Database Migration Service que creó y, después, seleccione la instancia.

3. Seleccione **+ New Migration Project** (+ Nuevo proyecto de migración).

4. En la pantalla **Nuevo proyecto de migración**, especifique un nombre para el proyecto. En el cuadro de texto **Source server type** (Tipo de servidor de origen), seleccione **MongoDB**, en el cuadro de texto **Target server type** (Tipo de servidor de destino), seleccione **CosmosDB (API de MongoDB)** y, por último, en **Elegir tipo de actividad**, seleccione **Migración de datos sin conexión**. 

    ![Creación de un proyecto de Database Migration Service](media/tutorial-mongodb-to-cosmosdb/dms-create-project.png)

5.  Seleccione **Crear y ejecutar una actividad** para crear el proyecto y ejecutar la actividad de migración.

## <a name="specify-source-details"></a>Especificación de los detalles de origen
1. En la pantalla **Detalles del origen**, especifique los detalles de conexión del servidor de MongoDB de origen.
    
   También puede usar el modo de cadena de conexión y proporcionar una ubicación para un contenedor de archivos de almacén de blog en el que haya volcado los datos de la colección que se va a migrar.

   > [!NOTE]
   > Azure Database Migration Service también puede migrar documentos BSON o JSON en colecciones de la API de Mongo de Azure Cosmos DB.
    
   También puede usar la dirección IP en los casos en que no sea posible la resolución de nombres de DNS.

   ![Especificación de los detalles de origen](media/tutorial-mongodb-to-cosmosdb/dms-specify-source.png)

2. Seleccione **Guardar**.

## <a name="specify-target-details"></a>Especificación de los detalles de destino
1. En la pantalla **Detalles del destino de la migración**, especifique los detalles de conexión de la cuenta de Azure Cosmos DB de destino, que es la cuenta de MongoDB para Azure Cosmos DB aprovisionada previamente a la que va a migrar los datos de MongoDB.

    ![Especificación de los detalles de destino](media/tutorial-mongodb-to-cosmosdb/dms-specify-target.png)

2. Seleccione **Guardar**.

## <a name="map-to-target-databases"></a>Asignación a bases de datos de destino
1. En la pantalla **Map to target databases** (Asignar a bases de datos de destino), asigne la base de datos de origen y de destino para la migración.

    Si la base de datos de destino contiene el mismo nombre de base de datos que la de origen, Azure Database Migration Service selecciona la base de datos de destino de forma predeterminada.

    Si la cadena **Crear** aparece junto al nombre de la base de datos, indica que Azure Database Migration Service no encontró la base de datos de destino, y el servicio creará la base de datos automáticamente.

    En este momento de la migración, puede [aprovisionar rendimiento](https://docs.microsoft.com/azure/cosmos-db/set-throughput). En Cosmos DB, puede aprovisionar rendimiento a nivel de base de datos o individualmente para cada colección. El rendimiento se mide en [unidades de solicitud](https://docs.microsoft.com/azure/cosmos-db/request-units) (RU). Obtenga más información sobre los [precios de Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/).

    ![Asignación a bases de datos de destino](media/tutorial-mongodb-to-cosmosdb/dms-map-target-databases.png)

2. Seleccione **Guardar**.
3. En la pantalla **Configuración de colecciones**, expanda la lista de colecciones y, a continuación, revise la lista de las colecciones que se van a migrar.

    Tenga en cuenta que Azure Database Migration Service selecciona automáticamente todas las colecciones existentes en la instancia de MongoDB de origen que no existen en la cuenta de Azure Cosmos DB de destino. Si quiere volver a migrar las colecciones que ya incluyen datos, necesita seleccionar explícitamente las colecciones de esta hoja.

    Puede especificar la cantidad de unidades de solicitud que desea que las colecciones usen. Azure Database Migration Service sugiere valores predeterminados inteligentes en función del tamaño de la colección.

    También puede especificar una clave de partición para aprovechar las ventajas de la [creación de particiones en Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview) para una escalabilidad óptima. No olvide revisar los [procedimientos recomendados para seleccionar una clave de partición](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview#choose-partitionkey).

    ![Selección de tablas de colecciones](media/tutorial-mongodb-to-cosmosdb/dms-collection-setting.png)

4. Seleccione **Guardar**.

5. En la pantalla **Migration summary** (Resumen de migración), en el cuadro de texto **Nombre de actividad**, especifique un nombre para la actividad de migración.

    ![Resumen de migración](media/tutorial-mongodb-to-cosmosdb/dms-migration-summary.png)

## <a name="run-the-migration"></a>Ejecución de la migración
- Seleccione **Ejecutar migración**.

    Aparecerá la ventana de actividad de migración y el **estado** de la actividad será **Sin iniciar**.

    ![Estado de la actividad](media/tutorial-mongodb-to-cosmosdb/dms-activity-status.png)

## <a name="monitor-the-migration"></a>Supervisión de la migración
- En la pantalla de la actividad de migración, seleccione **Actualizar** para actualizar la vista hasta que el valor de **Estado** de la migración sea **Completado**.

   > [!NOTE]
   > Puede seleccionar la actividad para obtener los detalles de las métricas de migración a nivel de base de datos y de colección.

    ![Estado de actividad completado](media/tutorial-mongodb-to-cosmosdb/dms-activity-completed.png)

## <a name="verify-data-in-cosmos-db"></a>Verificación de datos en Cosmos DB

- Una vez completada la migración, puede consultar la cuenta de la API de MongoDB para Cosmos DB a fin de verificar que todas las colecciones se han migrado correctamente.

    ![Estado de actividad completado](media/tutorial-mongodb-to-cosmosdb/dms-cosmosdb-data-explorer.png)

## <a name="additional-resources"></a>Recursos adicionales

 * [Información sobre el servicio Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)

## <a name="next-steps"></a>Pasos siguientes
- Revise las directrices de migración en la [Guía de migración de bases de datos](https://datamigration.microsoft.com/) para consultar escenarios adicionales.