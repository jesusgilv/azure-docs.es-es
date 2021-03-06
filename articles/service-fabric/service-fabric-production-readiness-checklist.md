---
title: Lista de comprobación sobre la preparación de producción de Azure Service Fabric | Microsoft Docs
description: Prepare la producción del clúster y de la aplicación de Service Fabric mediante los procedimientos recomendados.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 7/10/2018
ms.author: aljo-microsoft
ms.openlocfilehash: 4e6d5cb3191be7188c1a7c4753200cf049800f04
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/15/2018
ms.locfileid: "53436014"
---
# <a name="production-readiness-checklist"></a>Lista de comprobación sobre la preparación de producción

¿La aplicación y el clúster están preparados para asumir el tráfico de producción? La ejecución y las pruebas de la aplicación y del clúster no significan necesariamente que todo esté listo para pasar a producción. Revise la siguiente lista de comprobación para que la aplicación y el clúster se ejecuten sin problemas. Se recomienda encarecidamente comprobar todos estos elementos. Obviamente, puede optar por usar soluciones alternativas para un elemento de línea específico (por ejemplo, sus propios marcos de diagnóstico).


## <a name="pre-requisites-for-production"></a>Requisitos previos para la producción
1. Los [procedimientos recomendados de Azure Service Fabric](https://docs.microsoft.com/azure/security/azure-service-fabric-security-best-practices) son: 
* Uso de certificados X.509
* Configuración de directivas de seguridad
* Configuración de SSL para Azure Service Fabric
* Uso de aislamiento y seguridad de red con Azure Service Fabric
* Configuración de Azure Key Vault para la seguridad
* Asignación de usuarios a roles
* Implementación de la configuración de seguridad de Reliable Actors si se usa el modelo de programación Actores
2. Para los clústeres con más de 20 núcleos o 10 nodos, cree un tipo de nodo principal dedicado para los servicios del sistema. Agregue [restricciones de colocación](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md) para reservar el tipo de nodo principal para los servicios del sistema. 
3. Use una SKU D2v2 o una versión superior para el tipo de nodo principal. Se recomienda seleccionar una SKU con 50 GB de capacidad de disco duro como mínimo.
4. Los clústeres de producción deben ser [seguros](service-fabric-cluster-security.md). Para obtener un ejemplo de cómo configurar un clúster seguro, consulte esta [plantilla del clúster](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/7-VM-Windows-3-NodeTypes-Secure-NSG). Use nombres comunes para los certificados y evite el uso de certificados autofirmados.
5. Agregue [restricciones de recursos en contenedores y servicios](service-fabric-resource-governance.md), de modo que no consuman más del 75 % de los recursos del nodo. 
6. Conozca y establezca el [nivel de durabilidad](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster). Se recomienda el nivel de durabilidad Silver o superior para los tipos de nodos que ejecutan cargas de trabajo con estado. El tipo de nodo principal debe tener un nivel de durabilidad establecido en Silver o en un valor superior.
7. Comprenda y seleccione el [nivel de confiabilidad](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster) del tipo de nodo. Se recomienda el nivel de confiabilidad Silver o superior.
8. Cargue las cargas de trabajo y pruebe de escalarlas para identificar [los requisitos de capacidad](service-fabric-cluster-capacity.md) para el clúster. 
9. Mediante las alertas, se supervisan los servicios y las aplicaciones y se generan y almacenan registros de aplicaciones. Por ejemplo, consulte [Adición del registro a la aplicación de Service Fabric](service-fabric-how-to-diagnostics-log.md) y [Supervisión de contenedores con Log Analytics](service-fabric-diagnostics-oms-containers.md).
10. El clúster se supervisa mediante las alertas (por ejemplo, con [Log Analytics](service-fabric-diagnostics-event-analysis-oms.md)). 
11. La infraestructura subyacente del conjunto de escalado de máquinas virtuales se supervisa mediante las alertas (por ejemplo, con [Log Analytics](service-fabric-diagnostics-oms-agent.md).
12. El clúster siempre tiene [certificados principales y secundarios](service-fabric-cluster-security-update-certs-azure.md) (para que no experimente bloqueos).
13. Mantenga clústeres independientes para el desarrollo, el ensayo y la producción. 
14. Las [actualizaciones de la aplicación](service-fabric-application-upgrade.md) y las [actualizaciones del clúster](service-fabric-tutorial-upgrade-cluster.md) primero se prueban en clústeres de desarrollo y ensayo. 
15. Desactive las actualizaciones automáticas en los clústeres de producción y actívelas para los clústeres de desarrollo y ensayo (reviértalo según sea necesario). 
16. Establezca un objetivo de punto de recuperación (RPO) para el servicio y configure un [proceso de recuperación ante desastres](service-fabric-disaster-recovery.md) y pruébelo.
17. Planee el [escalado](service-fabric-cluster-scaling.md) del clúster manualmente o mediante programación.
18. Planee la [revisión](service-fabric-patch-orchestration-application.md) de los nodos de clúster. 
19. Establezca una canalización de CI/CD para que los cambios más recientes se prueben continuamente. Por ejemplo, con [Azure DevOps](service-fabric-tutorial-deploy-app-with-cicd-vsts.md) o [Jenkins](service-fabric-cicd-your-linux-applications-with-jenkins.md)
20. Pruebe los clústeres de desarrollo y ensayo con carga con el [servicio de análisis de errores](service-fabric-testability-overview.md) e inducza [caos](service-fabric-controlled-chaos.md) controlado. 
21. Planee el [escalado](service-fabric-concepts-scalability.md) de las aplicaciones. 


Si usa el modelo de programación de Reliable Services o Reliable Actors de Service Fabric, deben comprobarse los elementos siguientes:
22. Actualice las aplicaciones durante el desarrollo local para comprobar que el código de servicio respeta el token de cancelación en el método `RunAsync` y cierra los agentes de escucha de comunicación personalizada.
23. Evite [errores comunes](service-fabric-work-with-reliable-collections.md) al utilizar Reliable Collections.
24. Supervise los contadores de rendimiento de memoria de CLR de .NET al ejecutar pruebas de carga y busque tasas altas de crecimiento descontrolado en el montón o de recolección de elementos no utilizados.
25. Mantenga la copia de seguridad sin conexión de [Reliable Services y Reliable Actors](service-fabric-reliable-services-backup-restore.md) y pruebe el proceso de restauración. 


## <a name="optional-best-practices"></a>Procedimientos recomendados opcionales

Aunque las listas anteriores son requisitos previos para pasar a la producción, también deben considerarse los siguientes elementos:
26. Conectar el [modelo de estado de Service Fabric](service-fabric-health-introduction.md) para ampliar los informes y la evaluación de estado integrada.
27. Implementar un guardián personalizado que supervise la [carga](service-fabric-cluster-resource-manager-metrics.md) de la aplicación y de los informes para el [equilibrio de recursos](service-fabric-cluster-resource-manager-balancing.md). 


## <a name="next-steps"></a>Pasos siguientes
* [Implementación de un clúster Windows de Service Fabric](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
* [Implementación de un clúster de Service Fabric de Linux](service-fabric-tutorial-create-vnet-and-linux-cluster.md)
* Más información acerca del [ciclo de vida de aplicaciones](service-fabric-application-lifecycle.md) de Service Fabric.
