---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 03/19/2020
ms.author: mimart
ms.openlocfilehash: 4cb4cdef227c786b12790903037fdc02649592e3
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95990873"
---
## <a name="use-custom-page-content"></a>Aangepaste pagina-inhoud gebruiken

Met de functie voor het aanpassen van de gebruikers interface van de pagina kunt u het uiterlijk van elk aangepast beleid aanpassen. U kunt er ook voor zorgen dat er visuele en merkconsistentie is tussen uw toepassing en Azure AD B2C.

### <a name="how-it-works"></a>Uitleg

Azure AD B2C code wordt uitgevoerd in de browser van uw klant door gebruik te maken van [Cross-Origin Resource Sharing (CORS)](https://www.w3.org/TR/cors/). Tijdens runtime wordt inhoud geladen vanuit een URL die u opgeeft in uw gebruikers stroom of aangepast beleid. Elke pagina in de gebruikers ervaring laadt de inhoud van de URL die u voor die pagina opgeeft. Nadat de inhoud is geladen vanuit uw URL, wordt deze samengevoegd met een HTML-fragment dat is ingevoegd door Azure AD B2C, waarna de pagina wordt weer gegeven aan uw klant.

![Inhouds marge voor aangepaste pagina](./media/active-directory-b2c-html-templates/html-content-merging.png)

## <a name="custom-html-page-content"></a>Aangepaste HTML-pagina-inhoud

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

### <a name="customize-the-default-azure-ad-b2c-pages"></a>De standaard Azure AD B2C pagina's aanpassen

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

Wanneer u uw eigen HTML-en CSS-bestanden gebruikt om de gebruikers interface aan te passen, moet u de inhoud van de gebruikers interface hosten op een openbaar beschikbaar HTTPS-eind punt dat CORS ondersteunt. Bijvoorbeeld [Azure Blob Storage](../articles/storage/blobs/storage-blobs-introduction.md), [Azure-app Services](../articles/app-service/index.yml), webservers, cdn's, AWS S3 of bestands share systemen.

## <a name="guidelines-for-using-custom-page-content"></a>Richt lijnen voor het gebruik van aangepaste pagina-inhoud

- Gebruik een absolute URL wanneer u externe resources zoals media, CSS en Java script-bestanden opneemt in uw HTML-bestand.
- Met de [pagina-indeling versie](../articles/active-directory-b2c/page-layout.md) 1.2.0 en hoger kunt u het `data-preload="true"` kenmerk toevoegen aan uw HTML-tags om de laad volgorde voor CSS en Java script te bepalen. Met wordt `data-preload=true` de pagina samengesteld voordat deze wordt weer gegeven aan de gebruiker. Met dit kenmerk wordt voor komen dat de pagina wordt ' Flik keren ' door het CSS-bestand vooraf te laden, zonder dat de HTML ongedaan wordt weer gegeven voor de gebruiker. Het volgende HTML-code fragment toont het gebruik van de `data-preload` tag.
  ```HTML
  <link href="https://path-to-your-file/sample.css" rel="stylesheet" type="text/css" data-preload="true"/>
  ```
- U wordt aangeraden te beginnen met de standaard pagina-inhoud en deze boven op het scherm te bouwen.
- U kunt Java script toevoegen aan uw aangepaste inhoud voor zowel [gebruikers stromen](../articles/active-directory-b2c/user-flow-javascript-overview.md) als [aangepaste beleids regels](../articles/active-directory-b2c/javascript-samples.md).
- Ondersteunde browser versies zijn:
  - Internet Explorer 11, 10 en micro soft Edge
  - Beperkte ondersteuning voor Internet Explorer 9 en 8
  - Google Chrome 42,0 en hoger
  - Mozilla Firefox 38,0 en hoger
  - Safari voor iOS en macOS, versie 12 en hoger
- Vanwege beveiligings beperkingen wordt Azure AD B2C geen ondersteuning geboden voor `frame` , `iframe` of `form` HTML-elementen.

## <a name="custom-page-content-walkthrough"></a>Overzicht van aangepaste pagina-inhoud

Hier volgt een overzicht van het proces:

1. Een locatie voorbereiden voor het hosten van uw aangepaste pagina-inhoud (een met open bare toegang tot een HTTPS-eind punt waarvoor CORS is ingeschakeld).
1. Een standaard pagina-inhouds bestand downloaden en aanpassen, bijvoorbeeld `unified.html` .
1. Publiceer uw aangepaste pagina-inhoud uw openbaar beschik bare HTTPS-eind punt.
1. CORS (cross-Origin Resource Sharing) instellen voor uw web-app.
1. Wijs uw beleid naar de inhouds-URI van uw aangepaste beleid.

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

1. Plak het gekopieerde fragment in een tekst editor en sla het bestand op als *customize-ui.html*.

> [!NOTE]
> HTML-formulier elementen worden verwijderd vanwege beveiligings beperkingen als u login.microsoftonline.com gebruikt. Als u HTML-formulier elementen wilt gebruiken in uw aangepaste HTML-inhoud, [gebruikt u b2clogin.com](../articles/active-directory-b2c/b2clogin.md).

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