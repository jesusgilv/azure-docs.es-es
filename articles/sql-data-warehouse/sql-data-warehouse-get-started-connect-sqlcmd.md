---
title: Conexión a Azure SQL Data Warehouse sqlcmd | Microsoft Docs
description: Use la utilidad de línea de comandos [sqlcmd][sqlcmd] para conectar y consultar una instancia de Azure SQL Data Warehouse.
services: sql-data-warehouse
author: kavithaj
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: consume
ms.date: 04/17/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: 94f3955f9ce94fa52e89180fa649c4e412b80109
ms.sourcegitcommit: 2b2129fa6413230cf35ac18ff386d40d1e8d0677
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/30/2018
ms.locfileid: "43247720"
---
# <a name="connect-to-sql-data-warehouse-with-sqlcmd"></a>Conexión a SQL Data Warehouse con sqlcmd
> [!div class="op_single_selector"]
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

Use la utilidad de línea de comandos [sqlcmd][sqlcmd] para conectarse a Azure SQL Data Warehouse y realizar consultas.  

## <a name="1-connect"></a>1. Conectar
Para empezar a trabajar con [sqlcmd][sqlcmd], abra el símbolo del sistema y escriba **sqlcmd**, seguido de la cadena de conexión de la base de datos de SQL Data Warehouse. La cadena de conexión requiere los siguientes parámetros:

* **Server (-S):** servidor con el formato `<`Nombre del servidor`>`.database.windows.net
* **Base de datos (-d):** nombre de la base de datos.
* **Habilitar identificadores entre comillas (-I):** los identificadores entre comillas deben estar habilitados para poder conectarse a una instancia de SQL Data Warehouse.

Para utilizar la autenticación de SQL Server, debe agregar los parámetros de nombre de usuario y contraseña:

* **Usuario (-U):** usuario de servidor con el formato `<`usuario`>`
* **Contraseña (-P):** la contraseña asociada con el usuario.

Por ejemplo, la cadena de conexión podría ser similar a la siguiente:

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
```

Para usar la autenticación integrada de Azure Active Directory, debe agregar los parámetros de Azure Active Directory:

* **Autenticación de Azure Active Directory (-G):** use Azure Active Directory para la autenticación.

Por ejemplo, la cadena de conexión podría ser similar a la siguiente:

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -G -I
```

> [!NOTE]
> Necesita [habilitar la autenticación de Azure Active Directory](sql-data-warehouse-authentication.md) para autenticarse con Active Directory.
> 
> 

## <a name="2-query"></a>2. Consultar
Después de la conexión, puede emitir cualquier instrucción Transact-SQL en la instancia.  En este ejemplo, las consultas se envían en modo interactivo.

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
1> SELECT name FROM sys.tables;
2> GO
3> QUIT
```

Los siguientes ejemplos muestran cómo se pueden ejecutar las consultas en el modo por lotes con la opción -Q o mediante la canalización de su SQL a sqlcmd.

```sql
sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I -Q "SELECT name FROM sys.tables;"
```

```sql
"SELECT name FROM sys.tables;" | sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I > .\tables.out
```

## <a name="next-steps"></a>Pasos siguientes
Para más información sobre las opciones disponibles en sqlcmd, consulte la [documentación de sqlcmd][sqlcmd].

<!--Image references-->

<!--Article references-->

<!--MSDN references--> 
[sqlcmd]: https://msdn.microsoft.com/library/ms162773.aspx
[Azure portal]: https://portal.azure.com

<!--Other Web references-->
