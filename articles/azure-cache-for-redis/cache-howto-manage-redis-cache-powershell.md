---
title: Administración de Azure Cache for Redis con Azure PowerShell | Microsoft Docs
description: Aprenda a realizar tareas administrativas para Azure Cache for Redis usando Azure PowerShell.
services: azure-cache-for-redis
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: 1136efe5-1e33-4d91-bb49-c8e2a6dca475
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: azure-cache-for-redis
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: wesmc
ms.openlocfilehash: 0186882183ed0848fd8b14d186b55ad1a5ff58ea
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/07/2018
ms.locfileid: "53022091"
---
# <a name="manage-azure-cache-for-redis-with-azure-powershell"></a>Administración de Azure Cache for Redis con Azure PowerShell
> [!div class="op_single_selector"]
> * [PowerShell](cache-howto-manage-redis-cache-powershell.md)
> * [CLI de Azure](cache-manage-cli.md)
> 
> 

Este tema muestra cómo realizar tareas comunes, como crear, actualizar y escalar las instancias de Azure Cache for Redis, cómo volver a generar las claves de acceso y cómo ver información acerca de las memorias caché. Para obtener una lista completa de cmdlets de Azure Cache for Redis PowerShell, consulte [Azure Cache for Redis cmdlets](https://docs.microsoft.com/powershell/module/azurerm.rediscache/?view=azurermps-6.6.0).

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

Para más información sobre el modelo de implementación clásico, consulte [Implementación mediante Azure Resource Manager frente al modelo clásico. Conozca los modelos de implementación y el estado de los recursos](../azure-resource-manager/resource-manager-deployment-model.md).

## <a name="prerequisites"></a>Requisitos previos
Si ya ha instalado Azure PowerShell, debe tener la versión 1.0.0 (o posterior) de Azure PowerShell. Puede comprobar la versión de Azure PowerShell que ha instalado con este comando en el símbolo del sistema de Azure PowerShell.

    Get-Module azure | format-table version


En primer lugar, debe iniciar sesión en Azure con este comando.

    Connect-AzureRmAccount

Especifique la dirección de correo electrónico de la cuenta de Azure y su contraseña en el cuadro de diálogo de inicio de sesión de Microsoft Azure.

A continuación, si tiene varias suscripciones de Azure, deberá establecer la suscripción de Azure. Para ver una lista de las suscripciones actuales, ejecute este comando.

    Get-AzureRmSubscription | sort SubscriptionName | Select SubscriptionName

Para especificar la suscripción, ejecute el siguiente comando. En el ejemplo siguiente, el nombre de la suscripción es `ContosoSubscription`.

    Select-AzureRmSubscription -SubscriptionName ContosoSubscription

Para poder usar Windows PowerShell con el Administrador de recursos de Azure, necesita lo siguiente:

* Windows PowerShell, versión 3.0 o 4.0. Para buscar la versión de Windows PowerShell, escriba:`$PSVersionTable` y compruebe que el valor de `PSVersion` es 3.0 o 4.0. Para instalar una versión compatible, consulte [Windows Management Framework 3.0](https://www.microsoft.com/download/details.aspx?id=34595) o [Windows Management Framework 4.0](https://www.microsoft.com/download/details.aspx?id=40855).

Para obtener ayuda detallada con cualquier cmdlet que aparezca en este tutorial, use el cmdlet Get-Help.

    Get-Help <cmdlet-name> -Detailed

Por ejemplo, para obtener ayuda para el cmdlet `New-AzureRmRedisCache` , escriba:

    Get-Help New-AzureRmRedisCache -Detailed

### <a name="how-to-connect-to-other-clouds"></a>Conexión a otras nubes
De forma predeterminada, el entorno de Azure es `AzureCloud`, que representa la instancia de nube de Azure global. Para conectarse a una instancia diferente, utilice el comando `Connect-AzureRmAccount` con el conmutador de línea de comandos `-Environment` o -`EnvironmentName` con el nombre de entorno o el entorno deseado.

Para ver la lista de entornos disponibles, ejecute el cmdlet `Get-AzureRmEnvironment` .

### <a name="to-connect-to-the-azure-government-cloud"></a>Conexión a la nube de Azure Government
Para conectarse a la nube de Azure Government, utilice uno de los siguientes comandos.

    Connect-AzureRmAccount -EnvironmentName AzureUSGovernment

o

    Connect-AzureRmAccount -Environment (Get-AzureRmEnvironment -Name AzureUSGovernment)

Para crear una memoria caché en la nube de Azure Government, utilice una de las siguientes ubicaciones.

* USGov Virginia
* USGov Iowa

Para más información acerca de la nube de Azure Government, consulte [Microsoft Azure Government](https://azure.microsoft.com/features/gov/) y la [Guía para desarrolladores de Microsoft Azure Government](../azure-government-developer-guide.md).

### <a name="to-connect-to-the-azure-china-cloud"></a>Para conectarse a la nube de China de Azure
Para conectarse a la nube de China de Azure, use uno de los siguientes comandos.

    Connect-AzureRmAccount -EnvironmentName AzureChinaCloud

o

    Connect-AzureRmAccount -Environment (Get-AzureRmEnvironment -Name AzureChinaCloud)

Para crear una caché en la nube de China de Azure, use una de las siguientes ubicaciones.

* Este de China
* Norte de China

Para obtener más información acerca de la nube de China de Azure, consulte [AzureChinaCloud for Azure operated by 21Vianet in China (Nube de China de Azure operada por 21Vianet en China)](http://www.windowsazure.cn/).

### <a name="to-connect-to-microsoft-azure-germany"></a>Conexión a Microsoft Azure Alemania
Para conectarse a Microsoft Azure Alemania, utilice uno de los siguientes comandos.

    Connect-AzureRmAccount -EnvironmentName AzureGermanCloud


o

    Connect-AzureRmAccount -Environment (Get-AzureRmEnvironment -Name AzureGermanCloud)

Para crear una memoria caché en Microsoft Azure Alemania, use una de las siguientes ubicaciones.

* Centro de Alemania
* Noreste de Alemania

Para obtener más información acerca de Microsoft Azure Alemania, consulte [Microsoft Azure Germany](https://azure.microsoft.com/overview/clouds/germany/).

### <a name="properties-used-for-azure-cache-for-redis-powershell"></a>Propiedades utilizadas para Azure Cache for Redis PowerShell
La tabla siguiente contiene las propiedades y las descripciones de los parámetros normalmente utilizados al crear y administrar las instancias de Azure Cache for Redis mediante Azure PowerShell.

| Parámetro | DESCRIPCIÓN | Valor predeterminado |
| --- | --- | --- |
| NOMBRE |Nombre de la memoria caché | |
| Ubicación |Ubicación de la memoria caché | |
| ResourceGroupName |Nombre del grupo de recursos en el que se va a crear la memoria caché | |
| Tamaño |El tamaño de la memoria caché. Los valores válidos son: P1, P2, P3, P4, C0, C1, C2, C3, C4, C5, C6, 250MB, 1GB, 2.5GB, 6GB, 13GB, 26GB, 53GB |1 GB |
| ShardCount |El número de particiones para crear durante la creación de una memoria caché premium con clúster habilitado. Los valores válidos son: 1, 2, 3, 4, 5, 6, 7, 8, 9, 10 | |
| SKU |Especifica la SKU de la memoria caché. Los valores válidos son: Básico, Estándar, Premium |Estándar |
| RedisConfiguration |Especifica la configuración de Redis. Para ver detalles sobre cada opción de configuración, consulte la siguiente tabla [Propiedades de RedisConfiguration](#redisconfiguration-properties) . | |
| EnableNonSslPort |Indica si el puerto no SSL está habilitado. |False |
| MaxMemoryPolicy |Este parámetro está en desuso; utilice RedisConfiguration en su lugar. | |
| StaticIP |Si hospeda la memoria caché en una red virtual, especifica una dirección IP única en la subred de la memoria caché. Si no se ofrece, elija una para usted en la subred. | |
| Subred |Si hospeda la memoria caché en una red virtual, especifica el nombre de la subred en la que se va a implementar la memoria caché. | |
| VirtualNetwork |Si hospeda la memoria caché en una red virtual, especifica el identificador de recurso de la red virtual en la que se va a implementar la memoria caché. | |
| KeyType |Especifica la clave de acceso que hay que volver a generar cuando se renueven las claves de acceso. Los valores válidos son: Principal, secundario | |

### <a name="redisconfiguration-properties"></a>Propiedades de RedisConfiguration
| Propiedad | DESCRIPCIÓN | Planes de tarifa |
| --- | --- | --- |
| rdb-backup-enabled |Si [Persistencia de los datos en Redis](cache-how-to-premium-persistence.md) está habilitado |Solo Premium |
| rdb-storage-connection-string |La cadena de conexión a la cuenta de almacenamiento para [Persistencia de los datos en Redis](cache-how-to-premium-persistence.md) |Solo Premium |
| rdb-backup-frequency |La frecuencia de copia de seguridad de [Persistencia de los datos en Redis](cache-how-to-premium-persistence.md) |Solo Premium |
| maxmemory-reserved |Configura la [memoria reservada](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) para procesos que no están en caché |Estándar y Premium |
| maxmemory-policy |Configura la [directiva de expulsión](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) para la memoria caché |Todos los planes de tarifa |
| notify-keyspace-events |Configura las [notificaciones de Keyspace](cache-configure.md#keyspace-notifications-advanced-settings) |Estándar y Premium |
| hash-max-ziplist-entries |Configura la [optimización de memoria](http://redis.io/topics/memory-optimization) para tipos de datos agregados pequeños |Estándar y Premium |
| hash-max-ziplist-value |Configura la [optimización de memoria](http://redis.io/topics/memory-optimization) para tipos de datos agregados pequeños |Estándar y Premium |
| set-max-intset-entries |Configura la [optimización de memoria](http://redis.io/topics/memory-optimization) para tipos de datos agregados pequeños |Estándar y Premium |
| zset-max-ziplist-entries |Configura la [optimización de memoria](http://redis.io/topics/memory-optimization) para tipos de datos agregados pequeños |Estándar y Premium |
| zset-max-ziplist-value |Configura la [optimización de memoria](http://redis.io/topics/memory-optimization) para tipos de datos agregados pequeños |Estándar y Premium |
| bases de datos |Configura el número de bases de datos. Esta propiedad solo se puede configurar al crear la memoria caché. |Estándar y Premium |

## <a name="to-create-an-azure-cache-for-redis"></a>Creación de una instancia de Azure Redis Cache
Se crean nuevas instancias de Azure Cache for Redis mediante el cmdlet [New-AzureRmRedisCache](https://docs.microsoft.com/powershell/module/azurerm.rediscache/new-azurermrediscache?view=azurermps-6.6.0).

> [!IMPORTANT]
> La primera vez que crea una instancia de Azure Cache for Redis en una suscripción con Azure Portal, el portal registra el espacio de nombres `Microsoft.Cache` para esa suscripción. Si trata de crear la primera instancia de Azure Cache for Redis en una suscripción mediante PowerShell, primero debe registrar ese espacio de nombres mediante el comando siguiente; en caso contrario los cmdlets como `New-AzureRmRedisCache` y `Get-AzureRmRedisCache` producirán un error.
> 
> `Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.Cache"`
> 
> 

Para ver una lista de parámetros disponibles y sus descripciones para `New-AzureRmRedisCache`, ejecute el siguiente comando.

    PS C:\> Get-Help New-AzureRmRedisCache -detailed

    NAME
        New-AzureRmRedisCache

    SYNOPSIS
        Creates a new Azure Cache for Redis.


    SYNTAX
        New-AzureRmRedisCache -Name <String> -ResourceGroupName <String> -Location <String> [-RedisVersion <String>]
        [-Size <String>] [-Sku <String>] [-MaxMemoryPolicy <String>] [-RedisConfiguration <Hashtable>] [-EnableNonSslPort
        <Boolean>] [-ShardCount <Integer>] [-VirtualNetwork <String>] [-Subnet <String>] [-StaticIP <String>]
        [<CommonParameters>]


    DESCRIPTION
        The New-AzureRmRedisCache cmdlet creates a new Azure Cache for Redis.


    PARAMETERS
        -Name <String>
            Name of the Azure Cache for Redis to create.

        -ResourceGroupName <String>
            Name of resource group in which to create the Azure Cache for Redis.

        -Location <String>
            Location in which to create the Azure Cache for Redis.

        -RedisVersion <String>
            RedisVersion is deprecated and will be removed in future release.

        -Size <String>
            Size of the Azure Cache for Redis. The default value is 1GB or C1. Possible values are P1, P2, P3, P4, C0, C1, C2, C3,
            C4, C5, C6, 250MB, 1GB, 2.5GB, 6GB, 13GB, 26GB, 53GB.

        -Sku <String>
            Sku of Azure Cache for Redis. The default value is Standard. Possible values are Basic, Standard and Premium.

        -MaxMemoryPolicy <String>
            The 'MaxMemoryPolicy' setting has been deprecated. Please use 'RedisConfiguration' setting to set
            MaxMemoryPolicy. e.g. -RedisConfiguration @{"maxmemory-policy" = "allkeys-lru"}

        -RedisConfiguration <Hashtable>
            All Redis Configuration Settings. Few possible keys: rdb-backup-enabled, rdb-storage-connection-string,
            rdb-backup-frequency, maxmemory-reserved, maxmemory-policy, notify-keyspace-events, hash-max-ziplist-entries,
            hash-max-ziplist-value, set-max-intset-entries, zset-max-ziplist-entries, zset-max-ziplist-value, databases.

        -EnableNonSslPort <Boolean>
            EnableNonSslPort is used by Azure Cache for Redis. If no value is provided, the default value is false and the
            non-SSL port will be disabled. Possible values are true and false.

        -ShardCount <Integer>
            The number of shards to create on a Premium Cluster Cache.

        -VirtualNetwork <String>
            The exact ARM resource ID of the virtual network to deploy the Azure Cache for Redis in. Example format: /subscriptions/{
            subid}/resourceGroups/{resourceGroupName}/providers/Microsoft.ClassicNetwork/VirtualNetworks/{vnetName}

        -Subnet <String>
            Required when deploying an Azure Cache for Redis inside an existing Azure Virtual Network.

        -StaticIP <String>
            Required when deploying an Azure Cache for Redis inside an existing Azure Virtual Network.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

Para crear una memoria caché con parámetros predeterminados, ejecute el siguiente comando.

    New-AzureRmRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US"

`ResourceGroupName`, `Name`, y `Location` son parámetros necesarios, pero el resto son opcionales y tienen valores predeterminados. Ejecutar el comando anterior crea una instancia de SKU Azure Cache for Redis estándar con el nombre, ubicación y grupo de recursos especificados, que es de 1 GB de tamaño con el puerto no SSL deshabilitado.

Para crear una memoria caché premium, especifique un tamaño de P1 (6 GB - 60 GB), P2 (13 GB - 130 GB), P3 (26 GB - 260 GB) o P4 (53 GB - 530 GB). Para habilitar la agrupación en clústeres, especifique un número de particiones mediante el parámetro `ShardCount` . En el ejemplo siguiente se crea una memoria caché premium P1 con 3 particiones. Una memoria caché premium P1 tiene 6 GB de tamaño y, puesto que especificamos tres particiones el tamaño total es de 18 GB (3 x 6 GB).

    New-AzureRmRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -Sku Premium -Size P1 -ShardCount 3

Para especificar valores para el parámetro `RedisConfiguration`, incluya los valores dentro de `{}` como pares de valor o de clave como en `@{"maxmemory-policy" = "allkeys-random", "notify-keyspace-events" = "KEA"}`. En el ejemplo siguiente se crea una memoria caché estándar de 1 GB con `allkeys-random` maxmemory-policy y notificaciones de keyspace configuradas con `KEA`. Para más información, consulte [Notificaciones de Keyspace (configuración avanzada)](cache-configure.md#keyspace-notifications-advanced-settings) y [Directivas de memoria](cache-configure.md#memory-policies).

    New-AzureRmRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -RedisConfiguration @{"maxmemory-policy" = "allkeys-random", "notify-keyspace-events" = "KEA"}

<a name="databases"></a>

## <a name="to-configure-the-databases-setting-during-cache-creation"></a>Configuración de las bases de datos al crear la memoria caché
El parámetro `databases` se puede configurar al crear la memoria caché. En el ejemplo siguiente se crea una memoria caché premium P3 (26 GB) con 48 bases de datos mediante el cmdlet [New-AzureRmRedisCache](https://docs.microsoft.com/powershell/module/azurerm.rediscache/New-AzureRmRedisCache?view=azurermps-6.6.0) .

    New-AzureRmRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -Sku Premium -Size P3 -RedisConfiguration @{"databases" = "48"}

Para más información sobre la propiedad `databases`, consulte [Configuración predeterminada del servidor Azure Cache for Redis](cache-configure.md#default-redis-server-configuration). Para más información sobre la creación de una memoria caché mediante el cmdlet [New-AzureRmRedisCache](https://docs.microsoft.com/powershell/module/azurerm.rediscache/new-azurermrediscache?view=azurermps-6.6.0), consulte la sección anterior [Creación de una instancia de Azure Redis Cache](#to-create-a-redis-cache).

## <a name="to-update-an-azure-cache-for-redis"></a>Actualización de una instancia de Azure Redis Cache
Las instancias de Azure Cache for Redis se actualizan mediante el cmdlet [Set-AzureRmRedisCache](https://docs.microsoft.com/powershell/module/azurerm.rediscache/Set-AzureRmRedisCache?view=azurermps-6.6.0).

Para ver una lista de parámetros disponibles y sus descripciones para `Set-AzureRmRedisCache`, ejecute el siguiente comando.

    PS C:\> Get-Help Set-AzureRmRedisCache -detailed

    NAME
        Set-AzureRmRedisCache

    SYNOPSIS
        Set Azure Cache for Redis updatable parameters.

    SYNTAX
        Set-AzureRmRedisCache -Name <String> -ResourceGroupName <String> [-Size <String>] [-Sku <String>]
        [-MaxMemoryPolicy <String>] [-RedisConfiguration <Hashtable>] [-EnableNonSslPort <Boolean>] [-ShardCount
        <Integer>] [<CommonParameters>]

    DESCRIPTION
        The Set-AzureRmRedisCache cmdlet sets Azure Cache for Redis parameters.

    PARAMETERS
        -Name <String>
            Name of the Azure Cache for Redis to update.

        -ResourceGroupName <String>
            Name of the resource group for the cache.

        -Size <String>
            Size of the Azure Cache for Redis. The default value is 1GB or C1. Possible values are P1, P2, P3, P4, C0, C1, C2, C3,
            C4, C5, C6, 250MB, 1GB, 2.5GB, 6GB, 13GB, 26GB, 53GB.

        -Sku <String>
            Sku of Azure Cache for Redis. The default value is Standard. Possible values are Basic, Standard and Premium.

        -MaxMemoryPolicy <String>
            The 'MaxMemoryPolicy' setting has been deprecated. Please use 'RedisConfiguration' setting to set
            MaxMemoryPolicy. e.g. -RedisConfiguration @{"maxmemory-policy" = "allkeys-lru"}

        -RedisConfiguration <Hashtable>
            All Redis Configuration Settings. Few possible keys: rdb-backup-enabled, rdb-storage-connection-string,
            rdb-backup-frequency, maxmemory-reserved, maxmemory-policy, notify-keyspace-events, hash-max-ziplist-entries,
            hash-max-ziplist-value, set-max-intset-entries, zset-max-ziplist-entries, zset-max-ziplist-value.

        -EnableNonSslPort <Boolean>
            EnableNonSslPort is used by Azure Cache for Redis. The default value is null and no change will be made to the
            currently configured value. Possible values are true and false.

        -ShardCount <Integer>
            The number of shards to create on a Premium Cluster Cache.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

El cmdlet `Set-AzureRmRedisCache` puede utilizarse para actualizar propiedades tales como `Size`, `Sku`, `EnableNonSslPort` y los valores de `RedisConfiguration`. 

El comando siguiente actualiza el parámetro maxmemory-policy para la instancia de Azure Cache for Redis denominada myCache.

    Set-AzureRmRedisCache -ResourceGroupName "myGroup" -Name "myCache" -RedisConfiguration @{"maxmemory-policy" = "allkeys-random"}

<a name="scale"></a>

## <a name="to-scale-an-azure-cache-for-redis"></a>Escalado de una instancia de Azure Cache for Redis
Se puede usar `Set-AzureRmRedisCache` para escalar una instancia de Azure Cache for Redis si se modifican las propiedades `Size`, `Sku`, o `ShardCount`. 

> [!NOTE]
> El escalado de una caché con PowerShell está sujeto a los mismos límites y directrices que el escalado de una caché desde el Portal de Azure. Puede escalar a un nivel de precios diferente con las siguientes restricciones.
> 
> * No se puede escalar desde un plan de tarifa superior a un plan de tarifa inferior.
> * No puede cambiar de una memoria caché **Premium** a una memoria caché **Estándar** o **Básica**.
> * No puede cambiar de una memoria caché **Estándar** a una memoria caché **Básica**.
> * Puede cambiar de una memoria caché **Básica** a una memoria caché **Estándar**, pero no puede cambiar el tamaño al mismo tiempo. Si necesita un tamaño distinto, puede realizar una operación de escalado posterior hasta el tamaño deseado.
> * No puede escalar de una memoria caché **Básica** directamente a una memoria caché **Premium**. Debe escalar desde **Básica** a **Estándar** en una operación de escalado y, después, desde **Estándar** a **Premium** en una operación de escalado posterior.
> * No puede escalar desde un tamaño mayor hasta el tamaño **C0 (250 MB)** .
> 
> Para más información, vea [How to Scale Azure Cache for Redis](cache-how-to-scale.md) (Cómo escalar una instancia de Azure Cache for Redis).
> 
> 

En el ejemplo siguiente se muestra cómo escalar una memoria caché denominada `myCache` a una caché de 2,5 GB. Tenga en cuenta que este comando funciona con una memoria caché básica o una estándar.

    Set-AzureRmRedisCache -ResourceGroupName myGroup -Name myCache -Size 2.5GB

Después de emitir este comando, el estado de la memoria caché se devuelve (de forma similar a una llamada a `Get-AzureRmRedisCache`). Tenga en cuenta que `ProvisioningState` es `Scaling`.

    PS C:\> Set-AzureRmRedisCache -Name myCache -ResourceGroupName myGroup -Size 2.5GB


    Name               : mycache
    Id                 : /subscriptions/12ad12bd-abdc-2231-a2ed-a2b8b246bbad4/resourceGroups/mygroup/providers/Mi
                         crosoft.Cache/Redis/mycache
    Location           : South Central US
    Type               : Microsoft.Cache/Redis
    HostName           : mycache.redis.cache.windows.net
    Port               : 6379
    ProvisioningState  : Scaling
    SslPort            : 6380
    RedisConfiguration : {[maxmemory-policy, volatile-lru], [maxmemory-reserved, 150], [notify-keyspace-events, KEA],
                         [maxmemory-delta, 150]...}
    EnableNonSslPort   : False
    RedisVersion       : 3.0
    Size               : 1GB
    Sku                : Standard
    ResourceGroupName  : mygroup
    PrimaryKey         : ....
    SecondaryKey       : ....
    VirtualNetwork     :
    Subnet             :
    StaticIP           :
    TenantSettings     : {}
    ShardCount         :

Cuando se complete la operación de escalado, `ProvisioningState` cambia a `Succeeded`. Si necesita realizar una operación de escalado posterior, como cambiar de básica a estándar y, a continuación, cambiar el tamaño, debe esperar hasta que se complete la operación anterior o recibirá un error similar al siguiente.

    Set-AzureRmRedisCache : Conflict: The resource '...' is not in a stable state, and is currently unable to accept the update request.

## <a name="to-get-information-about-an-azure-cache-for-redis"></a>Para obtener información acerca de Azure Cache for Redis
Puede recuperar información sobre una caché con el cmdlet [Get-AzureRmRedisCache](https://docs.microsoft.com/powershell/module/azurerm.rediscache/get-azurermrediscache?view=azurermps-6.6.0) .

Para ver una lista de parámetros disponibles y sus descripciones para `Get-AzureRmRedisCache`, ejecute el siguiente comando.

    PS C:\> Get-Help Get-AzureRmRedisCache -detailed

    NAME
        Get-AzureRmRedisCache

    SYNOPSIS
        Gets details about a single cache or all caches in the specified resource group or all caches in the current
        subscription.

    SYNTAX
        Get-AzureRmRedisCache [-Name <String>] [-ResourceGroupName <String>] [<CommonParameters>]

    DESCRIPTION
        The Get-AzureRmRedisCache cmdlet gets the details about a cache or caches depending on input parameters. If both
        ResourceGroupName and Name parameters are provided then Get-AzureRmRedisCache will return details about the
        specific cache name provided.

        If only ResourceGroupName is provided than it will return details about all caches in the specified resource group.

        If no parameters are given than it will return details about all caches the current subscription.

    PARAMETERS
        -Name <String>
            The name of the cache. When this parameter is provided along with ResourceGroupName, Get-AzureRmRedisCache
            returns the details for the cache.

        -ResourceGroupName <String>
            The name of the resource group that contains the cache or caches. If ResourceGroupName is provided with Name
            then Get-AzureRmRedisCache returns the details of the cache specified by Name. If only the ResourceGroup
            parameter is provided, then details for all caches in the resource group are returned.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

Para devolver información sobre todas las cachés de la suscripción actual, ejecute `Get-AzureRmRedisCache` sin ningún parámetro.

    Get-AzureRmRedisCache

Para devolver información sobre todas las cachés de un grupo de recursos específico, ejecute `Get-AzureRmRedisCache` con el parámetro `ResourceGroupName`.

    Get-AzureRmRedisCache -ResourceGroupName myGroup

Para devolver información sobre una caché específica, ejecute `Get-AzureRmRedisCache` con el parámetro `Name` que contiene el nombre de la caché y el parámetro `ResourceGroupName` con el grupo de recursos que contiene esa caché.

    PS C:\> Get-AzureRmRedisCache -Name myCache -ResourceGroupName myGroup

    Name               : mycache
    Id                 : /subscriptions/12ad12bd-abdc-2231-a2ed-a2b8b246bbad4/resourceGroups/myGroup/providers/Mi
                         crosoft.Cache/Redis/mycache
    Location           : South Central US
    Type               : Microsoft.Cache/Redis
    HostName           : mycache.redis.cache.windows.net
    Port               : 6379
    ProvisioningState  : Succeeded
    SslPort            : 6380
    RedisConfiguration : {[maxmemory-policy, volatile-lru], [maxmemory-reserved, 62], [notify-keyspace-events, KEA],
                         [maxclients, 1000]...}
    EnableNonSslPort   : False
    RedisVersion       : 3.0
    Size               : 1GB
    Sku                : Standard
    ResourceGroupName  : myGroup
    VirtualNetwork     :
    Subnet             :
    StaticIP           :
    TenantSettings     : {}
    ShardCount         :

## <a name="to-retrieve-the-access-keys-for-an-azure-cache-for-redis"></a>Recuperación de las teclas de acceso de una instancia de Azure Cache for Redis.
Para recuperar las claves de acceso de la caché, puede usar el cmdlet [Get-AzureRmRedisCacheKey](https://docs.microsoft.com/powershell/module/azurerm.rediscache/Get-AzureRmRedisCacheKey?view=azurermps-6.6.0) .

Para ver una lista de parámetros disponibles y sus descripciones para `Get-AzureRmRedisCacheKey`, ejecute el siguiente comando.

    PS C:\> Get-Help Get-AzureRmRedisCacheKey -detailed

    NAME
        Get-AzureRmRedisCacheKey

    SYNOPSIS
        Gets the accesskeys for the specified Azure Cache for Redis.


    SYNTAX
        Get-AzureRmRedisCacheKey -Name <String> -ResourceGroupName <String> [<CommonParameters>]

    DESCRIPTION
        The Get-AzureRmRedisCacheKey cmdlet gets the access keys for the specified cache.

    PARAMETERS
        -Name <String>
            Name of the Azure Cache for Redis.

        -ResourceGroupName <String>
            Name of the resource group for the cache.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

Para recuperar las claves de la caché, llame al cmdlet `Get-AzureRmRedisCacheKey` y pase el nombre de la memoria caché y el nombre del grupo de recursos que contiene la memoria caché.

    PS C:\> Get-AzureRmRedisCacheKey -Name myCache -ResourceGroupName myGroup

    PrimaryKey   : b2wdt43sfetlju4hfbryfnregrd9wgIcc6IA3zAO1lY=
    SecondaryKey : ABhfB757JgjIgt785JgKH9865eifmekfnn649303JKL=

## <a name="to-regenerate-access-keys-for-your-azure-cache-for-redis"></a>Generación de las teclas de acceso para la instancia de Azure Cache for Redis.
Para regenerar las claves de acceso de la caché, puede usar el cmdlet [New-AzureRmRedisCacheKey](https://docs.microsoft.com/powershell/module/azurerm.rediscache/New-AzureRmRedisCacheKey?view=azurermps-6.6.0) .

Para ver una lista de parámetros disponibles y sus descripciones para `New-AzureRmRedisCacheKey`, ejecute el siguiente comando.

    PS C:\> Get-Help New-AzureRmRedisCacheKey -detailed

    NAME
        New-AzureRmRedisCacheKey

    SYNOPSIS
        Regenerates the access key of an Azure Cache for Redis.

    SYNTAX
        New-AzureRmRedisCacheKey -Name <String> -ResourceGroupName <String> -KeyType <String> [-Force] [<CommonParameters>]

    DESCRIPTION
        The New-AzureRmRedisCacheKey cmdlet regenerate the access key of an Azure Cache for Redis.

    PARAMETERS
        -Name <String>
            Name of the Azure Cache for Redis.

        -ResourceGroupName <String>
            Name of the resource group for the cache.

        -KeyType <String>
            Specifies whether to regenerate the primary or secondary access key. Possible values are Primary or Secondary.

        -Force
            When the Force parameter is provided, the specified access key is regenerated without any confirmation prompts.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

Para regenerar la clave principal o secundaria de la caché, llame al cmdlet `New-AzureRmRedisCacheKey` y pase el nombre, el grupo de recursos y especifique `Primary` o `Secondary` para el parámetro `KeyType`. En el ejemplo siguiente, se regenera la clave de acceso secundaria de una memoria caché.

    PS C:\> New-AzureRmRedisCacheKey -Name myCache -ResourceGroupName myGroup -KeyType Secondary

    Confirm
    Are you sure you want to regenerate Secondary key for Azure Cache for Redis 'myCache'?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y


    PrimaryKey   : b2wdt43sfetlju4hfbryfnregrd9wgIcc6IA3zAO1lY=
    SecondaryKey : c53hj3kh4jhHjPJk8l0jji785JgKH9865eifmekfnn6=

## <a name="to-delete-an-azure-cache-for-redis"></a>Eliminación de una instancia de Azure Redis Cache
Para eliminar una instancia de Azure Cache for Redis, use el cmdlet [Remove-AzureRmRedisCache](https://docs.microsoft.com/powershell/module/azurerm.rediscache/remove-azurermrediscache?view=azurermps-6.6.0).

Para ver una lista de parámetros disponibles y sus descripciones para `Remove-AzureRmRedisCache`, ejecute el siguiente comando.

    PS C:\> Get-Help Remove-AzureRmRedisCache -detailed

    NAME
        Remove-AzureRmRedisCache

    SYNOPSIS
        Remove Azure Cache for Redis if exists.

    SYNTAX
        Remove-AzureRmRedisCache -Name <String> -ResourceGroupName <String> [-Force] [-PassThru] [<CommonParameters>

    DESCRIPTION
        The Remove-AzureRmRedisCache cmdlet removes an Azure Cache for Redis if it exists.

    PARAMETERS
        -Name <String>
            Name of the Azure Cache for Redis to remove.

        -ResourceGroupName <String>
            Name of the resource group of the cache to remove.

        -Force
            When the Force parameter is provided, the cache is removed without any confirmation prompts.

        -PassThru
            By default Remove-AzureRmRedisCache removes the cache and does not return any value. If the PassThru par
            is provided then Remove-AzureRmRedisCache returns a boolean value indicating the success of the operatio

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

En el ejemplo siguiente, se quita la memoria caché denominada `myCache` .

    PS C:\> Remove-AzureRmRedisCache -Name myCache -ResourceGroupName myGroup

    Confirm
    Are you sure you want to remove Azure Cache for Redis 'myCache'?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y


## <a name="to-import-an-azure-cache-for-redis"></a>Importación de una instancia de Azure Cache for Redis
Puede importar datos a una instancia de Azure Cache for Redis mediante el cmdlet `Import-AzureRmRedisCache`.

> [!IMPORTANT]
> Import/Export solo está disponible para las memorias caché de [nivel premium](cache-premium-tier-intro.md) . Para más información e instrucciones sobre Import/Export, consulte [Importación y exportación de datos en Azure Cache for Redis](cache-how-to-import-export-data.md).
> 
> 

Para ver una lista de parámetros disponibles y sus descripciones para `Import-AzureRmRedisCache`, ejecute el siguiente comando.

    PS C:\> Get-Help Import-AzureRmRedisCache -detailed

    NAME
        Import-AzureRmRedisCache

    SYNOPSIS
        Import data from blobs to Azure Cache for Redis.


    SYNTAX
        Import-AzureRmRedisCache -Name <String> -ResourceGroupName <String> -Files <String[]> [-Format <String>] [-Force]
        [-PassThru] [<CommonParameters>]


    DESCRIPTION
        The Import-AzureRmRedisCache cmdlet imports data from the specified blobs into Azure Cache for Redis.


    PARAMETERS
        -Name <String>
            The name of the cache.

        -ResourceGroupName <String>
            The name of the resource group that contains the cache.

        -Files <String[]>
            SAS urls of blobs whose content should be imported into the cache.

        -Format <String>
            Format for the blob.  Currently "rdb" is the only supported, with other formats expected in the future.

        -Force
            When the Force parameter is provided, import will be performed without any confirmation prompts.

        -PassThru
            By default Import-AzureRmRedisCache imports data in cache and does not return any value. If the PassThru
            parameter is provided then Import-AzureRmRedisCache returns a boolean value indicating the success of the
            operation.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).


El siguiente comando importa datos desde el blob especificado por el identificador URI de SAS de Azure Cache for Redis.

    PS C:\>Import-AzureRmRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -Files @("https://mystorageaccount.blob.core.windows.net/mycontainername/blobname?sv=2015-04-05&sr=b&sig=caIwutG2uDa0NZ8mjdNJdgOY8%2F8mhwRuGNdICU%2B0pI4%3D&st=2016-05-27T00%3A00%3A00Z&se=2016-05-28T00%3A00%3A00Z&sp=rwd") -Force

## <a name="to-export-an-azure-cache-for-redis"></a>Exportación de una instancia de Azure Cache for Redis
Puede exportar datos a una instancia de Azure Cache for Redis mediante el cmdlet `Export-AzureRmRedisCache`.

> [!IMPORTANT]
> Import/Export solo está disponible para las memorias caché de [nivel premium](cache-premium-tier-intro.md) . Para más información e instrucciones sobre Import/Export, consulte [Importación y exportación de datos en Azure Cache for Redis](cache-how-to-import-export-data.md).
> 
> 

Para ver una lista de parámetros disponibles y sus descripciones para `Export-AzureRmRedisCache`, ejecute el siguiente comando.

    PS C:\> Get-Help Export-AzureRmRedisCache -detailed

    NAME
        Export-AzureRmRedisCache

    SYNOPSIS
        Exports data from Azure Cache for Redis to a specified container.


    SYNTAX
        Export-AzureRmRedisCache -Name <String> -ResourceGroupName <String> -Prefix <String> -Container <String> [-Format
        <String>] [-PassThru] [<CommonParameters>]


    DESCRIPTION
        The Export-AzureRmRedisCache cmdlet exports data from Azure Cache for Redis to a specified container.


    PARAMETERS
        -Name <String>
            The name of the cache.

        -ResourceGroupName <String>
            The name of the resource group that contains the cache.

        -Prefix <String>
            Prefix to use for blob names.

        -Container <String>
            SAS url of container where data should be exported.

        -Format <String>
            Format for the blob.  Currently "rdb" is the only supported, with other formats expected in the future.

        -PassThru
            By default Export-AzureRmRedisCache does not return any value. If the PassThru parameter is provided
            then Export-AzureRmRedisCache returns a boolean value indicating the success of the operation.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).


El siguiente comando exporta los datos desde una instancia de Azure Cache for Redis en el contenedor especificado por el identificador URI de SAS.

        PS C:\>Export-AzureRmRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -Prefix "blobprefix"
        -Container "https://mystorageaccount.blob.core.windows.net/mycontainer?sv=2015-04-05&sr=c&sig=HezZtBZ3DURmEGDduauE7
        pvETY4kqlPI8JCNa8ATmaw%3D&st=2016-05-27T00%3A00%3A00Z&se=2016-05-28T00%3A00%3A00Z&sp=rwdl"

## <a name="to-reboot-an-azure-cache-for-redis"></a>Reinicio de una instancia de Azure Cache for Redis
Puede reiniciar la instancia de Azure Cache for Redis mediante el cmdlet `Reset-AzureRmRedisCache`.

> [!IMPORTANT]
> El reinicio solo está disponible para las memorias caché de [nivel premium](cache-premium-tier-intro.md) . Para más información acerca de cómo reiniciar la caché, consulte [Administración de caché: reinicio](cache-administration.md#reboot).
> 
> 

Para ver una lista de parámetros disponibles y sus descripciones para `Reset-AzureRmRedisCache`, ejecute el siguiente comando.

    PS C:\> Get-Help Reset-AzureRmRedisCache -detailed

    NAME
        Reset-AzureRmRedisCache

    SYNOPSIS
        Reboot specified node(s) of an Azure Cache for Redis instance.


    SYNTAX
        Reset-AzureRmRedisCache -Name <String> -ResourceGroupName <String> -RebootType <String> [-ShardId <Integer>]
        [-Force] [-PassThru] [<CommonParameters>]


    DESCRIPTION
        The Reset-AzureRmRedisCache cmdlet reboots the specified node(s) of an Azure Cache for Redis instance.


    PARAMETERS
        -Name <String>
            The name of the cache.

        -ResourceGroupName <String>
            The name of the resource group that contains the cache.

        -RebootType <String>
            Which node to reboot. Possible values are "PrimaryNode", "SecondaryNode", "AllNodes".

        -ShardId <Integer>
            Which shard to reboot when rebooting a premium cache with clustering enabled.

        -Force
            When the Force parameter is provided, reset will be performed without any confirmation prompts.

        -PassThru
            By default Reset-AzureRmRedisCache does not return any value. If the PassThru parameter is provided
            then Reset-AzureRmRedisCache returns a boolean value indicating the success of the operation.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).


El siguiente comando reinicia ambos nodos de la memoria caché especificada.

        PS C:\>Reset-AzureRmRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -RebootType "AllNodes"
        -Force


## <a name="next-steps"></a>Pasos siguientes
Para obtener más información acerca de Windows PowerShell con Azure, consulte los siguientes recursos:

* [Documentación de cmdlet de Azure Cache for Redis en MSDN](https://docs.microsoft.com/powershell/module/azurerm.rediscache/?view=azurermps-6.6.0)
* [Cmdlets de Azure Resource Manager](https://go.microsoft.com/fwlink/?LinkID=394765): obtenga información acerca del uso de los cmdlets en el módulo de Azure Resource Manager.
* [Uso de grupos de recursos para administrar los recursos de Azure](../azure-resource-manager/resource-group-template-deploy-portal.md): Aprenda a crear y administrar grupos de recursos en Azure Portal.
* [Blog sobre Azure](https://azure.microsoft.com/blog/): obtenga información acerca de las nuevas características de Azure.
* [Blog de Windows PowerShell](https://blogs.msdn.com/powershell): obtenga información acerca de las nuevas características de Windows PowerShell.
* [Blog "¡Hola, chicos del scripting!"](http://blogs.technet.com/b/heyscriptingguy/): obtenga sugerencias y trucos del mundo real de la comunidad de Windows PowerShell.

