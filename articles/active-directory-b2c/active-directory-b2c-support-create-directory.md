---
title: 'Usługa Azure Active Directory B2C: Rozwiązywanie problemów z dzierżawcom tworzenie | Dokumentacja firmy Microsoft'
description: Problemy i rozwiązania do tworzenia dzierżawy usługi Azure Active Directory lub Azure Active Directory B2C.
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 12/06/2016
ms.author: davidmu
ms.openlocfilehash: 3daf232d7fb1f95c390c1e6b8c168ec585484c65
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2018
---
# <a name="troubleshoot-creating-an-azure-active-directory-or-azure-active-directory-b2c-tenant"></a>Rozwiązywanie problemów z tworzenie dzierżawy usługi Azure Active Directory lub Azure Active Directory B2C 

## <a name="create-an-azure-ad-tenant"></a>Tworzenie dzierżawy usługi Azure AD
Jeśli dzierżawa usługi Azure Active Directory (Azure AD) nie można utworzyć przy pierwszej próbie, spróbuj ponownie. Jeśli problem będzie się powtarzać, skontaktuj się z pomocą techniczną platformy Azure.

## <a name="create-an-azure-ad-b2c-tenant"></a>Tworzenie dzierżawy usługi Azure AD B2C
Jeśli wystąpią problemy podczas możesz [Tworzenie usługi Azure Active Directory B2C dzierżawy (Azure AD B2C)](active-directory-b2c-get-started.md), spróbuj następujących opcji:

* Jeśli dzierżawy usługi Azure AD B2C nie widać na liście dzierżawcy, spróbuj ponownie utworzyć dzierżawcę.
* Jeśli zostanie wyświetlony następujący komunikat o błędzie dzierżawy usługi Azure AD B2C jest wyświetlany na liście dzierżawcy, Usuń dzierżawcy i utwórz go ponownie:

    "Nie można ukończyć tworzenia dzierżawy B2C"contosob2c". Odwiedź stronę to [łącze](http://go.microsoft.com/fwlink/?LinkID=624192&clcid=0x409) Aby uzyskać więcej pomocy. "
* Istnieją znane problemy podczas usuwania istniejącej dzierżawy usługi Azure AD B2C i utwórz go ponownie przy użyciu tej samej nazwy domeny. Podczas tworzenia nowej dzierżawy usługi Azure AD B2C, należy użyć nazwy innej domeny.
* Jeśli te rozwiązania nie pomoże, skontaktuj się z pomocą techniczną platformy Azure. Aby uzyskać więcej informacji, zobacz [pliku żądania pomocy technicznej usługi Azure AD B2C](active-directory-b2c-support.md).

