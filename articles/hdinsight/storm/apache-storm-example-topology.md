---
title: Topologías de Apache Storm de ejemplo en Azure HDInsight
description: Una lista de topologías de ejemplo de Storm creada y probada con Apache Storm en HDInsight, incluidas las topologías básicas de C# y Java, y trabajando con Event Hubs.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/30/2018
ms.openlocfilehash: 985bb755e4754d2aa371d7b86594297c16fedd27
ms.sourcegitcommit: 56d20d444e814800407a955d318a58917e87fe94
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/29/2018
ms.locfileid: "52583574"
---
# <a name="example-apache-storm-topologies-and-components-for-apache-storm-on-hdinsight"></a>Topologías y componentes de ejemplo de Apache Storm para Apache Storm en HDInsight

La siguiente es una lista de ejemplos creada y mantenida por Microsoft para su uso con [Apache Storm](http://storm.apache.org/) en HDInsight. Estos ejemplos tratan diversos temas, desde la creación de topologías básicas en C# y Java para trabajar con servicios de Azure como Event Hubs, Cosmos DB, SQL Database, [Apache HBase](http://hbase.apache.org/) en HDInsight y Azure Storage. Algunos ejemplos también muestran cómo trabajar con tecnologías que no son de Azure, o incluso que no son de Microsoft, como SignalR y Socket.IO.

| DESCRIPCIÓN | Muestra | Lenguaje/Marco de trabajo |
|:--- |:--- |:--- |
| [Escritura en el Almacén de Azure Data Lake desde Apache Storm](apache-storm-write-data-lake-store.md) |Escritura en el Almacén de Azure Data Lake |Java |
| [Origen de spout y bolt de Centro de eventos](https://github.com/apache/storm/tree/master/external/storm-eventhubs) |Origen de spout y bolt de Centro de eventos |Java |
| [Desarrollo de topologías basadas en Java para Apache Storm en HDInsight][5797064f] |Maven |Java |
| [Desarrollo de topologías de C# para Apache Storm en HDInsight con Visual Studio][16fce2d1] |Herramientas de HDInsight para Visual Studio |C#, Java |
| [Procesamiento de eventos desde Azure Event Hubs con Apache Storm en HDInsight (C#)][844d1d81] |Event Hubs |C# y Java |
| [Procesamiento de eventos desde Azure Event Hubs con Storm en HDInsight (Java)](https://azure.microsoft.com/resources/samples/hdinsight-java-storm-eventhub/) |Event Hubs |Java |
| [Procesamiento de datos de sensor de vehículos desde Event Hubs mediante Apache Storm en HDInsight][246ee964] |Event Hubs, Cosmos DB y Azure Storage Blob (WASB) |C#, Java |
| [Extracción, transformación y carga (ETL) desde Azure Event Hubs en Apache HBase usando Apache Storm en HDInsight][b4b68194] |Event Hubs, HBase |C# |
| [Proyecto de topología de Storm en C# para plantillas para trabajar con servicios de Azure desde Apache Storm en HDInsight][ce0c02a2] |Event Hubs, Cosmos DB, SQL Database, HBase y SignalR |C#, Java |
| [Pruebas comparativas de escalabilidad para leer desde Azure Event Hubs usando Apache Storm en HDInsight][d6c540e3] |Rendimiento de mensajes, Event Hubs, SQL Database |C#, Java |
| [Uso de Python con Apache Storm en HDInsight](apache-storm-develop-python-topology.md) |Componentes de Python con una topología Flux |Python |
| [Uso de Apache Kafka con Apache Storm en HDInsight](../hdinsight-apache-storm-with-kafka.md) | Lectura y escritura de Apache Storm en Apache Kafka | Java |

> [!WARNING]
> Los ejemplos de C# de esta lista se crearon y probaron originalmente con HDInsight para Windows y puede que no funcionen correctamente con los clústeres de HDInsight para Linux. Los clústeres para Linux utilizan Mono para ejecutar código .NET y pueden tener problemas de compatibilidad con los entornos y paquetes utilizados en el ejemplo.
>
> Linux es el único sistema operativo que se usa en la versión 3.4 de HDInsight, o en las versiones posteriores.

### <a name="next-steps"></a>Pasos siguientes

* [Introducción a Apache Storm en HDInsight][2b8c3488]
* [Implementación y administración de topologías de Apache Storm con Apache Storm en HDInsight][6eb0d3b8]

[2b8c3488]:apache-storm-tutorial-get-started-linux.md "Aprenda a crear un clúster de Apache Storm en HDInsight y a utilizar el panel de Storm para implementar topologías de ejemplo."
[6eb0d3b8]:apache-storm-deploy-monitor-topology.md "Aprenda a implementar y administrar topologías mediante el panel de Apache Storm basado en web y la interfaz de usuario de Storm o las Herramientas de HDInsight para Visual Studio."
[16fce2d1]:apache-storm-develop-csharp-visual-studio-topology.md "Aprenda a crear topologías de Storm en C# mediante las Herramientas de HDInsight para Visual Studio."
[5797064f]:apache-storm-develop-java-topology.md "Aprenda a crear topologías de Storm en Java mediante Maven creando una topología de recuento de palabras básica."
[844d1d81]:apache-storm-develop-csharp-event-hub-topology.md "Aprenda a leer y escribir datos desde Azure Event Hubs con Storm en HDInsight."
[246ee964]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/IotExample/README.md "Aprenda a usar una topología de Storm para leer mensajes desde Azure Event Hubs, leer documentos de Azure Cosmos DB para hacer referencia a datos y guardar datos en Azure Storage."
[d6c540e3]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/EventCountExample "Varias topologías para mostrar el rendimiento cuando se lee desde Azure Event Hubs y se realiza el almacenamiento en SQL Database mediante Apache Storm en HDInsight."
[b4b68194]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/RealTimeETLExample "Aprenda a leer datos desde Azure Event Hubs, a agregar y transformar los datos y, a continuación, a almacenarlos en HBase en HDInsight."
[ce0c02a2]: https://github.com/hdinsight/hdinsight-storm-examples/tree/master/templates/HDInsightStormExamples "Este proyecto contiene plantillas para spouts, bolts y topologías para interactuar con distintos servicios de Azure como Event Hubs, Cosmos DB y SQL Database."

