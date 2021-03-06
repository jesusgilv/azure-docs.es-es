---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 792589f4aa2a80c05378224ffe4e4d1dad2f935c
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/26/2018
ms.locfileid: "50164733"
---
<!--author=alkohli last changed:02/22/16-->

#### <a name="to-attach-the-sas-cables"></a>Conexión de los cables SAS
1. Identifique los receptáculos principal y EBOD Los dos alojamientos pueden identificarse examinando sus respectivos planos posteriores. Consulta la siguiente imagen para obtener instrucciones. 
   
    ![Plano posterior del alojamiento principal y de EBOD](./media/storsimple-sas-cable-8600/HCSBackplaneofprimaryandEBODenclosure.png)
   
    **Vista posterior del alojamiento principal y de EBOD**
   
   | Etiqueta | DESCRIPCIÓN |
   |:--- |:--- |
   | 1 |Receptáculo principal |
   | 2 |Receptáculo EBOD |
2. Busca los números de serie del alojamiento principal y de EBOD. La etiqueta del número de serie está colocada en el dispositivo de escucha posterior de cada alojamiento. Los números de serie deben ser idénticos en ambos receptáculos. [Contactar al servicio técnico de Microsoft](../articles/storsimple/storsimple-contact-microsoft-support.md) inmediatamente si no coinciden los números de serie. Consulta la siguiente ilustración para localizar los números de serie.
   
    ![Vista trasera del alojamiento que muestra el número de serie](./media/storsimple-sas-cable-8600/HCSRearviewofenclosureindicatinglocationofserialnumbersticker.png)
   
    **Ubicación de la etiqueta del número de serie**
   
   | Etiqueta | DESCRIPCIÓN |
   |:--- |:--- |
   | 1 |Dispositivo de escucha del receptáculo |
3. Después utiliza los cables SAS proporcionados para conectar el alojamiento de EBOD al alojamiento principal de esta manera:
   
   1. Identifica los cuatro puertos SAS en el alojamiento principal y en el alojamiento de EBOD. Los puertos SAS se etiquetan como EBOD en el alojamiento principal y se corresponden con el puerto A en el alojamiento de EBOD, tal y como se muestra en la ilustración del cableado de SAS.
   2. Use los cables SAS proporcionados para conectar el puerto EBOD al puerto A.
   3. El puerto EBOD del controlador 0 debe estar conectado al puerto A del controlador EBOD 0. El puerto EBOD del controlador 1 debe estar conectado al puerto A del controlador EBOD 1. Vea la ilustración siguiente como guía. 
      
      ![Cableado SAS para tu dispositivo](./media/storsimple-sas-cable-8600/HCSSAScablingforyourdevice.png)
      
      **Cableado de SAS**
      
      | Etiqueta | DESCRIPCIÓN |
      |:--- |:--- |
      | Encontrará |Receptáculo principal |
      | b |Receptáculo EBOD |
      | 1 |Controlador 0 |
      | 2 |Controlador 1 |
      | 3 |Controlador EBOD 0 |
      | 4 |Controlador EBOD 1 |
      | 5, 6 |Puertos SAS del receptáculo principal (con la etiqueta EBOD) |
      | 7, 8 |Puertos SAS del receptáculo EBOD (puerto A) |

