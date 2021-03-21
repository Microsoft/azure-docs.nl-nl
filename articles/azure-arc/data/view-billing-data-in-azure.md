---
title: Upload facturerings gegevens naar Azure en Bekijk deze in de Azure Portal
description: Upload facturerings gegevens naar Azure en Bekijk deze in de Azure Portal
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 03/02/2021
ms.topic: how-to
ms.openlocfilehash: 7ef1cd43d2efbc5ab92cc2b4cba4d237805d8921
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102202651"
---
# <a name="upload-billing-data-to-azure-and-view-it-in-the-azure-portal"></a>Upload facturerings gegevens naar Azure en Bekijk deze in de Azure Portal

> [!IMPORTANT] 
>  Er zijn geen kosten voor het gebruik van Azure Arc ingeschakelde gegevens Services tijdens de preview-periode. Hoewel het facturerings systeem end-to-end is, wordt de facturerings meter ingesteld op $0.  Als u dit scenario volgt, worden er vermeldingen in uw factuur weer gegeven voor een service met de naam **Hybrid Data Services** en voor resources van een type genaamd **micro soft `<resource type>` . AzureArcData/**. U kunt een record bekijken voor elke gegevens service-Azure-Arc die u maakt, maar elke record wordt in rekening gebracht voor $0.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="connectivity-modes---implications-for-billing-data"></a>Connectiviteits modi-implicaties voor facturerings gegevens

In de toekomst zijn er twee modi waarin u uw Azure Arc-gegevens Services kunt uitvoeren:

- **Indirect verbonden** : er is geen rechtstreekse verbinding met Azure. Gegevens worden alleen verzonden naar Azure via een export-en upload proces. Alle Azure Arc Data Services-implementaties werken in deze modus vandaag nog in de preview-versie.
- **Direct verbonden** -in deze modus is er een afhankelijkheid van de Azure Arc enabled Kubernetes-service om een directe verbinding te bieden tussen Azure en het Kubernetes-cluster waarop de Azure-Arc-gegevens services worden uitgevoerd. Hiermee worden meer mogelijkheden ingeschakeld en kunt u ook de Azure Portal en de Azure CLI gebruiken voor het beheren van uw gegevens services van Azure Arc, net zoals u uw gegevens Services beheert in azure PaaS.  Deze connectiviteits modus is nog niet beschikbaar in de preview-versie, maar binnenkort wel.

U vindt meer informatie over het verschil tussen de [connectiviteits modi](./connectivity.md).

In de indirect verbonden modus worden facturerings gegevens periodiek vanuit de Azure-Arc-gegevens controller naar een beveiligd bestand geëxporteerd en vervolgens geüpload naar Azure en verwerkt.  In de aanstaande, direct verbonden modus worden de facturerings gegevens automatisch ongeveer 1/uur verzonden naar Azure om een nabije real-time weer gave te geven van de kosten van uw services. Het proces voor het exporteren en uploaden van de gegevens in de indirect verbonden modus kan ook worden geautomatiseerd met behulp van scripts of we kunnen een service bouwen die deze voor u zal doen.

## <a name="upload-billing-data-to-azure"></a>Facturerings gegevens uploaden naar Azure

Als u facturerings gegevens wilt uploaden naar Azure, moet u eerst het volgende doen:

1. Maak een Azure Arc-gegevens service als u er nog geen hebt. Maak bijvoorbeeld een van de volgende opties:
   - [Een door Azure SQL beheerd exemplaar maken op Azure Arc](create-sql-managed-instance.md)
   - [Een PostgreSQL Hyperscale-servergroep met Azure Arc maken](create-postgresql-hyperscale-server-group.md)
1. [Upload resource-inventaris, gebruiks gegevens, metrische gegevens en logboeken naar Azure monitor](upload-metrics-and-logs-to-azure-monitor.md) als u dat nog niet hebt gedaan.
1. Wacht ten minste 2 uur nadat de gegevens service is gemaakt, zodat het verzamelings proces facturering enkele facturerings gegevens kan verzamelen.

Voer de volgende opdracht uit om de facturerings gegevens te exporteren:

```console
azdata arc dc export -t usage -p usage.json
```

Het bestand is nu niet versleuteld, zodat u de inhoud kunt zien. U kunt in een tekst editor openen en zien hoe de inhoud eruitziet.

U ziet dat er twee sets gegevens zijn: `resources` en `data` . De `resources` zijn de gegevens controller, postgresql grootschalige-Server groepen en SQL Managed instances. De `resources` records in de gegevens vastleggen de relevante gebeurtenissen in de geschiedenis van een resource, wanneer deze is gemaakt, wanneer deze is bijgewerkt en wanneer deze is verwijderd. In de `data` records wordt vastgelegd hoeveel kernen er voor elk uur beschikbaar zijn voor gebruik door een bepaald exemplaar.

Voor beeld van een `resource` vermelding:

```console
    {
        "customObjectName": "<resource type>-2020-29-5-23-13-17-164711",
        "uid": "4bc3dc6b-9148-4c7a-b7dc-01afc1ef5373",
        "instanceName": "sqlInstance001",
        "instanceNamespace": "arc",
        "instanceType": "<resource>",
        "location": "eastus",
        "resourceGroupName": "production-resources",
        "subscriptionId": "482c901a-129a-4f5d-86e3-cc6b294590b2",
        "isDeleted": false,
        "externalEndpoint": "32.191.39.83:1433",
        "vCores": "2",
        "createTimestamp": "05/29/2020 23:13:17",
        "updateTimestamp": "05/29/2020 23:13:17"
    }
```

Voor beeld van een `data` vermelding:

```console
        {
          "requestType": "usageUpload",
          "clusterId": "4b0917dd-e003-480e-ae74-1a8bb5e36b5d",
          "name": "DataControllerTestName",
          "subscriptionId": "482c901a-129a-4f5d-86e3-cc6b294590b2",
          "resourceGroup": "production-resources",
          "location": "eastus",
          "uploadRequest": {
            "exportType": "usages",
            "dataTimestamp": "2020-06-17T22:32:24Z",
            "data": "[{\"name\":\"sqlInstance001\",
                       \"namespace\":\"arc\",
                       \"type\":\"<resource type>\",
                       \"eventSequence\":1, 
                       \"eventId\":\"50DF90E8-FC2C-4BBF-B245-CB20DC97FF24\",
                       \"startTime\":\"2020-06-17T19:11:47.7533333\",
                       \"endTime\":\"2020-06-17T19:59:00\",
                       \"quantity\":1,
                       \"id\":\"4BC3DC6B-9148-4C7A-B7DC-01AFC1EF5373\"}]",
           "signature":"MIIE7gYJKoZIhvcNAQ...2xXqkK"
          }
        }
```

Voer de volgende opdracht uit om de usage.jsvoor het bestand naar Azure te uploaden:

```console
azdata arc dc upload -p usage.json
```

## <a name="view-billing-data-in-azure-portal"></a>Facturerings gegevens in Azure Portal weer geven

Volg deze stappen om facturerings gegevens weer te geven in de Azure Portal:

1. Open de [Azure Portal](https://portal.azure.com).
1. In het zoekvak boven aan het scherm typt u in **Cost Management** en klikt u op de Cost Management-service.
1. Klik onder **Cost Management-overzicht** op het tabblad **Cost Management** .
1. Klik op het tabblad **kosten analyse** aan de linkerkant.
1. Klik op de knop **kosten per resource** aan de bovenkant van de weer gave.
1. Zorg ervoor dat uw bereik is ingesteld op het abonnement waarin uw gegevens service resources zijn gemaakt.
1. Selecteer **kosten per resource** in de vervolg keuzelijst weer geven naast de scope selector aan de bovenkant van de weer gave.
1. Zorg ervoor dat het datum filter is ingesteld op **deze maand** of een ander tijds bereik dat duidelijk wordt gezien de timing van het moment waarop u uw gegevens service bronnen hebt gemaakt.
1. Klik op **filter toevoegen** om een filter toe te voegen op **resource type**  =  `Microsoft.AzureArcData/<data service type>` Als u wilt filteren op slechts één type gegevens service van Azure Arc ingeschakeld.
1. Er wordt nu een lijst weer geven met alle resources die zijn gemaakt en geüpload naar Azure. Omdat de facturerings meter $0 is, zult u zien dat de kosten altijd $0 zijn.

## <a name="download-billing-data"></a>Facturerings gegevens downloaden

U kunt facturerings samenvattings gegevens rechtstreeks vanuit de Azure Portal downloaden.

1. Klik in de weer gave van de resource typen die u hebt bereikt met de bovenstaande instructies op de knop downloaden in dezelfde **analyse-> weer geven per bron type** .
1. Kies het Download bestands type: Excel of CSV-en klik op de knop **gegevens downloaden** .
1. Open het bestand in een geschikte editor op basis van het geselecteerde bestands type.

## <a name="export-billing-data"></a>Facturerings gegevens exporteren

U kunt ook regel matig **gedetailleerde** gebruiks-en facturerings gegevens exporteren naar een Azure storage-container door een export taak voor facturering te maken. Dit is handig als u de details van uw facturering wilt zien, zoals het aantal uren dat een bepaalde instantie in de facturerings periode in rekening is gebracht.

Volg deze stappen om een export taak voor facturering in te stellen:

1. Klik op **exporteren** aan de linkerkant.
1. Klik op **Add**.
1. Voer een naam en een export frequentie in en klik op volgende.
1. Kies of u een nieuw opslag account wilt maken of een bestaand wilt gebruiken en vul het formulier in om het opslag account, de container en het mappad op te geven waarnaar u de facturerings gegevens bestanden wilt exporteren en klik op volgende.
1. Klik op **Create**.

De export bestanden van de facturerings gegevens zijn ongeveer 4 uur beschikbaar en worden geëxporteerd volgens het schema dat u hebt opgegeven bij het maken van de export taak voor facturering.

Volg deze stappen om de te exporteren facturerings gegevensbestanden weer te geven:

U kunt de facturerings gegevens bestanden in de Azure Portal valideren. 

> [!IMPORTANT]
> Nadat u de export taak voor facturering hebt gemaakt, wacht u 4 uur voordat u verdergaat met de volgende stappen.

1. In het zoekvak boven aan de portal typt u **opslag accounts** en klikt u op **opslag accounts**.
3. Klik op het opslag account dat u hebt opgegeven bij het maken van de export taak voor facturering hierboven.
4. Klik op containers aan de linkerkant.
5. Klik op de container die u hebt opgegeven bij het maken van de export taak voor facturering hierboven.
6. Klik op de map die u hebt opgegeven bij het maken van de export taak voor facturering hierboven.
7. Zoom in op de gegenereerde mappen en bestanden en klik op een van de gegenereerde CSV-bestanden.
8. Klik op de knop **downloaden** , waarmee het bestand wordt opgeslagen in de lokale map met Down loads.
9. Open het bestand met behulp van een CSV-bestands viewer zoals Excel.
10. De resultaten filteren om alleen de rijen met het **resource type** weer te geven  =  `Microsoft.AzureArcData/<data service resource type` .
11. U ziet het aantal uren dat het exemplaar is gebruikt in de huidige periode van 24 uur in de kolom UsageQuantity.
