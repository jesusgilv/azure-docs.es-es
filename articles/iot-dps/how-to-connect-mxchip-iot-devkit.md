---
title: Registro del MXChip IoT DevKit con IoT Hub mediante el aprovisionamiento automático con el servicio Azure IoT Hub Device Provisioning | Microsoft Docs
description: Registro del MXChip IoT DevKit con IoT Hub mediante el aprovisionamiento automático con el servicio Azure IoT Hub Device Provisioning
author: liydu
ms.author: liydu
ms.date: 04/04/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: jeffya
ms.openlocfilehash: d8912a5da8c4df2069d8bc53454748b5fb3d5c39
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/20/2018
ms.locfileid: "42144449"
---
# <a name="use-azure-iot-hub-device-provisioning-service-auto-provisioning-to-register-the-mxchip-iot-devkit-with-iot-hub"></a>Registro del MXChip IoT DevKit con IoT Hub mediante el aprovisionamiento automático con el servicio Azure IoT Hub Device Provisioning

En este artículo se describe el registro del MXChip IoT DevKit con Azure IoT Hub mediante el [aprovisionamiento automático](concepts-auto-provisioning.md) con el servicio Azure IoT Hub Device Provisioning. En este tutorial, aprenderá a:

* Configurar el punto de conexión global del servicio Device Provisioning en un dispositivo.
* Usar un secreto de dispositivo único (UDS) para generar un certificado X.509.
* Inscribir un dispositivo individual.
* Comprobar que el dispositivo está registrado.

[MXChip IoT DevKit](https://aka.ms/iot-devkit) es una placa integral compatible con Arduino que incluye numerosos periféricos y sensores. Se puede desarrollar para ella con la [extensión de Visual Studio Code para Arduino](https://aka.ms/arduino). DevKit incluye un [catálogo de proyectos](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) cada vez mayor que sirve de guía para crear prototipos de soluciones de Internet de las cosas (IoT) que aprovechen los servicios de Azure.

## <a name="before-you-begin"></a>Antes de empezar

Para completar los pasos de este tutorial, deberá realizar las siguientes tareas:

* Prepare su DevKit siguiendo los pasos descritos en [Conexión de IoT DevKit AZ3166 a Azure IoT Hub en la nube](/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started).
* Actualice al firmware más reciente (1.3.0 o posterior) con el tutorial de [actualización del firmware de DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/firmware-upgrading/).
* Cree y vincule una instancia de IoT Hub con una instancia del servicio Device Provisioning mediante los pasos que se indican en [Configuración de Azure IoT Hub Device Provisioning Service con Azure Portal](/azure/iot-dps/quick-setup-auto-provision).

## <a name="build-and-deploy-auto-provisioning-registration-software-to-the-device"></a>Compilación e implementación del software de registro de aprovisionamiento automático en el dispositivo

Para conectar DevKit a la instancia del servicio Device Provisioning que ha creado:

1. En Azure Portal, seleccione el panel **Información general** del servicio Device Provisioning y anote los valores de **Punto de conexión global del dispositivo** e **Ámbito de id**.
  ![Punto de conexión global y ámbito de id. del servicio Device Provisioning](./media/how-to-connect-mxchip-iot-devkit/dps-global-endpoint.png)

2. Asegúrese de tener instalado `git` en su máquina y que se agrega a las variables de entorno accesibles para la ventana de comandos. Consulte las [herramientas de cliente de Git de Software Freedom Conservancy](https://git-scm.com/download/) para instalar la versión más reciente.

3. Abra el símbolo del sistema. Clone el repositorio de GitHub del código de ejemplo del servicio Device Provisioning:
  ```bash
  git clone https://github.com/DevKitExamples/DevKitDPS.git
  ```

4. Abra Visual Studio Code, conecte DevKit a su equipo y abra la carpeta que contiene el código que ha clonado.

5. Abra **DevKitDPS.ino**. Busque y reemplace `[Global Device Endpoint]` y `[ID Scope]` por los valores que acababa de anotar.
  ![Punto de conexión del servicio Device Provisioning](./media/how-to-connect-mxchip-iot-devkit/endpoint.png) Puede dejar **registrationId** en blanco. La aplicación genera uno automáticamente en función de la versión de firmware y dirección MAC. Si quiere personalizar el identificador de registro, debe usar solo combinaciones de caracteres alfanuméricos, minúsculas y guiones hasta una longitud máxima de 128 caracteres. Para más información, consulte [Administración de inscripciones de dispositivos con Azure Portal](https://docs.microsoft.com/azure/iot-dps/how-to-manage-enrollments).

6. Use Quick Open en VS Code (Windows: `Ctrl+P`, macOS: `Cmd+P`) y escriba *task device-upload* para crear y cargar el código en DevKit.

7. La ventana de salida muestra si la tarea se ha realizado correctamente.

## <a name="save-a-unique-device-secret-on-an-stsafe-security-chip"></a>Guardar el secreto de dispositivo único en un chip de seguridad de STSAFE

Se puede configurar el aprovisionamiento automático en el dispositivo en función del [mecanismo de atestación](concepts-security.md#attestation-mechanism) de este. MXChip IoT DevKit usa la tecnología [Device Identity Composition Engine](https://trustedcomputinggroup.org/wp-content/uploads/Foundational-Trust-for-IOT-and-Resource-Constrained-Devices.pdf) de [Trusted Computing Group](https://trustedcomputinggroup.org). Se usa un *secreto de dispositivo único* (UDS) guardado en un chip de seguridad de STSAFE en DevKit para generar el certificado [X.509](concepts-security.md#x509-certificates) único del dispositivo. El certificado se usa más adelante en el proceso de inscripción del servicio Device Provisioning y durante el registro en el entorno de ejecución.

Un secreto de dispositivo único normal es una cadena de 64 caracteres, tal como se muestra en el ejemplo siguiente:

```
19e25a259d0c2be03a02d416c05c48ccd0cc7d1743458aae1cb488b074993eae
```

La cadena se divide en pares de caracteres que se usan en los cálculos de seguridad. En el ejemplo anterior, UDS se resuelve como: `0x19`, `0xe2`, `0x5a`, `0x25`, `0x9d`, `0x0c`, `0x2b`, `0xe0`, `0x3a`, `0x02`, `0xd4`, `0x16`, `0xc0`, `0x5c`, `0x48`, `0xcc`, `0xd0`, `0xcc`, `0x7d`, `0x17`, `0x43`, `0x45`, `0x8a`, `0xae`, `0x1c`, `0xb4`, `0x88`, `0xb0`, `0x74`, `0x99`, `0x3e`, `0xae`.

Para guardar el secreto de dispositivo único en DevKit:

1. Abra al monitor serie mediante una herramienta como Putty. Para más información, consulte [Use configuration mode](https://microsoft.github.io/azure-iot-developer-kit/docs/use-configuration-mode/) (Uso del modo de configuración).

2. Con DevKit conectado al equipo, mantenga presionado el botón **A** y, después, presione y suelte el botón de **restablecimiento** para acceder al modo de configuración. La pantalla muestra el identificador de DevKit y "Configuration" (Configuración).

3. Tome la cadena de UDS de ejemplo y cambie uno o varios caracteres por otros valores entre `0` y `f` en su propio UDS.

4. En la ventana del monitor serie, escriba *set_dps_uds [your_own_uds_value]* y presione Entrar.
  > [!NOTE]
  > Por ejemplo, si establece su propio secreto de dispositivo único mediante el cambio de los dos últimos caracteres a `f`, debe introducir el comando así: `set_dps_uds 19e25a259d0c2be03a02d416c05c48ccd0cc7d1743458aae1cb488b074993eff`.

5. Sin cerrar la ventana del monitor serie, presione el botón de **restablecimiento** en DevKit.

6. Anote el valor de **dirección MAC de DevKit** y la **versión de firmware de DevKit**.
  ![Versión de firmware](./media/how-to-connect-mxchip-iot-devkit/firmware-version.png)

## <a name="generate-an-x509-certificate"></a>Generar un certificado X.509

Ahora debe generar un certificado X.609. 

### <a name="windows"></a>Windows

1. Abra el Explorador de archivos y vaya a la carpeta que contiene el código de ejemplo del servicio Device Provisioning que clonó anteriormente. En la carpeta **.build**, busque y copie **DPS.ino.bin** y **DPS.ino.map**.
  ![Archivos generados](./media/how-to-connect-mxchip-iot-devkit/generated-files.png)
  > [!NOTE]
  > Si cambió la configuración de `built.path` para Arduino a otra carpeta, debe buscar esos archivos en la carpeta que configuró.

2. Pegue estos dos archivos en la carpeta **tools** en el mismo nivel que la carpeta **.build**.

3. Ejecute **dps_cert_gen.exe**. Siga las instrucciones para escribir su **UDS**, la **dirección MAC** de DevKit y la **versión de firmware** para generar el certificado X.509.
  ![Ejecución de dps-cert-gen.exe](./media/how-to-connect-mxchip-iot-devkit/dps-cert-gen.png)

4. Una vez generado el certificado X.509, se guarda un certificado **.pem** en la misma carpeta.

## <a name="create-a-device-enrollment-entry-in-the-device-provisioning-service"></a>Creación de una entrada de inscripción de dispositivos en el servicio Device Provisioning

1. En Azure Portal, vaya a la instancia del servicio Device Provisioning. Haga clic en **Administrar inscripciones** y seleccione la pestaña **Inscripciones individuales**. ![Inscripciones individuales](./media/how-to-connect-mxchip-iot-devkit/individual-enrollments.png)

2. Seleccione **Agregar**.

3. En el panel Add enrollment (Agregar inscripción):

   - Seleccione **X.509** en **Mechanism** (Mecanismo).
   - Haga clic en "Select a file" (Seleccione un archivo) en **Primary Certificate .pem or .cer file** (Archivo .pem o .cer del certificado principal).
   - En el cuadro de diálogo File Open (Abrir archivo), vaya al certificado **.pem** que acaba de crear y cárguelo.
   - Deje el resto tal como está y haga clic en **Guardar**.

   ![Carga del certificado](./media/how-to-connect-mxchip-iot-devkit/upload-cert.png)

  > [!NOTE]
  > Si obtiene un error con este mensaje:
  >
  > `{"message":"BadRequest:{\r\n \"errorCode\": 400004,\r\n \"trackingId\": \"1b82d826-ccb4-4e54-91d3-0b25daee8974\",\r\n \"message\": \"The certificate is not a valid base64 string value\",\r\n \"timestampUtc\": \"2018-05-09T13:52:42.7122256Z\"\r\n}"}`
  >
  > Abra el archivo de certificado **.pem** como texto (con el Bloc de notas o cualquier editor de texto) y elimine las líneas:
  >
  > `"-----BEGIN CERTIFICATE-----"` y `"-----END CERTIFICATE-----"`.
  >

## <a name="start-the-devkit"></a>Inicio del DevKit

1. Abra VS Code y el monitor serie.

2. Presione el botón **Restablecer** en DevKit.

Verá que DevKit inicia el registro en el servicio Device Provisioning.

![Salida de VS Code](./media/how-to-connect-mxchip-iot-devkit/vscode-output.png)

## <a name="verify-that-the-devkit-is-registered-with-azure-iot-hub"></a>Comprobar que DevKit está registrado en Azure IoT Hub

Una vez que arranca el dispositivo, tienen lugar las siguientes acciones:

1. El dispositivo envía una solicitud de registro al servicio Device Provisioning.
2. El servicio devuelve un desafío de registro al que responde el dispositivo.
3. Una vez que el registro se ha realizado correctamente, el servicio Device Provisioning envía el URI de IoT Hub, el identificador de dispositivo y la clave cifrada nuevamente al dispositivo.
4. A continuación, la aplicación cliente de IoT Hub del dispositivo se conecta al centro.
5. Después de una conexión correcta, el dispositivo aparece en la instancia de Device Explorer de IoT Hub.
  ![Dispositivo registrado](./media/how-to-connect-mxchip-iot-devkit/device-registered.png)

## <a name="problems-and-feedback"></a>Problemas y comentarios

Si tiene problemas, consulte las [preguntas frecuentes](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) de Iot DevKit o póngase en contacto con el soporte técnico en los siguientes canales:

* [Gitter.im](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stackoverflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, aprendió a inscribir un dispositivo de forma segura en el servicio Device Provisioning mediante Device Identity Composition Engine, para que el dispositivo se pueda registrar automáticamente en Azure IoT Hub. 

En resumen, ha aprendido a:

> [!div class="checklist"]
> * Configurar el punto de conexión global del servicio Device Provisioning en un dispositivo.
> * Usar un secreto de dispositivo único para generar un certificado X.509.
> * Inscribir un dispositivo individual.
> * Comprobar que el dispositivo está registrado.

Aprenda cómo [crear y aprovisionar un dispositivo simulado](./quick-create-simulated-device.md).

