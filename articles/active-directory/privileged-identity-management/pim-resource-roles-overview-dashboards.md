---
title: 'Uso de un panel de recursos para realizar una revisión de acceso: Azure | Microsoft Docs'
description: Describe cómo usar un panel de recursos para realizar una revisión de acceso en Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: pim
ms.date: 03/30/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 20172cf7413397aedc4b3c32d0f1419531a2588a
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/29/2018
ms.locfileid: "43188504"
---
# <a name="use-a-resource-dashboard-to-perform-an-access-review"></a>Uso de un panel de control de recursos para realizar una revisión de acceso

Puede usar un panel de recursos para realizar una revisión de acceso en Privileged Identity Management (PIM) para Azure Resources El panel Vista de administrador consta de tres elementos principales:

- Una representación gráfica de las activaciones de rol de recursos.
- Dos gráficos que muestran la distribución de las asignaciones de roles por tipo de asignación.
- Un área de datos que pertenece a nuevas asignaciones de roles.

![Captura de pantalla del panel de la vista de administrador, que muestra gráficos y tablas](media/azure-pim-resource-rbac/rbac-overview-top.png)

![Captura de pantalla del panel de la vista de administrador, que muestra listas de datos](media/azure-pim-resource-rbac/role-settings.png)

La representación gráfica de las activaciones de rol de recursos de los últimos siete días. Estos datos se limitan al recurso seleccionado y muestran las activaciones de los roles más comunes (propietario, colaborador, administrador de acceso de usuario) y de la combinación de todos los roles.

A la derecha del grafo de activaciones, dos gráficos muestran la distribución de las asignaciones de roles por tipo de asignación, tanto de usuario como de grupo. Para cambiar el valor a un porcentaje (o viceversa), seleccione un segmento del gráfico.

Por debajo de los gráficos, verá el número de usuarios y grupos con nuevas asignaciones de roles de los últimos 30 días y una lista de los roles ordenada por asignaciones totales (en orden descendente).

## <a name="next-steps"></a>Pasos siguientes

- [Iniciar una revisión de acceso para los roles de recurso de Azure en PIM](pim-resource-roles-start-access-review.md) 
