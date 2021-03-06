---
title: "Raport o zabezpieczeniach dotyczący użytkowników oflagowanych w związku z ryzykiem w portalu usługi Azure Active Directory | Microsoft Docs"
description: "Dowiedz się więcej o raporcie o zabezpieczeniach dotyczącym użytkowników oflagowanych w związku z ryzykiem w portalu usługi Azure Active Directory"
services: active-directory
author: MarkusVi
manager: femila
ms.assetid: addd60fe-d5ac-4b8b-983c-0736c80ace02
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/24/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: ed6201e9edcef39b14b948b6b2f6e0b5da01ec60
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2018
---
# <a name="remediate-users-flagged-for-risk-in-the-azure-active-directory-portal"></a>Rozwiązywanie problemów dotyczących użytkowników oflagowanych w związku z ryzykiem w portalu usługi Azure Active Directory

Dzięki raportom o zabezpieczeniach w usłudze Azure Active Directory (Azure AD) możesz uzyskać wgląd w prawdopodobieństwo naruszenia bezpieczeństwa kont użytkowników w środowisku. [Użytkownik oflagowany w związku z ryzykiem](active-directory-identityprotection.md#users-flagged-for-risk) jest wskaźnikiem konta użytkownika, którego bezpieczeństwo mogło zostać naruszone.

Firma Microsoft dokłada starań, aby pomóc zapewnić bezpieczne środowisko. W ramach tego zobowiązania firma Microsoft stale monitoruje działania, które są nietypowe lub zgodne ze znanymi wzorcami ataków. 


Gdy zostaną wykryte nietypowe działania, które mogą wskazywać na nieautoryzowany dostęp do pewnych kont Twoich użytkowników, otrzymasz powiadomienia umożliwiające podjęcie działań. Dostarczanie powiadomień nie oznacza, że systemy firmy Microsoft w jakikolwiek sposób zostały naruszone.
 

## <a name="azure-active-directory-report-access"></a>Dostęp do raportów usługi Azure Active Directory

Możesz przejrzeć użytkowników oflagowanych z powodu ryzyka, korzystając z raportu online usługi Azure Active Directory. Jeśli nie masz subskrypcji platformy Azure, możesz przejść przez proces subskrypcji bez ponoszenia kosztów na stronie [http://aka.ms/AccessAAD](http://aka.ms/AccessAAD).  
Po wykonaniu tych czynności możesz użyć poświadczeń usługi Office 365 do uzyskania dostępu do Centrum administracyjnego platformy Azure. Należy pamiętać, że na poziomie subskrypcji podstawowej udostępniany zakres szczegółowości jest ograniczony. Dodatkowe dane i analizy będą dostępne dla subskrybentów platformy Azure w wersji Premium. Aby uzyskać więcej informacji, zobacz [raport o zabezpieczeniach dotyczący użytkowników oflagowanych w związku z ryzykiem w portalu usługi Azure Active Directory](active-directory-reporting-security-user-at-risk.md).

Po uaktywnieniu dostępu do usługi Azure AD nastąpi przekierowanie do [portalu usługi Azure AD](https://portal.azure.com). Aby przejść bezpośrednio do raportu, użyj następującego adresu URL: [https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/UsersAtRisk](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/UsersAtRisk).

**Aby uzyskać dostęp do raportów o użytkownikach oflagowanych w związku z ryzykiem w Centrum administracyjnym usługi Office 365:**

1.  W menu nawigacji po lewej stronie kliknij pozycję **Centra administracyjne**. 
2.  Kliknij pozycję **Azure AD**.
3.  Zaloguj się do **Centrum administracyjnego usługi Azure Active Directory**.
4.  Jeśli u góry strony zostanie wyświetlony baner z komunikatem **Zapoznaj się z nowym portalem**, kliknij link.
4.  W menu nawigacji po lewej stronie kliknij pozycję **Azure Active Directory**. 
5.  W okienku nawigacji w obszarze **Zabezpieczenia** kliknij pozycję **Użytkownicy oflagowani w związku z ryzykiem**.

Przejrzyj wyświetlane w tym miejscu informacje. Należy zresetować hasło dla każdego konta, które jest tutaj wymienione. 

## <a name="remediation-actions"></a>Akcje naprawcze

Wykonaj następujące akcje, aby pomóc usunąć zagrożenie z kont i zabezpieczyć swoje środowisko:

1.  [Zweryfikuj](http://aka.ms/MFAValid) poprawność informacji dla uwierzytelniania wieloskładnikowego i samoobsługowego resetowania hasła. 
2.  [Włącz](http://aka.ms/MFAuth) uwierzytelnianie wieloskładnikowe dla wszystkich użytkowników. 
3.  Za pomocą tego [skryptu korygującego](http://aka.ms/remediate) dla każdego dotkniętego konta możesz automatycznie wykonać następujące kroki: 

    a. Zresetować hasło do zabezpieczania konta i skasować aktywne sesje.

    b. Usunąć delegatów skrzynki pocztowej.

    c. Wyłączyć reguły przekazywania poczty do domen zewnętrznych.

    d. Usunąć globalną właściwość przekazywania poczty w skrzynce pocztowej.

    e. Włączyć uwierzytelnianie wieloskładnikowe na koncie użytkownika.

    f. Ustawić złożoność hasła dla konta tak, aby była wysoka.

    g. Włączyć inspekcję skrzynki pocztowej.

    h. Utworzyć dziennik inspekcji do sprawdzenia przez administratora.

4. Zbadaj dzierżawę usługi Office 365 i pozostałą infrastrukturę IT, łącznie z przeglądem wszystkich ustawień dzierżawy, kont użytkowników i ustawień konfiguracji poszczególnych użytkowników pod kątem ewentualnych modyfikacji. Sprawdź, czy wskaźniki metod trwałości oraz wskaźniki intruza mogą korzystać z początkowego stopnia w celu uzyskania poświadczeń sieci VPN lub dostępu do innych zasobów organizacji. 

5.  W ramach badania należy rozważyć, czy powinno się lub czy bezwzględnie trzeba powiadomić organy rządowe, w tym uprawnione organy ścigania.

Ponadto wykonaj następujące czynności:

- Przeczytaj i wdróż te [wskazówki](http://aka.ms/fixaccount) dotyczące rozwiązywania nietypowych działań. 
- [Włącz potok inspekcji](http://aka.ms/improvesecurity), aby pomóc analizować działania w Twojej dzierżawie. Po wykonaniu tych czynności magazyn inspekcji rozpoczyna zapełnianie wszystkich dzienników aktywności. W tym momencie możesz również wykorzystać [funkcje wyszukiwania i badania Centrum zabezpieczeń i zgodności](http://aka.ms/sccsearch). 
- Użyj tego [skryptu](http://aka.ms/mailboxaudit1), aby włączyć inspekcję skrzynki pocztowej dla wszystkich kont. 
- Przejrzyj reguły delegowania uprawnień i przekazywania poczty dla wszystkich skrzynek pocztowych. Do wykonania tego zadania możesz użyć tego [skryptu programu PowerShell](http://aka.ms/delegateforwardrules). 



## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji na temat ochrony tożsamości w usłudze Azure Active Directory, zobacz [Ochrona tożsamości w usłudze Azure Active Directory](active-directory-identityprotection.md).

