---
title: 'Tutorial: Integración de Azure Active Directory con Workrite | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Workrite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 2a5c2956-a011-4d5c-877b-80679b6587b5
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: jeedes
ms.openlocfilehash: dd6a58656d91ff8dacbba36050198cba19f4d1e2
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/02/2018
ms.locfileid: "39437994"
---
# <a name="tutorial-azure-active-directory-integration-with-workrite"></a>Tutorial: Integración de Azure Active Directory con Workrite

En este tutorial, aprenderá a integrar Workrite con Azure Active Directory (Azure AD).

La integración de Workrite con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Workrite.
- Puede permitir que los usuarios inicien sesión automáticamente en Workrite (Inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Workrite, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en Workrite

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. El escenario descrito en este tutorial consta de dos bloques de creación principales:

1. Agregar Workrite desde la galería
1. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-workrite-from-the-gallery"></a>Agregar Workrite desde la galería
Para configurar la integración de Workrite en Azure AD, deberá agregar Workrite desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Workrite desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

1. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
1. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

1. En el cuadro de búsqueda, escriba **Workrite**, seleccione **Workrite** en el panel de resultados y, luego, haga clic en el botón **Agregar** para agregar la aplicación.

    ![Workrite en la lista de resultados](./media/workrite-tutorial/tutorial_workrite_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con Workrite con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Workrite para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Workrite.

En Workrite, para establecer la relación de vínculo, asigne el valor de **nombre de usuario** de Azure AD como valor de **Nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con Workrite, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)**: para que los usuarios puedan usar esta característica.
1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**, para probar el inicio de sesión único de Azure AD con Britta Simon.
1. **[Creación de un usuario de prueba de Workrite](#create-a-workrite-test-user)**: para tener un homólogo de Britta Simon en Workrite que esté vinculado a su representación en Azure AD.
1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**, para permitir que Britta Simon use el inicio de sesión único de Azure AD.
1. **[Prueba del inicio de sesión único](#test-single-sign-on)**: para comprobar si la configuración funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y lo configurará en la aplicación Workrite.

**Para configurar el inicio de sesión único de Azure AD con Workrite, realice los pasos siguientes:**

1. En Azure Portal, en la página de integración de la aplicación **Workrite**, haga clic en **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

1. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Cuadro de diálogo Inicio de sesión único](./media/workrite-tutorial/tutorial_workrite_samlbase.png)

1. En la sección **Dominio y direcciones URL de Workrite**, lleve a cabo los pasos siguientes:

    ![Información de dominio y direcciones URL de inicio de sesión único de Workrite](./media/workrite-tutorial/tutorial_workrite_url.png)

    En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://app.workrite.co.uk/securelogin/samlgateway.aspx?id=<uniqueid>`.

    > [!NOTE] 
    > Este valor no es real. Actualícelo con la dirección URL de inicio de sesión real. Póngase en contacto con el [equipo de soporte al cliente de Workrite](mailto:support@workrite.co.uk) para obtener este valor.

1. En la sección **Certificado de firma de SAML**, haga clic en **Certificado (Base64)** y, luego, guarde el archivo de certificado en el equipo.

    ![Vínculo de descarga del certificado](./media/workrite-tutorial/tutorial_workrite_certificate.png) 

1. Haga clic en el botón **Guardar** .

    ![Botón Configurar inicio de sesión único](./media/workrite-tutorial/tutorial_general_400.png)

1. En la sección **Configuración de Workrite**, haga clic en **Configurar Workrite** para abrir la ventana **Configurar inicio de sesión**. Copie la **URL del servicio de inicio de sesión único de SAML, el identificador de entidad de SAML y la dirección URL de cierre de sesión** de la sección **Referencia rápida**.

    ![Configuración de Workrite](./media/workrite-tutorial/tutorial_workrite_configure.png) 

1. Para configurar el inicio de sesión único en **Workrite**, es preciso enviar los datos descargados de **Certificado (Base64), Sign-Out URL (Dirección URL de cierre de sesión), SAML Entity ID (Identificador de identidad de SAML) y SAML Single Sign-On Service (Dirección URL del servicio de inicio de sesión único de SAML)** al [equipo de soporte técnico de Workrite](mailto:support@workrite.co.uk).

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

   ![Creación de un usuario de prueba de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel izquierdo de Azure Portal, haga clic en el botón **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/workrite-tutorial/create_aaduser_01.png)

1. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y, luego, haga clic en **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/workrite-tutorial/create_aaduser_02.png)

1. En la parte superior del cuadro de diálogo **Todos los usuarios**, haga clic en **Agregar** para abrir el cuadro de diálogo **Agregar**.

    ![Botón Agregar](./media/workrite-tutorial/create_aaduser_03.png)

1. En el cuadro de diálogo **Usuario** , realice los pasos siguientes:

    ![Cuadro de diálogo Usuario](./media/workrite-tutorial/create_aaduser_04.png)

    a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

    c. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.

    d. Haga clic en **Create**(Crear).
 
### <a name="create-a-workrite-test-user"></a>Creación de un usuario de prueba de Workrite

El objetivo de esta sección es crear una usuaria de prueba llamada Britta Simon en Workrite.

**Para crear una usuaria llamada Britta Simon en Workrite, realice los pasos siguientes:**

1. Inicie sesión en su sitio de la compañía de Workrite como administrador.

1. En el panel de navegación, haga clic en **Administrador**.
   
    ![Control de administración][400]

1. Vaya a Vínculos rápidos y, a continuación, haga clic en **Crear un usuario**.
   
    ![Sección Crear usuario][401]

1. En el cuadro de diálogo **Crear usuario** , realice los pasos siguientes:
   
    ![Cuadro de diálogo Crear usuario][402]
    
    a. En el cuadro de texto **Correo electrónico**, escriba la dirección de correo electrónico de un usuario, por ejemplo, Brittasimon@contoso.com.

    b. En el cuadro de texto **Nombre**, escriba el nombre del usuario, en este caso, Britta.

    c. En el cuadro de texto **Apellidos**, escriba los apellidos del usuario, en este caso, Simon.
    
    d. Seleccione **Administrador de cliente** como **Elegir rol**.
    
    e. Haga clic en **Save**(Guardar).   

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, concederá acceso a Brita Simon a Wokrite para permitirle que use el inicio de sesión único de Azure.

![Asignación de rol de usuario][200] 

**Para asignar a Britta Simon a Workrite, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, navegue a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

1. En la lista de aplicaciones, seleccione **Workrite**.

    ![Vínculo a Workrite en la lista de aplicaciones](./media/workrite-tutorial/tutorial_workrite_app.png)  

1. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

1. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Panel Agregar asignación][203]

1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

1. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

1. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

El objetivo de esta sección es probar la configuración del inicio de sesión único de Azure AD mediante el panel de acceso.

Al hacer clic en el icono de Workrite en el Panel de acceso, debería iniciar sesión automáticamente en su aplicación Workrite.

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/workrite-tutorial/tutorial_general_01.png
[2]: ./media/workrite-tutorial/tutorial_general_02.png
[3]: ./media/workrite-tutorial/tutorial_general_03.png
[4]: ./media/workrite-tutorial/tutorial_general_04.png

[100]: ./media/workrite-tutorial/tutorial_general_100.png

[200]: ./media/workrite-tutorial/tutorial_general_200.png
[201]: ./media/workrite-tutorial/tutorial_general_201.png
[202]: ./media/workrite-tutorial/tutorial_general_202.png
[203]: ./media/workrite-tutorial/tutorial_general_203.png
[400]: ./media/workrite-tutorial/tutorial_workrite_400.png
[401]: ./media/workrite-tutorial/tutorial_workrite_401.png
[402]: ./media/workrite-tutorial/tutorial_workrite_402.png

