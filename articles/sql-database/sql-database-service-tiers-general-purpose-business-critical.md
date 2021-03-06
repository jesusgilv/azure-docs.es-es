---
title: 'Azure SQL Database: uso general y crítico para la empresa | Microsoft Docs'
description: En el artículo se describen los niveles de servicio de uso general y críticos para la empresa en el modelo de compra de núcleos virtuales.
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: sashan, moslake
manager: craigg
ms.date: 10/15/2018
ms.openlocfilehash: 15fd86a88c3025f81741d614b03d5c4c7c60262c
ms.sourcegitcommit: 8e06d67ea248340a83341f920881092fd2a4163c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/16/2018
ms.locfileid: "49351749"
---
# <a name="general-purpose-and-business-critical-service-tiers"></a>Niveles de servicio de uso general y críticos para la empresa.

En este artículo se describen consideraciones de almacenamiento y copia de seguridad para los niveles de servicio de uso general y crítico para la empresa en el modelo de compra basado en núcleos virtuales.

> [!NOTE]
> Para obtener información sobre el nivel de servicio de Hiperescalado en el modelo de compra basado en núcleos virtuales, consulte [Hyperscale service tier](sql-database-service-tier-hyperscale.md) (Nivel de servicio de Hiperescalado). Para ver una comparación entre el modelo de compra basado en núcleos virtuales y el modelo de compra basado en DTU, consulte [Modelos de compra y recursos de Azure SQL Database](sql-database-service-tiers.md).

## <a name="data-and-log-storage"></a>Almacenamiento de datos y de registro

Tenga en cuenta lo siguiente.

- El almacenamiento asignado lo usan los archivos de datos (MDF) y los archivos de registros (LDF).
- Cada tamaño del proceso de una base de datos única admite un tamaño máximo de base de datos, con un tamaño máximo predeterminado de 32 GB.
- Cuando se configura el tamaño de base de datos única requerido (tamaño de MDF), el 30 % de almacenamiento adicional se agrega automáticamente para admitir LDF
- El tamaño de almacenamiento en la instancia administrada debe especificarse en múltiplos de 32 GB.
- Puede seleccionar cualquier tamaño de base de datos única entre 10 GB y el máximo admitido.
  - Para Standard Storage, aumente o disminuya el tamaño en incrementos de 10 GB.
  - Para Premium Storage, aumente o disminuya el tamaño en incrementos de 250 GB.
- En el nivel de servicio De uso general, `tempdb` usa una SSD asociada y este costo de almacenamiento se incluye en el precio del núcleo virtual.
- En el nivel de servicio Crítico para la empresa, `tempdb` comparte la SSD asociada con los archivos MDF y LDF y el costo de almacenamiento de tempDB se incluye en el precio del núcleo virtual.

> [!IMPORTANT]
> Se le cobra por el almacenamiento total asignado de MDF y LDF.

Para supervisar el tamaño total actual de MDF y LDF, use [sp_spaceused](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-spaceused-transact-sql). Para supervisar el tamaño actual de los archivos MDF y LDF individuales, use [sys.database_files](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql).

> [!IMPORTANT]
> En algunas circunstancias, puede que deba reducir una base de datos para reclamar el espacio no utilizado. Para más información, consulte [Administración del espacio de archivo en Azure SQL Database](sql-database-file-space-management.md).

## <a name="backups-and-storage"></a>Copias de seguridad y almacenamiento

Para admitir las funcionalidades Restauración a un momento dado (PITR) y [Retención a largo plazo (LTR)](sql-database-long-term-retention.md) de SQL Database, se asigna almacenamiento a las copias de seguridad de base de datos. Este almacenamiento se asigna por separado para cada base de datos y se factura como dos cargos independientes por base de datos.

- **PITR**: las copias de seguridad de base de datos individuales se copian en el [almacenamiento de RA-GRS](../storage/common/storage-designing-ha-apps-with-ragrs.md) automáticamente. El tamaño de almacenamiento aumenta dinámicamente a medida que se crean las nuevas copias de seguridad.  El almacenamiento se usa para realizar cada cinco minutos copias de seguridad completas semanales, copias de seguridad diferenciales diarias y copias de seguridad de registros de transacciones. El consumo de almacenamiento depende de la tasa de cambio de la base de datos y del período de retención. Puede configurar un período de retención diferente para cada base de datos de entre 7 y 35 días. Se ofrece una cantidad de almacenamiento mínimo igual al tamaño de los datos sin costo adicional. En la mayoría de las bases de datos, esta cantidad es suficiente para almacenar copias de seguridad durante 7 días.
- **LTR**: SQL Database ofrece la opción de configurar la retención a largo plazo de copias de seguridad completas durante un período máximo de 10 años. Si la directiva de LTR está habilitada, estas copias de seguridad se almacenan en almacenamiento RA-GRS automáticamente, pero puede controlar la frecuencia con que se realizan las copias de seguridad. Para satisfacer los distintos requisitos de cumplimiento, puede seleccionar distintos períodos de retención para copias de seguridad semanales, mensuales o anuales. Esta configuración definirá la cantidad de almacenamiento que se usará para las copias de seguridad de LTR. Para estimar el costo del almacenamiento de LTR, se puede usar la calculadora de precios de LTR. Para más información, consulte [retención a largo plazo](sql-database-long-term-retention.md).

## <a name="next-steps"></a>Pasos siguientes

- Para obtener más información sobre las opciones de tamaños de proceso y de almacenamiento específicas que hay disponibles para las bases de datos únicas en los niveles de servicio de uso general y crítico para la empresa, consulte [Límites del modelo de compra basado en núcleos virtuales de Azure SQL Database para una base de datos única](sql-database-vcore-resource-limits-single-databases.md#general-purpose-service-tier-storage-sizes-and-compute-sizes).
- Para obtener más información sobre las opciones de tamaños de proceso y de almacenamiento específicas que hay disponibles para los grupos elásticos en los niveles de servicio de uso general y crítico para la empresa, consulte [Límites del modelo de compra basado en núcleos virtuales de Azure SQL Database para grupos elásticos](sql-database-vcore-resource-limits-elastic-pools.md#general-purpose-service-tier-storage-sizes-and-compute-sizes).
