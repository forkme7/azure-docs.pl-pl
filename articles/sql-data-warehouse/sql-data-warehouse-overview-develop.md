---
title: Zasoby na potrzeby tworzenia magazynu danych na platformie Azure | Dokumentacja firmy Microsoft
description: Pojęcia dotyczące programowania, decyzji projektowych i zalecenia dotyczące technik programowania dla usługi SQL Data Warehouse.
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: barbkess
editor: ''
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: develop
ms.date: 03/22/2018
ms.author: jrj;barbkess
ms.openlocfilehash: c81a6f9af556a86f10d2482333fdc98f9cfb1454
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="design-decisions-and-coding-techniques-for-sql-data-warehouse"></a>Decyzje dotyczące projektu i technik programowania dla usługi SQL Data Warehouse
Zapoznaj się za pośrednictwem tych artykułach programowanie, aby lepiej zrozumieć kluczowych decyzji projektowych, zalecenia i technik programowania dla usługi SQL Data Warehouse.

## <a name="key-design-decisions"></a>Kluczowych decyzji projektowych
Następujące artykuły Wyróżnij pojęcia i decyzje dotyczące projektu związane z opracowywaniem magazynu danych rozproszonych za pomocą usługi SQL Data Warehouse:

* [Połączenia][connections]
* [concurrency][concurrency]
* [Transakcje][transactions]
* [Schematy zdefiniowane przez użytkownika][user-defined schemas]
* [Dystrybucja tabeli][table distribution]
* [indeksy tabel][table indexes]
* [partycje tabeli][table partitions]
* [CTAS][CTAS]
* [Statystyki][statistics]

## <a name="development-recommendations-and-coding-techniques"></a>Zalecenia dotyczące tworzenia i technik programowania
Te artykuły wyróżnianie określonych technik programowania, wskazówki i zalecenia dotyczące Tworzenie magazynu danych SQL:

* [Procedury składowane][stored procedures]
* [labels][labels]
* [Widoki][views]
* [tabele tymczasowe][temporary tables]
* [dynamic SQL][dynamic SQL]
* [looping][looping]
* [Grupuj według opcje][group by options]
* [Przypisanie zmiennej][variable assignment]

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacje, zobacz [instrukcji SQL Data Warehouse T-SQL](sql-data-warehouse-reference-tsql-statements.md).

<!--Image references-->

<!--Article references-->
[concurrency]: ./resource-classes-for-workload-management.md
[connections]: ./sql-data-warehouse-connect-overview.md
[CTAS]: ./sql-data-warehouse-develop-ctas.md
[dynamic SQL]: ./sql-data-warehouse-develop-dynamic-sql.md
[group by options]: ./sql-data-warehouse-develop-group-by-options.md
[labels]: ./sql-data-warehouse-develop-label.md
[looping]: ./sql-data-warehouse-develop-loops.md
[statistics]: ./sql-data-warehouse-tables-statistics.md
[stored procedures]: ./sql-data-warehouse-develop-stored-procedures.md
[table distribution]: ./sql-data-warehouse-tables-distribute.md
[table indexes]: ./sql-data-warehouse-tables-index.md
[table partitions]: ./sql-data-warehouse-tables-partition.md
[temporary tables]: ./sql-data-warehouse-tables-temporary.md
[transactions]: ./sql-data-warehouse-develop-transactions.md
[user-defined schemas]: ./sql-data-warehouse-develop-user-defined-schemas.md
[variable assignment]: ./sql-data-warehouse-develop-variable-assignment.md
[views]: ./sql-data-warehouse-develop-views.md


<!--MSDN references-->
[renaming objects]: https://msdn.microsoft.com/library/mt631611.aspx

<!--Other Web references-->
