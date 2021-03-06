---
title: archivo de inclusión
description: archivo de inclusión
services: iot-hub
author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 11/06/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 4a4ec51430242cc3e3d6a0b801f2b4be7858ab50
ms.sourcegitcommit: 5a1d601f01444be7d9f405df18c57be0316a1c79
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2018
ms.locfileid: "51516116"
---
<!-- put the ## header in the file that includes this file -->

En esta sección, creará una identidad de dispositivo en el registro de identidades del centro de IoT. No se puede conectar un dispositivo a IoT Hub a menos que tenga una entrada en el registro de identidades. Para más información, consulte la sección "Registro de identidades" de la [Guía para desarrolladores de IoT Hub](../articles/iot-hub/iot-hub-devguide-identity-registry.md) 

1. En el menú de navegación del centro de IoT, abra **Dispositivos IoT** y, a continuación, haga clic en **Agregar** para registrar un dispositivo en el centro de IoT.

    ![Creación de la identidad del dispositivo en el portal](./media/iot-hub-include-create-device/create-identity-portal.png)

1. Proporcione un nombre para el dispositivo nuevo, como **myDeviceId**, y haga clic en **Guardar**. Esta acción crea una nueva identidad de dispositivo para su instancia de IoT Hub.

   ![Agregar un nuevo dispositivo](./media/iot-hub-include-create-device/create-a-device.png)

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]


1. Después de crear el dispositivo, ábralo desde la lista del panel **Dispositivos de IoT**. Copie el valor de **Cadena de conexión: clave principal** para usarlo más adelante.

    ![Cadena de conexión de dispositivo](./media/iot-hub-include-create-device/device-details.png)

> [!NOTE]
> El registro de identidades de IoT Hub solo almacena identidades de dispositivos para permitir el acceso seguro al centro de IoT. Almacena las claves y los identificadores de dispositivo para usarlos como credenciales de seguridad, y un indicador de habilitado o deshabilitado que permite deshabilitar el acceso a un dispositivo individual. Si la aplicación necesita almacenar otros metadatos específicos del dispositivo, debe usar un almacén específico de la aplicación. Consulte la [guía de desarrolladores de IoT Hub](../articles/iot-hub/iot-hub-devguide-identity-registry.md) para obtener más información.
