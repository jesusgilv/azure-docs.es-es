---
title: Publicación de la oferta de imagen de contenedor de Azure | Microsoft Docs
description: Cómo publicar una oferta de contenedor de Azure.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: pbutlerm
ms.openlocfilehash: 7533d1a133c9c474bc39f0f64c5f1a8183ab30f8
ms.sourcegitcommit: 1fc949dab883453ac960e02d882e613806fabe6f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2018
ms.locfileid: "50980341"
---
# <a name="publish-container-offer"></a>Publicación de oferta de contenedor

 Después de crear una oferta nueva mediante la página **New Offer** (Nueva oferta), puede publicar la oferta. Seleccione **Publicar** para iniciar el proceso de publicación.

El diagrama siguiente muestra los pasos principales del proceso de publicación de una oferta para "lanzarla".

![Pasos de publicación de la oferta de contenedor](./media/offer-publishing-steps.png)

## <a name="detailed-description-of-publishing-steps"></a>Descripción detallada de los pasos de publicación

En la tabla siguiente se describe cada uno de los pasos de publicación: También se proporciona un tiempo estimado para finalizar cada paso.


|  **Paso de publicación**           | **Hora**    | **Descripción**                                                            |
|  -------------------           | --------    | ---------------                                                            |
| Validar requisitos previos         | 15 minutos   | Se validan la información y la configuración de la oferta.                        |
| Certificación                  | 1 semana | El equipo de certificación de Azure analiza la oferta. Se examina la oferta en busca de virus, malware, cumplimiento de seguridad y problemas de seguridad. La oferta se comprueba para ver si cumple todos los criterios de elegibilidad. Para más información, consulte las secciones de [requisitos previos](./cpp-prerequisites.md) y [preparación de los recursos técnicos](./cpp-create-technical-assets.md). Si se detecta un problema, se proporcionan comentarios. |
| Packaging | 1 hora  | Los recursos técnicos de la oferta se empaquetan para uso del cliente y se configuran los sistemas del cliente potencial. |
|  Aprobación del publicador             |  -        | Revisión final del publicador y confirmación antes del lanzamiento de la oferta. Puede implementar la oferta en las suscripciones seleccionadas (en los pasos de información de la oferta) para comprobar que cumple todos los requisitos.  Seleccione **Transmitir** para que la oferta pueda pasar al paso siguiente. |
| Packaging                 | 1 hora | La oferta terminada se replica en las regiones y los sistemas de producción de Marketplace. | 
| En vivo                           | 4 días |La oferta se lanza, se replica en las regiones necesarias y se pone a disposición del público. |

Deje pasar hasta diez días laborables para que finalice el proceso de publicación y la oferta se publique. Cuando termina el proceso de publicación, la oferta de contenedor se mostrará en [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules).

## <a name="next-steps"></a>Pasos siguientes

[Actualización de una oferta de contenedor existente en Azure Marketplace](./cpp-update-existing-offer.md)
