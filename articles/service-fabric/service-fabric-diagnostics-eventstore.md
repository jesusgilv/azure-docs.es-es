---
title: Almacén de eventos de Azure Service Fabric | Microsoft Docs
description: Información acerca de EventStore de Azure Service Fabric
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: cd58e24a51b153d6bf217f7d32a82e882183ed73
ms.sourcegitcommit: beb4fa5b36e1529408829603f3844e433bea46fe
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/22/2018
ms.locfileid: "52290678"
---
# <a name="eventstore-service-overview"></a>Información general del servicio EventStore

## <a name="overview"></a>Información general

El servicio EventStore, introducido en la versión 6.2, es una opción de supervisión de Service Fabric, que proporciona una manera de reconocer el estado del clúster o las cargas de trabajo en un momento dado en el tiempo. El servicio EventStore expone eventos de Service Fabric a través de un conjunto de API (accesibles a través de puntos de conexión de REST o de la biblioteca cliente). Estas API de EventStore le permiten consultar el clúster directamente para obtener datos de diagnóstico sobre cualquier entidad del clúster y debe usarse como ayuda para:

* Diagnosticar problemas en el desarrollo, prueba o allí donde use una canalización de supervisión
* Confirmar que el clúster procesa correctamente las acciones de administración que realiza en el clúster

Para ver una lista completa de los eventos disponibles en EventStore, consulte [Eventos de Service Fabric](service-fabric-diagnostics-event-generation-operational.md).

>[!NOTE]
>A partir de Service Fabric versión 6.2., las API de EventStore se encuentran en versión preliminar solo para los clústeres de Windows que se ejecutan en Azure. Se está trabajando para portar esta funcionalidad a Linux, así como nuestros clústeres independientes.

Se puede consultar el servicio EventStore sobre los eventos que están disponibles para cada entidad y tipo de entidad del clúster. Esto significa que puede consultar los eventos en los siguientes niveles:
* Clúster: eventos de todos los niveles de clúster
* Nodos: eventos de todos los niveles de nodo
* Nodo: eventos específicos de un nodo, en función de `nodeName`
* Aplicaciones: eventos de todos los niveles de aplicación
* Aplicación: eventos específicos de una aplicación
* Servicios: eventos de todos los servicios de los clústeres
* Servicio: eventos de un servicio específico
* Particiones: eventos de todas las particiones
* Partición: eventos de una partición específica
* Réplicas: eventos de todas las réplicas o instancias
* Réplica: eventos de una réplica o instancia específica


El servicio EventStore también tiene la capacidad de correlacionar los eventos del clúster. El examen de los eventos que se escribieron al mismo tiempo desde distintas entidades y que pueden haberse afectado entre sí permite al servicio EventStore vincular estos eventos para identificar las causas de las actividades del clúster. Por ejemplo, si una de las aplicaciones pasa a un estado incorrecto sin realizar ningún cambio inducido, EventStore también examinará otros eventos expuestos por la plataforma y podría correlacionarlos con un evento `NodeDown`. Esto ayuda a detectar los errores y analizar las causas raíz más rápidamente.

Se recomienda usar EventStore para el análisis rápido y la obtención de una instantánea de cómo funciona el clúster y de si todo ocurre tal como se espera.

Para empezar a usar el servicio EventStore, vea [Query EventStore APIs for cluster events](service-fabric-diagnostics-eventstore-query.md) (Consultar los eventos de clúster en las API de EventStore).

## <a name="next-steps"></a>Pasos siguientes
* Información general de supervisión y diagnóstico de Service Fabric: [Supervisión y diagnóstico para Azure Service Fabric](service-fabric-diagnostics-overview.md)
* Más información sobre cómo supervisar el clúster: [Supervisión del clúster y la plataforma](service-fabric-diagnostics-event-generation-infra.md).