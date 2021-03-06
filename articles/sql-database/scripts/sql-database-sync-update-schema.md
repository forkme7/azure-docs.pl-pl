---
title: Przykład z programu PowerShell — aktualizowanie schematu synchronizacji funkcji SQL Data Sync (wersja zapoznawcza) | Microsoft Docs
description: Przykładowy skrypt z programu Azure PowerShell służący do aktualizowania schematu synchronizacji dla funkcji SQL Data Sync
services: sql-database
documentationcenter: sql-database
author: jognanay
manager: craigg
editor: ''
tags: ''
ms.assetid: ''
ms.service: sql-database
ms.custom: load & move data, mvc
ms.devlang: PowerShell
ms.topic: sample
ms.tgt_pltfrm: sql-database
ms.workload: database
ms.date: 01/10/2018
ms.author: jognanay
ms.reviewer: douglasl
ms.openlocfilehash: f306eba91adf574f8bb20b2aa459f890b97bb732
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
---
# <a name="use-powershell-to-update-the-sync-schema-in-an-existing-sync-group"></a>Używanie programu PowerShell do zaktualizowania schematu synchronizacji w istniejącej grupie synchronizacji

Ten przykład z programu PowerShell służy do aktualizacji schematu synchronizacji w istniejącej grupie synchronizacji funkcji SQL Data Sync (wersja zapoznawcza). Podczas synchronizowania wielu tabel ten skrypt ułatwia wydajnie aktualizowanie schematu synchronizacji.

W tym przykładzie przedstawiono użycie skryptu **UpdateSyncSchema**, który jest dostępny w witrynie GitHub jako [UpdateSyncSchema.ps1](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/sql-data-sync/UpdateSyncSchema.ps1).

Omówienie usługi SQL Data Sync zawiera temat [Sync data across multiple cloud and on-premises databases with Azure SQL Data Sync (Preview) (Synchronizowanie danych między wieloma bazami danych w chmurze i lokalnie za pomocą usługi Azure SQL Data Sync — wersja zapoznawcza)](../sql-database-sync-data.md).
## <a name="prerequisites"></a>Wymagania wstępne

Ten przykładowy skrypt wymaga modułu Azure PowerShell w wersji 4.2 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable AzureRM`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps).
 
Uruchom polecenie `Connect-AzureRmAccount`, aby utworzyć połączenia z platformą Azure.

## <a name="examples"></a>Przykłady

### <a name="example-1---add-all-tables-to-the-sync-schema"></a>Przykład 1 — dodawanie wszystkich tabel do schematu synchronizacji

Poniższy przykład służy do odświeżania schematu bazy danych i dodawania wszystkich prawidłowych tabel w bazie danych centrum do schematu synchronizacji.

```powershell
UpdateSyncSchema.ps1 -SubscriptionId <subscription_id> -ResourceGroupName <resource_group_name> -ServerName <server_name> -DatabaseName <database_name> -SyncGroupName <sync_group_name> -RefreshDatabaseSchema $true -AddAllTables $true
```

### <a name="example-2---add-and-remove-tables-and-columns"></a>Przykład 2 — dodawanie oraz usuwanie tabel i kolumn

Poniższy przykład służy do dodawania tabel `[dbo].[Table1]` i `[dbo].[Table2].[Column1]` do schematu synchronizacji oraz usuwania tabeli `[dbo].[Table3]`.

```powershell
UpdateSyncSchema.ps1 -SubscriptionId <subscription_id> -ResourceGroupName <resource_group_name> -ServerName <server_name> -DatabaseName <database_name> -SyncGroupName <sync_group_name> -TablesAndColumnsToAdd "[dbo].[Table1],[dbo].[Table2].[Column1]" -TablesAndColumnsToRemove "[dbo].[Table3]"
```

## <a name="script-parameters"></a>Parametry skryptu

Skrypt **UpdateSyncSchema** ma następujące parametry:

| Parametr | Uwagi |
|---|---|
| $SubscriptionId | Subskrypcja, w ramach której tworzona jest grupa synchronizacji. |
| $ResourceGroupName | Grupa zasobów, w ramach której tworzona jest grupa synchronizacji.|
| $ServerName | Nazwa serwera bazy danych centrum.|
| $DatabaseName | Nazwa bazy danych centrum. |
| $SyncGroupName | Nazwa grupy synchronizacji. |
| $MemberName | Określ nazwę elementu członkowskiego, jeśli schemat bazy danych ma zostać załadowany z elementu członkowskiego synchronizacji zamiast z bazy danych centrum. Jeśli schemat bazy danych ma zostać załadowany z centrum, pozostaw ten parametr pusty. |
| $TimeoutInSeconds | Limit czasu odświeżania schematu bazy danych przez skrypt. Wartość domyślna to 900 sekund. |
| $RefreshDatabaseSchema | Określ, czy skrypt ma odświeżać schemat bazy danych. Jeśli schemat bazy danych został zmieniony względem poprzedniej konfiguracji, na przykład jeśli dodano nową tabelę lub od nową kolumnę, należy odświeżyć schemat przed jego ponownym skonfigurowaniem. Wartość domyślna to false. |
| $AddAllTables | Jeśli ta wartość to true, wszystkie prawidłowe tabele i kolumny zostaną dodane do schematu synchronizacji. Wartości parametrów $TablesAndColumnsToAdd i $TablesAndColumnsToRemove są ignorowane. |
| $TablesAndColumnsToAdd | Określ tabelę lub kolumny do dodania do schematu synchronizacji. Wszystkie nazwy tabel lub kolumn muszą być w pełni rozdzielone od nazwy schematu. Przykład: `[dbo].[Table1]`, `[dbo].[Table2].[Column1]`. Można określić wiele nazw tabel lub kolumn i rozdzielić je przecinkami (,). |
| $TablesAndColumnsToRemove | Określ tabele lub kolumny do usunięcia ze schematu synchronizacji. Wszystkie nazwy tabel lub kolumn muszą być w pełni rozdzielone od nazwy schematu. Przykład: `[dbo].[Table1]`, `[dbo].[Table2].[Column1]`. Można określić wiele nazw tabel lub kolumn i rozdzielić je przecinkami (,). |
|||

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Skrypt **UpdateSyncSchema** używa następujących poleceń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [Get-AzureRmSqlSyncGroup](https://docs.microsoft.com/powershell/module/azurerm.sql/get-azurermsqlsyncgroup) | Zwraca informacje o grupie synchronizacji. |
| [Update-AzureRmSqlSyncGroup](https://docs.microsoft.com/powershell/module/azurerm.sql/update-azurermsqlsyncgroup) | Aktualizuje grupę synchronizacji. |
| [Get-AzureRmSqlSyncMember](https://docs.microsoft.com/powershell/module/azurerm.sql/get-azurermsqlsyncmember) | Zwraca informacje o składniku synchronizacji. |
| [Get-AzureRmSqlSyncSchema](https://docs.microsoft.com/powershell/module/azurerm.sql/get-azurermsqlsyncschema) | Zwraca informacje o schemacie synchronizacji. |
| [Update-AzureRmSqlSyncSchema](https://docs.microsoft.com/powershell/module/azurerm.sql/update-azurermsqlsyncschema) | Aktualizuje schemat synchronizacji. |
|||

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat programu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](/powershell/azure/overview).

Więcej przykładowych skryptów programu PowerShell dla usługi SQL Database można znaleźć w [skryptach programu PowerShell dla usługi Azure SQL Database](../sql-database-powershell-samples.md).

Aby uzyskać więcej informacji na temat usługi SQL Data Sync, zobacz:

-   [Sync data across multiple cloud and on-premises databases with Azure SQL Data Sync (Synchronizowanie danych między wieloma bazami danych w chmurze i lokalnie za pomocą usługi Azure SQL Data Sync)](../sql-database-sync-data.md)
-   [Set up Azure SQL Data Sync (Konfigurowanie usługi Azure SQL Data Sync)](../sql-database-get-started-sql-data-sync.md)
-   [Best practices for Azure SQL Data Sync (Najlepsze rozwiązania dotyczące korzystania z usługi Azure SQL Data Sync)](../sql-database-best-practices-data-sync.md)
-   [Monitor Azure SQL Data Sync with Log Analytics (Monitorowanie usługi Azure SQL Data Sync za pomocą usługi Log Analytics)](../sql-database-sync-monitor-oms.md)
-   [Troubleshoot issues with Azure SQL Data Sync (Rozwiązywanie problemów z usługą Azure SQL Data Sync)](../sql-database-troubleshoot-data-sync.md)

-   Pełne przykładowe skrypty programu PowerShell przedstawiające sposób konfigurowania usługi SQL Data Sync:
    -   [Użycie programu PowerShell do synchronizowania wielu baz danych Azure SQL Database](sql-database-sync-data-between-sql-databases.md)
    -   [Use PowerShell to sync between an Azure SQL Database and a SQL Server on-premises database (Synchronizacja bazy danych usługi Azure SQL i lokalnej bazy danych programu SQL Server przy użyciu programu PowerShell)](sql-database-sync-data-between-azure-onprem.md)

-   [Pobierz dokumentację interfejsu API REST usługi SQL Data Sync](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_REST_API.pdf?raw=true)

Aby uzyskać więcej informacji na temat usługi SQL Database, zobacz:

-   [Omówienie usługi SQL Database](../sql-database-technical-overview.md)
-   [Database Lifecycle Management (Zarządzanie cyklem życia bazy danych)](https://msdn.microsoft.com/library/jj907294.aspx)
