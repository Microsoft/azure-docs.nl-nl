---
title: Een share point online-indexer configureren (preview-versie)
titleSuffix: Azure Cognitive Search
description: Stel een share point online-indexer in om het indexeren van document bibliotheek inhoud in azure Cognitive Search te automatiseren.
manager: luisca
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/01/2021
ms.openlocfilehash: 558df115043d76acf865f19611e8c4cd322e00a7
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101679715"
---
# <a name="how-to-configure-sharepoint-online-indexing-in-cognitive-search-preview"></a>Indexering van share point online configureren in Cognitive Search (preview)

> [!IMPORTANT] 
> Share point online-ondersteuning is momenteel beschikbaar in een **open bare preview-versie**. U kunt toegang tot de gated preview aanvragen door [dit formulier](https://aka.ms/azure-cognitive-search/indexer-preview)in te vullen.
>
> Deze previewfunctie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.
> 
> De [rest API versie 2020-06-30-preview](search-api-preview.md) biedt deze functie. Er is momenteel geen portal-of SDK-ondersteuning.

In dit artikel wordt beschreven hoe u Azure Cognitive Search gebruikt voor het indexeren van documenten (zoals Pdf's, Microsoft Office documenten en enkele andere algemene indelingen) die zijn opgeslagen in share point online-document bibliotheken in een Azure Cognitive Search-index. Eerst worden de basis principes uitgelegd van het instellen en configureren van de Indexeer functie. Vervolgens biedt het een uitgebreidere onderzoek van gedrag en scenario's die u waarschijnlijk zult tegen komen.

## <a name="functionality"></a>Functionaliteit
Een Indexeer functie in azure Cognitive Search is een crawler die Doorzoek bare gegevens en meta gegevens ophaalt uit een gegevens bron. De share point online-indexer maakt verbinding met uw share point online-site en indexeert documenten uit een of meer document bibliotheken. De Indexeer functie biedt de volgende functionaliteit:
+ Inhoud van een of meer share point online-document bibliotheken indexeren.
+ Index inhoud van share point online-document bibliotheken die zich in dezelfde Tenant bevinden als uw Azure Cognitive Search-service. De Indexeer functie werkt niet met share point-sites die zich in een andere Tenant bevinden dan uw Azure Cognitive Search-service. 
+ De Indexeer functie biedt ondersteuning voor incrementele indexering, wat betekent dat de inhoud in de document bibliotheek is gewijzigd en alleen de bijgewerkte inhoud voor toekomstige index uitvoeringen kan indexeren. Als er bijvoorbeeld vijf Pdf's zijn geïndexeerd door de Indexeer functie en 1 is bijgewerkt, wordt de Indexeer functie opnieuw uitgevoerd. de Indexeer functie indexeert alleen de 1 PDF die is bijgewerkt.
+ Tekst en genormaliseerde afbeeldingen worden standaard geëxtraheerd uit de documenten die worden geïndexeerd. U kunt desgewenst een vaardig heden toevoegen aan de pijp lijn voor verdere inhouds verrijking. Meer informatie over vaardig heden vindt u in de concepten van de artikel [vaardighedenset in Azure Cognitive Search](cognitive-search-working-with-skillsets.md).

## <a name="supported-document-formats"></a>Ondersteunde documentindelingen

Met de Indexeer functie van Azure Cognitive Search share point online kunt u tekst ophalen uit de volgende document indelingen:

[!INCLUDE [search-document-data-sources](../../includes/search-blob-data-sources.md)]

## <a name="incremental-indexing-and-deletion-detection"></a>Incrementele indexering en detectie van verwijderingen
De share point online-indexer ondersteunt standaard incrementele indexering, wat betekent dat de inhoud in de document bibliotheek is gewijzigd en alleen de bijgewerkte inhoud van toekomstige index uitvoeringen indexeert. Als vijf Word-documenten bijvoorbeeld oorspronkelijk zijn geïndexeerd door de Indexeer functie en 1 is bijgewerkt, wordt de Indexeer functie opnieuw uitgevoerd. de Indexeer functie zal het 1 Word-document dat is bijgewerkt, alleen opnieuw indexeren.

Detectie van verwijderingen wordt standaard ook ondersteund. Dit betekent dat als een document wordt verwijderd uit een share point online-document bibliotheek, de Indexeer functie het verwijderen detecteert tijdens een toekomstige Indexeer functie en het document uit de index verwijdert.

## <a name="setting-up-sharepoint-online-indexing"></a>Share point online-indexering instellen
Als u de share point online-indexer wilt instellen, moet u bepaalde acties uitvoeren in de Azure Portal en sommige acties met behulp van de preview-REST API. Deze preview wordt niet ondersteund door de SDK.

### <a name="step-1-enable-system-assigned-managed-identity"></a>Stap 1: door het systeem toegewezen beheerde identiteit inschakelen
Wanneer een door het systeem toegewezen beheerde identiteit is ingeschakeld, maakt Azure een identiteit voor uw zoek service die door de Indexeer functie kan worden gebruikt.

![Door het systeem toegewezen beheerde identiteit inschakelen](media/search-howto-index-sharepoint-online/enable-managed-identity.png "Door het systeem toegewezen beheerde identiteit inschakelen")

Nadat u **Opslaan** hebt geselecteerd, ziet u een object-id die is toegewezen aan uw zoek service.

![Door het systeem toegewezen beheerde identiteit](media/search-howto-index-sharepoint-online/system-assigned-managed-identity.png "Door het systeem toegewezen beheerde identiteit")

### <a name="step-2-create-an-aad-application"></a>Stap 2: een AAD-toepassing maken
De share point online-indexer maakt gebruik van deze AAD-toepassing voor authenticatie. 

1.  Navigeer naar [Azure Portal](https://portal.azure.com/).

1.  Open het menu aan de linkerkant van de hoofd pagina en selecteer **Azure Active Directory** selecteer vervolgens **app-registraties**. Selecteer **+ nieuwe registratie**.
    1.  Geef een naam op voor uw app.
    2.  Selecteer **één Tenant**.
    3.  Geen omleidings-URI vereist.
    4.  Selecteer **Registreren**

1.  Selecteer **API-machtigingen** in het menu aan de linkerkant en **Voeg vervolgens een machtiging toe**. vervolgens **Microsoft Graph** vervolgens **gedelegeerde machtigingen**. Voeg de volgende API-machtigingen toe: 
    1.  **Gedelegeerde bestanden. Read. all** 
    2.  **Gedelegeerde-sites. Read. all** 
    3.  **Gedelegeerde-User. Read**

    ![Machtigingen voor gedelegeerde API](media/search-howto-index-sharepoint-online/delegated-api-permissions.png "Machtigingen voor gedelegeerde API")

    Het gebruik van gedelegeerde machtigingen betekent dat de Indexeer functie toegang heeft tot de share point-site in de context van de gebruiker. Wanneer u de Indexeer functie uitvoert, heeft deze alleen toegang tot de inhoud waartoe de aangemelde gebruiker toegang heeft. Gebruikers aanmelding gebeurt wanneer u de Indexeer functie maakt of de datum bron bijwerkt. De aanmeldings stap wordt verderop in dit artikel beschreven.

1.  Selecteer het tabblad **verificatie** . Stel **open bare client stromen toestaan** in op **Ja** en selecteer vervolgens **Opslaan**.

1.  Selecteer **+ een platform toevoegen**, vervolgens **mobiele en bureaublad toepassingen** en vervolgens controleren `https://login.microsoftonline.com/common/oauth2/nativeclient` en vervolgens **configureren**.

    ![Configuratie van AAD-app-verificatie](media/search-howto-index-sharepoint-online/aad-app-authentication-configuration.png "Configuratie van AAD-app-verificatie")

1.  Beheerders toestemming geven (alleen vereist voor bepaalde tenants).

    Sommige tenants zijn zodanig vergrendeld dat toestemming van de beheerder is vereist voor deze gedelegeerde API-machtigingen. Als dat het geval is, moet u een beheerder toestemming geven voor deze AAD-toepassing voordat u de Indexeer functie maakt. 

    Omdat niet alle tenants deze vereiste hebben, raden we u aan deze stap eerst over te slaan en verder te gaan met de instructies. U weet of u toestemming van de beheerder nodig hebt als u de Indexeer functie wilt maken, mislukt de verificatie dat u een beheerder nodig hebt om de verificatie goed te keuren. In dat geval moet u een Tenant beheerder toestemming geven met behulp van de onderstaande knop.

    ![Toestemming van AAD-app Grant-beheerder](media/search-howto-index-sharepoint-online/aad-app-grant-admin-consent.png "Toestemming van AAD-app Grant-beheerder")

<a name="create-data-source"></a>

### <a name="step-3-create-data-source"></a>Stap 3: een gegevens bron maken
> [!IMPORTANT] 
> In deze sectie moet u de preview-REST API gebruiken om de resterende stappen uit te voeren. Als u niet bekend bent met de Azure Cognitive Search REST API, wordt u geadviseerd om deze [Snelstartgids](search-get-started-rest.md)te bekijken.

Een gegevens bron specificeert welke gegevens moeten worden geïndexeerd, de referenties die nodig zijn voor toegang tot de gegevens en het beleid om de wijzigingen in de gegevens (nieuwe, gewijzigde of verwijderde rijen) efficiënt te kunnen identificeren. Een gegevens bron kan door meerdere Indexeer functies in dezelfde zoek service worden gebruikt.

Voor het indexeren van share point moet de gegevens bron de volgende vereiste eigenschappen hebben:
+ **naam** is de unieke naam van de gegevens bron in uw zoek service.
+ **type** moet share point zijn. Dit is hoofdletter gevoelig.
+ **referenties** bieden het share point online-eind punt en de client-id van de Aad-toepassing. Een voor beeld van een share point online-eind punt is `https://microsoft.sharepoint.com/teams/MySharePointSite` . U kunt het share point online-eind punt ophalen door te navigeren naar de start pagina van uw share point-site en de URL te kopiëren vanuit de browser.
+ **container** specificeert welke document bibliotheek moet worden geïndexeerd. Meer informatie over het maken van de container vindt u in de sectie [bepalen welke documenten worden geïndexeerd](#controlling-which-documents-are-indexed) in dit document.

Een gegevens bron maken:

```http
POST https://[service name].search.windows.net/datasources?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [admin key]

{
    "name" : "sharepoint-datasource",
    "type" : "sharepoint",
    "credentials" : { "connectionString" : "SharePointOnlineEndpoint=[SharePoint Online site url];ApplicationId=[AAD App ID]" },
    "container" : { "name" : "defaultSiteLibrary", "query" : null }
}
```

### <a name="step-4-create-an-index"></a>Stap 4: een index maken
De index specificeert de velden in een document, kenmerken en andere constructies die de zoek ervaring vormen.

Hier volgt een overzicht van het maken van een index met een Doorzoek bare inhouds veld voor het opslaan van de tekst die is geëxtraheerd uit documenten in een document bibliotheek:

```http
POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "sharepoint-index",
    "fields": [
        { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
        { "name": "metadata_spo_item_name", "type": "Edm.String", "key": false, "searchable": true, "filterable": false, "sortable": false, "facetable": false },
        { "name": "metadata_spo_item_path", "type": "Edm.String", "key": false, "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "metadata_spo_item_content_type", "type": "Edm.String", "key": false, "searchable": false, "filterable": true, "sortable": false, "facetable": true },
        { "name": "metadata_spo_item_last_modified", "type": "Edm.DateTimeOffset", "key": false, "searchable": false, "filterable": false, "sortable": true, "facetable": false },
        { "name": "metadata_spo_item_size", "type": "Edm.Int64", "key": false, "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
    ]
}

```

Zie [Create Index (rest API) (Engelstalig)](https://docs.microsoft.com/rest/api/searchservice/create-index)voor meer informatie.

### <a name="step-5-create-an-indexer"></a>Stap 5: een Indexeer functie maken
Een Indexeer functie verbindt een gegevens bron met een doel zoek index en biedt een planning voor het automatiseren van de gegevens vernieuwing. Zodra de index en gegevens bron zijn gemaakt, kunt u de Indexeer functie maken.

In deze sectie wordt u gevraagd om u aan te melden met de referenties van uw organisatie die toegang hebben tot de share point-site. Als dat mogelijk is, kunt u het beste een nieuw gebruikers account voor de organisatie maken en de nieuwe gebruiker de exacte machtigingen geven die u wilt voor de Indexeer functie.

Er zijn enkele stappen voor het maken van de Indexeer functie:

1.  Verzend een aanvraag om de Indexeer functie te maken.

    ```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30-Preview
    Content-Type: application/json
    api-key: [admin key]
    
        {
          "name" : "sharepoint-indexer",
          "dataSourceName" : "sharepoint-datasource",
          "targetIndexName" : "sharepoint-index"
        }
    
    ```

1.  Bij het maken van de Indexeer functie voor de eerste keer dat deze mislukt, wordt de volgende fout weer geven. Ga naar de koppeling in het fout bericht. Als u niet binnen tien minuten naar de koppeling gaat, verloopt de code en moet u de [gegevens bron](#create-data-source)opnieuw maken.

    ```http
    {
        "error": {
            "code": "",
            "message": "Error with data source: To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code <CODE> to authenticate.  Please adjust your data source definition in order to proceed."
        }
    }
    ```

1.  Geef de code op die is verstrekt in het fout bericht.

    ![De toestel code invoeren](media/search-howto-index-sharepoint-online/enter-device-code.png "De toestel code invoeren")

1.  De share point-indexer heeft toegang tot de share point-inhoud als de aangemelde gebruiker. De gebruiker die zich tijdens deze stap aanmeldt, is die aangemelde gebruiker. Als u zich dus aanmeldt met een gebruikers account dat geen toegang heeft tot een document in de document bibliotheek die u wilt indexeren, heeft de Indexeer functie geen toegang tot dat document.

    Als dat mogelijk is, kunt u het beste een nieuw gebruikers account maken en de nieuwe gebruiker de exacte machtigingen geven die u wilt voor de Indexeer functie.

1.  De aangevraagde machtigingen goed keuren.

    ![API-machtigingen goed keuren](media/search-howto-index-sharepoint-online/aad-app-approve-api-permissions.png "API-machtigingen goed keuren")

1.  De aanvraag voor het maken van de Indexeer functie opnieuw verzenden. Deze keer dat de aanvraag slaagt. 

    ```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30-Preview
    Content-Type: application/json
    api-key: [admin key]
    
    {
        "name" : "sharepoint-indexer",
        "dataSourceName" : "sharepoint-datasource",
        "targetIndexName" : "sharepoint-index"
    }
    ```

### <a name="step-6-check-the-indexer-status"></a>Stap 6: de Indexeer functie-status controleren
Nadat de Indexeer functie is gemaakt, kunt u de status van de Indexeer functie controleren door de volgende aanvraag uit te voeren.

```http
GET https://[service name].search.windows.net/indexers/sharepoint-indexer/status?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [admin key]
```

Meer informatie over de status van de Indexeer functie vindt u hier: de status van de [Indexeer functie ophalen](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status).

## <a name="updating-the-data-source"></a>De gegevens bron bijwerken
Als er geen updates zijn voor het gegevens bron object, kan de Indexeer functie zonder tussen komst van de gebruiker worden uitgevoerd volgens een schema. Telkens wanneer het gegevens bron object van Azure Cognitive Search wordt bijgewerkt, moet u zich echter opnieuw aanmelden om de Indexeer functie uit te voeren. Als u bijvoorbeeld de query van de gegevens bron wijzigt, moet u zich opnieuw aanmelden met behulp van de `https://microsoft.com/devicelogin` en een nieuwe code.

Wanneer de gegevens bron is bijgewerkt, volgt u de onderstaande stappen:

1.  Een uitvoering van de Indexeer functie hand matig starten.

    ```http
    POST https://[service name].search.windows.net/indexers/sharepoint-indexer/run?api-version=2020-06-30-Preview  
    Content-Type: application/json
    api-key: [admin key]
    ```

    Meer informatie over de aanvraag voor het uitvoeren van de indexering vindt u hier: [Voer Indexeer functie uit](https://docs.microsoft.com/rest/api/searchservice/run-indexer).

1.  Controleer de status van de Indexeer functie. Als er een fout optreedt in de laatste uitvoering van de Indexeer functie `https://microsoft.com/devicelogin` , gaat u naar die pagina en geeft u de nieuwe code op. 

    ```http
    GET https://[service name].search.windows.net/indexers/sharepoint-indexer/status?api-version=2020-06-30-Preview
    Content-Type: application/json
    api-key: [admin key]
    ```

    Meer informatie over de status van de Indexeer functie vindt u hier: de status van de [Indexeer functie ophalen](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status).

1.  Aanmelden

1.  Hand matig starten van een Indexeer functie opnieuw uitvoeren en controleer de status van de Indexeer functie. Nu moet de Indexeer functie worden gestart.

## <a name="indexing-document-metadata"></a>Meta gegevens van document indexeren
Als u de Indexeer functie voor het indexeren van meta gegevens van documenten hebt ingesteld, zijn de volgende meta gegevens beschikbaar om te indexeren.

> [!NOTE]
> Aangepaste meta gegevens worden niet opgenomen in de huidige versie van de preview.

| Id | Type | Beschrijving | 
| ------------- | -------------- | ----------- |
| metadata_spo_site_library_item_id | Edm.String | De combinatie sleutel van de site-ID, bibliotheek-ID en item-ID waarmee een item in een document bibliotheek voor een site uniek wordt geïdentificeerd. |
| metadata_spo_site_id | Edm.String | De ID van de share point online-site. |
| metadata_spo_library_id | Edm.String | De ID van de document bibliotheek. |
| metadata_spo_item_id | Edm.String | De ID van het (document) item in de bibliotheek. |
| metadata_spo_item_last_modified | Edm.DateTimeOffset | De datum/tijd van de laatste wijziging (UTC) van het item. |
| metadata_spo_item_name | Edm.String | De naam van het item. |
| metadata_spo_item_size | Edm.Int64 | De grootte (in bytes) van het item. | 
| metadata_spo_item_content_type | Edm.String | Het inhouds type van het item. | 
| metadata_spo_item_extension | Edm.String | De uitbrei ding van het item. |
| metadata_spo_item_weburi | Edm.String | De URI van het item. |
| metadata_spo_item_path | Edm.String | De combi natie van het bovenliggende pad en de naam van het item. | 

De share point online-Indexeer functie biedt ook ondersteuning voor meta gegevens die specifiek zijn voor elk document type. Meer informatie vindt u in [Eigenschappen van meta gegevens voor inhoud die worden gebruikt in Azure Cognitive Search](search-blob-metadata-properties.md).

<a name="controlling-which-documents-are-indexed"></a>

## <a name="controlling-which-documents-are-indexed"></a>Bepalen welke documenten worden geïndexeerd
Eén share point online-indexer kan inhoud van een of meer document bibliotheken indexeren. Gebruik de *container* parameter bij het maken van de gegevens bron om de document bibliotheken aan te geven die u wilt indexeren. De gegevens bron *container* heeft twee eigenschappen: *naam* en *query*. 

### <a name="name"></a>Name
De eigenschap *name* is vereist en moet een van de drie waarden zijn:
+ *defaultSiteLibrary*
    + Indexeer alle inhoud van de standaard document bibliotheek van de site.
+   *allSiteLibraries*
    + Alle inhoud van alle document bibliotheken in een site indexeren. Hiermee worden geen document bibliotheken van een subsite geïndexeerd. Deze kunnen worden opgegeven in de *query* .
+   *useQuery*
    + Alleen index inhoud die is gedefinieerd in de *query*.

### <a name="query"></a>Query’s uitvoeren
De *query* -eigenschap bestaat uit sleutel woorden/waardeparen. Hieronder ziet u de tref woorden die kunnen worden gebruikt. De waarden zijn site-url's of url's van document bibliotheken.

> [!NOTE]
> Als u de waarde voor een specifiek tref woord wilt ophalen, kunt u het beste share point online openen in een browser. Hiermee gaat u naar de document bibliotheek die u probeert op te nemen of uit te sluiten en kopieert u de URI vanuit de browser. Dit is de eenvoudigste manier om de waarde te verkrijgen die moet worden gebruikt met een tref woord in de query.

| Zoek | Query Beschrijving | Voorbeeld |
| ------------- | -------------- | ----------- |
| null | Als null of leeg is, indexeert u de standaard document bibliotheek of alle document bibliotheken, afhankelijk van de naam van de container. | Alle inhoud indexeren vanuit de standaard site bibliotheek: <br><br>  ``` "container" : { "name" : "defaultSiteLibrary", "query" : null } ``` |
| includeLibrariesInSite | Indexeer inhoud van alle bibliotheken in de gedefinieerde site in de connection string. Deze zijn beperkt tot subsites van uw site <br><br> De *query* waarde voor dit sleutel woord moet de URI van de site of subsite zijn. | Alle inhoud van alle document bibliotheken in mijn site indexeren. <br><br> ``` "container" : { "name" : "useQuery", "query" : "includeLibrariesInSite=https://mycompany.sharepoint.com/mysite" } ``` |
| includeLibrary | Indexeer inhoud uit deze bibliotheek. <br><br> De *query* waarde voor dit sleutel woord moet een van de volgende indelingen hebben: <br><br> Voorbeeld 1: <br><br> *includeLibrary = [site of subsite]/[document bibliotheek]* <br><br> Voorbeeld 2: <br><br> De URI is gekopieerd uit uw browser. | Alle inhoud indexeren vanuit MyDocumentLibrary: <br><br> Voorbeeld 1: <br><br> ``` "container" : { "name" : "useQuery", "query" : "includeLibrary=https://mycompany.sharepoint.com/mysite/MyDocumentLibrary" } ``` <br><br> Voorbeeld 2: <br><br> ``` "container" : { "name" : "useQuery", "query" : "includeLibrary=https://mycompany.sharepoint.com/teams/mysite/MyDocumentLibrary/Forms/AllItems.aspx" } ``` |
| excludeLibrary |  Indexeer geen inhoud uit deze bibliotheek. <br><br> De *query* waarde voor dit sleutel woord moet een van de volgende indelingen hebben: <br><br> Voorbeeld 1: <br><br> *excludeLibrary = [site-of subsite-URI]/[document bibliotheek]* <br><br> Voorbeeld 2: <br><br> De URI is gekopieerd uit uw browser. | Alle inhoud van al mijn bibliotheken indexeren, met uitzonde ring van MyDocumentLibrary: <br><br> Voorbeeld 1: <br><br> ``` "container" : { "name" : "useQuery", "query" : "includeLibrariesInSite=https://mysite.sharepoint.com/subsite1; excludeLibrary=https://mysite.sharepoint.com/subsite1/MyDocumentLibrary" } ``` <br><br> Voorbeeld 2: <br><br> ``` "container" : { "name" : "useQuery", "query" : "includeLibrariesInSite=https://mycompany.sharepoint.com/teams/mysite; excludeLibrary=https://mycompany.sharepoint.com/teams/mysite/MyDocumentLibrary/Forms/AllItems.aspx" } ``` |

## <a name="index-by-file-type"></a>Indexeren op bestands type
U kunt bepalen welke documenten worden geïndexeerd en welke worden overgeslagen.

### <a name="include-documents-having-specific-file-extensions"></a>Documenten met specifieke bestands extensies toevoegen
U kunt alleen de documenten indexeren met de bestandsnaam extensies die u opgeeft met behulp van de para meter voor de configuratie van de `indexedFileNameExtensions` Indexeer functie. De waarde is een teken reeks met een door komma's gescheiden lijst met bestands extensies (met een voorloop punt). Als u bijvoorbeeld alleen de wilt indexeren. PDF en. DOCX-documenten:

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [admin key]

{
    ... other parts of indexer definition
    "parameters" : { "configuration" : { "indexedFileNameExtensions" : ".pdf,.docx" } }
}
```

### <a name="exclude-documents-having-specific-file-extensions"></a>Documenten met specifieke bestands extensies uitsluiten
U kunt documenten met specifieke bestandsnaam extensies uitsluiten van indexering met behulp van de `excludedFileNameExtensions` configuratie parameter. De waarde is een teken reeks met een door komma's gescheiden lijst met bestands extensies (met een voorloop punt). Als u bijvoorbeeld alle inhoud wilt indexeren, behalve die van de. PNG en. JPEG-extensies:

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [admin key]

{
    ... other parts of indexer definition
    "parameters" : { "configuration" : { "excludedFileNameExtensions" : ".png,.jpeg" } }
}
```

Als beide `indexedFileNameExtensions` en `excludedFileNameExtensions` -para meters aanwezig zijn, controleert Azure Cognitive Search eerst op `indexedFileNameExtensions` , vervolgens op `excludedFileNameExtensions` . Dit betekent dat als dezelfde bestands extensie aanwezig is in beide lijsten, het indexeren wordt uitgesloten van indexering.

## <a name="handling-errors"></a>Afhandeling van fouten
De share point online-indexer stopt standaard zodra er een document wordt aangetroffen met een niet-ondersteund inhouds type (bijvoorbeeld een afbeelding). U kunt natuurlijk gebruikmaken van de `excludedFileNameExtensions` para meter om bepaalde inhouds typen over te slaan. Het is echter mogelijk dat u documenten moet indexeren zonder alle mogelijke inhouds typen vooraf te weten. Als u wilt door gaan met indexeren wanneer er een niet-ondersteund inhouds type wordt aangetroffen, stelt `failOnUnsupportedContentType` u de configuratie parameter in op False:

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [admin key]

{
    ... other parts of indexer definition
    "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false } }
}
```

Voor sommige documenten kan Azure Cognitive Search het inhouds type niet bepalen of kan een document van een andere ondersteund inhouds type niet verwerken. Als u deze fout modus wilt negeren, stelt `failOnUnprocessableDocument` u de configuratie parameter in op ONWAAR:

```http
"parameters" : { "configuration" : { "failOnUnprocessableDocument" : false } }
```

Azure Cognitive Search beperkt de grootte van documenten die worden geïndexeerd. Deze limieten worden beschreven in de [service limieten in Azure Cognitive Search](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity). Documenten met een grote grootte worden standaard als fouten beschouwd. U kunt echter wel de meta gegevens van de opslag van grote documenten indexeren als u de `indexStorageMetadataOnlyForOversizedDocuments` para meter Configuration instelt op True:

```http
"parameters" : { "configuration" : { "indexStorageMetadataOnlyForOversizedDocuments" : true } }
```

U kunt ook door gaan met indexeren als er fouten optreden tijdens de verwerking, hetzij tijdens het parseren van documenten of bij het toevoegen van documenten aan een index. Als u een bepaald aantal fouten wilt negeren, stelt `maxFailedItems` `maxFailedItemsPerBatch` u de para meters en in op de gewenste waarden. Bijvoorbeeld:

```http
{
    ... other parts of indexer definition
    "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 10 }
}
```

## <a name="see-also"></a>Zie ook
+ [Indexeerfuncties in Azure Cognitive Search](search-indexer-overview.md)
+ [Eigenschappen van meta gegevens van inhoud die worden gebruikt in azure Cognitive Search](search-blob-metadata-properties.md)
