---
title: Solución de problemas de creación de inquilinos en Azure Active Directory B2C | Microsoft Docs
description: Problemas y soluciones a la hora de crear un inquilino de Azure Active Directory o de Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/06/2016
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 1d510f51ed28c28c698437f905c4911a8c32e5ce
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/07/2018
ms.locfileid: "51234993"
---
# <a name="troubleshoot-creating-an-azure-active-directory-or-azure-active-directory-b2c-tenant"></a>Solución de problemas a la hora de crear un inquilino de Azure Active Directory o de Azure Active Directory B2C 

## <a name="create-an-azure-ad-tenant"></a>Creación de un inquilino de Azure AD
Si no puede crear un inquilino de Azure Active Directory (Azure AD) en el primer intento, vuelva a intentarlo. Si el problema persiste, póngase en contacto con el servicio de soporte técnico de Azure.

## <a name="create-an-azure-ad-b2c-tenant"></a>Creación de un inquilino de Azure AD B2C
Si detecta problemas al [crear un inquilino de Azure Active Directory B2C (Azure AD B2C)](active-directory-b2c-get-started.md), intente las siguientes opciones:

* Si el inquilino de Azure AD B2C no aparece en la lista de inquilinos, vuelva a intentar crear el inquilino.
* Si el inquilino de Azure AD B2C aparece en la lista de inquilinos y ve el siguiente mensaje de error, elimine el inquilino y vuelva a crearlo:

    "No se pudo completar la creación del inquilino B2C "contosob2c". Para obtener más ayuda, visite este [vínculo](https://go.microsoft.com/fwlink/?LinkID=624192&clcid=0x409)".
* Existen problemas conocidos al eliminar un inquilino existente de Azure AD B2C y volver a crearlo con el mismo nombre de dominio. Al crear un inquilino de Azure AD B2C, debe usar un nombre de dominio diferente.
* Si estas soluciones no funcionan, póngase en contacto con el soporte técnico de Azure. Para obtener más información, consulte [Azure Active Directory B2C: presentación de solicitudes de soporte técnico](active-directory-b2c-support.md).

