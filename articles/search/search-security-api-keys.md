---
title: API-sleutel verificatie
titleSuffix: Azure Cognitive Search
description: Een API-sleutel beheert de inkomende toegang tot het service-eind punt. Beheer sleutels verlenen schrijf toegang. Query sleutels kunnen worden gemaakt voor alleen-lezen toegang.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/08/2021
ms.openlocfilehash: 6954ce289cb3cf219f8c4024a112411fd60d70e0
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107310662"
---
# <a name="create-and-manage-api-keys-for-authentication-to-azure-cognitive-search"></a>API-sleutels voor verificatie voor Azure-Cognitive Search maken en beheren

Wanneer u verbinding maakt met een zoek service, moeten alle aanvragen een alleen-lezen API-sleutel bevatten die specifiek voor uw service is gegenereerd. De API-sleutel is het enige mechanisme voor het verifiëren van inkomende aanvragen voor uw zoek service-eind punt en is vereist voor elke aanvraag. 

+ In [rest oplossingen](search-get-started-rest.md)wordt de `api-key` is doorgaans opgegeven in een aanvraag header

+ In [.net-oplossingen](search-howto-dotnet-sdk.md)wordt een sleutel vaak opgegeven als een configuratie-instelling en vervolgens door gegeven als een [AzureKeyCredential](/dotnet/api/azure.azurekeycredential)

U kunt de API-sleutels weer geven en beheren in de [Azure Portal](https://portal.azure.com), of via [Power shell](/powershell/module/az.search), [Azure cli](/cli/azure/search)of [rest API](/rest/api/searchmanagement/).

:::image type="content" source="media/search-manage/azure-search-view-keys.png" alt-text="Portal-pagina, instellingen ophalen, sectie sleutels" border="false":::

## <a name="what-is-an-api-key"></a>Wat is een API-sleutel?

Een API-sleutel is een unieke teken reeks die bestaat uit wille keurig gegenereerde getallen en letters die worden door gegeven op elke aanvraag bij de zoek service. De service accepteert de aanvraag, als zowel de aanvraag zelf als de sleutel geldig zijn. 

Er worden twee soorten sleutels gebruikt om toegang te krijgen tot uw zoek service: beheerder (lezen/schrijven) en query (alleen-lezen).

|Sleutel|Beschrijving|Limieten|  
|---------|-----------------|------------|  
|Beheerder|Verleent volledige rechten voor alle bewerkingen, met inbegrip van de mogelijkheid om de service te beheren, indexen, Indexeer functies en gegevens bronnen te maken en te verwijderen.<br /><br /> Twee beheer sleutels, aangeduid als *primaire* en *secundaire* sleutels in de portal, worden gegenereerd wanneer de service wordt gemaakt en kunnen op aanvraag afzonderlijk opnieuw worden gegenereerd. Als u twee sleutels hebt, kunt u één sleutel gebruiken terwijl u de tweede toets gebruikt voor verdere toegang tot de service.<br /><br /> Beheerders sleutels worden alleen opgegeven in HTTP-aanvraag headers. U kunt geen beheerder-API-sleutel in een URL plaatsen.|Maximum van 2 per service|  
|Query|Geeft alleen-lezen toegang tot indexen en documenten, en wordt doorgaans gedistribueerd naar client toepassingen die zoek aanvragen uitgeven.<br /><br /> Query sleutels worden op aanvraag gemaakt.<br /><br /> Query sleutels kunnen worden opgegeven in een header van een HTTP-aanvraag voor zoeken, suggestie of opzoek bewerking. U kunt ook een query sleutel als een para meter door geven op een URL. Afhankelijk van hoe uw client toepassing de aanvraag opvraagt, is het wellicht eenvoudiger om de sleutel als een query parameter door te geven:<br /><br /> `GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2020-06-30&api-key=[query key]`|50 per service|  

 Er is visueel geen onderscheid tussen een beheer sleutel of query sleutel. Beide sleutels zijn teken reeksen die bestaan uit 32 wille keurig gegenereerde alfanumerieke tekens. Als u het soort sleutel dat in uw toepassing is opgegeven, niet ziet, kunt u [de sleutel waarden in de portal controleren](https://portal.azure.com).  

> [!NOTE]  
> Het wordt gezien als een slechte beveiligings procedure om gevoelige gegevens op te geven, zoals een `api-key` in de aanvraag-URI. Daarom accepteert Azure Cognitive Search alleen een query sleutel als een `api-key` in de query teken reeks en moet u dit voor komen, tenzij de inhoud van uw index openbaar moet zijn. Als algemene regel wordt u aangeraden om uw `api-key` als aanvraag header door te geven.  

## <a name="find-existing-keys"></a>Bestaande sleutels zoeken

U kunt toegangs sleutels verkrijgen in de portal of via [Power shell](/powershell/module/az.search), [Azure cli](/cli/azure/search)of [rest API](/rest/api/searchmanagement/).

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Vermeld de [Zoek Services](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) voor uw abonnement.
1. Selecteer de service en klik op de pagina overzicht op **instellingen**  > **sleutels** om de beheer-en query sleutels weer te geven.

   :::image type="content" source="media/search-security-overview/settings-keys.png" alt-text="Portal-pagina, instellingen weer geven, sectie sleutels" border="false":::

## <a name="create-query-keys"></a>Query sleutels maken

Query sleutels worden gebruikt voor alleen-lezen toegang tot documenten in een index voor bewerkingen die zijn gericht op een verzameling documenten. Zoek-, filter-en suggesties query's zijn alle bewerkingen die een query sleutel uitvoeren. Een alleen-lezen bewerking die systeem gegevens of object definities retourneert, zoals een index definitie of Indexeer functie-status, vereist een beheerders sleutel.

Het beperken van toegang en bewerkingen in client-apps is essentieel voor het beveiligen van de zoek assets op uw service. Gebruik altijd een query sleutel in plaats van een beheerders sleutel voor query's die afkomstig zijn uit een client-app.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Vermeld de [Zoek Services](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)  voor uw abonnement.
3. Selecteer de service en klik op de pagina overzicht op **instellingen**  > **sleutels**.
4. Klik op **query sleutels beheren**.
5. Gebruik de query sleutel die al is gegenereerd voor uw service of maak 50 nieuwe query sleutels. De standaard query sleutel heeft geen naam, maar aanvullende query sleutels kunnen worden benoemd voor beheer baarheid.

   :::image type="content" source="media/search-security-overview/create-query-key.png" alt-text="Een query sleutel maken of gebruiken" border="false":::

> [!Note]
> Een code voorbeeld met het gebruik van de query sleutel vindt u in [DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo).

<a name="regenerate-admin-keys"></a>

## <a name="regenerate-admin-keys"></a>Beheer sleutels opnieuw genereren

Voor elke service worden twee beheer sleutels gemaakt, zodat u een primaire sleutel kunt draaien met behulp van de secundaire sleutel voor bedrijfs continuïteit.

1. Op de pagina **instellingen**  > **sleutels** kopieert u de secundaire sleutel.
2. Voor alle toepassingen moet u de API-sleutel instellingen bijwerken voor gebruik van de secundaire sleutel.
3. Genereer de primaire sleutel opnieuw.
4. Werk alle toepassingen bij om de nieuwe primaire sleutel te gebruiken.

Als u per ongeluk beide sleutels tegelijk opnieuw genereert, mislukken alle client aanvragen die gebruikmaken van deze sleutels met HTTP 403 verboden. Inhoud wordt echter niet verwijderd en u bent niet permanent vergrendeld. 

U kunt nog steeds toegang krijgen tot de service via de portal of via een programma. Beheer functies zijn van kracht via een abonnements-ID die geen service-API-sleutel is, en is dus nog steeds beschikbaar, zelfs als uw API-sleutels niet zijn. 

Nadat u nieuwe sleutels via de portal of de Management-laag hebt gemaakt, wordt de toegang tot uw inhoud (indexen, Indexeer functies, gegevens bronnen, synoniemen kaarten) hersteld zodra u de nieuwe sleutels hebt en deze sleutels op aanvragen hebt ingesteld.

## <a name="secure-api-keys"></a>Beveiligde API-sleutels

Via [op rollen gebaseerde machtigingen](search-security-rbac.md)kunt u de sleutels verwijderen of lezen, maar u kunt geen sleutel vervangen door een door de gebruiker gedefinieerd wacht woord of Active Directory gebruiken als de primaire verificatie methode voor het openen van zoek bewerkingen. 

De belangrijkste beveiliging wordt gewaarborgd door de toegang te beperken via de portal of de Resource Manager-interfaces (Power shell of de opdracht regel Interface). Zoals aangegeven, kunnen abonnements beheerders alle API-sleutels weer geven en opnieuw genereren. Raadpleeg de roltoewijzingen voor meer informatie over wie toegang heeft tot de beheer sleutels.

+ Klik in het service dashboard op **toegangs beheer (IAM)** en vervolgens op **het tabblad roltoewijzingen om** roltoewijzingen voor uw service weer te geven.

Leden van de volgende rollen kunnen sleutels weer geven en opnieuw genereren: eigenaar, Inzender, [Search service inzenders](../role-based-access-control/built-in-roles.md#search-service-contributor)

## <a name="see-also"></a>Zie ook

+ [Beveiliging in azure Cognitive Search](search-security-overview.md)
+ [Toegangs beheer op basis van rollen in Azure in azure Cognitive Search](search-security-rbac.md)
+ [Beheren met PowerShell](search-manage-powershell.md) 