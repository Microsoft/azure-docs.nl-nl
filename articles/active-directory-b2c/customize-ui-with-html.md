---
title: De gebruikersinterface aanpassen
titleSuffix: Azure AD B2C
description: Meer informatie over het aanpassen van de gebruikers interface voor uw toepassingen die gebruikmaken van Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/10/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 869cf5a47831844b04e0461a95fb7d16aa4d1569
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/11/2020
ms.locfileid: "97111300"
---
# <a name="customize-the-user-interface-in-azure-active-directory-b2c"></a>De gebruikers interface in Azure Active Directory B2C aanpassen

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

De gebruikers interface die Azure Active Directory B2C (Azure AD B2C) aan uw klanten wordt weer gegeven, kunt u voorzien van een naadloze gebruikers ervaring in uw toepassing. Deze ervaring omvat het aanmelden, aanmelden, het bewerken van profielen en het opnieuw instellen van wacht woorden. Dit artikel bevat een inleiding tot de methoden voor het aanpassen van de gebruikers interface (UI). 

> [!TIP]
> Als u alleen het banner logo, de achtergrond afbeelding en de achtergrond kleur van de pagina's van uw gebruikers stroom wilt wijzigen, kunt u de [bedrijfs huisstijl](company-branding.md) functie proberen.


## <a name="custom-html-and-css-overview"></a>Overzicht van aangepaste HTML en CSS


Azure AD B2C code wordt uitgevoerd in de browser van uw klant door gebruik te maken van [Cross-Origin Resource Sharing (CORS)](https://www.w3.org/TR/cors/). Tijdens runtime wordt inhoud geladen vanuit een URL die u opgeeft in uw gebruikers stroom of aangepast beleid. Elke pagina in de gebruikers ervaring laadt de inhoud van de URL die u voor die pagina opgeeft. Nadat de inhoud is geladen vanuit uw URL, wordt deze samengevoegd met een HTML-fragment dat is ingevoegd door Azure AD B2C, waarna de pagina wordt weer gegeven aan uw klant.

![Inhouds marge voor aangepaste pagina](./media/customize-ui-with-html/html-content-merging.png)

### <a name="custom-html-page-content"></a>Aangepaste HTML-pagina-inhoud

Maak een HTML-pagina met uw eigen huis stijl om uw aangepaste pagina-inhoud te leveren. Deze pagina kan een statische `*.html` pagina zijn of een dynamische pagina zoals .net, Node.js of php.

De inhoud van uw aangepaste pagina kan HTML-elementen bevatten, waaronder CSS en Java script, maar kan geen onbeveiligde elementen zoals iframes bevatten. Het enige vereiste element is een div-element waarvan is `id` ingesteld op `api` , zoals dit is `<div id="api"></div>` in de HTML-pagina.

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

#### <a name="customize-the-default-azure-ad-b2c-pages"></a>De standaard Azure AD B2C pagina's aanpassen

In plaats van uw aangepaste pagina-inhoud helemaal zelf te maken, kunt u de standaard pagina-inhoud van Azure AD B2C's aanpassen.

De volgende tabel bevat de standaard pagina-inhoud die wordt verschaft door Azure AD B2C. Down load de bestanden en gebruik deze als uitgangs punt voor het maken van uw eigen aangepaste pagina's.

| Standaard pagina | Description | ID van de inhouds definitie<br/>(alleen aangepast beleid) |
|:-----------------------|:--------|-------------|
| [exception.html](https://login.microsoftonline.com/static/tenant/default/exception.cshtml) | **Fout pagina**. Deze pagina wordt weer gegeven wanneer er een uitzonde ring of een fout wordt aangetroffen. | *API. error* |
| [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) |  **Zelfbevestigende pagina**. Gebruik dit bestand als aangepaste pagina-inhoud voor een aanmeldings pagina voor een sociaal account, een aanmeldings pagina voor een lokaal account, een aanmeldings pagina voor het lokale account, het opnieuw instellen van wacht woorden en meer. Het formulier kan verschillende invoer besturings elementen bevatten, zoals een tekstinvoervak, een vak voor het invoeren van een wacht woord, een keuze rondje, vervolg keuze vakjes en meervoudige selectie vakjes. | *API. localaccountsignin*, *API. localaccountsignup*, *API. localaccountpasswordreset*, *API. selfasserted* |
| [multifactor-1.0.0.html](https://login.microsoftonline.com/static/tenant/default/multifactor-1.0.0.cshtml) | **Multi-factor Authentication-pagina**. Op deze pagina kunnen gebruikers hun telefoon nummers (met behulp van tekst of spraak) verifiëren tijdens het registreren of aanmelden. | *API. Phone factor* |
| [updateprofile.html](https://login.microsoftonline.com/static/tenant/default/updateProfile.cshtml) | **Pagina Profiel bijwerken**. Deze pagina bevat een formulier dat gebruikers kunnen gebruiken om hun profiel bij te werken. Deze pagina is vergelijkbaar met de aanmeldings pagina voor het sociaal account, met uitzonde ring van de velden voor het invoeren van wacht woorden. | *API. selfasserted. profileupdate* |
| [unified.html](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) | **Uniforme registratie-of aanmeldings pagina**. Op deze pagina worden de gebruikers registratie en het aanmeldings proces afgehandeld. Gebruikers kunnen ondernemings-id-providers, sociale id-providers, zoals Facebook of Google + of lokale accounts gebruiken. | *API. signuporsignin* |

## <a name="hosting-the-page-content"></a>De pagina-inhoud hosten

Wanneer u uw eigen HTML-en CSS-bestanden gebruikt om de gebruikers interface aan te passen, moet u de inhoud van de gebruikers interface hosten op een openbaar beschikbaar HTTPS-eind punt dat CORS ondersteunt. Bijvoorbeeld [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md), [Azure-app Services](../app-service/index.yml), webservers, cdn's, AWS S3 of bestands share systemen.

## <a name="guidelines-for-using-custom-page-content"></a>Richt lijnen voor het gebruik van aangepaste pagina-inhoud

- Gebruik een absolute URL wanneer u externe resources zoals media, CSS en Java script-bestanden opneemt in uw HTML-bestand.
- Met de [pagina-indeling versie](page-layout.md) 1.2.0 en hoger kunt u het `data-preload="true"` kenmerk toevoegen aan uw HTML-tags om de laad volgorde voor CSS en Java script te bepalen. Met wordt `data-preload=true` de pagina samengesteld voordat deze wordt weer gegeven aan de gebruiker. Met dit kenmerk wordt voor komen dat de pagina wordt ' Flik keren ' door het CSS-bestand vooraf te laden, zonder dat de HTML ongedaan wordt weer gegeven voor de gebruiker. Het volgende HTML-code fragment toont het gebruik van de `data-preload` tag.
  ```HTML
  <link href="https://path-to-your-file/sample.css" rel="stylesheet" type="text/css" data-preload="true"/>
  ```
- U wordt aangeraden te beginnen met de standaard pagina-inhoud en deze boven op het scherm te bouwen.
- U kunt [Java script toevoegen](javascript-and-page-layout.md) aan uw aangepaste inhoud.
- Ondersteunde browser versies zijn:
  - Internet Explorer 11, 10 en micro soft Edge
  - Beperkte ondersteuning voor Internet Explorer 9 en 8
  - Google Chrome 42,0 en hoger
  - Mozilla Firefox 38,0 en hoger
  - Safari voor iOS en macOS, versie 12 en hoger
- Vanwege beveiligings beperkingen wordt Azure AD B2C geen ondersteuning geboden voor `frame` , `iframe` of `form` HTML-elementen.

## <a name="localize-content"></a>Inhoud lokaliseren

U kunt uw HTML-inhoud lokaliseren door [taal aanpassing](language-customization.md) in te scha kelen in uw Azure AD B2C-Tenant. Als u deze functie inschakelt, kunnen Azure AD B2C de OpenID Connect Connect-para meter `ui_locales` naar uw eind punt door sturen. De inhouds server kan deze para meter gebruiken om taalspecifieke HTML-pagina's op te geven.

Inhoud kan vanaf verschillende locaties worden opgehaald op basis van de land instelling die wordt gebruikt. In het eind punt waarvoor CORS is ingeschakeld, stelt u een mapstructuur in om inhoud voor specifieke talen te hosten. U belt het juiste nummer als u de Joker teken waarde gebruikt `{Culture:RFC5646}` .

Uw aangepaste pagina-URI kan er bijvoorbeeld als volgt uitzien:

```http
https://contoso.blob.core.windows.net/{Culture:RFC5646}/myHTML/unified.html
```

U kunt de pagina in het Frans laden door inhoud op te halen uit:

```http
https://contoso.blob.core.windows.net/fr/myHTML/unified.html
```

## <a name="custom-page-content-walkthrough"></a>Overzicht van aangepaste pagina-inhoud

Hier volgt een overzicht van het proces:

1. Een locatie voorbereiden voor het hosten van uw aangepaste pagina-inhoud (een met open bare toegang tot een HTTPS-eind punt waarvoor CORS is ingeschakeld).
1. Een standaard pagina-inhouds bestand downloaden en aanpassen, bijvoorbeeld `unified.html` .
1. Publiceer uw aangepaste pagina-inhoud uw openbaar beschik bare HTTPS-eind punt.
1. CORS (cross-Origin Resource Sharing) instellen voor uw web-app.
1. Wijs uw beleid naar de inhouds-URI van uw aangepaste beleid.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]


### <a name="1-create-your-html-content"></a>1. uw HTML-inhoud maken

Maak een aangepaste pagina-inhoud met de merk naam van uw product in de titel.

1. Kopieer het volgende HTML-code fragment. Het is een goed gevormde HTML5 met een leeg element dat *\<div id="api"\>\</div\>* zich binnen de Tags bevindt *\<body\>* . Dit element geeft aan waar Azure AD B2C inhoud moet worden ingevoegd.

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

1. Plak het gekopieerde fragment in een tekst editor
1. Gebruik CSS om de UI-elementen die Azure AD B2C invoegen in uw pagina, te opmaken. In het volgende voor beeld ziet u een eenvoudig CSS-bestand dat ook instellingen bevat voor de door aanmeldingen geïnjecteerde HTML-elementen:

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

1.  Sla het bestand op als *customize-ui.html*.

> [!NOTE]
> HTML-formulier elementen worden verwijderd vanwege beveiligings beperkingen als u login.microsoftonline.com gebruikt. Als u HTML-formulier elementen wilt gebruiken in uw aangepaste HTML-inhoud, [gebruikt u b2clogin.com](b2clogin.md).

### <a name="2-create-an-azure-blob-storage-account"></a>2. Maak een Azure Blob-opslag account

In dit artikel gebruiken we Azure Blob-opslag om onze inhoud te hosten. U kunt ervoor kiezen om uw inhoud op een webserver te hosten, maar u moet [CORS inschakelen op de webserver](https://enable-cors.org/server.html).

Als u uw HTML-inhoud in Blob Storage wilt hosten, voert u de volgende stappen uit:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Selecteer in het **hub** -menu de optie **Nieuw**  >  **opslag**  >  **opslag account**.
1. Selecteer een **abonnement** voor uw opslag account.
1. Maak een **resource groep** of selecteer een bestaande.
1. Voer een unieke **naam** in voor uw opslag account.
1. Selecteer de **geografische locatie** voor uw opslag account.
1. **Implementatie model** kan **Resource Manager** blijven.
1. **Prestaties** kunnen **standaard** blijven.
1. Wijzig het **type account** in **Blob Storage**.
1. **Replicatie** kan **Ra-GRS** blijven.
1. De **toegangs laag** kan **Hot** blijven.
1. Selecteer **controleren + maken** om het opslag account te maken.
    Nadat de implementatie is voltooid, wordt de pagina **opslag account** automatisch geopend.

#### <a name="21-create-a-container"></a>2,1 een container maken

Als u een open bare container in Blob Storage wilt maken, voert u de volgende stappen uit:

1. Onder **BLOB service** in het linkermenu selecteert u **blobs**.
1. Selecteer **+ container**.
1. Voer *basis* in bij **naam**. De naam kan een naam zijn van uw keuze, bijvoorbeeld *Contoso*, maar in dit voor beeld wordt het *hoofd* gebruikt voor eenvoud.
1. Selecteer **BLOB** voor **openbaar toegangs niveau** en klik vervolgens op **OK**.
1. Selecteer **hoofd** om de nieuwe container te openen.

#### <a name="22-upload-your-custom-page-content-files"></a>2,2 uw aangepaste pagina met inhouds bestanden uploaden

1. Selecteer **Uploaden**.
1. Selecteer het mappictogram naast **een bestand selecteren**.
1. Navigeer naar en selecteer **customize-ui.html**, die u eerder hebt gemaakt in de sectie UI-aanpassing van de pagina.
1. Als u wilt uploaden naar een submap, vouwt u **Geavanceerd** uit en voert u de naam van een map in **uploaden naar map** in.
1. Selecteer **Uploaden**.
1. Selecteer de **customize-ui.html** -blob die u hebt geüpload.
1. Selecteer rechts van het tekstvak **URL** het pictogram **kopiëren naar klem bord** om de URL naar het klem bord te kopiëren.
1. Navigeer in webbrowser naar de URL die u hebt gekopieerd om te controleren of de blob die u hebt geüpload, toegankelijk is. Als het niet toegankelijk is, bijvoorbeeld als er een fout optreedt `ResourceNotFound` , controleert u of het toegangs type voor de container is ingesteld op **BLOB**.

### <a name="3-configure-cors"></a>3. CORS configureren

Configureer de Blob-opslag voor cross-Origin-resource delen door de volgende stappen uit te voeren:

1. Selecteer in het menu de optie **CORS**.
1. Geef `https://your-tenant-name.b2clogin.com` op bij **Toegestane oorsprongen**. Vervang `your-tenant-name` door de naam van uw Azure AD B2C-tenant. Bijvoorbeeld `https://fabrikam.b2clogin.com`. Gebruik alleen kleine letters bij het invoeren van de naam van uw Tenant.
1. Voor **toegestane methoden** selecteert u beide `GET` en `OPTIONS` .
1. Voer bij **Toegestane headers** een asterisk (*) in.
1. Voer bij **Zichtbare headers** een asterisk (*) in.
1. Voer bij **Maximumleeftijd** 200 in.
1. Selecteer **Opslaan**.

#### <a name="31-test-cors"></a>CORS voor 3,1 testen

Controleer of u klaar bent door de volgende stappen uit te voeren:

1. Herhaal de stap CORS configureren. Voer voor **toegestane oorsprongen**`https://www.test-cors.org`
1. Ga naar [www.test-cors.org](https://www.test-cors.org/) 
1. Plak in het vak **externe URL** de URL van uw HTML-bestand. Bijvoorbeeld: `https://your-account.blob.core.windows.net/root/azure-ad-b2c/unified.html`
1. Selecteer **aanvraag verzenden**.
    Het resultaat moet zijn `XHR status: 200` . 
    Als u een fout bericht ontvangt, moet u ervoor zorgen dat de CORS-instellingen juist zijn. Mogelijk moet u ook de cache van de browser wissen of een persoonlijke browser sessie openen door op CTRL + SHIFT + P te drukken.


::: zone pivot="b2c-user-flow"

### <a name="4-update-the-user-flow"></a>4. werk de stroom van de gebruiker bij

1. Kies linksboven in Azure Portal **Alle services**, zoek **Azure AD B2C** en selecteer dit.
1. Selecteer **Gebruikersstromen** en vervolgens de gebruikersstroom *B2C_1_signupsignin1*.
1. Selecteer **Pagina-indelingen** en klik vervolgens bij **Gecombineerde pagina voor registratie en aanmelding** op **Ja** voor **Aangepaste pagina-inhoud gebruiken**.
1. Geef in **URI voor aangepaste pagina** de URI op voor het eerder gemaakte bestand *custom-ui.html*.
1. Klik bovenaan de pagina op **Opslaan**.

### <a name="5-test-the-user-flow"></a>5. de gebruikers stroom testen

1. Selecteer in uw Azure AD B2C-tenant de optie **Gebruikersstromen** en vervolgens de gebruikersstroom *B2C_1_signupsignin1*.
1. Klik bovenaan de pagina op **Gebruikersstroom uitvoeren**.
1. Klik op de knop **Gebruikersstroom uitvoeren**.

Als het goed is, ziet u een pagina die lijkt op het volgende voorbeeld, met de elementen gecentreerd op basis van het CSS-bestand dat u hebt gemaakt:

![Webbrowser met een registratie- of aanmeldingspagina met aangepaste UI-elementen](./media/customize-ui-with-html/run-now.png)

::: zone-end

::: zone pivot="b2c-custom-policy"

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

::: zone-end

## <a name="sample-templates"></a>Voorbeeldsjablonen

U kunt hier voorbeeld sjablonen voor UI-aanpassing vinden:

```bash
git clone https://github.com/Azure-Samples/Azure-AD-B2C-page-templates
```

Dit project bevat de volgende sjablonen:
- [Oceaan blauw](https://github.com/Azure-Samples/Azure-AD-B2C-page-templates/tree/master/ocean_blue)
- [Pastel grijs](https://github.com/Azure-Samples/Azure-AD-B2C-page-templates/tree/master/slate_gray)

Het voor beeld gebruiken:

1. Kloon de opslag plaats op uw lokale machine. Kies een sjabloon map `/ocean_blue` of `/slate_gray` .
1. Upload alle bestanden in de map Temp late en de `/assets` map naar Blob Storage, zoals beschreven in de vorige secties.
1. Open vervolgens elk `\*.html` bestand in de hoofdmap van ofwel `/ocean_blue` of `/slate_gray` , vervang alle exemplaren van relatieve Url's door de url's van de CSS-, afbeeldings-en letter typen bestanden die u hebt geüpload in stap 2. Bijvoorbeeld:
    ```html
    <link href="./css/assets.css" rel="stylesheet" type="text/css" />
    ```

    Tot
    ```html
    <link href="https://your-storage-account.blob.core.windows.net/your-container/css/assets.css" rel="stylesheet" type="text/css" />
    ```
1. Sla de `\*.html` bestanden op en upload deze naar de Blob-opslag.
1. Pas het beleid aan, zoals eerder is vermeld, naar uw HTML-bestand.
1. Als u ontbrekende letter typen, afbeeldingen of CSS ziet, controleert u uw referenties in het uitbrei ding beleid en de \* . html-bestanden.


## <a name="next-steps"></a>Volgende stappen

Meer informatie over het inschakelen van [Java script-code aan de client zijde](javascript-and-page-layout.md).



