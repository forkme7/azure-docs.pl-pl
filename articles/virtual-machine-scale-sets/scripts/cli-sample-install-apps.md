---
title: Przykłady interfejsu wiersza polecenia platformy Azure w wersji 2.0 — Instalowanie aplikacji | Microsoft Docs
description: Przykłady interfejsu wiersza polecenia platformy Azure w wersji 2.0
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 22c2f8d811da3dfc565ff32cb602e78b1097ffab
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="install-applications-into-a-virtual-machine-scale-set-with-the-azure-cli-20"></a>Instalowanie aplikacji w zestawie skalowania maszyn wirtualnych za pomocą interfejsu wiersza polecenia platformy Azure w wersji 2.0
Ten skrypt tworzy zestaw skalowania maszyn wirtualnych z systemem Ubuntu i używa rozszerzenia niestandardowego skryptu w celu zainstalowania podstawowej aplikacji internetowej. Po uruchomieniu skryptu możesz uzyskać dostęp do aplikacji internetowej za pośrednictwem przeglądarki internetowej.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Przykładowy skrypt
[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine-scale-sets/install-apps/install-apps.sh "Install apps into a scale set")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia
Uruchom następujące polecenie, aby usunąć grupę zasobów, zestaw skalowania i wszystkie powiązane zasoby.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu
Ten skrypt zawiera następujące polecenia służące do tworzenia grupy zasobów, zestawu skalowania maszyn wirtualnych i wszystkich powiązanych zasobów. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [az group create](/cli/azure/ad/group#az_ad_group_create) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [az vmss create](/cli/azure/vmss#az_vmss_create) | Tworzy zestaw skalowania maszyn wirtualnych i łączy go z siecią wirtualną, podsiecią i sieciową grupą zabezpieczeń. Zostanie też utworzony moduł równoważenia obciążenia w celu dystrybucji ruchu do wielu wystąpień maszyn wirtualnych. To polecenie określa również obraz maszyny wirtualnej do użycia oraz poświadczenia administracyjne.  |
| [az vmss extension set](/cli/azure/vmss/extension#az_vmss_extension_set) | Instaluje rozszerzenie niestandardowego skryptu platformy Azure w celu uruchomienia skryptu, który przygotowuje dyski danych w każdym wystąpieniu maszyny wirtualnej. |
| [az network lb rule create](/cli/azure/network/lb/rule#az_network_lb_rule_create) | Tworzy regułę równoważenia obciążenia w celu dystrybucji ruchu na porcie 80 protokołu TCP do wystąpień maszyn wirtualnych w zestawie skalowania. |
| [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show) | Pobiera informacje o przypisanym publicznym adresie IP używanym przez moduł równoważenia obciążenia. |
| [az group delete](/cli/azure/ad/group#delete) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure w wersji 2.0, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure w wersji 2.0](https://docs.microsoft.com/cli/azure/overview).

Dodatkowe przykłady skryptów interfejsu wiersza polecenia platformy Azure w wersji 2.0 dotyczących zestawu skalowania maszyn wirtualnych można znaleźć w [dokumentacji zestawu skalowania maszyn wirtualnych platformy Azure](../cli-samples.md).
