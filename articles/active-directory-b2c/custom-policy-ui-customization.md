---
title: De gebruikers interface van uw app aanpassen met een aangepast beleid
titleSuffix: Azure AD B2C
description: Meer informatie over het aanpassen van een gebruikers interface met behulp van een aangepast beleid in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/16/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 340c9629af89bfacb85b37503743fc5770070ae3
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94951839"
---
# <a name="customize-the-user-interface-of-your-application-using-a-custom-policy-in-azure-active-directory-b2c"></a>Pas de gebruikers interface van uw toepassing aan met behulp van een aangepast beleid in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Door de stappen in dit artikel uit te voeren, maakt u een aangepast aanmeldings-en aanmeldings beleid met uw merk en weer gave. Met Azure Active Directory B2C (Azure AD B2C) krijgt u bijna volledige controle over de HTML-en CSS-inhoud die wordt gepresenteerd aan gebruikers. Wanneer u een aangepast beleid gebruikt, configureert u de UI-aanpassing in XML in plaats van besturings elementen in de Azure Portal.

## <a name="prerequisites"></a>Vereisten

Voer de stappen in aan de [slag met aangepast beleid](custom-policy-get-started.md). U moet beschikken over een werkend aangepast beleid voor het aanmelden en aanmelden met lokale accounts.

[!INCLUDE [active-directory-b2c-html-how-to](../../includes/active-directory-b2c-html-how-to.md)]

### <a name="4-modify-the-extensions-file"></a>4. het extensie bestand wijzigen

Als u de UI-aanpassing wilt configureren, kopieert u de **ContentDefinition** en de onderliggende elementen van het basis bestand naar het extensie bestand.

1. Open het basis bestand van uw beleid. Bijvoorbeeld <em>`SocialAndLocalAccounts/`**`TrustFrameworkBase.xml`**</em> . Dit basis bestand is een van de beleids bestanden in het aangepaste beleids Starter Pack, die u in de vereiste moet hebben verkregen, aan de [slag met aangepast beleid](./custom-policy-get-started.md).
1. Zoek en kopieer de volledige inhoud van het **ContentDefinitions** -element.
1. Open het extensie bestand. Bijvoorbeeld *TrustFrameworkExtensions.xml*. Zoek het element **BuildingBlocks** . Als het element niet bestaat, voegt u het toe.
1. Plak de volledige inhoud van het **ContentDefinitions** -element dat u hebt gekopieerd als onderliggend element van het **Building Blocks** -object.
1. Zoek naar het **ContentDefinition** -element dat zich `Id="api.signuporsignin"` in de XML bevinden die u hebt gekopieerd.
1. Wijzig de waarde van **LoadUri** in de URL van het HTML-bestand dat u hebt geüpload naar opslag. Bijvoorbeeld `https://your-storage-account.blob.core.windows.net/your-container/customize-ui.html`.

    Het aangepaste beleid moet eruitzien zoals in het volgende code fragment:

    ```xml
    <BuildingBlocks>
      <ContentDefinitions>
        <ContentDefinition Id="api.signuporsignin">
          <LoadUri>https://your-storage-account.blob.core.windows.net/your-container/customize-ui.html</LoadUri>
          <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
          <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0</DataUri>
          <Metadata>
            <Item Key="DisplayName">Signin and Signup</Item>
          </Metadata>
        </ContentDefinition>
      </ContentDefinitions>
    </BuildingBlocks>
    ```

1. Sla het bestand met extensies op.

### <a name="5-upload-and-test-your-updated-custom-policy"></a>5. uw bijgewerkte aangepaste beleid uploaden en testen

#### <a name="51-upload-the-custom-policy"></a>5,1 het aangepaste beleid uploaden

1. Zorg ervoor dat u de map gebruikt die uw Azure AD B2C-tenant bevat door in het bovenste menu te klikken op het filter **Map en abonnement** en de map te kiezen waarin de tenant zich bevindt.
1. Zoek en selecteer **Azure AD B2C**.
1. Onder **beleids regels** selecteert u **identiteits ervaring-Framework**.
1. Selecteer **aangepast beleid uploaden**.
1. Upload het extensie bestand dat u eerder hebt gewijzigd.

#### <a name="52-test-the-custom-policy-by-using-run-now"></a>5,2 het aangepaste beleid testen met behulp van **nu uitvoeren**

1. Selecteer het beleid dat u hebt geüpload en selecteer **nu uitvoeren**.
1. U moet zich kunnen aanmelden met behulp van een e-mail adres.

[!INCLUDE [active-directory-b2c-html-templates](../../includes/active-directory-b2c-html-templates.md)]

## <a name="configure-dynamic-custom-page-content-uri"></a>URI voor dynamische aangepaste pagina-inhoud configureren

Door Azure AD B2C aangepast beleid te gebruiken, kunt u een para meter verzenden in het URL-pad of een query reeks. Door de parameter door te geven aan uw HTML-eindpunt, kunt u de pagina-inhoud dynamisch wijzigen. U kunt bijvoorbeeld de achtergrondafbeelding op de registratie- of aanmeldingspagina van Azure AD B2C wijzigen, op basis van een parameter die u doorgeeft vanuit uw web- of mobiele toepassing. De para meter kan elke [claim resolver](claim-resolver-overview.md)zijn, zoals de toepassings-id, taal-id of para meter voor de aangepaste query reeks, zoals `campaignId` .

### <a name="sending-query-string-parameters"></a>Query teken reeks parameters verzenden

Als u para meters voor query reeksen wilt verzenden, voegt u in het [Relying Party-beleid](relyingparty.md)een element toe, `ContentDefinitionParameters` zoals hieronder wordt weer gegeven.

```xml
<RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <UserJourneyBehaviors>
    <ContentDefinitionParameters>
        <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
        <Parameter Name="lang">{Culture:LanguageName}</Parameter>
        <Parameter Name="appId">{OIDC:ClientId}</Parameter>
    </ContentDefinitionParameters>
    </UserJourneyBehaviors>
    ...
</RelyingParty>
```

Wijzig in uw inhouds definitie de waarde van `LoadUri` in `https://<app_name>.azurewebsites.net/home/unified` . Het aangepaste beleid `ContentDefinition` moet eruitzien zoals in het volgende code fragment:

```xml
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>https://<app_name>.azurewebsites.net/home/unified</LoadUri>
  ...
</ContentDefinition>
```

Wanneer Azure AD B2C de pagina laadt, wordt een aanroep van het eind punt van de webserver uitgevoerd:

```http
https://<app_name>.azurewebsites.net/home/unified?campaignId=123&lang=fr&appId=f893d6d3-3b6d-480d-a330-1707bf80ebea
```

### <a name="dynamic-page-content-uri"></a>URI van dynamische pagina-inhoud

Inhoud kan vanaf verschillende locaties worden opgehaald op basis van de gebruikte para meters. Stel in het eind punt waarvoor CORS is ingeschakeld een mapstructuur in om inhoud te hosten. U kunt bijvoorbeeld de inhoud in de volgende structuur indelen. Hoofdmap */map per taal/uw HTML-bestanden*. Uw aangepaste pagina-URI kan er bijvoorbeeld als volgt uitzien:

```xml
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>https://contoso.blob.core.windows.net/{Culture:LanguageName}/myHTML/unified.html</LoadUri>
  ...
</ContentDefinition>
```

Azure AD B2C verzendt de ISO-code van twee letters voor de taal, `fr` voor Frans:

```http
https://contoso.blob.core.windows.net/fr/myHTML/unified.html
```

## <a name="next-steps"></a>Volgende stappen

Zie [Naslag Gids voor UI-aanpassing voor gebruikers stromen](customize-ui-overview.md)voor meer informatie over UI-elementen die kunnen worden aangepast.