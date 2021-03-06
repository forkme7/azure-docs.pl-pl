---
title: Jak uzyskać dzierżawę usługi Azure AD | Microsoft Docs
description: Jak uzyskać dzierżawę usługi Azure Active Directory w celu rejestracji i tworzenia aplikacji.
services: active-directory
documentationcenter: ''
author: mtillman
manager: mtillman
editor: ''
ms.assetid: 1f4b24eb-ab4d-4baa-a717-2a0e5b8d27cd
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 03/23/2018
ms.author: mtillman
ms.custom: aaddev
ms.openlocfilehash: ab7db49fa07f260de6ebbe4b2cee943b64cab7fe
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2018
---
# <a name="how-to-get-an-azure-active-directory-tenant"></a>Jak uzyskać dzierżawę usługi Azure Active Directory
W usłudze Azure Active Directory (Azure AD) [dzierżawa](https://msdn.microsoft.com/library/azure/jj573650.aspx#Anchor_0) reprezentuje organizację.  Jest to dedykowane wystąpienie usługi Azure AD, którą organizacja otrzymuje i której zostaje właścicielem po utworzeniu relacji z firmą Microsoft, takiej jak zarejestrowanie się w usłudze w chmurze firmy Microsoft, np. platformie Azure, usłudze Microsoft Intune czy usłudze Office 365.  Każda dzierżawa usługi Azure AD jest unikatowa i oddzielona od innych dzierżaw usługi Azure AD.  

Dzierżawa gromadzi użytkowników w firmie i informacje o nich — hasła, dane profilu użytkownika, uprawnienia i tak dalej.  Zawiera także grupy, aplikacje i inne informacje dotyczące organizacji i jej zabezpieczeń.

Aby umożliwić użytkownikom usługi Azure AD zalogowanie się do Twojej aplikacji, musisz zarejestrować aplikację w dzierżawie.  Tworzenie dzierżawy usługi Azure AD i publikowanie w niej aplikacji jest **całkowicie bezpłatne** (chociaż można płacić za funkcje premium w swojej dzierżawie).  W rzeczywistości wielu deweloperów tworzy kilka dzierżaw i aplikacji do celów testowych, związanych z programowaniem, przeprowadzaniem badań oraz w ramach etapu przejściowego.

## <a name="use-an-existing-azure-ad-tenant"></a>Używanie istniejącej dzierżawy usługi Azure AD

Wielu deweloperów już ma dzierżawy za pośrednictwem usług lub subskrypcji, które są powiązane z dzierżawami usługi Azure AD (na przykład: subskrypcje usługi Office 365 lub platformy Azure).  Aby sprawdzić, czy masz już dzierżawę, zaloguj się w witrynie [Azure Portal](https://portal.azure.com) przy użyciu konta, którego chcesz użyć do zarządzania aplikacją, i sprawdź prawy górny róg, w którym są wyświetlane informacje o Twoim koncie.  Jeśli masz dzierżawę, nastąpi automatyczne zalogowanie do niej i nazwa dzierżawy zostanie wyświetlona bezpośrednio pod nazwą Twojego konta.  Jeśli Twoje konto jest skojarzone z wieloma dzierżawami, możesz kliknąć nazwę swojego konta, aby otworzyć menu, w którym można przełączać się między dzierżawami.

Jeśli nie masz istniejącej dzierżawy skojarzonej z kontem, pod nazwą konta zostanie wyświetlony identyfikator GUID i nie będzie można wykonywać takich działań, jak rejestrowanie aplikacji, dopóki nie [utworzysz nowej dzierżawy](#create-a-new-azure-ad-tenant).

## <a name="create-a-new-azure-ad-tenant"></a>Tworzenie nowej dzierżawy usługi Azure AD

Jeśli nie masz jeszcze dzierżawy usługi Azure AD lub chcesz utworzyć nową, możesz to zrobić za pomocą [środowisko tworzenia katalogu](https://portal.azure.com/#create/Microsoft.AzureActiveDirectory) w witrynie [Azure Portal](https://portal.azure.com).  Proces potrwa około minuty, a na końcu zostanie wyświetlony monit, aby przejść do nowo utworzonej dzierżawy.