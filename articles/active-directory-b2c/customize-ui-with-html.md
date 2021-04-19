---
title: De gebruikersinterface aanpassen met HTML-sjablonen
titleSuffix: Azure AD B2C
description: Meer informatie over het aanpassen van de gebruikersinterface met HTML-sjablonen voor uw toepassingen die gebruikmaken van Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/19/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 8f9f6dc1abd08c5e53f3d44a8f6ec1b3e20786ed
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107717437"
---
# <a name="customize-the-user-interface-with-html-templates-in-azure-active-directory-b2c"></a>De gebruikersinterface aanpassen met HTML-sjablonen in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

De huisstijl en het aanpassen van de gebruikersinterface Azure Active Directory B2C (Azure AD B2C) wordt weergegeven aan uw klanten, zorgt voor een naadloze gebruikerservaring in uw toepassing. Deze ervaringen omvatten registreren, aanmelden, profiel bewerken en wachtwoord opnieuw instellen. In dit artikel worden de methoden voor het aanpassen van de gebruikersinterface (UI) beschreven. 

> [!TIP]
> Als u alleen het bannerlogo, de achtergrondafbeelding en de achtergrondkleur van uw gebruikersstroompagina's wilt wijzigen, kunt u de functie [Huisstijl](customize-ui.md) van uw bedrijf proberen.

## <a name="custom-html-and-css-overview"></a>Overzicht van aangepaste HTML en CSS

Azure AD B2C code wordt uitgevoerd in de browser van uw klant met behulp [van Cross-Origin Resource Sharing (CORS).](https://www.w3.org/TR/cors/) Tijdens runtime wordt inhoud geladen vanaf een URL die u in uw gebruikersstroom of aangepast beleid opgeeft. Elke pagina in de gebruikerservaring laadt de inhoud van de URL die u voor die pagina opgeeft. Nadat inhoud is geladen vanuit uw URL, wordt deze samengevoegd met een HTML-fragment dat is ingevoegd door Azure AD B2C, waarna de pagina wordt weergegeven aan uw klant.

![Marge voor aangepaste pagina-inhoud](./media/customize-ui-with-html/html-content-merging.png)

### <a name="custom-html-page-content"></a>Aangepaste HTML-pagina-inhoud

Maak een HTML-pagina met uw eigen huisstijl om uw aangepaste pagina-inhoud te leveren. Deze pagina kan een statische pagina zijn of een dynamische pagina zoals `*.html` .NET, Node.js of PHP.

Uw aangepaste pagina-inhoud kan html-elementen bevatten, waaronder CSS en JavaScript, maar kan geen onveilige elementen bevatten, zoals iframes. Het enige vereiste element is een div-element met `id` ingesteld op , zoals dit element op uw `api` `<div id="api"></div>` HTML-pagina.

```html
<!DOCTYPE html>
<html>
<head>
    <title>My Product Brand Name</title>
</head>
<body>
    <div id="api"></div>
</body>
</html>
```

#### <a name="customize-the-default-azure-ad-b2c-pages"></a>De standaardpagina'Azure AD B2C aanpassen

In plaats van de inhoud van uw aangepaste pagina volledig opnieuw te maken, kunt u de standaardpagina-inhoud Azure AD B2C van uw pagina aanpassen.

De volgende tabel bevat de standaardpagina-inhoud van Azure AD B2C. Download de bestanden en gebruik deze als uitgangspunt voor het maken van uw eigen aangepaste pagina's.

| Standaardpagina | Description | Id van inhoudsdefinitie<br/>(alleen aangepast beleid) |
|:-----------------------|:--------|-------------|
| [exception.html](https://login.microsoftonline.com/static/tenant/default/exception.cshtml) | **Foutpagina**. Deze pagina wordt weergegeven wanneer er een uitzondering of fout wordt aangetroffen. | *api.error* |
| [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) |  **Zelf-bevestigd pagina**. Gebruik dit bestand als aangepaste pagina-inhoud voor een aanmeldingspagina voor sociale account, een aanmeldingspagina voor een lokaal account, een aanmeldingspagina voor een lokaal account, wachtwoord opnieuw instellen en meer. Het formulier kan verschillende invoerbesturingselementen bevatten, zoals een tekstinvoervak, een wachtwoordinvoervak, een keuzerondje, vervolgkeuzevakken met één selectie en selectievakjes voor meervoudige selectie. | *api.localaccountsignin*, *api.localaccountsignup*, *api.localaccountpasswordreset*, *api.selfasserted* |
| [multifactor-1.0.0.html](https://login.microsoftonline.com/static/tenant/default/multifactor-1.0.0.cshtml) | **Pagina Meervoudige verificatie.** Op deze pagina kunnen gebruikers hun telefoonnummers controleren (met behulp van tekst of spraak) tijdens het registreren of aanmelden. | *api.phonefactor* |
| [updateprofile.html](https://login.microsoftonline.com/static/tenant/default/updateProfile.cshtml) | **Pagina profielupdate**. Deze pagina bevat een formulier dat gebruikers kunnen openen om hun profiel bij te werken. Deze pagina is vergelijkbaar met de aanmeldingspagina van het sociale account, met uitzondering van de velden voor wachtwoordinvoer. | *api.selfasserted.profileupdate* |
| [unified.html](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) | **Uniforme aanmeldings- of aanmeldingspagina**. Op deze pagina wordt het aanmeldingsproces voor gebruikers verwerkt. Gebruikers kunnen zakelijke id-providers, id-providers voor sociale netwerken, zoals Facebook of Google+, of lokale accounts gebruiken. | *api.signuporsignin* |

## <a name="hosting-the-page-content"></a>De pagina-inhoud hosten

Wanneer u uw eigen HTML- en CSS-bestanden gebruikt om de gebruikersinterface aan te passen, host u uw UI-inhoud op elk openbaar beschikbaar HTTPS-eindpunt dat CORS ondersteunt. Bijvoorbeeld [Azure Blob Storage,](../storage/blobs/storage-blobs-introduction.md) [Azure-app Services,](../app-service/index.yml)webservers, CDN's, AWS S3 of systemen voor bestandsdeling.

## <a name="guidelines-for-using-custom-page-content"></a>Richtlijnen voor het gebruik van aangepaste pagina-inhoud

- Gebruik een absolute URL wanneer u externe resources, zoals media, CSS en JavaScript-bestanden, in uw HTML-bestand op te nemen.
- Met [pagina-indelingsversie](page-layout.md) 1.2.0 en hoger kunt u het kenmerk toevoegen aan uw HTML-tags om de laadorder voor CSS en `data-preload="true"` JavaScript te bepalen. Met `data-preload="true"` wordt de pagina samengesteld voordat deze wordt weergegeven aan de gebruiker. Dit kenmerk helpt voorkomen dat de pagina wordt 'gesereerd' door het CSS-bestand vooraf te laden, zonder dat de niet-gestijlde HTML aan de gebruiker wordt weergegeven. Het volgende HTML-codefragment toont het gebruik van de `data-preload` tag.
  ```HTML
  <link href="https://path-to-your-file/sample.css" rel="stylesheet" type="text/css" data-preload="true"/>
  ```
- We raden u aan om te beginnen met de standaardpagina-inhoud en deze verder te bouwen.
- U kunt [JavaScript opnemen](javascript-and-page-layout.md) in uw aangepaste inhoud.
- Ondersteunde browserversies zijn:
  - Internet Explorer 11, 10 en Microsoft Edge
  - Beperkte ondersteuning voor Internet Explorer 9 en 8
  - Google Chrome 42.0 en hoger
  - Mozilla Firefox 38.0 en hoger
  - Safari voor iOS en macOS, versie 12 en hoger
- Vanwege beveiligingsbeperkingen biedt Azure AD B2C geen ondersteuning voor `frame` `iframe` HTML-elementen , `form` of .

## <a name="localize-content"></a>Inhoud lokaliseren

U lokaliseert uw HTML-inhoud door [taalaanpassing in te](language-customization.md) Azure AD B2C tenant. Door deze functie in te Azure AD B2C u de parameter OpenID Connect `ui_locales` doorsturen naar uw eindpunt. Uw inhoudsserver kan deze parameter gebruiken om taalspecifieke HTML-pagina's op te geven.

Inhoud kan van verschillende locaties worden gehaald op basis van de gebruikte landen. In het eindpunt met CORS-functie stelt u een mapstructuur in voor het hosten van inhoud voor specifieke talen. U roept het juiste aan als u de jokertekenwaarde `{Culture:RFC5646}` gebruikt.

Uw aangepaste pagina-URI kan er bijvoorbeeld als volgende uitzien:

```http
https://contoso.blob.core.windows.net/{Culture:RFC5646}/myHTML/unified.html
```

U kunt de pagina in het Frans laden door inhoud op te halen uit:

```http
https://contoso.blob.core.windows.net/fr/myHTML/unified.html
```

## <a name="custom-page-content-walkthrough"></a>Overzicht van aangepaste pagina-inhoud

Hier is een overzicht van het proces:

1. Bereid een locatie voor om uw aangepaste pagina-inhoud te hosten (een openbaar toegankelijk HTTPS-eindpunt met CORS-functie).
1. Download en pas een standaardbestand voor pagina-inhoud aan, bijvoorbeeld `unified.html` .
1. Publiceer de inhoud van uw aangepaste pagina uw openbaar beschikbare HTTPS-eindpunt.
1. Stel Cross-Origin Resource Sharing (CORS) in voor uw web-app.
1. Wijs uw beleid naar de inhouds-URI van uw aangepaste beleid.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

### <a name="1-create-your-html-content"></a>1. Uw HTML-inhoud maken

Maak een aangepaste pagina-inhoud met de merknaam van uw product in de titel.

1. Kopieer het volgende HTML-codefragment. Het is goed gevormde HTML5 met een leeg element met de naam *\<div id="api"\>\</div\>* dat zich in de tags *\<body\>* bevindt. Dit element geeft aan waar Azure AD B2C inhoud moet worden ingevoegd.

   ```html
   <!DOCTYPE html>
   <html>
   <head>
       <title>My Product Brand Name</title>
   </head>
   <body>
       <div id="api"></div>
   </body>
   </html>
   ```

1. Plak het gekopieerde fragment in een teksteditor
1. Gebruik CSS om de gebruikersinterface-elementen te Azure AD B2C op uw pagina worden geplaatst. In het volgende voorbeeld ziet u een eenvoudig CSS-bestand dat ook instellingen bevat voor de in de aanmelding geïnjecteerde HTML-elementen:

    ```css
    h1 {
      color: blue;
      text-align: center;
    }
    .intro h2 {
      text-align: center;
    }
    .entry {
      width: 400px ;
      margin-left: auto ;
      margin-right: auto ;
    }
    .divider h2 {
      text-align: center;
    }
    .create {
      width: 400px ;
      margin-left: auto ;
      margin-right: auto ;
    }
    ```

1.  Sla het bestand op *alscustomize-ui.html*.

> [!NOTE]
> HTML-formulierelementen worden verwijderd vanwege beveiligingsbeperkingen als u een login.microsoftonline.com. Als u HTML-formulierelementen wilt gebruiken in uw aangepaste HTML-inhoud, gebruikt [u b2clogin.com](b2clogin.md).

### <a name="2-create-an-azure-blob-storage-account"></a>2. Een Azure Blob Storage-account maken

In dit artikel gebruiken we Azure Blob Storage om onze inhoud te hosten. U kunt ervoor kiezen om uw inhoud op een webserver te hosten, maar u moet [CORS inschakelen op uw webserver.](https://enable-cors.org/server.html)

Voer de volgende stappen uit om uw HTML-inhoud te hosten in Blob Storage:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Selecteer in **het** menu Hub de optie **Nieuw**  >    >  **opslagaccount.**
1. Selecteer een **abonnement** voor uw opslagaccount.
1. Maak een **resourcegroep** of selecteer een bestaande.
1. Voer een unieke **naam in** voor uw opslagaccount.
1. Selecteer de **geografische locatie** voor uw opslagaccount.
1. **Het implementatiemodel** kan **Resource Manager.**
1. **Prestaties** kunnen Standard **blijven.**
1. Wijzig **Soort account** in Blob **Storage**.
1. **Replicatie** kan **RA-GRS blijven.**
1. **De toegangslaag** kan **Hot blijven.**
1. Selecteer **Beoordelen en maken om** het opslagaccount te maken.
    Nadat de implementatie is voltooid, wordt **de pagina Opslagaccount** automatisch geopend.

#### <a name="21-create-a-container"></a>2.1 Een container maken

Voer de volgende stappen uit om een openbare container te maken in Blob Storage:

1. Selecteer **Blob service** blobs in het menu aan **de linkerkant.**
1. Selecteer **+Container.**
1. Voer **bij Naam** de *hoofdmap in.* De naam kan een naam van uw keuze zijn, bijvoorbeeld *contoso*, maar we gebruiken *de* hoofdmap in dit voorbeeld voor het gemak.
1. Bij **Openbaar toegangsniveau** selecteert u **Blob** en vervolgens **OK.**
1. Selecteer **root om** de nieuwe container te openen.

#### <a name="22-upload-your-custom-page-content-files"></a>2.2 Uw aangepaste pagina-inhoudsbestanden uploaden

1. Selecteer **Uploaden**.
1. Selecteer het mappictogram naast **Een bestand selecteren.**
1. Navigeer naar en **selecteercustomize-ui.html**, die u eerder hebt gemaakt in de sectie Pagina-UI-aanpassing.
1. Als u wilt uploaden naar een submap, vouwt u **Geavanceerd** uit en voert u een mapnaam in **Uploaden naar map in.**
1. Selecteer **Uploaden**.
1. Selecteer de **customize-ui.html** blob die u hebt geüpload.
1. Selecteer rechts van het **tekstvak URL** het pictogram Naar **klembord** kopiëren om de URL naar het klembord te kopiëren.
1. Navigeer in de webbrowser naar de URL die u hebt gekopieerd om te controleren of de blob die u hebt geüpload toegankelijk is. Als deze niet toegankelijk is, bijvoorbeeld als er een fout wordt weergegeven, moet u ervoor zorgen dat het toegangstype voor de `ResourceNotFound` container is ingesteld op **blob**.

### <a name="3-configure-cors"></a>3. CORS configureren

Configureer Blob Storage voor Cross-Origin Resource Sharing door de volgende stappen uit te voeren:

1. Selecteer in het menu de optie **CORS**.
1. Geef `https://your-tenant-name.b2clogin.com` op bij **Toegestane oorsprongen**. Vervang `your-tenant-name` door de naam van uw Azure AD B2C-tenant. Bijvoorbeeld `https://fabrikam.b2clogin.com`. Gebruik alle kleine letters bij het invoeren van de naam van uw tenant.
1. Bij **Toegestane methoden** selecteert u `GET` zowel als `OPTIONS` .
1. Voer bij **Toegestane headers** een asterisk (*) in.
1. Voer bij **Zichtbare headers** een asterisk (*) in.
1. Voer bij **Maximumleeftijd** 200 in.
1. Selecteer **Opslaan**.

#### <a name="31-test-cors"></a>3.1 CORS testen

Controleer of u klaar bent door de volgende stappen uit te voeren:

1. Herhaal de stap CORS configureren. Bij **Toegestane oorsprongen** voert u in `https://www.test-cors.org`
1. Navigeer [naar www.test-cors.org](https://www.test-cors.org/) 
1. Plak in **het vak Externe URL** de URL van het HTML-bestand. Bijvoorbeeld: `https://your-account.blob.core.windows.net/root/azure-ad-b2c/unified.html`
1. Selecteer **Aanvraag verzenden.**
    Het resultaat moet `XHR status: 200` zijn. 
    Als er een foutmelding wordt weergegeven, moet u ervoor zorgen dat uw CORS-instellingen juist zijn. Mogelijk moet u ook uw browsercache wissen of een privébrowsersessie openen door op Ctrl+Shift+P te drukken.

::: zone pivot="b2c-user-flow"

### <a name="4-update-the-user-flow"></a>4. De gebruikersstroom bijwerken

1. Kies linksboven in Azure Portal **Alle services**, zoek **Azure AD B2C** en selecteer dit.
1. Selecteer **Gebruikersstromen** en vervolgens de gebruikersstroom *B2C_1_signupsignin1*.
1. Selecteer **Pagina-indelingen** en klik vervolgens bij **Gecombineerde pagina voor registratie en aanmelding** op **Ja** voor **Aangepaste pagina-inhoud gebruiken**.
1. Geef in **URI voor aangepaste pagina** de URI op voor het eerder gemaakte bestand *custom-ui.html*.
1. Klik bovenaan de pagina op **Opslaan**.

### <a name="5-test-the-user-flow"></a>5. De gebruikersstroom testen

1. Selecteer in uw Azure AD B2C-tenant de optie **Gebruikersstromen** en vervolgens de gebruikersstroom *B2C_1_signupsignin1*.
1. Klik bovenaan de pagina op **Gebruikersstroom uitvoeren**.
1. Klik op de knop **Gebruikersstroom uitvoeren**.

Als het goed is, ziet u een pagina die lijkt op het volgende voorbeeld, met de elementen gecentreerd op basis van het CSS-bestand dat u hebt gemaakt:

![Webbrowser met een registratie- of aanmeldingspagina met aangepaste UI-elementen](./media/customize-ui-with-html/run-now.png)

::: zone-end

::: zone pivot="b2c-custom-policy"

### <a name="4-modify-the-extensions-file"></a>4. Het extensiebestand wijzigen

Als u ui-aanpassing wilt configureren, kopieert **u ContentDefinition** en de onderliggende elementen van het basisbestand naar het extensiebestand.

1. Open het basisbestand van uw beleid. Bijvoorbeeld <em>`SocialAndLocalAccounts/`**`TrustFrameworkBase.xml`**</em> . Dit basisbestand is een van de beleidsbestanden die zijn opgenomen in het pakket voor aangepaste beleidsstarters, dat u moet hebben verkregen in de vereiste Aan de slag [met aangepaste beleidsregels.](./tutorial-create-user-flows.md?pivots=b2c-custom-policy)
1. Zoek en kopieer de volledige inhoud van het **element ContentDefinitions.**
1. Open het extensiebestand. U kunt *bijvoorbeeldTrustFrameworkExtensions.xml*. Zoek het **element BuildingBlocks.** Als het element niet bestaat, voegt u het toe.
1. Plak de volledige inhoud van het **element ContentDefinitions** dat u hebt gekopieerd als onderliggend element van **het element BuildingBlocks.**
1. Zoek het **element ContentDefinition** dat in de XML bevat `Id="api.signuporsignin"` die u hebt gekopieerd.
1. Wijzig de waarde van **LoadUri** in de URL van het HTML-bestand dat u naar de opslag hebt geüpload. Bijvoorbeeld `https://your-storage-account.blob.core.windows.net/your-container/customize-ui.html`.

    Uw aangepaste beleid moet er uitzien als het volgende codefragment:

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

1. Sla het extensiebestand op.

### <a name="5-upload-and-test-your-updated-custom-policy"></a>5. Uw bijgewerkte aangepaste beleid uploaden en testen

#### <a name="51-upload-the-custom-policy"></a>5.1 Het aangepaste beleid uploaden

1. Zorg ervoor dat u de map gebruikt die uw Azure AD B2C-tenant bevat door in het bovenste menu te klikken op het filter **Map en abonnement** en de map te kiezen waarin de tenant zich bevindt.
1. Zoek en selecteer **Azure AD B2C**.
1. Selecteer **onder** Beleid de **optie Identity Experience Framework.**
1. Selecteer **Aangepast beleid uploaden.**
1. Upload het extensiebestand dat u eerder hebt gewijzigd.

#### <a name="52-test-the-custom-policy-by-using-run-now"></a>5.2 Het aangepaste beleid testen met Nu **uitvoeren**

1. Selecteer het beleid dat u hebt geüpload en selecteer vervolgens **Nu uitvoeren.**
1. U moet zich kunnen registreren met behulp van een e-mailadres.

## <a name="configure-dynamic-custom-page-content-uri"></a>Dynamische URI voor aangepaste pagina-inhoud configureren

Door aangepaste Azure AD B2C te gebruiken, kunt u een parameter in het URL-pad of een queryreeks verzenden. Door de parameter door te geven aan uw HTML-eindpunt, kunt u de pagina-inhoud dynamisch wijzigen. U kunt bijvoorbeeld de achtergrondafbeelding op de registratie- of aanmeldingspagina van Azure AD B2C wijzigen, op basis van een parameter die u doorgeeft vanuit uw web- of mobiele toepassing. De parameter kan elke [claim resolver](claim-resolver-overview.md)zijn, zoals de toepassings-id, taal-id of aangepaste queryreeksparameter, zoals `campaignId` .

### <a name="sending-query-string-parameters"></a>Queryreeksparameters verzenden

Als u queryreeksparameters wilt verzenden, voegt u in [het relying party-beleid](relyingparty.md)een `ContentDefinitionParameters` -element toe, zoals hieronder wordt weergegeven.

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

Wijzig in uw inhoudsdefinitie de waarde van `LoadUri` in `https://<app_name>.azurewebsites.net/home/unified` . Uw aangepaste beleid `ContentDefinition` moet er als volgt uitzien:

```xml
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>https://<app_name>.azurewebsites.net/home/unified</LoadUri>
  ...
</ContentDefinition>
```

Wanneer Azure AD B2C pagina laadt, wordt het eindpunt van de webserver aanroepen:

```http
https://<app_name>.azurewebsites.net/home/unified?campaignId=123&lang=fr&appId=f893d6d3-3b6d-480d-a330-1707bf80ebea
```

### <a name="dynamic-page-content-uri"></a>URI voor dynamische pagina-inhoud

Inhoud kan van verschillende locaties worden gehaald op basis van de gebruikte parameters. Stel in het eindpunt met CORS-functie een mapstructuur in om inhoud te hosten. U kunt de inhoud bijvoorbeeld in de volgende structuur ordenen. *Hoofdmap/map per taal/uw HTML-bestanden.* Uw aangepaste pagina-URI kan er bijvoorbeeld als volgende uitzien:

```xml
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>https://contoso.blob.core.windows.net/{Culture:LanguageName}/myHTML/unified.html</LoadUri>
  ...
</ContentDefinition>
```

Azure AD B2C verzendt de tweeletterig ISO-code voor de taal, `fr` voor Frans:

```http
https://contoso.blob.core.windows.net/fr/myHTML/unified.html
```

::: zone-end

## <a name="sample-templates"></a>Voorbeeldsjablonen

U vindt hier voorbeeldsjablonen voor het aanpassen van de gebruikersinterface:

```bash
git clone https://github.com/azure-ad-b2c/html-templates
```

Dit project bevat de volgende sjablonen:
- [Oceaanblauw](https://github.com/azure-ad-b2c/html-templates/tree/main/templates/AzureBlue)
- [Slate Gray](https://github.com/azure-ad-b2c/html-templates/tree/main/templates/MSA)
- [Klassieke](https://github.com/azure-ad-b2c/html-templates/tree/main/templates/classic)
- [Sjabloonresources](https://github.com/azure-ad-b2c/html-templates/tree/main/templates/src)

Het voorbeeld gebruiken:

1. Kloon de repo op uw lokale computer. Kies een sjabloonmap `/AzureBlue` , `/MSA` of `/classic` .
1. Upload alle bestanden onder de sjabloonmap en de map `/src` naar Blob Storage, zoals beschreven in de vorige secties.
1. Open vervolgens elk bestand `\*.html` in de sjabloonmap. Vervang vervolgens alle exemplaren van `https://login.microsoftonline.com` URL's door de URL die u in stap 2 hebt geüpload. Bijvoorbeeld:
    
    Van:
    ```html
    https://login.microsoftonline.com/templates/src/fonts/segoeui.WOFF
    ```

    Aan:
    ```html
    https://your-storage-account.blob.core.windows.net/your-container/templates/src/fonts/segoeui.WOFF
    ```
    
1. Sla de `\*.html` bestanden op en upload ze naar de Blob-opslag.
1. Wijzig nu het beleid en verwijs naar uw HTML-bestand, zoals eerder vermeld.
1. Als u ontbrekende lettertypen, afbeeldingen of CSS ziet, controleert u uw verwijzingen in het extensiebeleid en de `\*.html` bestanden.

## <a name="use-company-branding-assets-in-custom-html"></a>Assets voor de huisstijl van uw bedrijf gebruiken in aangepaste HTML

Als u assets [voor de huisstijl](customize-ui.md#configure-company-branding) van uw bedrijf wilt gebruiken in een aangepaste HTML, voegt u de volgende tags buiten de `<div id="api">` tag toe. De bron van de afbeelding wordt vervangen door die van de achtergrondafbeelding en het bannerlogo.

```HTML
<img data-tenant-branding-background="true" />
<img data-tenant-branding-logo="true" alt="Company Logo" />
```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [inschakelen van JavaScript-code aan de clientzijde.](javascript-and-page-layout.md)
