---
title: Integración de soluciones de seguridad en Azure Security Center | Microsoft Docs
description: Aprenda cómo Azure Security Center se integra con los asociados para mejorar la seguridad general de los recursos de Azure.
services: security-center
documentationcenter: na
author: rkarlin
manager: mbaldwin
editor: ''
ms.assetid: 6af354da-f27a-467a-8b7e-6cbcf70fdbcb
ms.service: security-center
ms.topic: conceptual
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/26/2018
ms.author: rkarlin
ms.openlocfilehash: bbdda5012e6132940d00ae23a6d26469b0216fd0
ms.sourcegitcommit: 922f7a8b75e9e15a17e904cc941bdfb0f32dc153
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/27/2018
ms.locfileid: "52335451"
---
# <a name="integrate-security-solutions-in-azure-security-center"></a>Integración de soluciones de seguridad en Azure Security Center
Este documento le ayuda a administrar las soluciones de seguridad que ya está conectadas a Azure Security Center y a agregar otras nuevas.

## <a name="integrated-azure-security-solutions"></a>Soluciones de seguridad de Azure integradas
Security Center facilita la habilitación de soluciones de seguridad integradas en Azure. Dicha integración aporta las siguientes ventajas:

- **Implementación simplificada**: Security Center ofrece un aprovisionamiento optimizado de soluciones de asociados integradas. En el caso de algunas soluciones, como el antimalware y la evaluación de vulnerabilidades, Security Center puede aprovisionar al agente necesario en las máquinas virtuales, mientras que para las aplicaciones de firewall, Security Center puede ocuparse de gran parte de la configuración de red necesaria.
- **Detecciones integradas**: los eventos de seguridad de las soluciones de asociados se recopilan, agregan y muestran automáticamente como parte de las alertas e incidentes de Security Center. Estos eventos también se fusionan con las detecciones procedentes de otros orígenes para proporcionar funcionalidades avanzadas de detección de amenazas.
- **Administración y supervisión de del mantenimiento unificadas**: los clientes puede usar eventos de mantenimiento integrados para supervisar todas las soluciones de asociado de un vistazo. La administración básica está disponible con un acceso sencillo a la configuración avanzada mediante la solución de asociado.

En la actualidad, las soluciones de seguridad integradas incluyen:

- Protección de punto de conexión ([Trend Micro](https://help.deepsecurity.trendmicro.com/azure-marketplace-getting-started-with-deep-security.html), [Symantec](https://www.symantec.com/products), [McAfee](https://www.mcafee.com/us/products.aspx), [Windows Defender](https://www.microsoft.com/windows/comprehensive-security) y [System Center Endpoint Protection](https://docs.microsoft.com/sccm/protect/deploy-use/endpoint-protection))
- Firewall de aplicaciones web ([Barracuda](https://www.barracuda.com/products/webapplicationfirewall), [F5](https://support.f5.com/kb/en-us/products/big-ip_asm/manuals/product/bigip-ve-web-application-firewall-microsoft-azure-12-0-0.html), [Imperva](https://www.imperva.com/Products/WebApplicationFirewall-WAF), [Fortinet](https://www.fortinet.com/products.html) y [Azure Application Gateway](https://azure.microsoft.com/blog/azure-web-application-firewall-waf-generally-available/))
- Firewall de última generación ([Check Point](https://www.checkpoint.com/products/vsec-microsoft-azure/), [Barracuda](https://campus.barracuda.com/product/nextgenfirewallf/article/NGF/AzureDeployment/), [Fortinet](http://docs.fortinet.com/d/fortigate-fortios-handbook-the-complete-guide-to-fortios-5.2), [Cisco](http://www.cisco.com/c/en/us/td/docs/security/firepower/quick_start/azure/ftdv-azure-qsg.html) y [Palo Alto Networks](https://www.paloaltonetworks.com/products))
- Evaluación de vulnerabilidad ([Qualys](https://www.qualys.com/public-clouds/microsoft-azure/) y [Rapid7](https://www.rapid7.com/products/insightvm/))

> [!NOTE]
> Security Center no instala Microsoft Monitoring Agent en aplicaciones virtuales de asociado, porque la mayoría de los proveedores de seguridad prohíben que se ejecuten agentes externos en su aplicación.
>
>


| Endpoint Protection               | Plataformas                             | Instalación de Security Center | Detección de Security Center |
|-----------------------------------|---------------------------------------|------------------------------|---------------------------|
| Windows Defender (Microsoft Antimalware)                  | Windows Server 2016                   | No, se integra en el sistema operativo.           | SÍ                       |
| System Center Endpoint Protection (Microsoft Antimalware) | Windows Server 2012 R2, 2012, 2008 R2 (consulte la nota que hay a continuación) | Mediante extensión                | SÍ                       |
| Trend Micro: todas las versiones         | Familia de Windows Server                 | Sin                            | SÍ                       |
| Symantec v12.1.1100+              | Familia de Windows Server                 | Sin                            | SÍ                       |
| McAfee v10 o posterior                       | Familia de Windows Server                 | Sin                            | SÍ                       |
| Kaspersky                         | Familia de Windows Server                 | Sin                            | Sin                         |
| Sophos                            | Familia de Windows Server                 | Sin                            | Sin                         |

> [!NOTE]
> La detección de System Center Endpoint Protection (SCEP) en una máquina virtual de Windows Server 2008 R2 requiere que SCEP se instale después de PowerShell 3.0 (o una versión superior).
>
>

## <a name="how-security-solutions-are-integrated"></a>Integración de soluciones de seguridad
Las soluciones de seguridad de Azure que se implementan desde Security Center se conectan automáticamente. También se pueden conectar otros orígenes de datos de seguridad, como:

- Azure AD Identity Protection
- Equipos que se ejecutan de forma local o en otras nubes
- Solución de seguridad que admite Common Event Format (CEF)
- Microsoft Advanced Threat Analytics

![Integración de soluciones de asociados](./media/security-center-partner-integration/security-center-partner-integration-fig8.png)

## <a name="manage-integrated-azure-security-solutions-and-other-data-sources"></a>Administración de soluciones de seguridad de Azure integradas y otros orígenes de datos

1. Inicie sesión en el [Azure Portal](https://azure.microsoft.com/features/azure-portal/).

2. En el **menú de Microsoft Azure**, seleccione **Security Center**. Se abre **Security Center: Información general**.

3. En el menú de Security Center, seleccione **Soluciones de seguridad**.

  ![Introducción a Security Center](./media/security-center-partner-integration/overview.png)

En **Soluciones de seguridad**, puede ver información acerca del mantenimiento de las soluciones de seguridad integrada de Azure y realizar tareas de administración básicas. También puede conectar otros tipos de orígenes de datos de seguridad, como las alertas y los registros de firewall de Azure Active Directory Identity Protection en Common Event Format (CEF).

### <a name="connected-solutions"></a>Soluciones conectadas

La sección **Soluciones conectadas** incluye las soluciones de seguridad que están conectadas actualmente a Security Center e información acerca del estado de mantenimiento de cada solución.  

![Soluciones conectadas](./media/security-center-partner-integration/security-center-partner-integration-fig4.png)

Para más información, consulte [Administración de las soluciones conectadas de asociados con Azure Security Center](security-center-partner-solutions.md).

### <a name="discovered-solutions"></a>Soluciones detectadas

Security Center detecta automáticamente las soluciones de seguridad que se ejecutan en Azure, pero que no están conectadas a Security Center y las muestra en la sección **Soluciones detectadas**. Aquí se incluyen tanto soluciones de Azure, como [Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection), como soluciones de asociados.

> [!NOTE]
> El nivel estándar de Security Center es necesario en el nivel de suscripción de la característica de soluciones detectadas. Para obtener más información sobre los planes de tarifa de Security, consulte [Precios](security-center-pricing.md).
>
>

Seleccione **Conectar** en una solución para integrarla con Security Center y recibir alertas de seguridad.

![Soluciones detectadas](./media/security-center-partner-integration/security-center-partner-integration-fig5.png)

Security Center también detecta las soluciones implementadas en la suscripción que pueden reenviar registros de Common Event Format (CEF). Aprenda a [conectar una solución de seguridad](quick-security-solutions.md) que use registros de CEF a Security Center.

### <a name="add-data-sources"></a>Agregar orígenes de datos

La sección **Agregar orígenes de datos** incluye otros orígenes de datos disponibles que se pueden conectar. Para obtener instrucciones acerca de cómo agregar datos desde cualquiera de estos orígenes, haga clic en **ADD**.

![Orígenes de datos](./media/security-center-partner-integration/security-center-partner-integration-fig7.png)


## <a name="next-steps"></a>Pasos siguientes

En este documento, ha aprendido a integrar soluciones de asociados en Security Center. Para más información sobre Security Center, consulte los siguientes artículos:

* [Connecting Microsoft Advanced Threat Analytics to Azure Security Center](security-center-ata-integration.md) (Conexión de Microsoft Advanced Threat Analytics con Azure Security Center)
* [Conexión de Azure Active Directory Identity Protection con Azure Security Center](security-center-aadip-integration.md)
* [Supervisión del estado de seguridad en Security Center](security-center-monitoring.md). Aprenda a supervisar el estado de los recursos de Azure.
* [Supervise las soluciones de asociados con Security Center](security-center-partner-solutions.md). Aprenda cómo supervisar el estado de mantenimiento de las soluciones de sus asociados.
* [Preguntas más frecuentes sobre Azure Security Center](security-center-faq.md). Obtenga respuestas a preguntas frecuentes acerca del uso de Security Center.
* [Blog de seguridad de Azure](https://blogs.msdn.com/b/azuresecurity/). Encuentre artículos de blog sobre el cumplimiento y la seguridad de Azure.
