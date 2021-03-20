---
title: Veelvoorkomende problemen met zoek indexen oplossen
titleSuffix: Azure Cognitive Search
description: Corrigeer fouten en veelvoorkomende problemen met Indexeer functies in azure Cognitive Search, waaronder gegevens bron verbinding, firewall en ontbrekende documenten.
manager: nitinme
author: mgottein
ms.author: magottei
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 7eadc9121c54b636fa8b42579284d4018043e1c1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91355122"
---
# <a name="troubleshooting-common-indexer-issues-in-azure-cognitive-search"></a>Veelvoorkomende problemen met Indexeer functies in azure Cognitive Search oplossen

Indexeer functies kunnen in een aantal problemen worden uitgevoerd bij het indexeren van gegevens in azure Cognitive Search. De belangrijkste categorieën fouten zijn onder andere:

* [Verbinding maken met een gegevens bron of andere resources](#connection-errors)
* [Document verwerking](#document-processing-errors)
* [Opname van documenten naar een index documenteren](#index-errors)

## <a name="connection-errors"></a>Verbindingsfouten

> [!NOTE]
> Indexeer functies hebben beperkte ondersteuning voor toegang tot gegevens bronnen en andere bronnen die worden beveiligd door Azure-netwerk beveiligings mechanismen. Op dit moment kunnen Indexeer functies alleen toegang krijgen tot gegevens bronnen via de bijbehorende beperkings mechanismen voor IP-adres bereik of NSG regels, indien van toepassing. Details voor toegang tot elke ondersteunde gegevens bron vindt u hieronder.
>
> U kunt het IP-adres van uw zoek service nagaan door de Fully Qualified Domain Name (bijvoorbeeld) te pingen `<your-search-service-name>.search.windows.net` .
>
> U kunt het IP-adres bereik van de `AzureCognitiveSearch` [servicetag](../virtual-network/service-tags-overview.md#available-service-tags) van de service opvragen door [Download bare json-bestanden](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) te gebruiken of via de [service tag discovery-API](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview). Het IP-adres bereik wordt wekelijks bijgewerkt.

### <a name="configure-firewall-rules"></a>Firewallregels configureren

Azure Storage, CosmosDB en Azure SQL bieden een Configureer bare firewall. Er is geen specifiek fout bericht wanneer de firewall is ingeschakeld. Normaal gesp roken zijn firewall fouten algemeen en zien ze eruit als `The remote server returned an error: (403) Forbidden` of `Credentials provided in the connection string are invalid or have expired` .

Er zijn twee opties voor het toestaan van Indexeer functies voor toegang tot deze resources:

* Schakel de firewall uit door toegang vanaf **alle netwerken** toe te staan (indien mogelijk).
* U kunt ook toegang toestaan voor het IP-adres van uw zoek service en het IP-adres bereik van de `AzureCognitiveSearch` [service label](../virtual-network/service-tags-overview.md#available-service-tags) in de firewall regels van uw bron (beperking van het IP-adres bereik).

Details voor het configureren van IP-adres bereik beperkingen voor elk gegevens bron type vindt u op de volgende koppelingen:

* [Azure Storage](../storage/common/storage-network-security.md#grant-access-from-an-internet-ip-range)

* [Cosmos DB](../storage/common/storage-network-security.md#grant-access-from-an-internet-ip-range)

* [Azure SQL](../azure-sql/database/firewall-configure.md#create-and-manage-ip-firewall-rules)

**Beperking**: zoals vermeld in de bovenstaande documentatie voor Azure Storage, werken IP-adres bereik beperkingen alleen als uw zoek service en uw opslag account zich in verschillende regio's bevinden.

Azure functions (die kunnen worden gebruikt als [aangepaste web API-vaardigheid](cognitive-search-custom-skill-web-api.md)) ondersteunen ook [IP-adres beperkingen](../azure-functions/ip-addresses.md#ip-address-restrictions). De lijst met IP-adressen die moeten worden geconfigureerd, is het IP-adres van de zoek service en het IP-adres bereik van de `AzureCognitiveSearch` service label.

Details voor het openen van gegevens in SQL Server op een virtuele machine van Azure worden [hier](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md) beschreven

### <a name="configure-network-security-group-nsg-rules"></a>Regels voor netwerk beveiligings groepen (NSG) configureren

Bij het openen van gegevens in een SQL Managed instance of wanneer een virtuele machine van Azure wordt gebruikt als de webservice-URI voor een [aangepaste web API-vaardigheid](cognitive-search-custom-skill-web-api.md), hoeven klanten geen gebruik te maken van specifieke IP-adressen.

In dergelijke gevallen kan de virtuele machine van Azure of het beheerde exemplaar van SQL worden geconfigureerd zodat deze zich binnen een virtueel netwerk bevindt. Vervolgens kan een netwerk beveiligings groep worden geconfigureerd om het type netwerk verkeer te filteren dat in en uit de subnetten van het virtuele netwerk en netwerk interfaces kan stromen.

De servicetag `AzureCognitiveSearch` kan rechtstreeks worden gebruikt in de [regels](../virtual-network/manage-network-security-group.md#work-with-security-rules) voor inkomende NSG zonder dat het IP-adres bereik moet worden opgezocht.

Meer informatie over het openen van gegevens in een SQL Managed instance wordt [hier](search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers.md) beschreven

### <a name="cosmosdb-indexing-isnt-enabled"></a>CosmosDB ' indexeren ' is niet ingeschakeld

Azure Cognitive Search heeft een impliciete afhankelijkheid van Cosmos DB indexering. Als u automatische indexering in Cosmos DB uitschakelt, wordt door Azure Cognitive Search een succes volle status geretourneerd, maar kan de inhoud van de container niet worden geïndexeerd. Zie [Manage Indexing in azure Cosmos DB](../cosmos-db/how-to-manage-indexing-policy.md#use-the-azure-portal)voor instructies over het controleren van instellingen en het inschakelen van indexeren.

## <a name="document-processing-errors"></a>Document verwerkings fouten

### <a name="unprocessable-or-unsupported-documents"></a>Niet-verwerkte of niet-ondersteunde documenten

De BLOB-Indexeer [documenten die worden ondersteund door de document indelingen expliciet](search-howto-indexing-azure-blob-storage.md#SupportedFormats) Soms bevat een BLOB storage-container niet-ondersteunde documenten. Het kan zijn dat er problemen zijn met de documenten. U kunt voor komen dat uw Indexeer functie op deze documenten wordt gestopt door [configuratie opties te wijzigen](search-howto-indexing-azure-blob-storage.md#DealingWithErrors):

```
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false, "failOnUnprocessableDocument" : false } }
}
```

### <a name="missing-document-content"></a>Ontbrekende document inhoud

De BLOB-indexer [zoekt en extraheert tekst van blobs in een container](search-howto-indexing-azure-blob-storage.md#how-azure-search-indexes-blobs). Enkele problemen met het ophalen van tekst zijn onder andere:

* Het document bevat alleen gescande afbeeldingen. PDF-blobs met niet-tekst inhoud, zoals gescande afbeeldingen (JPGs), produceren geen resultaten in een standaard-BLOB-indexerings pijplijn. Als u afbeeldings inhoud met tekst elementen hebt, kunt u met [cognitieve zoek opdracht](cognitive-search-concept-image-scenarios.md) de tekst zoeken en extra heren.
* De BLOB-indexer is zo geconfigureerd dat alleen meta gegevens worden geïndexeerd. Als u inhoud wilt extra heren, moet de BLOB-indexer worden geconfigureerd voor het [extra heren van inhoud en meta gegevens](search-howto-indexing-azure-blob-storage.md#PartsOfBlobToIndex):

```
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "dataToExtract" : "contentAndMetadata" } }
}
```

## <a name="index-errors"></a>Index fouten

### <a name="missing-documents"></a>Ontbrekende documenten

Indexeer functies vinden documenten van een [gegevens bron](/rest/api/searchservice/create-data-source). Soms lijkt een document uit de gegevens bron dat moest worden geïndexeerd, te ontbreken in een index. Er zijn enkele veelvoorkomende redenen waarom deze fouten zich kunnen voordoen:

* Het document is niet geïndexeerd. Controleer de portal op de uitvoering van een geslaagde indexer.
* Controleer de waarde voor het [bijhouden van wijzigingen](/rest/api/searchservice/create-data-source#data-change-detection-policies) . Als uw hoge watermerk waarde een datum is die op een later tijdstip is ingesteld, worden alle documenten met een datum die kleiner is dan deze, overgeslagen door de Indexeer functie. Met de velden ' initialTrackingState ' en ' finalTrackingState ' in de [Indexeer functie](/rest/api/searchservice/get-indexer-status#indexer-execution-result)kunt u de status van het bijhouden van wijzigingen van de Indexeer functie begrijpen.
* Het document is bijgewerkt nadat de Indexeer functie is uitgevoerd. Als uw Indexeer functie volgens een [schema is gepland](/rest/api/searchservice/create-indexer#indexer-schedule), wordt het document uiteindelijk opnieuw uitgevoerd en opgehaald.
* De [query](/rest/api/searchservice/create-data-source) die is opgegeven in de gegevens bron, sluit het document uit. Indexeer functies kunnen geen documenten indexeren die geen deel uitmaken van de gegevens bron.
* [Veld Toewijzingen](/rest/api/searchservice/create-indexer#fieldmappings) of [AI-verrijking](./cognitive-search-concept-intro.md) hebben het document gewijzigd en het ziet er anders uit dan verwacht.
* Gebruik de [opzoek document-API](/rest/api/searchservice/lookup-document) om uw document te vinden.