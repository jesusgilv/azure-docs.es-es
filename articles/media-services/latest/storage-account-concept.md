---
title: Cuentas de almacenamiento en Azure Media Services | Microsoft Docs
description: En este artículo se describe cómo Media Services usa las cuentas de almacenamiento.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 10/16/2018
ms.author: juliako
ms.openlocfilehash: dbd8dea32bbd4cacefd64e91541ab20b1056731e
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/17/2018
ms.locfileid: "49379047"
---
# <a name="storage-accounts"></a>Cuentas de almacenamiento

Al crear una cuenta de Media Services, debe proporcionar el nombre de un recurso de cuenta de Azure Storage. La cuenta de almacenamiento especificada está asociada a su cuenta de Media Services. 

Debe tener una cuenta de almacenamiento **Principal** y puede tener cualquier número de cuentas de almacenamiento **Secundarias** asociadas a su cuenta de Media Services. Media Services admite cuentas de **Uso general v2** (GPv2) o **Uso general v1** (GPv1). 

>[!NOTE]
> No se permiten cuentas de solo BLOB como **Principal**. 

Se recomienda usar GPv2 para que pueda tener la ventaja de elegir entre niveles de acceso frecuente y niveles de acceso esporádico. Para más información sobre las cuentas de almacenamiento, consulte la [Información general acerca de la cuenta de Azure Storage](../../storage/common/storage-account-overview.md). 

## <a name="assets-in-a-storage-account"></a>Recursos de una cuenta de almacenamiento

En Media Services v3, las API de Storage se usan para cargar archivos. Para ver cómo usar las API de Storage con Media Services para cargar los archivos de entrada, consulte el artículo sobre la [creación de una entrada de trabajo a partir de un archivo local](job-input-from-local-file-how-to.md). 

> [!Note]
> No debe intentar cambiar el contenido de los contenedores de blobs que generó el SDK de Media Services sin usar las API de Media Services.

## <a name="next-steps"></a>Pasos siguientes

Para información sobre cómo adjuntar una cuenta de almacenamiento a la cuenta de Media Services, consulte [Creación de una cuenta](create-account-cli-quickstart.md).
