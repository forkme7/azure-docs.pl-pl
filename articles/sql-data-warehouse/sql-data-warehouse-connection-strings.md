---
title: Parametry połączenia dla usługi Azure SQL Data Warehouse | Dokumentacja firmy Microsoft
description: Parametry połączenia dla magazynu danych SQL
services: sql-data-warehouse
author: ronortloff
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/12/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 3445de83ff29ecf60cbd6d021b431f444284858c
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="connection-strings-for-azure-sql-data-warehouse"></a>Parametry połączenia dla usługi Azure SQL Data Warehouse
Usługa SQL Data Warehouse można nawiązać z kilku różnych aplikacji protokołów takich, jak [ADO.NET][ADO.NET], [ODBC][ODBC], [PHP] [ PHP] i [JDBC][JDBC]. Poniżej przedstawiono kilka przykładów ciągi połączenia dla każdego protokołu.  Azure portal umożliwia również tworzenie parametrów połączenia.  Aby utworzyć parametry połączenia przy użyciu portalu Azure, przejdź do bloku bazy danych, w obszarze *Essentials* kliknij *Pokaż parametry połączenia bazy danych*.

## <a name="sample-adonet-connection-string"></a>Parametry połączenia ADO.NET próbki
```csharp
Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

## <a name="sample-odbc-connection-string"></a>Ciąg połączenia ODBC próbki
```csharp
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

## <a name="sample-php-connection-string"></a>Parametry połączenia PHP próbki
```PHP
Server: {your_server}.database.windows.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( \"sqlsrv:server = tcp:{your_server}.database.windows.net,1433; Database = {your_database}\", \"{your_user_name}\", \"{your_password_here}\");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( \"Error connecting to SQL Server.\" );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array(\"UID\" => \"{your_user_name}\", \"pwd\" => \"{your_password_here}\", \"Database\" => \"{your_database}\", \"LoginTimeout\" => 30, \"Encrypt\" => 1, \"TrustServerCertificate\" => 0);\r\n$serverName = \"tcp:{your_server}.database.windows.net,1433\";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

## <a name="sample-jdbc-connection-string"></a>Parametry połączenia JDBC próbki
```Java
jdbc:sqlserver://yourserver.database.windows.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
```

> [!NOTE]
> Rozważ ustawienie Limit czasu połączenia do 300 sekund, aby zezwalały na połączenie przetrwać krótkich okresach niedostępności.
> 
> 

## <a name="next-steps"></a>Kolejne kroki
Aby uruchomić zapytanie magazynu danych z programu Visual Studio i innymi aplikacjami, zobacz [zapytania z programem Visual Studio][Query with Visual Studio].

<!--Image references-->

<!--Azure.com references-->
[Query with Visual Studio]: ./sql-data-warehouse-query-visual-studio.md

<!--MSDN references-->
[ADO.NET]: https://msdn.microsoft.com/library/e80y5yhx(v=vs.110).aspx
[ODBC]: https://msdn.microsoft.com/library/jj730314.aspx
[PHP]: https://msdn.microsoft.com/library/cc296172.aspx?f=255&MSPPError=-2147217396
[JDBC]: https://msdn.microsoft.com/library/mt484311(v=sql.110).aspx

<!--Other references-->
