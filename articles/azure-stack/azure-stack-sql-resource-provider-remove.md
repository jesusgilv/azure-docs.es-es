---
title: Eliminación del proveedor de recursos de SQL en Azure Stack | Microsoft Docs
description: Obtenga información sobre cómo quitar el proveedor de recursos de SQL de la implementación de Azure Stack.
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/20/2018
ms.author: jeffgilb
ms.reviewer: quying
ms.openlocfilehash: 8b6257519c3b1db2180335523047e8dc55558d57
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/06/2018
ms.locfileid: "52954600"
---
# <a name="remove-the-sql-resource-provider"></a>Eliminación del proveedor de recursos de SQL

Antes de quitar el proveedor de recursos de SQL, debe quitar todas las dependencias del proveedor. También necesitará una copia del paquete de implementación que se utilizó para instalar el proveedor de recursos.

> [!NOTE]
> Puede encontrar los vínculos de descarga para los instaladores del proveedor de recursos en [Requisitos previos de Implementación del proveedor de recursos](./azure-stack-sql-resource-provider-deploy.md#prerequisites).

Quitar el proveedor de recursos SQL no elimina las bases de datos de inquilino de los servidores de hospedaje.

## <a name="dependency-cleanup"></a>Limpieza de dependencias

Hay varias tareas de limpieza que debe realizar antes de ejecutar el script DeploySqlProvider.ps1 para quitar el proveedor de recursos.

El operador de Azure Stack es responsable de las siguientes tareas de limpieza:

* Elimine los planes que hacen referencia al adaptador de SQL.
* Elimine toda cuota asociada al adaptador de SQL.

## <a name="to-remove-the-sql-resource-provider"></a>Proceso para quitar el proveedor de recursos de SQL

1. Compruebe que ha quitado todas las dependencias del proveedor de recursos de SQL existentes.

   > [!NOTE]
   > La desinstalación del proveedor de recursos de SQL continuará incluso si los recursos dependientes utilizan el proveedor de recursos.
  
2. Obtenga una copia del paquete de instalación del proveedor de recursos de SQL y ejecute el extractor automático para extraer el contenido en un directorio temporal.

3. Abra una nueva ventana de consola de PowerShell con privilegios elevados y cambie al directorio en el que extrajo los archivos de instalación del proveedor de recursos de SQL.

4. Ejecute el script DeploySqlProvider.ps1 con los siguientes parámetros:

    * **Desinstalación**. Quita el proveedor de recursos y todos los recursos asociados.
    * **PrivilegedEndpoint**. Dirección IP o nombre DNS del punto de conexión con privilegios.
    * **AzureEnvironment**. El entorno de Azure usado para implementar Azure Stack. Requerido solo para implementaciones de Azure AD.
    * **CloudAdminCredential**. Credencial del administrador de la nube, necesaria para el acceso al punto de conexión con privilegios.
    * **AzCredential**. Credencial de la cuenta de administrador de servicio de Azure Stack. Use las mismas credenciales que para la implementación de Azure Stack.

## <a name="next-steps"></a>Pasos siguientes

[Oferta de App Services como PaaS](azure-stack-app-service-overview.md)
