---
title: Przykład programu PowerShell — grupa trybu failover — pojedyncza baza danych Azure SQL Database | Microsoft Docs
description: Przykładowy skrypt programu Azure PowerShell do konfigurowania aktywnej grupy trybu failover replikacji geograficznej dla pojedynczej bazy danych Azure SQL i przełączania jej w tryb failover.
services: sql-database
documentationcenter: sql-database
author: janeng
manager: jstrauss
editor: carlrab
tags: azure-service-management
ms.assetid: ''
ms.service: sql-database
ms.custom: business continuity, mvc
ms.devlang: PowerShell
ms.topic: sample
ms.tgt_pltfrm: sql-database
ms.workload: database
ms.date: 04/01/2018
ms.author: janeng
ms.openlocfilehash: 5217063dc99868be6777b09042e5aeb1fe9640a2
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="use-powershell-to-configure-an-active-geo-replication-failover-group-for-a-single-azure-sql-database"></a>Konfigurowanie aktywnej grupy trybu failover replikacji geograficznej dla pojedynczej bazy danych Azure SQL Database przy użyciu programu PowerShell

Ten przykładowy skrypt programu PowerShell umożliwia skonfigurowanie aktywnej grupy trybu failover replikacji geograficznej dla pojedynczej bazy danych Azure SQL Database i przełączenie jej w tryb failover do repliki pomocniczej bazy danych Azure SQL Database.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-scripts"></a>Przykładowe skrypty

[!code-powershell[main](../../../powershell_scripts/sql-database/setup-geodr-and-failover-database/setup-geodr-and-failover-database-failover-group.ps1?highlight=19-22 "Set up failover group for single database")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia

Po wykonaniu przykładowego skryptu możesz uruchomić następujące polecenie, aby usunąć grupę zasobów i wszystkie skojarzone z nią zasoby.

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName $primaryresourcegroupname
Remove-AzureRmResourceGroup -ResourceGroupName $secondaryresourcegroupname
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [New-AzureRmSqlServer](/powershell/module/azurerm.sql/new-azurermsqlserver) | Tworzy serwer logiczny hostujący bazę danych lub pulę elastyczną. |
| [New-AzureRmSqlElasticPool](/powershell/module/azurerm.sql/new-azurermsqlelasticpool) | Tworzy pulę elastyczną w ramach serwera logicznego. |
| [Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase) | Aktualizuje właściwości bazy danych lub przenosi informacje o bazie danych do pul elastycznych, poza nie lub między nimi. |
| [New-AzureRmSqlDatabaseSecondary](/powershell/module/azurerm.sql/new-azurermsqldatabasesecondary)| Tworzy pomocniczą bazę danych dla istniejącej bazy danych i rozpoczyna replikację danych. |
| [Get-AzureRmSqlDatabase](/powershell/module/azurerm.sql/get-azurermsqldatabase)| Pobiera co najmniej jedną bazę danych. |
| [Set-AzureRmSqlDatabaseSecondary](/powershell/module/azurerm.sql/set-azurermsqldatabasesecondary)| Przełącza pomocniczą bazę danych jako główną w celu zainicjowania trybu failover.|
| [Get-AzureRmSqlDatabaseReplicationLink](/powershell/module/azurerm.sql/get-azurermsqldatabasereplicationlink) | Pobiera linki replikacji geograficznej między bazą danych Azure SQL Database i grupą zasobów lub programem SQL Server. |
| [Remove-AzureRmSqlDatabaseSecondary](/powershell/module/azurerm.sql/remove-azurermsqldatabasesecondary) | Przerywa replikację danych między bazą danych SQL Database i wybraną pomocniczą bazą danych. |
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |
| [New-AzureRMSqlDatabaseFailoverGroup](/powershell/module/azurerm.sql/new-azurermsqldatabasefailovergroup) | Tworzy nową grupę trybu failover usługi Azure SQL Database dla wybranych serwerów. |
| [Switch-AzureRMSqlDatabaseFailoverGroup](/powershell/module/azurerm.sql/switch-azurermsqldatabasefailovergroup) | Zamienia role serwerów w grupie trybu failover i przełącza wszystkie pomocnicze bazy danych do roli głównej. |
| [Get-AzureRMSqlDatabaseFailoverGroup](/powershell/module/azurerm.sql/get-azurermsqldatabasefailovergroup) | Pobiera określoną grupę trybu failover bazy danych Azure SQL Database lub wyświetla listę grup trybu failover na serwerze. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat programu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](/powershell/azure/overview).

Więcej przykładowych skryptów programu PowerShell dla usługi SQL Database można znaleźć w [skryptach programu PowerShell dla usługi Azure SQL Database](../sql-database-powershell-samples.md).
