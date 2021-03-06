---
title: "Błąd RequestDisallowedByPolicy z zasadami zasobów platformy Azure | Dokumentacja firmy Microsoft"
description: "W tym artykule opisano przyczyny tego błędu RequestDisallowedByPolicy."
services: azure-resource-manager,azure-portal
documentationcenter: 
author: genlin
manager: cshepard
editor: 
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: support-article
ms.date: 03/09/2018
ms.author: genli
ms.openlocfilehash: 5a9efa6b807e933726104e7af315589ede5d9b74
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/12/2018
---
# <a name="requestdisallowedbypolicy-error-with-azure-resource-policy"></a>Błąd RequestDisallowedByPolicy z zasadami zasobów platformy Azure

W tym artykule opisano przyczyny tego błędu RequestDisallowedByPolicy, umożliwia także rozwiązania dla tego błędu.

## <a name="symptom"></a>Objaw

Podczas wdrażania, może zostać wyświetlony **RequestDisallowedByPolicy** błąd, który uniemożliwia tworzenie zasobów. W poniższym przykładzie pokazano kod błędu:

```json
{
  "statusCode": "Forbidden",
  "serviceRequestId": null,
  "statusMessage": "{\"error\":{\"code\":\"RequestDisallowedByPolicy\",\"message\":\"The resource action 'Microsoft.Network/publicIpAddresses/write' is disallowed by one or more policies. Policy identifier(s): '/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition'.\"}}",
  "responseBody": "{\"error\":{\"code\":\"RequestDisallowedByPolicy\",\"message\":\"The resource action 'Microsoft.Network/publicIpAddresses/write' is disallowed by one or more policies. Policy identifier(s): '/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition'.\"}}"
}
```

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Aby uzyskać szczegółowe informacje dotyczące zasad zablokowane wdrożenia, użyj następujących metod:

### <a name="powershell"></a>PowerShell

W programie PowerShell, podaj identyfikator zasad jako `Id` parametr, aby pobrać szczegółowe informacje dotyczące zasad zablokowane wdrożenia.

```PowerShell
(Get-AzureRmPolicyDefinition -Id "/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition").Properties.policyRule | ConvertTo-Json
```

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

W programie Azure CLI 2.0 należy podać nazwę definicji zasad:

```azurecli
az policy definition show --name regionPolicyAssignment
```

## <a name="solution"></a>Rozwiązanie

Zabezpieczeń lub zgodności administratorami subskrypcji może przypisać zasady ograniczające, w jaki sposób są wdrażane zasoby. Na przykład subskrypcji może zasad, który uniemożliwia tworzenie trasy zdefiniowane przez użytkownika adresów sieciowych grup zabezpieczeń, publiczny adres IP lub tabel tras. Komunikat o błędzie w **objawy** sekcja zawiera nazwę zasady.
Aby rozwiązać ten problem, przejrzyj zasady zasobów i określić, jak wdrożyć zasobów, które są zgodne z tymi zasadami.

Aby uzyskać więcej informacji zobacz następujące artykuły:

- [Co to jest Azure zasady?](../azure-policy/azure-policy-introduction.md)
- [Tworzenie i zarządzanie zasadami, by wymuszał zgodność](../azure-policy/create-manage-policy.md)
