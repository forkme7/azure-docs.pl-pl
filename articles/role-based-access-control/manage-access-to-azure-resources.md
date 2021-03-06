---
title: Zarządzanie dostępem do zasobów platformy Azure w usłudze Azure Active Directory
description: Więcej informacji na temat sposobów, aby zarządzać dostępem do zasobów platformy Azure przy użyciu różnych funkcji usługi Azure Active Directory.
services: active-directory
documentationcenter: ''
author: skwan
manager: mtillman
editor: bryanla
ms.assetid: f66abf54-3809-436c-92b6-018e1179780e
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/05/2017
ms.author: skwan
ms.openlocfilehash: f8f8af1380dc47a4a97845d9d47ac17bd4bba3e0
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="manage-access-to-azure-resources-with-azure-active-directory"></a>Zarządzanie dostępem do zasobów platformy Azure w usłudze Azure Active Directory

Zarządzanie tożsamościami i dostępem do zasobów w chmurze jest funkcją krytyczne dla każdej organizacji korzystających z chmury. Azure Active Directory (Azure AD) jest tożsamości i dostępu do systemu Microsoft Azure.  

Przed rozpoczęciem pracy z towarzyszące funkcji obszary usługi Azure AD, zapoznaj się z poniższym klipie wideo: "Zablokowanie dostępu do chmury platformy Azure przy użyciu logowania jednokrotnego, kontroli dostępu na podstawie ról i warunkowe." W ramach tego informacje o:

- Najlepsze rozwiązania dotyczące konfigurowania rejestracji jednokrotnej w portalu Azure, z lokalną usługą Active Directory.
- Przy użyciu usługi Azure RBAC dla szczegółowej kontroli dostępu do zasobów w ramach subskrypcji.
- Wymuszania reguł silne uwierzytelnianie za pomocą usługi Azure AD warunkowego dostępu.
- Pojęcie zarządzane tożsamość usługi, którym zasobów platformy Azure można automatycznie uwierzytelniania w usłudze Azure, a deweloperzy nie ma potrzeby obsługi interfejsu API kluczy lub kluczy tajnych.

> [!VIDEO https://www.youtube.com/embed/FKBoWWKRnvI]

## <a name="feature-areas"></a>Obszarów funkcji
Usługa Azure AD zapewnia następujące możliwości zarządzania dostępem do zasobów platformy Azure:

|||
|---|---|
| [Relacja między dzierżaw usługi Azure AD i subskrypcji](rbac-and-directory-admin-roles.md) | Dowiedz się więcej o usłudze Azure AD jest zaufanego źródła, użytkowników i grup dla subskrypcji platformy Azure. |
| [Kontrola dostępu oparta na rolach (RBAC)](overview.md) | Oferują precyzyjne zarządzanie dostępem za pomocą ról przypisane do użytkowników, grupy lub podmiotów zabezpieczeń usługi. |
| [Zarządzanie tożsamościami uprzywilejowanymi o RBAC](pim-azure-resource.md) | Formant wysokiej uprzywilejowany dostęp przez przypisanie ról uprzywilejowanych just-in-time. |
| [Dostęp warunkowy dla zarządzania platformy Azure](conditional-access-azure-management.md) | Konfigurowanie zasad dostępu warunkowego, aby umożliwić lub zablokować dostęp do punktów końcowych zarządzania platformy Azure. |
| [Tożsamość usługi zarządzanej (MSI)](../active-directory/pp/msi-overview.md) | Nadaj zasobów platformy Azure, takich jak tożsamość własne maszyny wirtualne, dzięki czemu nie trzeba wprowadzić poświadczenia w kodzie. |

 