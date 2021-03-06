---
title: 'Samouczek: Integracji Azure Active Directory z Ziflow | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i Ziflow.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 84e60fa4-36fb-49c4-a642-95538c78f926
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/09/2018
ms.author: jeedes
ms.openlocfilehash: 5256faab4d1c1dd44cf55f3aa1f6e57f324ccdef
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="tutorial-azure-active-directory-integration-with-ziflow"></a>Samouczek: Integracji Azure Active Directory z Ziflow

Z tego samouczka dowiesz się integrowanie Ziflow z usługi Azure Active Directory (Azure AD).

Integracja z usługą Azure AD Ziflow zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do Ziflow.
- Umożliwia użytkownikom automatycznie pobrać zalogowane do Ziflow (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD.
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure.

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z Ziflow, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Ziflow logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz [uzyskać miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie Ziflow z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-ziflow-from-the-gallery"></a>Dodawanie Ziflow z galerii
Aby skonfigurować integrację usługi Azure AD Ziflow, należy dodać Ziflow z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać Ziflow z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Blok aplikacje przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **Ziflow**, wybierz pozycję **Ziflow** z panelu wyników kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Ziflow na liście wyników](./media/active-directory-saas-ziflow-tutorial/tutorial_ziflow_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Ziflow w oparciu o nazwie "Britta Simona" użytkownika testowego.

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w Ziflow jest dla użytkownika, w usłudze Azure AD. Innymi słowy link relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w Ziflow musi się.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Ziflow, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego Ziflow](#create-a-ziflow-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta Ziflow połączonego z usługi Azure AD reprezentację użytkownika.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Test rejestracji jednokrotnej](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji Ziflow.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z Ziflow, wykonaj następujące czynności:**

1. W portalu Azure na **Ziflow** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/active-directory-saas-ziflow-tutorial/tutorial_ziflow_samlbase.png)

3. Na **Ziflow domeny i adres URL** sekcji, wykonaj następujące czynności:

    ![Adresy URL i domeny Ziflow pojedynczy informacje logowania jednokrotnego](./media/active-directory-saas-ziflow-tutorial/tutorial_ziflow_url.png)

    a. W **Zaloguj się na adres URL** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<subdomain>.ziflow.io/#/login-sso/<Unique ID>`

    b. W **identyfikator** tekstowym, wpisz adres URL, używając następującego wzorca: `urn:auth0:ziflow-production:<Unique ID>`

    > [!NOTE] 
    > Poprzednie wartości nie są prawdziwe. Rzeczywista wartość, która znajduje się w dalszej części tego samouczka zostanie zaktualizowany unikatowa wartość Identyfikatora identyfikator i zaloguj się na adres URL. Skontaktuj się z [zespołem pomocy technicznej Ziflow](mailto:support@ziflow.com) dla wartości poddomeny w adresie URL logowania jednokrotnego.
    
4. Na **certyfikat podpisywania SAML** kliknij **certyfikatu (Base64)** , a następnie zapisz plik certyfikatu na tym komputerze.

    ![Łącze pobierania certyfikatu](./media/active-directory-saas-ziflow-tutorial/tutorial_ziflow_certificate.png) 

5. Kliknij przycisk **zapisać** przycisku.

    ![Skonfiguruj przycisk pojedynczego logowania jednokrotnego Zapisz](./media/active-directory-saas-ziflow-tutorial/tutorial_general_400.png)

6. Na **konfiguracji Ziflow** , kliknij przycisk **skonfigurować Ziflow** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **Sign-Out adresu URL i SAML pojedynczy znak na adres URL usługi** z **sekcji krótkimi opisami.**

    ![Konfiguracja Ziflow](./media/active-directory-saas-ziflow-tutorial/tutorial_ziflow_configure.png) 

7. W oknie przeglądarki innej witryny sieci web, zaloguj się jako Administrator zabezpieczeń Ziflow.


8. Kliknij awatara w prawym górnym rogu, a następnie kliknij przycisk **Zarządzaj kontem**.

    ![Zarządzanie Ziflow konfiguracji](./media/active-directory-saas-ziflow-tutorial/tutorial_ziflow_manage.png)

9. W lewej górnej części, kliknij przycisk **rejestracji jednokrotnej**.

    ![Ziflow konfiguracji logowania](./media/active-directory-saas-ziflow-tutorial/tutorial_ziflow_signon.png)

10. Na **rejestracji jednokrotnej** wykonaj następujące czynności:

    ![Ziflow konfiguracji pojedynczej](./media/active-directory-saas-ziflow-tutorial/tutorial_ziflow_page.png)

    a. Wybierz **typu** jako **SAML2.0**.

    b.In **adres URL logowania** pole tekstowe, Wklej wartość **SAML pojedynczy znak na adres URL usługi**, które zostały skopiowane z portalu Azure.

    c. Przekaż base-64 zakodowanego certyfikatu, który został pobrany z portalu Azure, do **X509 certyfikatu podpisywania**.

    d. W **adresu URL wylogowania** pole tekstowe, Wklej wartość **Sign-Out URL**, które zostały skopiowane z portalu Azure.

    e. Z **ustawienia konfiguracji dla identyfikatora dostawcy** sekcji, skopiuj wyróżnione unikatowa wartość Identyfikatora i dołącza je z identyfikatora i zaloguj się na adres URL w **sekcji Ziflow domeny i adresy URL** na Portalu Azure.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W portalu Azure, w okienku po lewej stronie kliknij **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/active-directory-saas-ziflow-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "Wszyscy użytkownicy" łącza](./media/active-directory-saas-ziflow-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/active-directory-saas-ziflow-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/active-directory-saas-ziflow-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwy użytkownika** wpisz adres e-mail użytkownika Simona Britta.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zanotuj wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.
  
### <a name="create-a-ziflow-test-user"></a>Tworzenie użytkownika testowego Ziflow

Aby umożliwić użytkownikom usługi Azure AD zalogować się do Ziflow, musi być przygotowana do Ziflow. W Ziflow Inicjowanie obsługi to zadanie ręczne.

Aby udostępnić konta użytkownika, wykonaj następujące czynności:

1. Zaloguj się jako Administrator zabezpieczeń Ziflow.

2. Przejdź do **osób** u góry.

    ![Osoby Ziflow konfiguracji](./media/active-directory-saas-ziflow-tutorial/tutorial_ziflow_people.png)

3. Kliknij przycisk **Dodaj** , a następnie kliknij przycisk **Dodaj użytkownika**.

    ![Dodawanie użytkownika Ziflow konfiguracji](./media/active-directory-saas-ziflow-tutorial/tutorial_ziflow_add.png)

4. Na **dodać użytkownika** menu podręczne, wykonaj następujące czynności:

    ![Dodawanie użytkownika Ziflow konfiguracji](./media/active-directory-saas-ziflow-tutorial/tutorial_ziflow_adduser.png)

    a. W **E-mail** tekstowym wprowadź adres e-mail użytkownika, takich jak brittasimon@contoso.com.

    b. W **imię** tekst Wprowadź imię użytkownika, takich jak Britta.

    c. W **nazwisko** tekst Wprowadź nazwisko użytkownika, takich jak Simona.

    d. Wybierz swoją rolę Ziflow.

    e. Kliknij przycisk **użytkownika Dodaj 1**.

    > [!NOTE]
    > Właściciel konta usługi Azure Active Directory otrzymuje wiadomość e-mail i następuje łącze, aby potwierdzić swoje konto, zanim staje się aktywny.

### <a name="assign-the-azure-ad-test-user"></a>Przypisz użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu Ziflow.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Simona Britta Ziflow, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **Ziflow**.

    ![Łącze Ziflow na liście aplikacji](./media/active-directory-saas-ziflow-tutorial/tutorial_ziflow_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Łącze "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![W okienku Dodaj przydziału][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Test rejestracji jednokrotnej

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka Ziflow w panelu dostępu użytkownik powinien pobrać automatycznie zalogowane do aplikacji Ziflow.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-ziflow-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-ziflow-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-ziflow-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-ziflow-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-ziflow-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-ziflow-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-ziflow-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-ziflow-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-ziflow-tutorial/tutorial_general_203.png

