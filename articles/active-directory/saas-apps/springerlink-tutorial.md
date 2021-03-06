---
title: 'Tutorial: Integración de Azure Active Directory con Springer Link | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Springer Link.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 58cdf029-bdc0-43c4-a469-b921c2a669bd
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2017
ms.author: jeedes
ms.openlocfilehash: 8138c7605b0024dfe4569e33843cb1e9d169271f
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/02/2018
ms.locfileid: "39438773"
---
# <a name="tutorial-azure-active-directory-integration-with-springer-link"></a>Tutorial: Integración de Azure Active Directory con Springer Link

En este tutorial, obtendrá información sobre cómo integrar Springer Link con Azure Active Directory (Azure AD).

La integración de Springer Link con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Springer Link.
- Puede permitir que los usuarios inicien sesión automáticamente en Springer Link (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Springer Link, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en Springer Link

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. El escenario descrito en este tutorial consta de dos bloques de creación principales:

1. Incorporación de Springer Link desde la galería
1. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-springer-link-from-the-gallery"></a>Incorporación de Springer Link desde la galería
Para configurar la integración de Springer Link en Azure AD, hay que agregar Springer Link desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Springer Link desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

1. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
1. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

1. En el cuadro de búsqueda, escriba **Springer Link**, seleccione **Springer Link** en el panel de resultados y, luego, haga clic en el botón **Agregar** para agregar la aplicación.

    ![Springer Link en la lista de resultados](./media/springerlink-tutorial/tutorial_springerlink_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con Springer Link con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Springer Link para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Springer Link.

Para establecer la relación de vínculo, en Springer Link asigne el valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con Springer Link, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)**: para que los usuarios puedan usar esta característica.
1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**, para probar el inicio de sesión único de Azure AD con Britta Simon.
1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**, para permitir que Britta Simon use el inicio de sesión único de Azure AD.
1. **[Prueba del inicio de sesión único](#test-single-sign-on)**: para comprobar si la configuración funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y lo configurará en la aplicación Springer Link.

**Para configurar el inicio de sesión único de Azure AD con Springer Link, realice los pasos siguientes:**

1. En Azure Portal, en la página de integración de la aplicación **Springer Link**, haga clic en **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

1. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Cuadro de diálogo Inicio de sesión único](./media/springerlink-tutorial/tutorial_springerlink_samlbase.png)

1. Vaya a la sección **Dominio y direcciones URL de Springer Link**, si quiere configurar la aplicación en modo iniciado por **IDP**:

    ![Información de inicio de sesión único de Dominio y direcciones URL de Springer Link](./media/springerlink-tutorial/tutorial_springerlink_url1.png)

    a. En el cuadro de texto **Identificador**, escriba la dirección URL: `https://fsso.springer.com`

    b. En el cuadro de texto **URL de respuesta**, escriba la siguiente dirección URL: `https://fsso-qa1.springer.com/federation/Consumer/metaAlias/SpringerServiceProvider`    

1. Active **Mostrar configuración avanzada de URL**. Si quiere volver a configurar la aplicación en modo iniciado por **SP**:

    ![Información de inicio de sesión único de Dominio y direcciones URL de Springer Link](./media/springerlink-tutorial/tutorial_springerlink_url.png)

    En el cuadro de texto **URL de inicio de sesión**, escriba la dirección URL: `https://fsso.springer.com/federation/Consumer/metaAlias/SpringerServiceProvider`

1. En la sección **Certificado de firma de SAML**, haga clic en el botón Copiar para copiar la **dirección URL de metadatos de federación de la aplicación** y péguela en el Bloc de notas. 

    ![Vínculo de descarga del certificado](./media/springerlink-tutorial/tutorial_springerlink_certificate.png)    

1. Haga clic en el botón **Guardar** .

    ![Botón Configurar inicio de sesión único](./media/springerlink-tutorial/tutorial_general_400.png)

1. Para configurar el inicio de sesión único en **Springer Link**, hay que enviar la **dirección URL de metadatos de federación de la aplicación** al [equipo de soporte técnico de Springer Link](mailto:identity@springernature.com).

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

   ![Creación de un usuario de prueba de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel izquierdo de Azure Portal, haga clic en el botón **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/springerlink-tutorial/create_aaduser_01.png)

1. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y, luego, haga clic en **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/springerlink-tutorial/create_aaduser_02.png)

1. En la parte superior del cuadro de diálogo **Todos los usuarios**, haga clic en **Agregar** para abrir el cuadro de diálogo **Agregar**.

    ![Botón Agregar](./media/springerlink-tutorial/create_aaduser_03.png)

1. En el cuadro de diálogo **Usuario** , realice los pasos siguientes:

    ![Cuadro de diálogo Usuario](./media/springerlink-tutorial/create_aaduser_04.png)

    a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

    c. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.

    d. Haga clic en **Create**(Crear).
 
### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a Springer Link.

![Asignación de rol de usuario][200] 

**Para asignar Britta Simon a Springer Link, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, navegue a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

1. En la lista de aplicaciones, seleccione **Springer Link**.

    ![Vínculo Springer Link de la lista de aplicaciones](./media/springerlink-tutorial/tutorial_springerlink_app.png)  

1. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

1. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Panel Agregar asignación][203]

1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

1. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

1. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de Springer Link en el panel de acceso, debería iniciar sesión automáticamente en la aplicación Springer Link.
Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/springerlink-tutorial/tutorial_general_01.png
[2]: ./media/springerlink-tutorial/tutorial_general_02.png
[3]: ./media/springerlink-tutorial/tutorial_general_03.png
[4]: ./media/springerlink-tutorial/tutorial_general_04.png

[100]: ./media/springerlink-tutorial/tutorial_general_100.png

[200]: ./media/springerlink-tutorial/tutorial_general_200.png
[201]: ./media/springerlink-tutorial/tutorial_general_201.png
[202]: ./media/springerlink-tutorial/tutorial_general_202.png
[203]: ./media/springerlink-tutorial/tutorial_general_203.png

