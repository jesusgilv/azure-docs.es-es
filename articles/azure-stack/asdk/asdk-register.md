---
title: Registro del Kit de desarrollo de Azure Stack en Azure | Microsoft Docs
description: Describe cómo registrar Azure Stack en Azure para habilitar la redifusión de Marketplace y los informes de uso.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/19/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: e86ff4ebf91d0c0b691caf429d9489bf769f16af
ms.sourcegitcommit: 8314421d78cd83b2e7d86f128bde94857134d8e1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/19/2018
ms.locfileid: "51975199"
---
# <a name="azure-stack-registration"></a>Registro de Azure Stack
La instalación del Kit de desarrollo de Azure Stack se puede registrar en Azure para descargar elementos de Marketplace de Azure y configurar informes de datos comerciales para Microsoft. Es necesario que el registro admita todas funcionalidades de Azure Stack, incluida la redifusión de Marketplace. Dicho registro es aconsejable, porque permite probar una funcionalidad importante de Azure Stack, como es la redifusión de Marketplace y los informes de uso. Después de registrar Azure Stack, se informa del uso para el comercio de Azure. Puede verlo en la suscripción que usó para el registro. Sin embargo, a los usuarios del ASDK no se les cobra ningún uso que comuniquen.

Si no registra su ASDK, puede que vea la alerta de advertencia **Activación necesaria** que le informa que debe registrar el Kit de desarrollo de Azure Stack. Este comportamiento es normal.

## <a name="prerequisites"></a>Requisitos previos
Antes de usar estas instrucciones para registrar el ASDK en Azure, asegúrese de que ha instalado Azure Stack PowerShell y ha descargado las herramientas de Azure Stack como se describe en el artículo de [configuración posterior a la implementación](asdk-post-deploy.md).

Además, el modo de lenguaje de PowerShell se debe establecer en **FullLanguageMode** en el equipo que se usa para registrar el ASDK con Azure. Para comprobar que el modo de lenguaje actual está establecido en FullLanguageMode, abra una ventana de PowerShell con privilegios elevados y ejecute los siguientes comandos de PowerShell:

```PowerShell  
$ExecutionContext.SessionState.LanguageMode
```

Asegúrese de que la salida devuelve **FullLanguageMode**. Si se devuelve cualquier otro modo de lenguaje, el tendrá que ejecutarse en otro equipo o el modo de lenguaje tendrá que establecerse en **FullLanguageMode** antes de continuar.

## <a name="register-azure-stack-with-azure"></a>Registro de Azure Stack con Azure
Siga estos pasos para registrar el ASDK en Azure.

> [!NOTE]
> Todos estos pasos se deben ejecutar desde un equipo que tenga acceso al punto de conexión con privilegios. En el caso del Kit de desarrollo de Azure Stack, este es el equipo host del kit de desarrollo.

1. Abra una consola de PowerShell como administrador.  

2. Ejecute los siguientes comandos de PowerShell para registrar la instalación de ASDK con Azure. Tendrá que iniciar sesión tanto en su suscripción de Azure como en la instalación local de ASDK. Si aún no tiene una suscripción de Azure, puede crear una [cuenta gratuita de Azure aquí](https://azure.microsoft.com/free/?b=17.06). El registro de Azure Stack no supone ningún costo en su suscripción de Azure.<br><br>Si el script de registro se ejecuta en más de una instancia de Azure Stack con el mismo identificador de suscripción de Azure, establezca un nombre único para el registro al ejecutar el cmdlet **Set-AzsRegistration**. El parámetro **RegistrationName** tiene el valor predeterminado **AzureStackRegistration**. Sin embargo, si usa el mismo nombre en más de una instancia de Azure Stack, el script no se ejecutará correctamente.

    ```PowerShell  
    # Add the Azure cloud subscription environment name. 
    # Supported environment names are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using.
    Add-AzureRmAccount -EnvironmentName "<environment name>"

    # Register the Azure Stack resource provider in your Azure subscription
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack

    #Import the registration module that was downloaded with the GitHub tools
    Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1

    #Register Azure Stack
    $AzureContext = Get-AzureRmContext
    $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the credentials to access the privileged endpoint."
    $RegistrationName = "<unique-registration-name>"
    Set-AzsRegistration `
    -PrivilegedEndpointCredential $CloudAdminCred `
    -PrivilegedEndpoint AzS-ERCS01 `
    -BillingModel Development `
    -RegistrationName $RegistrationName `
    -UsageReportingEnabled:$true
    ```
3. Cuando finalice el script, verá el mensaje **Your environment is now registered and activated using the provided parameters** (El entorno ya está registrado y se ha activado mediante los parámetros proporcionados).

    ![El entorno ya está registrado](media/asdk-register/1.PNG)


## <a name="register-in-disconnected-environments"></a>Registro en entornos desconectados
Si va a registrar Azure Stack en un entorno desconectado (sin conexión a Internet), tendrá que obtener un token de registro del entorno de Azure Stack y, después, utilizarlo en un equipo que pueda conectarse a Azure para registrarse y crear un recurso de activación para el entorno de ASDK.
 
 > [!IMPORTANT]
 > Antes de usar estas instrucciones para registrar Azure Stack, asegúrese de que ha instalado PowerShell para Azure Stack y de que ha descargado las herramientas de Azure Stack tal como se describe en el artículo de [configuración posterior a la implementación](asdk-post-deploy.md) tanto en el equipo host de ASDK como en el equipo con acceso a Internet que utilizará para conectarse a Azure y realizar el registro.

### <a name="get-a-registration-token-from-the-azure-stack-environment"></a>Obtención de un token de registro del entorno de Azure Stack
En el equipo host de ASDK, inicie PowerShell como administrador y vaya a la carpeta **Registration** del directorio **AzureStack-Tools-master** que creó cuando descargó las herramientas de Azure Stack. Use los siguientes comandos de PowerShell para importar el módulo **RegisterWithAzure.psm1** y, después, use el cmdlet **Get AzsRegistrationToken** para obtener el token de registro:  

   ```PowerShell  
   Import-Module .\RegisterWithAzure.psm1
   $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the credentials to access the privileged endpoint."
   $FilePathForRegistrationToken = $env:SystemDrive\RegistrationToken.txt
   $RegistrationToken = Get-AzsRegistrationToken -PrivilegedEndpointCredential $CloudAdminCred `
   -UsageReportingEnabled:$false `
   -PrivilegedEndpoint AzS-ERCS01 `
   -BillingModel Development `
   -MarketplaceSyndicationEnabled:$false `
   -TokenOutputFilePath $FilePathForRegistrationToken
   ```
De forma predeterminada, el token de registro se guarda en el archivo especificado por el parámetro *$FilePathForRegistrationToken*. Puede cambiar la ruta del archivo o el nombre de archivo a su entera discreción.

Guarde este token de registro para usarlo en el equipo conectado a Internet. El archivo o el texto se pueden copiar de $FilePathForRegistrationToken.

### <a name="connect-to-azure-and-register"></a>Conexión a Azure y posterior registro
En el equipo conectado a Internet, inicie PowerShell como administrador y vaya a la carpeta **Registration** del directorio **AzureStack-Tools-master** que creó cuando descargó las herramientas de Azure Stack. Use los siguientes comandos de PowerShell para importar el módulo **RegisterWithAzure.psm1** y, después, use el cmdlet **Register-AzsEnvironment** para registrarse en Azure al proporcionar el token de registro que acaba de crear y un nombre de registro único:  

  ```PowerShell  
  $registrationToken = "<your registration token>"
  $RegistrationName = "<unique registration name>"
  Register-AzsEnvironment -RegistrationToken $registrationToken `
  -RegistrationName $RegistrationName
  ```

También puede usar el cmdlet **Get-Content** para señalar a un archivo que contenga el token de registro:

  ```PowerShell  
  $registrationToken = Get-Content -Path '<path>\<registration token file>'
  Register-AzsEnvironment -RegistrationToken $registrationToken `
  -RegistrationName $RegistrationName
  ```

Guarde el token de registro y el nombre de recurso de registro para futuras referencias.

### <a name="retrieve-an-activation-key-from-the-azure-registration-resource"></a>Recuperación de una clave de activación del recurso de registro de Azure

Mediante el equipo conectado a Internet, recupere una clave de activación del recurso de registro que se creó al registrarse en Azure.

Para obtener la clave de activación, ejecute los siguientes comandos de PowerShell y use el mismo valor de nombre de registro único que proporcionó al registrarse en Azure durante el paso anterior:  

  ```Powershell
  $RegistrationResourceName = "<unique-registration-name>"
  $KeyOutputFilePath = "$env:SystemDrive\ActivationKey.txt"
  $ActivationKey = Get-AzsActivationKey -RegistrationName $RegistrationResourceName `
  -KeyOutputFilePath $KeyOutputFilePath
  ```

La clave de activación se guarda en el archivo especificado para *$KeyOutputFilePath*. Puede cambiar la ruta del archivo o el nombre de archivo a su entera discreción.

### <a name="create-an-activation-resource-in-azure-stack"></a>Crear un recurso de activación en Azure Stack

Vuelva al entorno de Azure Stack con el archivo o el texto de la clave de activación creada a partir de **Get-AzsActivationKey**. Ejecute los siguientes comandos de PowerShell para crear un recurso de activación en Azure Stack con dicha clave de activación:   

  ```Powershell
  $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the credentials to access the privileged endpoint."
  $ActivationKey = "<activation key>"
  New-AzsActivationResource -PrivilegedEndpointCredential $CloudAdminCred `
  -PrivilegedEndpoint AzS-ERCS01 `
  -ActivationKey $ActivationKey
  ```

También puede usar el cmdlet **Get-Content** para señalar a un archivo que contenga el token de registro:

  ```Powershell
  $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the credentials to access the privileged endpoint."
  $ActivationKey = Get-Content -Path '<path>\<Activation Key File>'
  New-AzsActivationResource -PrivilegedEndpointCredential $CloudAdminCred `
  -PrivilegedEndpoint AzS-ERCS01 `
  -ActivationKey $ActivationKey
  ```

## <a name="verify-the-registration-was-successful"></a>Comprobación de que el registro fue correcto
Siga estos pasos para comprobar que el registro de ASDK con Azure se realizó correctamente.

1. Inicie sesión en el [portal de administración de Azure Stack](https://adminportal.local.azurestack.external).

2. Haga clic en **Administración de Marketplace** > **Add from Azure** (Agregar desde Azure).

    ![](media/asdk-register/2.PNG)

3. Si aparece una lista de elementos disponibles de Azure, la activación fue correcta.

    ![](media/asdk-register/3.PNG)

## <a name="move-a-registration-resource"></a>Traslado de un recurso de registro
Se **admite** el traslado de un recurso de registro entre grupos de recursos en la misma suscripción. Para más información sobre cómo mover recursos a un nuevo grupo de recursos, consulte [Traslado de los recursos a un nuevo grupo de recursos o a una nueva suscripción](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources).


## <a name="next-steps"></a>Pasos siguientes
[Incorporación de un elemento de Marketplace de Azure Stack](.\.\azure-stack-marketplace.md)
