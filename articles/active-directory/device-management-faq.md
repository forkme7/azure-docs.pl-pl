---
title: Zarządzanie urządzeniami w usłudze Azure Active Directory — często zadawane pytania | Dokumentacja firmy Microsoft
description: Azure Active Directory Zarządzanie urządzeniami — często zadawane pytania.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: cdc25576-37f2-4afb-a786-f59ba4c284c2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 4358b57284721642957d56ad8cfeea2b0f53fd89
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="azure-active-directory-device-management-faq"></a>Zarządzanie urządzeniami w usłudze Azure Active Directory — często zadawane pytania



**Pytanie: jak zarejestrować urządzenie macOS?**

**Odpowiedź:** zarejestrować urządzenie macOS:

1.  [Tworzenie zasad zgodności](https://docs.microsoft.com/intune/compliance-policy-create-mac-os)
2.  [Definiowanie zasad dostępu warunkowego dla urządzeń macOS](active-directory-conditional-access-azure-portal.md) 

**Uwagi:**

- Użytkownicy, które znajdują się w zasadach dostępu warunkowego muszą [obsługiwana wersja pakietu Office dla macOS](active-directory-conditional-access-technical-reference.md#client-apps-condition) dostęp do zasobów. 

- Podczas pierwszej próby dostępu użytkownikom monit o zarejestrowanie urządzenia przy użyciu portalu firmy.

---

**Pytanie: czy mogę ostatnio zarejestrowane urządzenia. Dlaczego nie widzę urządzenia w obszarze Moje informacje użytkownika w portalu Azure?**

**Odpowiedź:** urządzenia systemu Windows 10, które są hybrydowego przyłączonych do usługi Azure AD nie są wyświetlane w obszarze urządzenia użytkowników.
Należy użyć programu PowerShell, aby wyświetlić wszystkie urządzenia. 

Urządzenia użytkowników należą następujące urządzenia:

- Przyłączony do wszystkich urządzeń osobistych, które nie są hybrydowej usługi Azure AD. 
- Wszystkie z systemem innym niż Windows 10 / urządzeń z systemem Windows Server 2016.
- Wszystkie urządzenia z systemem innym niż Windows 

---

**Pytanie: Dlaczego nie można wyświetlić wszystkich urządzeń zarejestrowanych w usłudze Azure Active Directory w portalu Azure?** 

**Odpowiedź:** można przeglądać je w obszarze Katalog usługi Azure AD -> wszystkie urządzenia menu. Za pomocą programu PowerShell Azure można również znaleźć wszystkie urządzenia. Aby uzyskać więcej informacji, zobacz [Get-MsolDevice](/powershell/module/msonline/get-msoldevice?view=azureadps-1.0) polecenia cmdlet.

--- 

**Pytanie: jak sprawdzić, co to jest stan rejestracji urządzenia klienta?**

**Odpowiedź:** dla systemu Windows 10 i Windows Server 2016 lub urządzenia z nowszymi wersjami, uruchom dsregcmd.exe/status.

Dla wcześniejszych wersji systemu operacyjnego uruchom "%programFiles%\Microsoft Join\autoworkplace.exe obszar roboczy"

---

**Pytanie: Dlaczego jest to urządzenie I został usunięty w portalu Azure lub nadal przy użyciu programu Windows PowerShell wymienione w zarejestrowany?**

**Odpowiedź:** to jest celowe. Urządzenie nie ma dostępu do zasobów w chmurze. Jeśli chcesz ponownie zarejestrować ponownie akcji ręcznej musi być podejmowane na urządzeniu. 

Wyczyść stan przyłączenia z systemem Windows 10 i Windows Server 2016, które są lokalne przyłączonych do domeny usługi AD:

1.  Otwórz wiersz polecenia jako administrator.

2.  Typ `dsregcmd.exe /debug /leave`

3.  Wyloguj się i zaloguj się do wyzwolenia zaplanowane zadanie, które rejestruje urządzenia z usługą Azure AD ponownie. 

Dla wersji systemu operacyjnego Windows niższego poziomu, które są lokalne AD przyłączonych do domeny:

1.  Otwórz wiersz polecenia jako administrator.
2.  Wpisz polecenie `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /l"`.
3.  Wpisz polecenie `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /j"`.

---

**Pytanie: Dlaczego Zobacz urządzenia zduplikowanych wpisów w portalu Azure?**

**ODP.:**

-   Dla systemu Windows 10 i Windows Server 2016 w przypadku kolejnych nieudanych prób Odłączanie i dołączyć ponownie do tego samego urządzenia mogą istnieć zduplikowane wpisy. 

-   Jeśli używasz konta Dodaj firmowego lub szkolnego każdego użytkownika systemu windows, który używa konta Dodaj firmowego lub szkolnego utworzy nowy rekord urządzenia z tę samą nazwę urządzenia.

-   Dla wersji systemu operacyjnego Windows niższego poziomu, które są lokalne AD przyłączonych do domeny za pomocą automatycznej rejestracji utworzy nowy rekord urządzenia tę samą nazwę urządzenia dla każdego użytkownika domeny, który loguje się do urządzenia. 

-   Komputerze dołączonym do usługi Azure AD, zostały wyczyszczone, ponowna instalacja i ponownie połączony z tej samej nazwie, zostaną wyświetlone jako inny rekord z tą samą nazwą urządzenia.

---

**Pytanie: Dlaczego użytkownik nadal dostęp do zasobów z urządzenia, które można wyłączyć w portalu Azure?**

**Odpowiedź:** może potrwać do godziny dla odwołania do zastosowania.

>[!Note] 
>Dla zarejestrowanych urządzeń zaleca się czyszczenie urządzenia, aby upewnić się, że użytkownicy nie mogą uzyskać dostęp do zasobów. Aby uzyskać więcej informacji, zobacz [rejestrowanie urządzeń do zarządzania w usłudze Intune](https://docs.microsoft.com/intune/deploy-use/enroll-devices-in-microsoft-intune). 


---

**Pytanie: Dlaczego Moi użytkownicy widzą "Nie można pobrać istnieje w tym miejscu"?**

**Odpowiedź:** Jeśli został skonfigurowany, niektóre zasady dostępu warunkowego będą musieli stanu określonego urządzenia, a urządzenie nie spełnia kryteriów, użytkownicy są blokowane, a ten komunikat zostanie wyświetlony. Oszacowanie reguł zasad dostępu warunkowego i upewnij się, że urządzenie jest w stanie spełnia kryteria, aby uniknąć występowania tego komunikatu.

---


**Pytanie: Zobacz rekordem urządzenia w obszarze informacje o użytkowniku w portalu Azure i można zobaczyć stan zarejestrowany na kliencie. Czy prawidłowo skonfigurować do korzystania z dostępu warunkowego?**

**Odpowiedź:** stan przyłączenia urządzenia, sprawdzając deviceID, musi zgodne ze specyfikacją na usługi Azure AD i spełniać wszystkie kryteria oceny dostępu warunkowego. Aby uzyskać więcej informacji, zobacz [wprowadzenie do rejestracji urządzeń usługi Azure Active Directory](active-directory-device-registration.md).

---

**Pytanie: Dlaczego uzyskać komunikat "Nazwa użytkownika lub hasło jest niepoprawne" dla urządzenia, które tylko po dołączeniu do usługi Azure AD?**

**Odpowiedź:** są typowe przyczyny tego scenariusza:

- Poświadczenia użytkownika nie są już prawidłowe.

- Komputer nie może nawiązać połączenia z usługą Azure Active Directory. Sprawdź, czy wszystkie problemy z połączeniem sieciowym.

- Azure AD Join wymagania wstępne nie zostały spełnione. Upewnij się, że zostały wykonane kroki [rozszerzanie funkcji chmury na urządzeniach z systemem Windows 10 za pomocą usługi Azure Active Directory Join](active-directory-azureadjoin-overview.md).  

- Logowania federacyjnego wymaga serwerze federacyjnym w celu obsługi aktywny punkt końcowy protokołu WS-Trust. 

---

**Pytanie: Dlaczego zobacz "Oops... Wystąpił błąd!" okna dialogowego przy próbie, czy usługi Azure AD join komputer?**

**Odpowiedź:** jest to wynik konfigurowania rejestracji usługi Azure Active Directory z usługą Intune. Upewnij się, że użytkownik próby przyłączenia usługi Azure AD ma poprawne przypisanej licencji usługi Intune. Aby uzyskać więcej informacji, zobacz [Konfigurowanie zarządzania urządzeniami z systemem Windows](https://docs.microsoft.com/intune/deploy-use/set-up-windows-device-management-with-microsoft-intune#azure-active-directory-enrollment).  

---

**Pytanie: Dlaczego moja próby dołączenia komputera nie chociaż uzyskane informacje o błędzie?**

**Odpowiedź:** prawdopodobną przyczyną jest to, że użytkownik jest zalogowany do urządzenia przy użyciu wbudowanego konta administratora. Utwórz konto lokalne różnych przed przy użyciu usługi Azure Active Directory Join, aby ukończyć instalację. 

---

**Pytanie: gdzie można znaleźć instrukcje do instalacji automatycznej rejestracji urządzeń?**

**Odpowiedź:** szczegółowe instrukcje, zobacz [Konfigurowanie automatycznej rejestracji urządzeń z systemem Windows przyłączonych do domeny w usłudze Azure Active Directory](active-directory-conditional-access-automatic-device-registration-setup.md)

---

**Pytanie: gdzie mogę znaleźć, rozwiązywanie problemów z informacji o automatycznej rejestracji urządzeń?**

**Odpowiedź:** informacje dotyczące rozwiązywania problemów, zobacz:

- [Rozwiązywanie problemów z automatyczną rejestrację domeny komputery przyłączone do usługi Azure AD — Windows 10 i Windows Server 2016](device-management-troubleshoot-hybrid-join-windows-current.md)

- [Rozwiązywanie problemów z automatyczną rejestrację domeny komputery przyłączone do usługi Azure AD dla klientów niższego poziomu z systemem Windows](device-management-troubleshoot-hybrid-join-windows-legacy.md)
 
---

