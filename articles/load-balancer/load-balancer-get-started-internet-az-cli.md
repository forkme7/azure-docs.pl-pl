---
title: Utwórz publiczny obciążenia standardowego równoważenia z strefowo nadmiarowy frontonu adres publiczny adres IP przy użyciu wiersza polecenia platformy Azure | Dokumentacja firmy Microsoft
description: Jak utworzyć publiczny obciążenia standardowego równoważenia z strefowo nadmiarowy frontonu adres publiczny adres IP przy użyciu wiersza polecenia platformy Azure
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2018
ms.author: kumud
ms.openlocfilehash: f3f479de8bc3975f4da07a7761ffc99f976db20e
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
---
#  <a name="create-a-public-load-balancer-standard-with-zone-redundant-frontend-using-azure-cli"></a>Utwórz publiczny obciążenia standardowego równoważenia z strefowo nadmiarowy frontonu przy użyciu wiersza polecenia platformy Azure

W tym artykule opisano przez proces tworzenia publiczny [standardowe usługi równoważenia obciążenia](https://aka.ms/azureloadbalancerstandard) z strefowo nadmiarowy frontonu przy użyciu adresu publicznego adresu IP standardowa.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli wybierzesz do zainstalowania i używania interfejsu wiersza polecenia lokalnie, upewnij się, że zainstalowano najnowszą [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) i zalogować się do konta platformy Azure z [logowania az](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az_login).

> [!NOTE]
 Obsługa stref dostępności jest dostępna dla wybierz zasobów platformy Azure i regiony i rodziny rozmiar maszyny Wirtualnej. Więcej informacji na temat rozpocząć, które zasobów platformy Azure, regiony i rodziny rozmiar maszyny Wirtualnej można spróbować stref dostępności przy, zobacz [Przegląd stref dostępności](https://docs.microsoft.com/azure/availability-zones/az-overview). Aby uzyskać pomoc techniczną, możesz skorzystać z witryny [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) lub [otworzyć bilet pomocy technicznej platformy Azure](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 


## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów, przy użyciu następującego polecenia:

```azurecli-interactive
az group create --name myResourceGroupSLB --location westeurope
```

## <a name="create-a-public-ip-standard"></a>Utwórz publiczny Standard IP

Tworzenie publicznego adresu IP standardowe przy użyciu następującego polecenia:

```azurecli-interactive
az network public-ip create --resource-group myResourceGroupSLB --name myPublicIP --sku Standard
```

## <a name="create-a-load-balancer"></a>Tworzenie modułu równoważenia obciążenia

Utwórz publiczny obciążenia standardowego modułu równoważenia o standardowych publicznego adresu IP, utworzony w poprzednim kroku przy użyciu następującego polecenia:

```azurecli-interactive
az network lb create --resource-group myResourceGroupSLB --name myLoadBalancer --public-ip-address myPublicIP --frontend-ip-name myFrontEnd --backend-pool-name myBackEndPool --sku Standard
```

## <a name="create-an-lb-probe-on-port-80"></a>Utworzyć sondy równoważeniem obciążenia na porcie 80

Utwórz sondy kondycji modułu równoważenia obciążenia za pomocą następującego polecenia:

```azurecli-interactive
az network lb probe create --resource-group myResourceGroupSLB --lb-name myLoadBalancer \
  --name myHealthProbe --protocol tcp --port 80
```

## <a name="create-an-lb-rule-for-port-80"></a>Tworzenie reguły LB dla portu 80

Utwórz regułę modułu równoważenia obciążenia za pomocą następującego polecenia:

```azurecli-interactive
az network lb rule create --resource-group myResourceGroup --lb-name myLoadBalancer --name myLoadBalancerRuleWeb \
  --protocol tcp --frontend-port 80 --backend-port 80 --frontend-ip-name myFrontEnd \
  --backend-pool-name myBackEndPool --probe-name myHealthProbe
```

## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się więcej o [standardowego modułu równoważenia obciążenia i dostępność strefy](load-balancer-standard-availability-zones.md).



