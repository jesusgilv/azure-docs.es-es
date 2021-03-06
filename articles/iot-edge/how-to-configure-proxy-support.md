---
title: Configuración de dispositivos de Azure IoT Edge para servidores proxy de red | Microsoft Docs
description: Aprenda a configurar el entorno de ejecución de Azure IoT Edge y todos los módulos de IoT Edge a los que se pueda acceder desde Internet para que se comuniquen a través de un servidor proxy.
author: kgremban
manager: ''
ms.author: kgremban
ms.date: 11/01/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 72855058c5e8294eece55f8dbcdc501025c9aabf
ms.sourcegitcommit: 799a4da85cf0fec54403688e88a934e6ad149001
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2018
ms.locfileid: "50913230"
---
# <a name="configure-an-iot-edge-device-to-communicate-through-a-proxy-server"></a>Configuración de un dispositivo de IoT Edge para que se comunique a través de un servidor proxy

Los dispositivos de IoT Edge envían solicitudes HTTPS para comunicarse con IoT Hub. Si el dispositivo está conectado a una red que usa un servidor proxy, deberá configurar el entorno de ejecución de IoT Edge para que se comunique a través del servidor. Los servidores proxy también pueden afectar a módulos de IoT Edge individuales si estos realizan solicitudes HTTP o HTTPS que no se enrutan a través del centro de Edge. 

Para configurar un dispositivo de IoT Edge para que funcione con un servidor proxy siga estos pasos básicos: 

1. Instale el entorno de ejecución de Azure IoT Edge en el dispositivo. 
2. Configure los demonios de Docker y de IoT Edge en el dispositivo para que usen el servidor proxy.
3. Configure las propiedades de edgeAgent en el archivo config.yaml del dispositivo.
4. Establezca las variables de entorno del entorno de ejecución de Azure IoT Edge y de otros módulos de IoT Edge en el manifiesto de implementación. 

## <a name="know-your-proxy-url"></a>Dirección URL de proxy

Para configurar tanto el demonio Docker como IoT Edge en el dispositivo, deberá saber la dirección URL del proxy. 

Las direcciones URL de proxy tienen el formato siguiente: **protocolo**://**host_proxy**:**puerto_proxy**. 

* El **protocolo** es HTTP o HTTPS. El demonio Docker puede configurarse con cualquier protocolo, según la configuración del registro de contenedor, pero los contenedores de tiempo de ejecución y demonio de IoT Edge deben utilizar siempre HTTPS.

* El **host_proxy** es una dirección para el servidor proxy. Si el servidor proxy requiere autenticación, puede proporcionar sus credenciales como parte del host_proxy con el formato de **usuario**:**contraseña**@**host_proxy**. 

* El **puerto_proxy** es el puerto de red en el que el proxy responde al tráfico de red. 

## <a name="install-the-runtime"></a>Instalación de la instancia en tiempo de ejecución

Si va a instalar el entorno de ejecución de Azure IoT Edge en un dispositivo Linux, configure el administrador de paquetes para que pase por el servidor proxy para acceder al paquete de instalación. Por ejemplo, [configure apt-get para usar un http-proxy](https://help.ubuntu.com/community/AptGet/Howto/#Setting_up_apt-get_to_use_a_http-proxy). Una vez que el administrador de paquetes esté configurado, siga las instrucciones de [Instalación del entorno de ejecución de Azure IoT Edge en Linux (ARM32v7/armhf)](how-to-install-iot-edge-linux-arm.md) o [Instalar el entorno de ejecución de Azure IoT Edge en Linux (x64)](how-to-install-iot-edge-linux.md) como de costumbre. 

Si va a instalar el entorno de ejecución de Azure IoT Edge en un dispositivo Windows, debe pasar por el servidor proxy para acceder al paquete de instalación. Puede configurar la información del proxy en la configuración de Windows, o incluir la información del proxy directamente en el script de instalación. El siguiente script de PowerShell es un ejemplo de una instalación de Windows con el argumento `-proxy`:

```powershell
. {Invoke-WebRequest -proxy <proxy URL> -useb aka.ms/iotedge-win} | Invoke-Expression; `
Install-SecurityDaemon -Manual -ContainerOs Windows
```

Para más información y opciones de instalación, consulte [Instalación del entorno de ejecución de Azure IoT Edge en Windows para usar con contenedores de Windows](how-to-install-iot-edge-windows-with-windows.md) o [Instalación del entorno de ejecución de Azure IoT Edge en Windows para su uso con contenedores de Linux](how-to-install-iot-edge-windows-with-linux.md).

Una vez que el entorno de ejecución de Azure IoT Edge esté instalado, use la sección siguiente para configurarlo con la información del proxy. 

## <a name="configure-the-daemons"></a>Configuración de los demonios

Los demonios de Docker e IoT Edge que se ejecutan en el dispositivo de IoT Edge se deben configurar para que usen el servidor proxy. El demonio de Docker realiza solicitudes web para extraer imágenes de contenedor desde los registros de contenedor. El demonio de IoT Edge realiza solicitudes web para comunicarse con IoT Hub.

### <a name="docker-daemon"></a>Demonio de Docker

Consulte la documentación de Docker para configurar el demonio de Docker con variables de entorno. La mayoría de los registros de contenedor (incluidos los de DockerHub y Azure Container) admiten solicitudes HTTPS, por lo que el parámetro que se debe establecer es **HTTPS_PROXY**. Si va a extraer imágenes de un registro que no admite la seguridad de la capa de transporte (TLS), debe establecer el parámetro **HTTP_PROXY**. 

Elija el artículo aplicable a su versión de Docker: 

* [Docker](https://docs.docker.com/config/daemon/systemd/#httphttps-proxy)
* [Docker para Windows](https://docs.docker.com/docker-for-windows/#proxies)

### <a name="iot-edge-daemon"></a>Demonio de IoT Edge

El demonio de IoT Edge se configura de forma similar al de Docker. Todas las solicitudes que IoT Edge envía a IoT Hub usan HTTPS. Utilice los pasos siguientes para establecer una variable de entorno para el servicio según su sistema operativo. 

#### <a name="linux"></a>Linux

Abra un editor en el terminal para configurar el demonio de IoT Edge. 

```bash
sudo systemctl edit iotedge
```

Escriba el texto siguiente, reemplazando **\<dirección URL del proxy >** por la dirección del servidor proxy y el puerto. A continuación, guarde y salga. 

```text
[Service]
Environment="https_proxy=<proxy URL>"
```

Actualice el administrador de servicios para que incluya la nueva configuración de iotedge.

```bash
sudo systemctl daemon-reload
```

Reinicie IoT Edge para que los cambios surtan efecto.

```bash
sudo systemctl restart iotedge
```

Compruebe que se creó la variable de entorno y que se cargó la nueva configuración. 

```bash
systemctl show --property=Environment iotedge
```

#### <a name="windows"></a>Windows

Abra una ventana de PowerShell como administrador y ejecute el comando siguiente para editar el registro con la nueva variable de entorno. Reemplace **\<dirección URL del proxy >** por la dirección del servidor proxy y el puerto. 

```powershell
reg add HKLM\SYSTEM\CurrentControlSet\Services\iotedge /v Environment /t REG_MULTI_SZ /d https_proxy=<proxy URL>
```

Reinicie IoT Edge para que los cambios surtan efecto.

```powershell
Restart-Service iotedge
```

## <a name="configure-the-edge-agent"></a>Configuración del agente de Edge

El agente de Edge es el primer módulo que se inicia en cualquier dispositivo de IoT Edge. Se inicia por primera vez en función de la información del archivo config.yaml de IoT Edge. El agente de Edge se conecta posteriormente a IoT Hub para recuperar los manifiestos de implementación, los cuales declaran qué otros módulos deberían estar implementados en el dispositivo.

Abra el archivo config.yaml en el dispositivo de IoT Edge. En los sistemas Linux, este archivo se encuentra en **/etc/iotedge/config.yaml**. En los sistemas Windows, este archivo se encuentra en **C:\ProgramData\iotedge\config.yaml**. El archivo de configuración está protegido, por lo que necesita privilegios de administrador para acceder a él. En sistemas Linux, eso significa que hay que usar el comando `sudo` antes de abrir el archivo en el editor de texto que prefiera. En Windows, esto significa abrir un editor de texto como el Bloc de notas para ejecutarlo como administrador y, a continuación, abrir el archivo. 

En el archivo config.yaml, busque la sección **especificación del módulo de agente de Edge**. La definición del agente de Edge incluye un parámetro **env** en el que puede agregar variables de entorno. 

<!--
![edgeAgent definition](./media/how-to-configure-proxy-support/edgeagent-unedited.png)
-->

Elimine las llaves que son marcadores de posición del parámetro env y agregue la nueva variable en una nueva línea. Recuerde que las sangrías de YAML son dos espacios. 

```yaml
https_proxy: "<proxy URL>"
```

El runtime de IoT Edge usa AMQP de forma predeterminada para comunicarse con IoT Hub. Algunos servidores proxy bloquean los puertos AMQP. Si es así, también debe configurar edgeAgent para que use AMQP sobre WebSocket. Agregue una segunda variable de entorno.

```yaml
UpstreamProtocol: "AmqpWs"
```

![definición de edgeAgent con variables de entorno](./media/how-to-configure-proxy-support/edgeagent-edited.png)

Guarde los cambios en config.yaml y cierre el editor. Reinicie IoT Edge para que los cambios surtan efecto. 

* Linux: 

   ```bash
   sudo systemctl restart iotedge
   ```

* Windows:

   ```powershell
   Restart-Service iotedge
   ```

## <a name="configure-deployment-manifests"></a>Configuración de manifiestos de implementación  

Una vez que el dispositivo de IoT Edge esté configurado para funcionar con el servidor proxy, deberá declarar las variables de entorno en todos los manifiestos de implementación futuros. Los dos módulos del entorno de ejecución, edgeAgent y edgeHub, deben tener siempre el servidor proxy configurado para mantener la comunicación con IoT Hub. Puede configurar cualquier módulo de IoT Edge para que se comunique a través de un servidor proxy, pero no es necesario en el caso de módulos que enrutan sus mensajes a través de edgeHub o que solo se comunican con otros módulos del dispositivo. 

Puede crear manifiestos de implementación mediante Azure Portal o editando manualmente un archivo JSON. 

### <a name="azure-portal"></a>Azure Portal

Cuando usa el asistente de **Establecimiento de módulos** para crear implementaciones para dispositivos de IoT Edge, todos los módulos tienen una sección **Variables de entorno** que puede usar para configurar las conexiones con el servidor proxy. 

Para configurar el agente de Edge y los módulos del centro de Edge, seleccione **Configurar las opciones avanzadas del entorno en tiempo de ejecución de Edge** en el primer paso del asistente. 

![Configurar las opciones avanzadas del entorno en tiempo de ejecución de Edge](./media/how-to-configure-proxy-support/configure-runtime.png)

Agregue la variable de entorno **https_proxy** al agente de Edge y a las definiciones de módulo del centro de Edge. Si incluyó la variable de entorno **UpstreamProtocol** en el archivo config.yaml del dispositivo de IoT Edge, agréguela también a la definición del módulo del agente de Edge. 

![Establecimiento de variables de entorno](./media/how-to-configure-proxy-support/edgehub-environmentvar.png)

Todos los demás módulos que agregue a un manifiesto de implementación seguirán el mismo patrón. En la página en la que estableció el nombre y la imagen del módulo hay una sección de variables de entorno.

### <a name="json-deployment-manifest-files"></a>Archivos JSON del manifiesto de implementación

Si va a crear implementaciones para dispositivos IoT Edge mediante las plantillas de Visual Studio Code o mediante la creación manual de archivos JSON, puede agregar las variables de entorno directamente a cada definición del módulo. 

Use el siguiente formato JSON: 

```json
"env": {
    "https_proxy": {
        "value": "<proxy URL>"
    }
}
```

Con las variables de entorno incluidas, la definición del módulo debería ser similar al siguiente ejemplo de edgeHub:

```json
"edgeHub": {
    "type": "docker",
    "settings": {
        "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
        "createOptions": ""
    },
    "env": {
        "https_proxy": {
            "value": "https://proxy.example.com:3128"
        }
    },
    "status": "running",
    "restartPolicy": "always"
}
```

Si incluyó la variable de entorno **UpstreamProtocol** en el archivo config.yaml del dispositivo de IoT Edge, agréguela también a la definición del módulo del agente de Edge. 

```json
"env": {
    "https_proxy": {
        "value": "<proxy URL>"
    },
    "UpstreamProtocol": {
        "value": "AmqpWs"
    }
}
```

## <a name="next-steps"></a>Pasos siguientes

Más información sobre los roles del [entorno de ejecución de Azure IoT Edge](iot-edge-runtime.md).

Solución de errores de instalación y configuración con [Problemas habituales y soluciones para Azure IoT Edge](troubleshoot.md)

