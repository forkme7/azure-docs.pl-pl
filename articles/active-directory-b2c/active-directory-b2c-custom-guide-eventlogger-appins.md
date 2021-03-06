---
title: Śledzenia zachowania użytkownika za pomocą zdarzeń w usłudze Application Insights z usługi Azure AD B2C | Dokumentacja firmy Microsoft
description: Przewodnik krok po kroku, aby włączyć dzienniki zdarzeń w usłudze Application Insights z usługi Azure AD B2C podróże użytkownika za pomocą zasad niestandardowych (wersja zapoznawcza)
services: active-directory-b2c
documentationcenter: dev-center-name
author: davidmu1
manager: mtillman
ms.service: active-directory-b2c
ms.topic: article
ms.workload: identity
ms.date: 3/15/2018
ms.author: davidmu
ms.openlocfilehash: 28c4cefdd7604dbddf6887dcf494ecea65d658f1
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="track-user-behavior-in-azure-ad-b2c-journeys-by-using-application-insights"></a>Śledzenia zachowania użytkownika w usłudze Azure AD B2C podróże przy użyciu usługi Application Insights

Usługa Azure Active Directory B2C (Azure AD B2C) działa dobrze w usłudze Azure Application Insights. Zapewniają szczegółowe i dostosowane dzienniki zdarzeń dla Twojego podróże utworzonych niestandardowo użytkownika. W tym artykule pokazano, jak rozpocząć pracę, można wykonywać następujące czynności: 
* Uzyskaj informacje na zachowanie użytkownika.
* Rozwiązywanie problemów z własnych zasad do rozwoju lub w środowisku produkcyjnym.
* Miara wydajności.
* Utwórz powiadomienia usługi Application Insights.

> [!NOTE]
> Ta funkcja jest dostępna w wersji zapoznawczej.

## <a name="how-it-works"></a>Jak to działa
Framework obsługi tożsamości w usłudze Azure AD B2C zawiera teraz dostawcy `Handler="Web.TPEngine.Providers.UserJourneyContextProvider, Web.TPEngine, Version=1.0.0.0`. Wysyła dane zdarzenia bezpośrednio do usługi Application Insights przy użyciu klucza Instrumentacji dostarczone do usługi Azure AD B2C. 

Techniczne profilu używa tego dostawcy, aby zdefiniować zdarzenie z B2C. Profil określa nazwę zdarzenia, oświadczenia, które mają być rejestrowane i klucz instrumentacji. Można opublikować zdarzenia, techniczne profilu jest dodawane jako *krok aranżacji* lub jako *techniczne profil sprawdzania poprawności* w podróży użytkownika niestandardowego. 

Usługa Application Insights można ujednolicenie zdarzenia przy użyciu Identyfikatora korelacji Aby zarejestrować sesję użytkownika. Usługa Application Insights udostępnia zdarzeń i sesji w sekundach i przedstawia wiele wizualizacji, eksportowania i narzędzi analitycznych.



## <a name="prerequisites"></a>Wymagania wstępne
Wykonaj kroki [wprowadzenie do zasad niestandardowych](active-directory-b2c-get-started-custom.md). W tym artykule przyjęto założenie, że używasz pakietu starter zasady niestandardowe. Ale pakiet początkowy nie jest wymagane.



## <a name="step-1-create-an-application-insights-resource-and-get-the-instrumentation-key"></a>Krok 1. Tworzenie zasobu usługi Application Insights i Uzyskaj klucz Instrumentacji

Podczas korzystania z usługi Application Insights z usługi Azure AD B2C, jedynym wymaganiem jest tworzenie zasobu i uzyskanie klucza instrumentacji. Utwórz zasób w [portalu Azure](https://portal.azure.com).

1. W portalu Azure w ramach subskrypcji dzierżawy, wybierz **+ Utwórz zasób**. Tej dzierżawy nie jest dzierżawy usługi Azure AD B2C.  
2. Wyszukaj i wybierz **usługi Application Insights**.  
3. Utwórz zasób, który używa **aplikacji sieci web ASP.NET** jako **typu aplikacji**, w ramach subskrypcji swoich preferencji.
4. Po utworzeniu zasobu usługi Application Insights, otwórz go i zanotuj klucz instrumentacji. 

![Klucz omówienie i instrumentacji Insights aplikacji](./media/active-directory-b2c-custom-guide-eventlogger-appins/app-ins-key.png)

Aby uzyskać więcej informacji, zobacz [pełnej dokumentacji usługi Application Insights](https://docs.microsoft.com/azure/application-insights/).

## <a name="step-2-add-new-claimtype-definitions-to-your-trust-framework-extension-file"></a>Krok 2. Dodaj nowe definicje typu oświadczenia do pliku rozszerzenie framework zaufania

Otwórz plik rozszerzenia z pakietu starter i dodaj następujące elementy do `<BuildingBlocks>` węzła. Nazwa pliku jest zwykle `yourtenant.onmicrosoft.com-B2C_1A_TrustFrameworkExtensions.xml`.

```xml

    <ClaimsSchema>
      <ClaimType Id="EventType">
        <DisplayName>EventType</DisplayName>
        <DataType>string</DataType>
        <AdminHelpText />
        <UserHelpText />
      </ClaimType>
      <ClaimType Id="PolicyId">
        <DisplayName>PolicyId</DisplayName>
        <DataType>string</DataType>
        <AdminHelpText />
        <UserHelpText />
      </ClaimType>
      <ClaimType Id="Culture">
        <DisplayName>Culture</DisplayName>
        <DataType>string</DataType>
        <AdminHelpText />
        <UserHelpText />
      </ClaimType>
      <ClaimType Id="CorrelationId">
        <DisplayName>CorrelationId</DisplayName>
        <DataType>string</DataType>
        <AdminHelpText />
        <UserHelpText />
      </ClaimType>
      <!-- Additional claims used for passing claims to the Application Insights provider. -->
      <ClaimType Id="federatedUser">
        <DisplayName>federatedUser</DisplayName>
        <DataType>boolean</DataType>
        <UserHelpText />
      </ClaimType>
      <ClaimType Id="parsedDomain">
        <DisplayName>Parsed Domain</DisplayName>
        <DataType>string</DataType>
        <UserHelpText>The domain portion of the email address.</UserHelpText>
      </ClaimType>
      <ClaimType Id="userInLocalDirectory">
        <DisplayName>userInLocalDirectory</DisplayName>
        <DataType>boolean</DataType>
        <UserHelpText />
      </ClaimType>
    </ClaimsSchema>

```

## <a name="step-3-add-new-technical-profiles-that-use-the-application-insights-provider"></a>Krok 3. Dodaj nowy techniczne profilów, które korzystają z dostawcy usługi Application Insights

Profile technicznych mogą zostać uwzględnione funkcje w tożsamości środowiska Framework programu Azure AD B2C. W tym przykładzie zdefiniowano pięć profilów techniczne Otwórz sesję i opublikuj zdarzenia:

| Profil techniczne       | Zadanie |
| ----------------------------- |:---------------------------------------------|
| AzureInsights-Common | Tworzy zestaw typowych parametrów, które mają zostać uwzględnione we wszystkich `AzureInsights` profile techniczne     | 
| JourneyContextForInsights   | Otwiera sesji w usłudze Application Insights i wysyła identyfikator korelacji |
| AzureInsights-SignInRequest     | Tworzy `SignIn` zdarzenie z zestaw oświadczeń, gdy otrzymano żądanie logowania      | 
| AzureInsights-UserSignup | Tworzy `UserSignup` zdarzenie, gdy użytkownik uruchamia opcją w podróży konta-konta/logowania     | 
| AzureInsights-SignInComplete | rejestruje pomyślne zakończenie uwierzytelniania, gdy token zostało wysłane do jednostki uzależnionej strony aplikacji     | 

Dodaj profile do pliku rozszerzenie z pakietu starter przez dodanie tych elementów do `<ClaimsProviders>` węzła. Nazwa pliku jest zwykle `yourtenant.onmicrosoft.com-B2C_1A_TrustFrameworkExtensions.xml`.

> [!IMPORTANT]
> Należy zmienić wartość klucza Instrumentacji w `ApplicationInsights-Common` techniczne profilu na identyfikator GUID, który zapewnia zasobu usługi Application Insights.

```xml
<ClaimsProvider>
      <DisplayName>Application Insights</DisplayName>
      <TechnicalProfiles>

        <TechnicalProfile Id="JourneyContextForInsights">
          <DisplayName>Application Insights</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.UserJourneyContextProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="CorrelationId" />
          </OutputClaims>
        </TechnicalProfile>

        <TechnicalProfile Id="AzureInsights-SignInRequest">
          <InputClaims>
            <!-- An input claim with PartnerClaimType="eventName" is required. The Application Insights provider uses it to create an event with the specified value. -->
            <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="SignInRequest" />
          </InputClaims>
          <IncludeTechnicalProfile ReferenceId="AzureInsights-Common" />
        </TechnicalProfile>

        <TechnicalProfile Id="AzureInsights-SignInComplete">
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="SignInComplete" />
            <InputClaim ClaimTypeReferenceId="federatedUser" PartnerClaimType="{property:FederatedUser}" DefaultValue="false" />
            <InputClaim ClaimTypeReferenceId="parsedDomain" PartnerClaimType="{property:FederationPartner}" DefaultValue="Not Applicable" />
          </InputClaims>
          <IncludeTechnicalProfile ReferenceId="AzureInsights-Common" />
        </TechnicalProfile>

        <TechnicalProfile Id="AzureInsights-UserSignup">
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="UserSignup" />
          </InputClaims>
          <IncludeTechnicalProfile ReferenceId="AzureInsights-Common" />
        </TechnicalProfile>

        <TechnicalProfile Id="AzureInsights-Common">
          <DisplayName>Alternate Email</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.Insights.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
            <!-- The Application Insights instrumentation key that will be used for logging the events. -->
            <Item Key="InstrumentationKey">xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx</Item>
            <!-- A Boolean that indicates whether developer mode is enabled. This controls how events are buffered. In a development environment with minimal event volume, enabling developer mode results in events being sent immediately to Application Insights.   -->
            <Item Key="DeveloperMode">false</Item>
            <!-- A Boolean that indicates whether telemetry should be enabled or not.   -->
            <Item Key="DisableTelemetry ">false</Item>
          </Metadata>
          <InputClaims>
            <!-- Properties of an event are added through the syntax {property:NAME}, where NAME is the name of the property being added to the event. DefaultValue can be either a static value or a value that's resolved by one of the supported DefaultClaimResolvers. -->
            <InputClaim ClaimTypeReferenceId="PolicyId" PartnerClaimType="{property:Policy}" DefaultValue="{Policy:PolicyId}" />
            <InputClaim ClaimTypeReferenceId="CorrelationId" PartnerClaimType="{property:JourneyId}" />
            <InputClaim ClaimTypeReferenceId="Culture" PartnerClaimType="{property:Culture}" DefaultValue="{Culture:RFC5646}" />
          </InputClaims>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>

```

## <a name="step-4-add-the-technical-profiles-for-application-insights-as-orchestration-steps-in-an-existing-user-journey"></a>Krok 4. W razie aranżacji czynnościach w ramach istniejącego przebieg użytkownika Dodaj techniczne profilów usługi Application Insights

Wywołanie `JournyeContextForInsights` jako aranżacji krok 1:

```xml
<!-- Initialize a session with Application Insights. -->
<OrchestrationStep Order="1" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="JourneyContextForInsights" TechnicalProfileReferenceId="JourneyContextForInsights" />
          </ClaimsExchanges>
        </OrchestrationStep>
```

Wywołanie `Azure-Insights-SignInRequest` jako aranżacji kroku 2, aby śledzić Otrzymano żądanie logowania — w/tworzenia konta:

```xml
<!-- Track that we have received a sign-in request. -->
        <OrchestrationStep Order="2" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="TrackSignInRequest" TechnicalProfileReferenceId="AzureInsights-SignInRequest" />
          </ClaimsExchanges>
        </OrchestrationStep>
```

Natychmiast *przed* `SendClaims` aranżacji kroku, należy dodać nowy krok, który wywołuje `Azure-Insights-UserSignup`. Jest on wyzwalane, gdy użytkownik wybierze przycisk rejestracji w podróży konta-konta/logowania.

```xml
        <!-- Handles the user selecting the sign-up link in the local account sign-in page. -->
        <OrchestrationStep Order="9" Type="ClaimsExchange">
          <Preconditions>
            <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
              <Value>newUser</Value>
              <Action>SkipThisOrchestrationStep</Action>
            </Precondition>
            <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
              <Value>newUser</Value>
              <Value>false</Value>
              <Action>SkipThisOrchestrationStep</Action>
            </Precondition>
          </Preconditions>
          <ClaimsExchanges>
            <ClaimsExchange Id="TrackUserSignUp" TechnicalProfileReferenceId="AzureInsights-UserSignup" />
          </ClaimsExchanges>
```

Natychmiast po `SendClaims` krok aranżacji, wywołaj `Azure-Insights-SignInComplete`. Ten krok odzwierciedla pomyślnie zakończono podróży.

```xml
        <!-- Track that we have successfully sent a token. -->
        <OrchestrationStep Order="11" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="TrackSignInComplete" TechnicalProfileReferenceId="AzureInsights-SignInComplete" />
          </ClaimsExchanges>
        </OrchestrationStep>

```

> [!IMPORTANT]
> Po dodaniu nowego kroki aranżacji numerowania kroki sekwencyjnie bez pominięcia dowolnej liczby całkowite z zakresu od 1 do N.


## <a name="step-5-upload-your-modified-extensions-file-run-the-policy-and-view-events-in-application-insights"></a>Krok 5. Przesłać plik rozszerzenia zmodyfikowane, uruchamianie zasad i wyświetlać zdarzenia w usłudze Application Insights

Zapisz i Przekaż nowy plik rozszerzenia framework zaufania. Następnie wywołaj metodę jednostki uzależnionej zasady firmy z aplikacji lub użyj **Uruchom teraz** w interfejsie usługi Azure AD B2C. W sekundach zdarzeń są dostępne w usłudze Application Insights.

1. Otwórz zasobu usługi Application Insights w dzierżawie usługi Azure Active Directory.
2. Wybierz **zdarzenia** w **użycia** podmenu.
3. Ustaw **podczas** do **ostatniej godziny** i **przez** do **3 minuty**. Może być konieczne wybranie **Odśwież** Aby wyświetlić wyniki.

![Wykres dla zdarzeń użycia usługi Application Insights](./media/active-directory-b2c-custom-guide-eventlogger-appins/app-ins-graphic.png)





## <a name="next-steps"></a>Kolejne kroki

Dodaj typów oświadczeń i zdarzenia do użytkownika podróży odpowiednio do potrzeb. Poniżej przedstawiono listę możliwych oświadczeń za pomocą oświadczeń dodatkowe programy rozpoznawania nazw.

### <a name="culture-specific-claims"></a>Oświadczenia specyficzne dla kultury

```xml
Culture-specific Claims
            Referenced using {Culture:One of the property names below}
 
        /// An enumeration of the claims supported by the <see cref="JourneyCultureDefaultClaimProcessor"/>
        public enum SupportedClaim
        {
             /// The two letter ISO code for the language i.e. en
            LanguageName
             
            /// The two letter ISO code for the region i.e. US
            RegionName,
 
            /// The RFC5646 language code i.e. en-US
            RFC5646,

            /// The LCID of language code i.e. 1033
            LCID
        }

```

### <a name="policy-specific-claims"></a>Zasady dotyczące oświadczeń

```xml
Policy-specific Claims
Referenced using {Policy:One of the property names below}
 
        /// An enumeration of the claims supported by the <see cref="PolicyDefaultClaimProcessor"/> 
        public enum SupportedClaim
        {
            /// The trustframework tenant id
            TrustFrameworkTenantId,
  
            /// The tenant id of the relying party
            RelyingPartyTenantId,
 
            /// The policy id of the policy
            PolicyId,
 
            /// The tenant object id of the policy
            TenantObjectId
}

```

### <a name="openid-connect-specific-claims"></a>Specyficzne dla protokołu OpenID Connect oświadczeń

```xml
OpenIDConnect Specific Claims
Referenced using {OIDC:One of the property names below}
 
/// 
        /// An enumeration of the claims supported by the <see cref="OpenIdConnectDefaultClaimProcessor"/>

        public enum SupportedClaim
        {
            /// The OpenIdConnect prompt parameter
            Prompt,
 
            /// The OpenIdConnect login_hint parameter
            LoginHint,

            /// The OpenIdConnect domain_hint parameter
            DomainHint,
 
             /// The OpenIdConnect max_age parameter
            MaxAge,
 
            /// The OpenIdConnect client_id parameter
            ClientId,
 
            /// The OpenIdConnect username parameter
            Username,

            /// The OpenIdConnect password parameter
            Password,
 
            /// The OpenIdConnect resource type parameter
            Resource,
 
            /// The OpendIdConext acr_values parameter
             AuthenticationContextReferences
        }
 
```

### <a name="non-protocol-parameters-included-with-oidc-and-oauth2-requests"></a>Parametry non-protocol dołączone do żądania OIDC i OAuth2

```xml
Referenced using { OAUTH-KV:Querystring parameter name }
```

Nazwa parametru uwzględniane jako część żądania OIDC lub OAuth2 mogą być mapowane na oświadczenia w podróży użytkownika. Można następnie zapisz go w zdarzeniu. Na przykład żądanie od aplikacji może zawierać parametr ciągu zapytania o nazwie `app_session`, `loyalty_number`, lub `any_string`.

Poniżej przedstawiono przykładowe żądanie od aplikacji:
```
https://login.microsoftonline.com/sampletenant.onmicrosoft.com/oauth2/v2.0/authorize?p=B2C_1A_signup_signin&client_id=e1d2612f-c2bc-4599-8e7b-d874eaca1ae1&nonce=defaultNonce&redirect_uri=https%3A%2F%2Fjwt.ms&scope=openid&response_type=id_token&prompt=login&app_session=0a2b45c&loyalty_number=1234567

```
Następnie można dodać oświadczenia przez dodanie `InputClaim` element do zdarzenia usługi Application Insights:
```
<InputClaim ClaimTypeReferenceId="app_session" PartnerClaimType="app_session" DefaultValue="{OAUTH-KV:app_session}" />
<InputClaim ClaimTypeReferenceId="loyalty_number" PartnerClaimType="loyalty_number" DefaultValue="{OAUTH-KV:loyalty_number}" />
```

### <a name="other-system-claims"></a>Pozostałe roszczenia systemu

Niektóre oświadczenia system musi można było dodać do zbioru oświadczenia są one dostępne do rejestrowania jako zdarzenia. Profil techniczne `SimpleUJContext` musi zostać wywołana jako etap aranżacji lub sprawdzania poprawności profilu techniczne przed oświadczenia te są dostępne.

```xml
<ClaimsProvider>
    <DisplayName>User Journey Context Provider</DisplayName>
        <TechnicalProfiles>
            <TechnicalProfile Id="SimpleUJContext">
                <DisplayName>User Journey Context Provide</DisplayName>
                <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.UserJourneyContextProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
                <OutputClaims>
                    <OutputClaim ClaimTypeReferenceId="IP-Address" />
                    <OutputClaim ClaimTypeReferenceId="CorrelationId" />
                    <OutputClaim ClaimTypeReferenceId="DateTimeInUtc" />
                    <OutputClaim ClaimTypeReferenceId="Build" />
                 </OutputClaims>
            </TechnicalProfile>
        </TechnicalProfiles>
</ClaimsProvider>



