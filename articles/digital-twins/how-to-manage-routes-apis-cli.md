---
title: Eind punten en routes beheren (Api's en CLI)
titleSuffix: Azure Digital Twins
description: Zie eind punten en gebeurtenis routes instellen en beheren voor Azure Digital Apparaatdubbels-gegevens.
author: alexkarcher-msft
ms.author: alkarche
ms.date: 11/18/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: e2623ebf929f6a24cfc977896acea514634ffb23
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/29/2021
ms.locfileid: "99054502"
---
# <a name="manage-endpoints-and-routes-in-azure-digital-twins-apis-and-cli"></a>Eind punten en routes beheren in azure Digital Apparaatdubbels (Api's en CLI)

[!INCLUDE [digital-twins-route-selector.md](../../includes/digital-twins-route-selector.md)]

In azure Digital Apparaatdubbels kunt u [gebeurtenis meldingen](how-to-interpret-event-data.md) naar downstream-Services of verbonden reken bronnen sturen. Dit doet u door eerst **eindpunten** in te stellen die de gebeurtenissen kunnen ontvangen. U kunt vervolgens  [**gebeurtenis routes**](concepts-route-events.md) maken om op te geven welke gebeurtenissen worden gegenereerd door Azure Digital apparaatdubbels naar welke eind punten worden verzonden.

Dit artikel begeleidt u bij het proces van het maken van eind punten en routes met de [rest api's](/rest/api/azure-digitaltwins/), de [.net (C#) SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true)en de [Azure Digital apparaatdubbels cli](how-to-use-cli.md).

U kunt ook eind punten en routes beheren met de [Azure Portal](https://portal.azure.com). Voor een versie van dit artikel die gebruikmaakt van de portal, Zie [*How-to: Manage endpoints and routes (Portal)*](how-to-manage-routes-portal.md).

## <a name="prerequisites"></a>Vereisten

- U hebt een **Azure-account** nodig (u kunt deze [hier](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)gratis instellen)
- U hebt een **Azure Digital apparaatdubbels-exemplaar** in uw Azure-abonnement nodig. Als u nog geen exemplaar hebt, kunt u er een maken met behulp van de stappen in [*How-to: een instantie en verificatie instellen*](how-to-set-up-instance-cli.md). Laat de volgende waarden van Setup handig zijn om later in dit artikel te gebruiken:
    - Exemplaarnaam
    - Resourcegroep

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]
    
## <a name="create-an-endpoint-for-azure-digital-twins"></a>Een eind punt maken voor Azure Digital Apparaatdubbels

Dit zijn de ondersteunde typen eind punten die u voor uw exemplaar kunt maken:
* [Event Grid](../event-grid/overview.md)
* [Event Hubs](../event-hubs/event-hubs-about.md)
* [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)

Zie [*kiezen tussen Azure Messa ging Services*](../event-grid/compare-messaging-services.md)voor meer informatie over de verschillende typen eind punten.

In deze sectie wordt uitgelegd hoe u deze eind punten maakt met behulp van de Azure CLI. U kunt ook eind punten beheren met de [DigitalTwinsEndpoint-besturings vlak-api's](/rest/api/digital-twins/controlplane/endpoints).

[!INCLUDE [digital-twins-endpoint-resources.md](../../includes/digital-twins-endpoint-resources.md)]

### <a name="create-the-endpoint"></a>Het eind punt maken

Zodra u de eindpunt resources hebt gemaakt, kunt u deze gebruiken voor een Azure Digital Apparaatdubbels-eind punt. In de volgende voor beelden ziet u hoe u eind punten maakt met behulp `az dt endpoint create` van de opdracht voor de [Azure Digital apparaatdubbels cli](how-to-use-cli.md). Vervang de tijdelijke aanduidingen in de opdrachten door de gegevens van uw eigen resources.

Een Event Grid-eind punt maken:

```azurecli-interactive
az dt endpoint create eventgrid --endpoint-name <Event-Grid-endpoint-name> --eventgrid-resource-group <Event-Grid-resource-group-name> --eventgrid-topic <your-Event-Grid-topic-name> -n <your-Azure-Digital-Twins-instance-name>
```

Een Event Hubs-eind punt maken:
```azurecli-interactive
az dt endpoint create eventhub --endpoint-name <Event-Hub-endpoint-name> --eventhub-resource-group <Event-Hub-resource-group> --eventhub-namespace <Event-Hub-namespace> --eventhub <Event-Hub-name> --eventhub-policy <Event-Hub-policy> -n <your-Azure-Digital-Twins-instance-name>
```

Een eind punt voor Service Bus-onderwerpen maken:
```azurecli-interactive 
az dt endpoint create servicebus --endpoint-name <Service-Bus-endpoint-name> --servicebus-resource-group <Service-Bus-resource-group-name> --servicebus-namespace <Service-Bus-namespace> --servicebus-topic <Service-Bus-topic-name> --servicebus-policy <Service-Bus-topic-policy> -n <your-Azure-Digital-Twins-instance-name>
```

Nadat deze opdrachten zijn uitgevoerd, is het gebeurtenis raster, het Event Hub Service Bus of het onderwerp beschikbaar als een eind punt in azure Digital Apparaatdubbels, onder de naam die u bij het `--endpoint-name` argument hebt opgegeven. Normaal gesp roken gebruikt u deze naam als het doel van een **gebeurtenis route**, die u [later in dit artikel](#create-an-event-route)kunt maken.

### <a name="create-an-endpoint-with-dead-lettering"></a>Een eind punt maken met onbestelbare berichten

Wanneer een eind punt een gebeurtenis binnen een bepaalde tijds periode niet kan leveren of nadat de gebeurtenis een bepaald aantal keren is geprobeerd, kan de gebeurtenis worden verzonden naar een opslag account. Dit proces wordt **onbestelbare berichten** genoemd.

Zie voor meer informatie over onbestelbare berichten [*concepten: gebeurtenis routes*](concepts-route-events.md#dead-letter-events). Ga verder met de rest van deze sectie voor instructies over het instellen van een eind punt met onbestelbare berichten.

#### <a name="set-up-storage-resources"></a>Opslag resources instellen

Voordat u de locatie van de onbestelbare letter instelt, moet u een [opslag account](../storage/common/storage-account-create.md?tabs=azure-portal) hebben met een [container](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container) die is ingesteld in uw Azure-account. 

U geeft de URL voor deze container op wanneer u later het eind punt maakt. De locatie van de onbestelbare berichten wordt aan het eind punt gegeven als container-URL met een [SAS-token](../storage/common/storage-sas-overview.md). Dat token moet `write` toestemming hebben voor de doel container in het opslag account. De volledig opgemaakte URL heeft de volgende indeling: `https://<storageAccountname>.blob.core.windows.net/<containerName>?<SASToken>` .

Volg de onderstaande stappen om deze opslag resources in uw Azure-account in te stellen, zodat u de eindpunt verbinding kunt instellen in de volgende sectie.

1. Volg de stappen in [*een opslag account maken*](../storage/common/storage-account-create.md?tabs=azure-portal) voor het maken van een **opslag account** in uw Azure-abonnement. Noteer de naam van het opslag account om deze later te gebruiken.
2. Volg de stappen in [*een container maken*](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container) voor het maken van een **container** in het nieuwe opslag account. Noteer de naam van de container om deze later te gebruiken.
3. Maak vervolgens een **SAS-token** voor uw opslag account dat door het eind punt kan worden gebruikt om het te openen. Begin met het navigeren naar uw opslag account in de [Azure Portal](https://ms.portal.azure.com/#home) (u kunt deze naam vinden met de zoek balk van de portal).
4. Kies op de pagina opslag account de koppeling _gedeelde toegangs handtekening_ in de linkernavigatiebalk om het instellen van het SAS-token te starten.

    :::image type="content" source="./media/how-to-manage-routes-apis-cli/generate-sas-token-1.png" alt-text="De pagina voor het opslag account in de Azure Portal" lightbox="./media/how-to-manage-routes-apis-cli/generate-sas-token-1.png":::

1. Selecteer op de *pagina gedeelde toegangs handtekening* onder *toegestane Services* en *toegestane bron typen* welke instellingen u wilt gebruiken. U moet ten minste één vak in elke categorie selecteren. Kies onder *toegestane machtigingen* de optie **schrijven** (u kunt ook andere machtigingen selecteren als u dat wilt).
1. Stel de gewenste waarden in voor de resterende instellingen.
1. Wanneer u klaar bent, selecteert u de knop _SAS genereren en Connection String_ om het SAS-token te genereren. 

    :::image type="content" source="./media/how-to-manage-routes-apis-cli/generate-sas-token-2.png" alt-text="De pagina opslag account in de Azure Portal alle instellings selectie voor het genereren van een SAS-token en het markeren van de knop SAS en connection string genereren" lightbox="./media/how-to-manage-routes-apis-cli/generate-sas-token-2.png"::: 

1. Hiermee genereert u onder aan dezelfde pagina verschillende SA'S-en connection string waarden onder de instellingen die u selecteert. Schuif omlaag om de waarden weer te geven en gebruik het pictogram *kopiëren naar klem bord* om de **SAS-token** waarde te kopiëren. Sla het bestand op om het later te gebruiken.

    :::image type="content" source="./media/how-to-manage-routes-apis-cli/copy-sas-token.png" alt-text="SAS-token kopiëren voor gebruik in het onbestelbare geheim." lightbox="./media/how-to-manage-routes-apis-cli/copy-sas-token.png":::
    
#### <a name="configure-the-endpoint"></a>Het eind punt configureren

Als u een eind punt wilt maken waarvoor onbestelbare berichten zijn ingeschakeld, kunt u het eind punt maken met behulp van de Azure Resource Manager-Api's. 

1. Gebruik eerst de documentatie van de [Azure Resource Manager api's](/rest/api/digital-twins/controlplane/endpoints/digitaltwinsendpoint_createorupdate) om een aanvraag in te stellen voor het maken van een eind punt en vul de vereiste aanvraag parameters in. 

2. Voeg vervolgens een `deadLetterSecret` veld toe aan het eigenschappen object in de **hoofd tekst** van de aanvraag. Stel deze waarde in op basis van de onderstaande sjabloon, waarmee een URL wordt gemaakt op basis van de naam van het opslag account, de container naam en de SAS-token waarde die u in de [vorige sectie](#set-up-storage-resources)hebt verzameld.
      
  :::code language="json" source="~/digital-twins-docs-samples/api-requests/deadLetterEndpoint.json":::

3. Verzend de aanvraag om het eind punt te maken.

Zie de Azure Digital Apparaatdubbels REST API-documentatie: [endpoints-DigitalTwinsEndpoint CreateOrUpdate](/rest/api/digital-twins/controlplane/endpoints/digitaltwinsendpoint_createorupdate)voor meer informatie over het structureren van deze aanvraag.

### <a name="message-storage-schema"></a>Schema voor bericht opslag

Zodra het eind punt met onbestelbare berichten is ingesteld, worden niet-gestuurde meldingen opgeslagen in de volgende indeling in uw opslag account:

`{container}/{endpointName}/{year}/{month}/{day}/{hour}/{eventId}.json`

Onbestelbare berichten komen overeen met het schema van de oorspronkelijke gebeurtenis die is bedoeld om aan uw oorspronkelijke eind punt te worden geleverd.

Hier volgt een voor beeld van een bericht met een onbestelbare melding voor een dubbele taak voor het maken van een [waarschuwing](how-to-interpret-event-data.md#digital-twin-life-cycle-notifications):

```json
{
  "specversion": "1.0",
  "id": "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.DigitalTwins.Twin.Create",
  "source": "<yourInstance>.api.<yourregion>.da.azuredigitaltwins-test.net",
  "data": {
    "$dtId": "<yourInstance>xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "$etag": "W/\"xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
    "TwinData": "some sample",
    "$metadata": {
      "$model": "dtmi:test:deadlettermodel;1",
      "room": {
        "lastUpdateTime": "2020-10-14T01:11:49.3576659Z"
      }
    }
  },
  "subject": "<yourInstance>xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "time": "2020-10-14T01:11:49.3667224Z",
  "datacontenttype": "application/json",
  "traceparent": "00-889a9094ba22b9419dd9d8b3bfe1a301-f6564945cb20e94a-01"
}
```

## <a name="create-an-event-route"></a>Een gebeurtenis route maken

Voor **waarde: u** moet eind punten maken zoals eerder in dit artikel wordt beschreven voordat u kunt verdergaan om een route te maken. U kunt door gaan met het maken van een gebeurtenis route wanneer de eind punten zijn ingesteld.

> [!NOTE]
> Als u onlangs uw eind punten hebt geïmplementeerd, controleert u of de implementatie is voltooid **voordat** u deze voor een nieuwe gebeurtenis route probeert te gebruiken. Als de implementatie van de route mislukt omdat de eind punten niet gereed zijn, wacht u enkele minuten en probeert u het opnieuw.
>
> Als u deze stroom bijwerkt, kunt u hiervoor het beste een account maken van 2-3 minuten wacht tijd voor de eindpunt service om de implementatie te volt ooien voordat u doorgaat met de installatie van.

Als u gegevens daad werkelijk van Azure Digital Apparaatdubbels naar een eind punt wilt verzenden, moet u een **gebeurtenis route** definiëren. Gebeurtenis routes worden gebruikt voor het verbinden van gebeurtenis stromen, in het systeem en op downstream-Services.

Een route definitie kan deze elementen bevatten:
* De naam van de route die u wilt gebruiken
* De naam van het eind punt dat u wilt gebruiken
* Een filter waarmee wordt bepaald welke gebeurtenissen naar het eindpunt worden verzonden 

Als er geen route naam is, worden er geen berichten meer doorgestuurd buiten Azure Digital Apparaatdubbels. Als er een route naam en het filter is `true` , worden alle berichten naar het eind punt doorgestuurd. Als er een route naam en een ander filter worden toegevoegd, worden berichten gefilterd op basis van het filter.

Voor één route moeten meerdere meldingen en gebeurtenis typen worden geselecteerd. 

Gebeurtenis routes kunnen worden gemaakt met de Azure Digital Apparaatdubbels [ **EventRoutes** -gegevens vlak-api's](/rest/api/digital-twins/dataplane/eventroutes) of [ **AZ DT-route** -cli-opdrachten](/cli/azure/ext/azure-iot/dt/route?view=azure-cli-latest&preserve-view=true). In de rest van deze sectie wordt het aanmaak proces door lopen.

### <a name="create-routes-with-the-apis-and-c-sdk"></a>Routes maken met de Api's en C# SDK

Een manier om gebeurtenis routes te definiëren, is met behulp van de [Data vlak-api's](how-to-use-apis-sdks.md#overview-data-plane-apis). In de voor beelden in deze sectie wordt de [.net (C#) SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true)gebruikt.

`CreateOrReplaceEventRouteAsync` is de SDK-aanroep die wordt gebruikt om een gebeurtenis route toe te voegen. Hier volgt een voor beeld van het gebruik:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/eventRoute_operations.cs" id="CreateEventRoute":::
    
> [!TIP]
> Alle SDK-functies zijn beschikbaar in synchrone en asynchrone versies.

#### <a name="event-route-sample-sdk-code"></a>Voorbeeld code voor gebeurtenis routering

De volgende voorbeeld methode laat zien hoe u een gebeurtenis route kunt maken, weer geven en verwijderen met de C#-SDK:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/eventRoute_operations.cs" id="FullEventRouteSample":::

### <a name="create-routes-with-the-cli"></a>Routes maken met de CLI

Routes kunnen ook worden beheerd met behulp van de [route opdrachten AZ DT](/cli/azure/ext/azure-iot/dt/route?view=azure-cli-latest&preserve-view=true) voor de Azure Digital apparaatdubbels cli. 

Zie [*How-to: use the Azure Digital APPARAATDUBBELS cli*](how-to-use-cli.md)(Engelstalig) voor meer informatie over het gebruik van de CLI en de opdrachten die beschikbaar zijn.

## <a name="filter-events"></a>Gebeurtenissen filteren

Als u geen filtert, ontvangen eind punten diverse gebeurtenissen van Azure Digital Apparaatdubbels:
* Telemetrie die wordt geactiveerd door [Digital apparaatdubbels](concepts-twins-graph.md) met de Azure Digital APPARAATDUBBELS Service API
* Dubbele eigenschaps wijzigings meldingen, die worden geactiveerd bij wijzigingen in de eigenschappen voor elke dubbele in het Azure Digital Apparaatdubbels-exemplaar
* Levenscyclus gebeurtenissen, die worden geactiveerd wanneer apparaatdubbels of relaties worden gemaakt of verwijderd

U kunt de verzonden gebeurtenissen beperken door een **filter** voor een eind punt toe te voegen aan de route van uw gebeurtenis.

Als u een filter wilt toevoegen, kunt u een PUT-aanvraag gebruiken voor *https://{uw-Azure-Digital-apparaatdubbels-hostname}/eventRoutes/{Event-route-name}? API-Version = 2020-10-31* met de volgende hoofd tekst:

:::code language="json" source="~/digital-twins-docs-samples/api-requests/filter.json":::

Dit zijn de ondersteunde route filters. Gebruik de details in de kolom *filter tekst schema* om de `<filter-text>` tijdelijke aanduiding te vervangen in de hoofd tekst van de aanvraag.

[!INCLUDE [digital-twins-route-filters](../../includes/digital-twins-route-filters.md)]

[!INCLUDE [digital-twins-route-metrics](../../includes/digital-twins-route-metrics.md)]

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de verschillende typen gebeurtenis berichten die u kunt ontvangen:
* [*Instructies: gebeurtenis gegevens interpreteren*](how-to-interpret-event-data.md)
