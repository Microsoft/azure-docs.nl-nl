---
title: Veelvoorkomende problemen met de zoekindexer oplossen
titleSuffix: Azure Cognitive Search
description: Fouten en veelvoorkomende problemen met indexeerfouten in Azure Cognitive Search, waaronder de verbinding met de gegevensbron, de firewall en ontbrekende documenten.
manager: nitinme
author: mgottein
ms.author: magottei
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: efdd9666c8876ddaf12b9555fa66beb62c56e93e
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107740053"
---
# <a name="troubleshooting-common-indexer-issues-in-azure-cognitive-search"></a>Veelvoorkomende problemen met indexering in Azure Cognitive Search

Indexeerders kunnen een aantal problemen krijgen bij het indexeren van gegevens in Azure Cognitive Search. De belangrijkste foutcategorieën zijn:

* [Verbinding maken met een gegevensbron of andere resources](#connection-errors)
* [Documentverwerking](#document-processing-errors)
* [Opname in een index documenteren](#index-errors)

## <a name="connection-errors"></a>Verbindingsfouten

> [!NOTE]
> Indexeerders bieden beperkte ondersteuning voor toegang tot gegevensbronnen en andere resources die worden beveiligd door Azure-netwerkbeveiligingsmechanismen. Op dit moment hebben indexeerders alleen toegang tot gegevensbronnen via overeenkomstige beperkingsmechanismen voor IP-adresbereiken of NSG-regels, indien van toepassing. Meer informatie over het openen van elke ondersteunde gegevensbron vindt u hieronder.
>
> U kunt het IP-adres van uw zoekservice vinden door de volledig gekwalificeerde domeinnaam (bijvoorbeeld ) te `<your-search-service-name>.search.windows.net` pingen.
>
> U kunt het IP-adresbereik van de servicetag vinden met behulp van `AzureCognitiveSearch` [](../virtual-network/service-tags-overview.md#available-service-tags) [downloadbare JSON-bestanden](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) of via de [Service Tag Discovery-API.](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview) Het IP-adresbereik wordt wekelijks bijgewerkt.

### <a name="configure-firewall-rules"></a>Firewallregels configureren

Azure Storage CosmosDB en Azure SQL bieden een configureerbare firewall. Er is geen specifiek foutbericht wanneer de firewall is ingeschakeld. Firewallfouten zijn doorgaans algemeen en zien eruit als `The remote server returned an error: (403) Forbidden` of `Credentials provided in the connection string are invalid or have expired` .

Er zijn twee opties voor het toestaan van indexen voor toegang tot deze resources in een dergelijke instantie:

* Schakel de firewall uit door toegang vanuit **alle** netwerken toe te staan (indien mogelijk).
* U kunt ook toegang toestaan voor het IP-adres van uw zoekservice en het IP-adresbereik van `AzureCognitiveSearch` [de servicetag](../virtual-network/service-tags-overview.md#available-service-tags) in de firewallregels van uw resource (beperking van IP-adresbereik).

Meer informatie over het configureren van ip-adresbereikbeperkingen voor elk gegevensbrontype vindt u via de volgende koppelingen:

* [Azure Storage](../storage/common/storage-network-security.md#grant-access-from-an-internet-ip-range)

* [Cosmos DB](../storage/common/storage-network-security.md#grant-access-from-an-internet-ip-range)

* [Azure SQL](../azure-sql/database/firewall-configure.md#create-and-manage-ip-firewall-rules)

**Beperking:** Zoals vermeld in de bovenstaande documentatie voor Azure Storage, werken beperkingen voor IP-adresbereiken alleen als uw zoekservice en uw opslagaccount zich in verschillende regio's hebben.

Azure-functies (die kunnen worden gebruikt als een [aangepaste web-API-vaardigheid](cognitive-search-custom-skill-web-api.md)) ondersteunen ook [IP-adresbeperkingen.](../azure-functions/ip-addresses.md#ip-address-restrictions) De lijst met TE configureren IP-adressen is het IP-adres van uw zoekservice en het IP-adresbereik van `AzureCognitiveSearch` de servicetag.

Details voor toegang tot gegevens in SQL Server op een Azure-VM worden hier [beschreven](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md)

### <a name="configure-network-security-group-nsg-rules"></a>NSG-regels (netwerkbeveiligingsgroep) configureren

Bij het openen van gegevens in een met SQL beheerd exemplaar of wanneer een Azure-VM wordt gebruikt als de webservice-URI voor een aangepaste [web-API-vaardigheid,](cognitive-search-custom-skill-web-api.md)hoeven klanten zich niet bezig te houden met specifieke IP-adressen.

In dergelijke gevallen kan de Azure-VM of het met SQL beheerde exemplaar worden geconfigureerd om zich in een virtueel netwerk te bevinden. Vervolgens kan een netwerkbeveiligingsgroep worden geconfigureerd om het type netwerkverkeer te filteren dat in en uit de subnetten en netwerkinterfaces van het virtuele netwerk kan stromen.

De `AzureCognitiveSearch` servicetag kan rechtstreeks worden gebruikt in de inkomende [NSG-regels](../virtual-network/manage-network-security-group.md#work-with-security-rules) zonder dat het IP-adresbereik hoeft te worden op zoek.

Meer informatie over het openen van gegevens in een met SQL beheerd exemplaar wordt hier [beschreven](search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers.md)

### <a name="cosmosdb-indexing-isnt-enabled"></a>'Indexeren' van CosmosDB is niet ingeschakeld

Azure Cognitive Search is impliciet afhankelijk van Cosmos DB indexeren. Als u automatisch indexeren in een Cosmos DB, Azure Cognitive Search een geslaagde status, maar kan de containerinhoud niet worden geïndexeerd. Zie Indexering beheren [in](../cosmos-db/how-to-manage-indexing-policy.md#use-the-azure-portal)Azure Cosmos DB voor instructies over het controleren van instellingen en het in Azure Cosmos DB.

### <a name="sharepoint-online-conditional-access-policies"></a>Beleid voor voorwaardelijke toegang voor SharePoint Online

Wanneer u een Indexer voor SharePoint Online maakt, moet u zich aanmelden bij uw AAD-app nadat u een apparaatcode hebt verstrekken. Als u een bericht ontvangt met de melding 'Uw aanmelding is geslaagd, maar uw beheerder vereist dat het apparaat dat toegang aanvraagt, [](https://review.docs.microsoft.com/azure/active-directory/conditional-access/overview) wordt beheerd', wordt de toegang tot de SharePoint Online-documentbibliotheek waarschijnlijk geblokkeerd door een beleid voor voorwaardelijke toegang.

Als u het beleid wilt bijwerken zodat de indexer toegang heeft tot de documentbibliotheek, volgt u de onderstaande stappen:

1. Open de Azure Portal en zoek **naar voorwaardelijke toegang van Azure AD** en selecteer vervolgens **Beleidsregels** in het menu links. Als u geen toegang hebt om deze pagina weer te geven, moet u iemand vinden die toegang heeft of toegang krijgt.

1. Bepaal welk beleid de indexeringsmanager van SharePoint Online blokkeert voor toegang tot de documentbibliotheek. Het beleid dat de indexer mogelijk blokkeert, bevat het gebruikersaccount dat u hebt gebruikt om te verifiëren tijdens de stap voor het maken van de indexer in de sectie **Gebruikers en** groepen. Het beleid kan ook voorwaarden **hebben die:**
    * **Windows-platformen** beperken.
    * **Beperk mobiele apps en desktopcl clients.**
    * De **apparaattoestand** is geconfigureerd op **Ja.**

1. Nadat u hebt bevestigd dat er een beleid is dat de indexering blokkeert, moet u vervolgens een uitzondering maken voor de indexer. Haal het IP-adres van de zoekservice op.

    1. Verkrijg de FQDN (Fully Qualified Domain Name) van uw zoekservice. Dit ziet er als `<search-service-name>.search.windows.net` uit. U kunt de FQDN vinden door uw zoekservice op te zoeken op Azure Portal.

   ![Service-FQDN verkrijgen](media\search-indexer-howto-secure-access\search-service-portal.png "Service-FQDN verkrijgen")

    Het IP-adres van de zoekservice kan worden verkregen door een `nslookup` (of een `ping` ) van de FQDN uit te voeren. In het onderstaande voorbeeld voegt u '150.0.0.1' toe aan een regel voor binnenkomende Azure Storage firewall. Nadat de firewallinstellingen zijn bijgewerkt, kan het tot vijftien minuten duren voordat de indexer van de zoekservice toegang heeft tot het Azure Storage account.

    ```azurepowershell

    nslookup contoso.search.windows.net
    Server:  server.example.org
    Address:  10.50.10.50
    
    Non-authoritative answer:
    Name:    <name>
    Address:  150.0.0.1
    Aliases:  contoso.search.windows.net
    ```

1. Haal de IP-adresbereiken op voor de uitvoeringsomgeving van de indexer voor uw regio.

    Extra IP-adressen worden gebruikt voor aanvragen die afkomstig zijn van de uitvoeringsomgeving voor [meerdere tenants van de indexer.](search-indexer-securing-resources.md#indexer-execution-environment) U kunt dit IP-adresbereik op halen uit de servicetag.

    De IP-adresbereiken voor de servicetag kunnen worden verkregen via de `AzureCognitiveSearch` [detectie-API (preview)](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview) of het [downloadbare JSON-bestand](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files).

    Voor dit scenario, ervan uitgaande dat de zoekservice de openbare Azure-cloud is, moet het openbare [JSON-bestand](https://www.microsoft.com/download/details.aspx?id=56519) van Azure worden gedownload.

   ![JSON-bestand downloaden](media\search-indexer-troubleshooting\service-tag.png "JSON-bestand downloaden")

    In het JSON-bestand, ervan uitgaande dat de zoekservice zich in VS - west-centraal, de lijst met IP-adressen voor de uitvoeringsomgeving voor de indexer met meerdere tenants vindt u hieronder.

    ```json
        {
          "name": "AzureCognitiveSearch.WestCentralUS",
          "id": "AzureCognitiveSearch.WestCentralUS",
          "properties": {
            "changeNumber": 1,
            "region": "westcentralus",
            "platform": "Azure",
            "systemService": "AzureCognitiveSearch",
            "addressPrefixes": [
              "52.150.139.0/26",
              "52.253.133.74/32"
            ]
          }
        }
    ```

1. Ga terug naar de pagina Voorwaardelijke toegang in Azure Portal selecteer Benoemde locaties **in** het menu aan de linkerkant en selecteer vervolgens locatie **+ IP-adresbereiken.** Geef de nieuwe benoemde locatie een naam en voeg de IP-adresbereiken toe voor uw zoekservice en uitvoeringsomgevingen van de indexer die u in de laatste twee stappen hebt verzameld.
    * Voor het IP-adres van uw zoekservice moet u mogelijk '/32' toevoegen aan het einde van het IP-adres, omdat het alleen geldige IP-adresbereiken accepteert.
    * Houd er wel voor dat u voor de IP-adresbereiken van de uitvoeromgeving van de indexer alleen de IP-adresbereiken hoeft toe te voegen voor de regio waarin uw zoekservice zich in de omgeving.

1. Sluit de nieuwe benoemde locatie uit van het beleid. 
    1. Selecteer **Beleidsregels** in het menu links. 
    1. Selecteer het beleid dat de indexering blokkeert.
    1. Selecteer **Voorwaarden**.
    1. Selecteer **Locaties.**
    1. Selecteer **Uitsluiten** en voeg vervolgens de nieuwe benoemde locatie toe.
    1. **Sla de** wijzigingen op.

1. Wacht enkele minuten totdat het beleid is bijgewerkt en de nieuwe beleidsregels worden afgedwongen.

1. Probeer de indexer opnieuw te maken
    1. Verzend een updateaanvraag voor het gegevensbronobject dat u hebt gemaakt.
    1. De aanvraag voor het maken van de indexer opnieuw indienen. Gebruik de nieuwe code om u aan te melden en verzend vervolgens een andere aanvraag voor het maken van de indexer na de geslaagde aanmelding.

## <a name="document-processing-errors"></a>Fouten bij documentverwerking

### <a name="unprocessable-or-unsupported-documents"></a>Niet-verwerkte of niet-ondersteunde documenten

De blob-indexer [documenteert welke documentindelingen expliciet worden ondersteund.](search-howto-indexing-azure-blob-storage.md#SupportedFormats). Soms bevat een blobopslagcontainer niet-ondersteunde documenten. Op andere momenten kunnen er problematische documenten zijn. U kunt voorkomen dat de indexering voor deze documenten wordt gestopt door [configuratieopties te wijzigen:](search-howto-indexing-azure-blob-storage.md#DealingWithErrors)

```
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false, "failOnUnprocessableDocument" : false } }
}
```

### <a name="missing-document-content"></a>Ontbrekende documentinhoud

De blob-indexer [zoekt en extraheert tekst uit blobs in een container](search-howto-indexing-azure-blob-storage.md#how-azure-search-indexes-blobs). Enkele problemen met het extraheren van tekst zijn:

* Het document bevat alleen gescande afbeeldingen. PDF-blobs met niet-tekstinhoud, zoals gescande afbeeldingen (JPG's), produceren geen resultaten in een standaard pijplijn voor het indexeren van blobs. Als u afbeeldingsinhoud met tekstelementen hebt, kunt u cognitief zoeken [gebruiken om](cognitive-search-concept-image-scenarios.md) de tekst te zoeken en te extraheren.
* De blob-indexer is geconfigureerd voor alleen indexmetagegevens. Als u inhoud wilt extraheren, moet de blob-indexer worden geconfigureerd om zowel [inhoud als metagegevens te extraheren:](search-howto-indexing-azure-blob-storage.md#PartsOfBlobToIndex)

```
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "dataToExtract" : "contentAndMetadata" } }
}
```

## <a name="index-errors"></a>Indexfouten

### <a name="missing-documents"></a>Ontbrekende documenten

Indexeerders zoeken documenten uit een [gegevensbron.](/rest/api/searchservice/create-data-source) Soms lijkt een document uit de gegevensbron dat had moeten worden geïndexeerd, te ontbreken in een index. Er zijn een aantal veelvoorkomende redenen waarom deze fouten kunnen optreden:

* Het document is niet geïndexeerd. Controleer de portal op een geslaagde indexering.
* Controleer de waarde [van uw wijzigingsvolgsysteem.](/rest/api/searchservice/create-data-source#data-change-detection-policies) Als uw boven watermerkwaarde een datum is die is ingesteld op een toekomstige tijd, worden documenten met een datum lager dan deze overgeslagen door de indexer. U kunt de status van de wijzigingstracking van uw indexer begrijpen met behulp van de velden 'initialTrackingState' en 'finalTrackingState' in de [indexerstatus](/rest/api/searchservice/get-indexer-status#indexer-execution-result).
* Het document is bijgewerkt nadat de indexer is uitgevoerd. Als uw indexeringsmanager volgens een [schema is,](/rest/api/searchservice/create-indexer#indexer-schedule)wordt het document uiteindelijk opnieuw en opgehaald.
* De [query](/rest/api/searchservice/create-data-source) die is opgegeven in de gegevensbron sluit het document uit. Indexeerders kunnen geen documenten indexeren die geen deel uitmaken van de gegevensbron.
* [Veldtoewijzingen](/rest/api/searchservice/create-indexer#fieldmappings) of [AI-verrijking](./cognitive-search-concept-intro.md) hebben het document gewijzigd en het ziet er anders uit dan verwacht.
* Gebruik de [document-API voor opzoek](/rest/api/searchservice/lookup-document) om uw document te vinden.