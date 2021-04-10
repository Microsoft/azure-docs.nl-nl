---
title: Indexeer verbindingen via een persoonlijk eind punt
titleSuffix: Azure Cognitive Search
description: Indexeer verbindingen configureren voor toegang tot inhoud van andere Azure-resources die zijn beveiligd via een persoonlijk eind punt.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: 0b30cb1767e733861d8418ea29e564bc90a5bc70
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101676518"
---
# <a name="make-indexer-connections-through-a-private-endpoint"></a>Indexeer verbindingen maken via een persoonlijk eind punt

Veel Azure-resources, zoals Azure Storage-accounts, kunnen worden geconfigureerd om verbindingen te accepteren van een lijst met virtuele netwerken en externe verbindingen te weigeren die afkomstig zijn van een openbaar netwerk. Als u een Indexeer functie gebruikt om gegevens in azure Cognitive Search te indexeren en uw gegevens bron zich op een particulier netwerk bevindt, kunt u een uitgaand [privé-eindpunt verbinding](../private-link/private-endpoint-overview.md) maken om de gegevens te bereiken.

Deze verbindings methode voor de Indexeer functie is onderhevig aan de volgende twee vereisten:

+ De Azure-resource die inhoud of code levert, moet eerder zijn geregistreerd bij de [Azure Private Link-service](https://azure.microsoft.com/services/private-link/).

+ De Azure Cognitive Search-service moet zich op de laag Basic of hoger. De functie is niet beschikbaar in de gratis laag. Als uw Indexeer functie bovendien een vaardig heden heeft, moet de laag standaard 2 (S2) of hoger zijn. Zie [service limieten](search-limits-quotas-capacity.md#shared-private-link-resource-limits)voor meer informatie.

## <a name="shared-private-link-resources-management-apis"></a>Beheer-Api's voor gedeelde persoonlijke koppelings bronnen

Privé-eind punten van beveiligde resources die zijn gemaakt via Azure Cognitive Search Api's worden *gedeelde persoonlijke koppelings resources* genoemd. Dit komt doordat u toegang hebt tot een resource, zoals een opslag account, die is geïntegreerd met de [Azure Private Link-service](https://azure.microsoft.com/services/private-link/).

Azure Cognitive Search biedt via de beheer REST API een [CreateOrUpdate](/rest/api/searchmanagement/sharedprivatelinkresources/createorupdate) bewerking die u kunt gebruiken om toegang te configureren vanaf een Azure Cognitive Search indexer.

U kunt alleen verbindingen met een persoonlijk eind punt maken voor bepaalde bronnen met behulp van de preview-versie van de API voor zoek beheer (versie *2020-08-01-preview* of hoger), die is aangeduid met *Preview* in de volgende tabel. Resources zonder een *Preview* -aanduiding kunnen worden gemaakt met de preview-versie of de algemeen beschik bare API (*2020-08-01* of hoger).

De volgende tabel bevat de Azure-resources waarvoor u uitgaande privé-eind punten kunt maken van Azure Cognitive Search. Als u een gedeelde persoonlijke koppelings resource wilt maken, voert u de **groeps-ID-** waarden precies zoals ze in de API zijn geschreven. De waarden zijn hoofdletter gevoelig.

| Azure-resource | Groeps-id |
| --- | --- |
| Azure Storage-BLOB (of) ADLS gen 2 | `blob`|
| Azure Storage-tabellen | `table`|
| Azure Cosmos DB-SQL-API | `Sql`|
| Azure SQL Database | `sqlServer`|
| Azure Database for MySQL (preview-versie) | `mysqlServer`|
| Azure Key Vault | `vault` |
| Azure Functions (preview-versie) | `sites` |

U kunt ook een query uitvoeren op de Azure-resources waarvoor uitgaande privé-eindpunt verbindingen worden ondersteund met behulp [van de lijst met ondersteunde api's](/rest/api/searchmanagement/privatelinkresources/listsupported).

In de rest van dit artikel wordt een combi natie van de [Azure cli](/cli/azure/) (of [ARMClient](https://github.com/projectkudu/ARMClient) indien gewenst) en [postman](https://www.postman.com/) (of een andere HTTP-client [, zoals uw](https://curl.se/) voor keur), gebruikt om de rest API-aanroepen te demonstreren.

> [!NOTE]
> De voor beelden in dit artikel zijn gebaseerd op de volgende veronderstellingen:
> * De naam van de zoek service is _Contoso-zoeken_, die bestaat in de resource groep _Contoso_ van een abonnement met abonnements-id _00000000-0000-0000-0000-000000000000_. 
> * De resource-ID van deze zoek service is _/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-Search_.

In de rest van de voor beelden ziet u hoe de service _Contoso-Search_ kan worden geconfigureerd zodat de bijbehorende Indexeer functies toegang hebben tot gegevens uit het beveiligde-opslag account _/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Storage/storageAccounts/contoso-Storage_.

## <a name="secure-your-storage-account"></a>Uw opslag account beveiligen

Configureer het opslag account zodanig [dat alleen toegang is toegestaan vanuit specifieke subnetten](../storage/common/storage-network-security.md#grant-access-from-a-virtual-network). Als u in het Azure Portal deze optie selecteert en de set leeg laat, betekent dit dat er geen verkeer van virtuele netwerken mag worden toegestaan.

   ![Scherm opname van het deel venster firewalls en virtuele netwerken, met de optie om toegang tot geselecteerde netwerken toe te staan. ](media\search-indexer-howto-secure-access\storage-firewall-noaccess.png)

> [!NOTE]
> U kunt de [methode vertrouwde micro soft-service](../storage/common/storage-network-security.md#trusted-microsoft-services) gebruiken om virtuele netwerk-of IP-beperkingen op een opslag account over te slaan. U kunt de zoek service ook inschakelen om toegang te krijgen tot gegevens in het opslag account. Als u dit wilt doen, raadpleegt u [de vertrouwde-service-uitzonde ring voor toegang tot Azure Storage](search-indexer-howto-access-trusted-service-exception.md). 
>
> Wanneer u deze methode gebruikt, wordt de communicatie tussen Azure Cognitive Search en uw opslag account echter uitgevoerd via het open bare IP-adres van het opslag account, via het beveiligde micro soft backbone-netwerk.

### <a name="step-1-create-a-shared-private-link-resource-to-the-storage-account"></a>Stap 1: een gedeelde persoonlijke koppelings bron maken naar het opslag account

Als u Azure Cognitive Search wilt aanvragen om een uitgaand privé-eind punt verbinding te maken met het opslag account, maakt u de volgende API-aanroep, bijvoorbeeld met [Azure cli](/cli/azure/): 

`az rest --method put --uri https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe?api-version=2020-08-01 --body @create-pe.json`

Of als u [ARMClient](https://github.com/projectkudu/ARMClient)wilt gebruiken:

`armclient PUT https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe?api-version=2020-08-01 create-pe.json`

De inhoud van het bestand *create-pe.js* , dat de hoofd tekst van de aanvraag aan de API vertegenwoordigt, is als volgt:

```json
{
      "name": "blob-pe",
      "properties": {
        "privateLinkResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Storage/storageAccounts/contoso-storage",
        "groupId": "blob",
        "requestMessage": "please approve"
      }
}
```

Er `202 Accepted` wordt een antwoord geretourneerd bij een geslaagde poging. Het proces voor het maken van een uitgaand privé-eind punt is een langlopende (langdurige) bewerking. Hierin moeten de volgende resources worden geïmplementeerd:

* Een persoonlijk eind punt dat is toegewezen aan een privé-IP-adres in een `"Pending"` status. Het privé-IP-adres wordt opgehaald uit de adres ruimte die is toegewezen aan het virtuele netwerk van de uitvoerings omgeving voor de zoek service-specifieke persoonlijke Indexeer functie. Na goed keuring van het persoonlijke eind punt, is communicatie van Azure Cognitive Search naar het opslag account afkomstig van het privé-IP-adres en een beveiligd persoonlijk koppelings kanaal.

* Een privé-DNS-zone voor het type resource, op basis van de `groupId` . Door deze bron te implementeren, zorgt u ervoor dat alle DNS-Zoek opdrachten naar de privé bron gebruikmaken van het IP-adres dat is gekoppeld aan het persoonlijke eind punt.

Zorg ervoor dat u de juiste opgeeft `groupId` voor het type resource waarvoor u het persoonlijke eind punt maakt. Niet-overeenkomend leidt tot een niet-geslaagd antwoord bericht.

Net als bij alle asynchrone Azure-bewerkingen `PUT` retourneert de aanroep een `Azure-AsyncOperation` header-waarde die er als volgt uitziet:

`"Azure-AsyncOperation": "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe/operationStatuses/08586060559526078782?api-version=2020-08-01"`

U kunt deze URI periodiek pollen om de status van de bewerking te verkrijgen. Voordat u doorgaat, wordt u aangeraden te wachten tot de status van de resource voor de gedeelde persoonlijke koppelings bron een Terminal status heeft bereikt (dat wil zeggen, de status van de bewerking is *geslaagd*).

`az rest --method get --uri https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe/operationStatuses/08586060559526078782?api-version=2020-08-01`

Of het gebruik van ARMClient:

`armclient GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe/operationStatuses/08586060559526078782?api-version=2020-08-01"`

```json
{
    "status": "running" | "succeeded" | "failed"
}
```

### <a name="step-2a-approve-the-private-endpoint-connection-for-the-storage-account"></a>Stap 2a: de verbinding met het persoonlijke eind punt voor het opslag account goed keuren

> [!NOTE]
> In deze sectie gebruikt u de Azure Portal om de goedkeurings stroom voor een persoonlijk eind punt te door lopen voor opslag. U kunt ook de [rest API](/rest/api/storagerp/privateendpointconnections) gebruiken die beschikbaar is via de provider van de opslag resource.
>
> Andere providers, zoals Azure Cosmos DB of Azure SQL Server, bieden vergelijk bare opslag Resource provider-Api's voor het beheren van particuliere endpoint-verbindingen.

1. Selecteer in het Azure Portal het tabblad **verbindingen met het particuliere eind punt** van uw opslag account. Nadat de asynchrone bewerking is voltooid, moet er een aanvraag voor een persoonlijk eind punt verbinding zijn met het aanvraag bericht van de vorige API-aanroep.

   ![Scherm opname van het Azure Portal, waarin het deel venster ' persoonlijke eindpunt verbindingen ' wordt weer gegeven.](media\search-indexer-howto-secure-access\storage-privateendpoint-approval.png)

1. Selecteer het persoonlijke eind punt dat Azure Cognitive Search heeft gemaakt. In de kolom **persoonlijk eind punt** identificeert u de verbinding van het particuliere eind punt met de naam die is opgegeven in de vorige API, selecteert u **goed keuren** en voert u vervolgens een geschikt bericht in. De inhoud van het bericht is niet belang rijk. 

   Zorg ervoor dat de verbinding van het particuliere eind punt wordt weer gegeven, zoals in de volgende scherm afbeelding wordt weer gegeven. Het kan een tot twee minuten duren voordat de status is bijgewerkt in de portal.

   ![Scherm afbeelding van de Azure Portal, waarbij de status goedgekeurd wordt weer gegeven in het deel venster verbindingen met het persoonlijke eind punt.](media\search-indexer-howto-secure-access\storage-privateendpoint-after-approval.png)

Nadat de verbindings aanvraag van het particuliere eind punt is goedgekeurd, *kan* verkeer via het persoonlijke eind punt stromen. Nadat het persoonlijke eind punt is goedgekeurd, maakt Azure Cognitive Search de benodigde DNS-zone toewijzingen in de DNS-zone die hiervoor is gemaakt.

### <a name="step-2b-query-the-status-of-the-shared-private-link-resource"></a>Stap 2b: de status van de gedeelde persoonlijke koppelings resource opvragen

Als u wilt controleren of de resource van de gedeelde persoonlijke koppeling na goed keuring is bijgewerkt, haalt u de status op met behulp van de [Get-API](/rest/api/searchmanagement/sharedprivatelinkresources/get).

`az rest --method get --uri https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe?api-version=2020-08-01`

Of het gebruik van ARMClient:

`armclient GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe?api-version=2020-08-01`

Als de `properties.provisioningState` resource is `Succeeded` en `properties.status` is `Approved` , betekent dit dat de resource van de gedeelde persoonlijke koppeling functioneel is en de Indexeer functie kan worden geconfigureerd om te communiceren via het persoonlijke eind punt.

```json
{
      "name": "blob-pe",
      "properties": {
        "privateLinkResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Storage/storageAccounts/contoso-storage",
        "groupId": "blob",
        "requestMessage": "please approve",
        "status": "Approved",
        "resourceRegion": null,
        "provisioningState": "Succeeded"
      }
}

```

### <a name="step-3-configure-the-indexer-to-run-in-the-private-environment"></a>Stap 3: de Indexeer functie configureren om te worden uitgevoerd in de privé omgeving

> [!NOTE]
> U kunt deze stap uitvoeren voordat de verbinding met een particulier eind punt wordt goedgekeurd. Totdat de verbinding met een particulier eind punt is goedgekeurd, wordt een Indexeer functie die probeert te communiceren met een beveiligde bron (zoals het opslag account) uiteindelijk in een tijdelijke fout status. Nieuwe Indexeer functies kunnen niet worden gemaakt. Zodra de verbinding met een particulier eind punt is goedgekeurd, hebben Indexeer functies toegang tot het privé-opslag account.

1. [Een gegevens bron maken](/rest/api/searchservice/create-data-source) die verwijst naar het beveiligde-opslag account en een geschikte container in het opslag account. In de volgende scherm afbeelding ziet u deze aanvraag in het bericht.

   ![Scherm afbeelding van het maken van een gegevens bron in de Postman-gebruikers interface.](media\search-indexer-howto-secure-access\create-ds.png )

1. U kunt ook een [index maken](/rest/api/searchservice/create-index) en desgewenst [een vaardig heden maken](/rest/api/searchservice/create-skillset) met behulp van de rest API.

1. [Een Indexeer functie maken](/rest/api/searchservice/create-indexer) die verwijst naar de gegevens bron, de index en de vaardig heden die u in de vorige stap hebt gemaakt. Daarnaast dwingt u de Indexeer functie uit in de persoonlijke uitvoerings omgeving door de configuratie-eigenschap van de Indexeer functie in `executionEnvironment` te stellen op `private` .

   ![Scherm afbeelding van het maken van een Indexeer functie op de Postman-gebruikers interface.](media\search-indexer-howto-secure-access\create-idr.png)

   Nadat de Indexeer functie is gemaakt, moet deze beginnen met het indexeren van inhoud vanuit het opslag account via de verbinding met het persoonlijke eind punt. U kunt de status van de Indexeer functie bewaken met behulp van de API voor de [Indexeer functie](/rest/api/searchservice/get-indexer-status).

> [!NOTE]
> Als u al bestaande Indexeer functies hebt, kunt u deze bijwerken via de [put API](/rest/api/searchservice/create-indexer) door `executionEnvironment` in te stellen op `private` .

## <a name="troubleshooting"></a>Problemen oplossen

- Als het maken van de Indexeer functie mislukt met een fout bericht zoals ' gegevens bron referenties zijn ongeldig ', betekent dit dat de status van de verbinding met het persoonlijke eind punt nog niet is *goedgekeurd* of dat de verbinding niet werkt. Om het probleem te verhelpen: 
  * De status van de gedeelde persoonlijke koppelings resource ophalen met behulp van de [Get-API](/rest/api/searchmanagement/sharedprivatelinkresources/get). Als de status *goedgekeurd* is, controleert u de `properties.provisioningState` bron. Als dit het geval is `Incomplete` , betekent dit dat sommige onderliggende afhankelijkheden voor de resource niet kunnen worden ingesteld. Bij het opnieuw uitgeven van de `PUT` aanvraag om de gedeelde persoonlijke koppelings bron opnieuw te maken, moet u het probleem oplossen. Een herkeuring kan nood zakelijk zijn. Controleer de status van de resource opnieuw om te controleren of het probleem is opgelost.

- Als u de Indexeer functie maakt zonder de `executionEnvironment` eigenschap in te stellen, kan het maken van de index slagen, maar wordt de uitvoerings geschiedenis weer gegeven dat de uitvoering van de Indexeer functie is mislukt. Om het probleem te verhelpen:
   * [Werk de Indexeer functie](/rest/api/searchservice/update-indexer) bij om de uitvoerings omgeving op te geven.

- Als u de Indexeer functie hebt gemaakt zonder de `executionEnvironment` eigenschap in te stellen en deze correct wordt uitgevoerd, betekent dit dat Azure Cognitive Search heeft besloten dat de uitvoerings omgeving van de zoek service-specifieke *privé* omgeving is. Dit kan veranderen, afhankelijk van de resources die worden verbruikt door de Indexeer functie, de belasting van de zoek service en andere factoren, en kan later mislukken. Om het probleem te verhelpen:
  * We raden u ten zeerste aan de eigenschap in te stellen op `executionEnvironment` `private` om ervoor te zorgen dat deze niet meer in de toekomst mislukt.

[Quota en limieten](search-limits-quotas-capacity.md) bepalen hoeveel gedeelde persoonlijke koppelings bronnen kunnen worden gemaakt en zijn afhankelijk van de SKU van de zoek service.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over persoonlijke eind punten:

- [Wat zijn privé-eind punten?](../private-link/private-endpoint-overview.md)
- [DNS-configuraties die nodig zijn voor privé-eind punten](../private-link/private-endpoint-dns.md)