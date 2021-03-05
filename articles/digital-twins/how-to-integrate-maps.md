---
title: Integreren met Azure Maps
titleSuffix: Azure Digital Twins
description: Zie Azure Functions gebruiken om een functie te maken die de dubbele grafiek en Azure Digital Apparaatdubbels-meldingen kan gebruiken voor het bijwerken van een Azure Maps binnenste kaart.
author: alexkarcher-msft
ms.author: alkarche
ms.date: 1/19/2021
ms.topic: how-to
ms.service: digital-twins
ms.reviewer: baanders
ms.openlocfilehash: 990a0ee73bd91ccb748c948b5fcf0e6124d84a03
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102201427"
---
# <a name="use-azure-digital-twins-to-update-an-azure-maps-indoor-map"></a>Azure Digital Apparaatdubbels gebruiken voor het bijwerken van een Azure Maps binnenste kaart

In dit artikel worden de stappen beschreven die nodig zijn om Azure Digital Apparaatdubbels-gegevens te gebruiken voor het bijwerken van informatie die wordt weer gegeven op een *binnenste kaart* met [Azure Maps](../azure-maps/about-azure-maps.md). Azure Digital Apparaatdubbels slaat een grafiek van uw IoT-apparaten op en stuurt telemetrie naar verschillende eind punten, waardoor het de perfecte service is voor het bijwerken van informatieve overlays op kaarten.

Deze procedure geldt voor:

1. Uw Azure Digital Apparaatdubbels-exemplaar configureren voor het verzenden van dubbele update gebeurtenissen naar een functie in [Azure functions](../azure-functions/functions-overview.md).
2. Het maken van een functie voor het bijwerken van een Azure Maps functie statusset van binnenste kaarten.
3. Het opslaan van uw Maps ID en de ID van de functie statusset in de Azure Digital Apparaatdubbels-grafiek.

### <a name="prerequisites"></a>Vereisten

* Volg de zelf studie over Azure Digital Apparaatdubbels [*: Verbind een end-to-end oplossing*](./tutorial-end-to-end.md).
    * U gaat dit twee verlengen met een extra eind punt en route. U kunt ook een andere functie toevoegen aan uw functie-app vanuit deze zelf studie. 
* Volg de Azure Maps [*zelf studie: gebruik Azure Maps Maker voor het maken van plattegronden*](../azure-maps/tutorial-creator-indoor-maps.md) om een Azure Maps binnenste kaart te maken met een *functie statusset*.
    * [Functie-statesets](../azure-maps/creator-indoor-maps.md#feature-statesets) zijn verzamelingen van dynamische eigenschappen (statussen) die zijn toegewezen aan functies van gegevensset, zoals kamers of apparatuur. In de bovenstaande zelf studie van Azure Maps is de statusset voor de functie statusset opgeslagen die u op een kaart wilt weer geven.
    * U hebt uw functie *statusset-id* en Azure Maps- *abonnements sleutel* nodig.

### <a name="topology"></a>Topologie

In de onderstaande afbeelding ziet u waar de integratie-elementen in de binnenste zelf studie in een groter, end-to-end Azure Digital Apparaatdubbels-scenario passen.

:::image type="content" source="media/how-to-integrate-maps/maps-integration-topology.png" alt-text="Een weer gave van Azure-Services in een end-to-end-scenario, waarbij u het integratie gedeelte over de binnenste kaarten markeert" lightbox="media/how-to-integrate-maps/maps-integration-topology.png":::

## <a name="create-a-function-to-update-a-map-when-twins-update"></a>Een functie maken om een kaart bij te werken wanneer apparaatdubbels update

Eerst maakt u een route in azure Digital Apparaatdubbels om alle dubbele update gebeurtenissen door te sturen naar een event grid-onderwerp. Vervolgens gebruikt u een functie om die update berichten te lezen en een functie statusset in Azure Maps bij te werken. 

## <a name="create-a-route-and-filter-to-twin-update-notifications"></a>Een route en filter maken voor updatemeldingen van de dubbel

Azure Digital Apparaatdubbels-instanties kunnen dubbele update gebeurtenissen verzenden wanneer de status van een twee is bijgewerkt. De zelf studie over Azure Digital Apparaatdubbels [*: verbinding maken met een end-to-end oplossing*](./tutorial-end-to-end.md) die hierboven wordt beschreven, wordt een scenario waarbij een thermo meter wordt gebruikt voor het bijwerken van een temperatuur kenmerk dat is gekoppeld aan de dubbele. U gaat deze oplossing uitbreiden door u te abonneren op update meldingen voor apparaatdubbels en deze informatie te gebruiken om uw kaarten bij te werken.

Dit patroon leest van de ruimte tussen direct, in plaats van het IoT-apparaat, dat u de flexibiliteit geeft om de onderliggende gegevens bron te wijzigen voor de Tempe ratuur zonder dat u uw toewijzings logica hoeft bij te werken. U kunt bijvoorbeeld meerdere Thermo meters toevoegen of deze ruimte instellen om een thermo meter te delen met een andere ruimte, zonder dat u uw toewijzings logica hoeft bij te werken.

1. Een event grid-onderwerp maken dat gebeurtenissen ontvangt van uw Azure Digital Apparaatdubbels-exemplaar.
    ```azurecli-interactive
    az eventgrid topic create -g <your-resource-group-name> --name <your-topic-name> -l <region>
    ```

2. Maak een eind punt om uw event grid-onderwerp aan Azure Digital Apparaatdubbels te koppelen.
    ```azurecli-interactive
    az dt endpoint create eventgrid --endpoint-name <Event-Grid-endpoint-name> --eventgrid-resource-group <Event-Grid-resource-group-name> --eventgrid-topic <your-Event-Grid-topic-name> -n <your-Azure-Digital-Twins-instance-name>
    ```

3. Maak een route in Azure Digital Twins om updategebeurtenissen van de dubbel naar uw eindpunt te verzenden.

    >[!NOTE]
    >Er is momenteel een **bekend probleem** in Cloud Shell dat deze opdrachtgroepen beïnvloedt: `az dt route`, `az dt model`, `az dt twin`.
    >
    >Om dit probleem op te lossen, kunt u `az login` in Cloud Shell uitvoeren voordat u de opdracht uitvoert, of kunt u de [lokale CLI](/cli/azure/install-azure-cli) gebruiken in plaats van Cloud Shell. Zie [*Problemen oplossen: Bekende problemen in Azure Digital Twins*](troubleshoot-known-issues.md#400-client-error-bad-request-in-cloud-shell) voor meer informatie hierover.

    ```azurecli-interactive
    az dt route create -n <your-Azure-Digital-Twins-instance-name> --endpoint-name <Event-Grid-endpoint-name> --route-name <my_route> --filter "type = 'Microsoft.DigitalTwins.Twin.Update'"
    ```

## <a name="create-a-function-to-update-maps"></a>Een functie maken om kaarten bij te werken

U gaat vanuit de end-to-end-zelf studie een door **Event grid geactiveerde functie** maken in uw functie-app ([*zelf studie: een end-to-end oplossing verbinden*](./tutorial-end-to-end.md)). Met deze functie worden deze meldingen uitgepakt en worden updates naar een Azure Maps-functie statusset verzonden om de Tempe ratuur van één kamer bij te werken.

Raadpleeg het volgende document voor referentie-informatie: [*Azure Event grid trigger voor Azure functions*](../azure-functions/functions-bindings-event-grid-trigger.md).

Vervang de functie code door de volgende code. Er worden alleen updates voor de ruimte apparaatdubbels, lees de bijgewerkte Tempe ratuur en verzend die informatie naar Azure Maps.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/updateMaps.cs":::

U moet twee omgevings variabelen instellen in uw functie-app. De ene is uw [Azure Maps primaire abonnements sleutel](../azure-maps/quick-demo-map-app.md#get-the-primary-key-for-your-account)en een van uw [Azure Maps stateset-id](../azure-maps/tutorial-creator-indoor-maps.md#create-a-feature-stateset).

```azurecli-interactive
az functionapp config appsettings set --name <your-App-Service-(function-app)-name> --resource-group <your-resource-group> --settings "subscription-key=<your-Azure-Maps-primary-subscription-key>"
az functionapp config appsettings set --name <your-App-Service-(function-app)-name>  --resource-group <your-resource-group> --settings "statesetID=<your-Azure-Maps-stateset-ID>"
```

### <a name="view-live-updates-on-your-map"></a>Live-updates op uw kaart weer geven

Volg de onderstaande stappen om de Tempe ratuur voor Live bijwerken te bekijken:

1. Begin met het verzenden van gesimuleerde IoT-gegevens door het **DeviceSimulator** -project uit te voeren vanuit de Azure Digital Apparaatdubbels- [*zelf studie: verbinding maken met een end-to-end oplossing*](tutorial-end-to-end.md). De instructies hiervoor vindt u in de sectie [*simulatie configureren en uitvoeren*](././tutorial-end-to-end.md#configure-and-run-the-simulation) .
2. Gebruik [de module Azure Maps in het **binnenste**](../azure-maps/how-to-use-indoor-module.md) om uw binnenste kaarten weer te geven die zijn gemaakt in azure Maps Maker.
    1. Kopieer de HTML uit het [*voor beeld: gebruik de module binnenste kaarten*](../azure-maps/how-to-use-indoor-module.md#example-use-the-indoor-maps-module) van de zelf studie over de binnenste kaarten [*: gebruik de module Azure Maps binnenste*](../azure-maps/how-to-use-indoor-module.md) kaarten naar een lokaal bestand.
    1. Vervang de *abonnements sleutel*, *tilesetId* en *statesetID*  in het lokale HTML-bestand door uw waarden.
    1. Open dat bestand in uw browser.

Beide steek proeven verzenden temperatuur in een compatibel bereik, zodat u de kleur van de room 121-update op de kaart ongeveer elke 30 seconden zou moeten zien.

:::image type="content" source="media/how-to-integrate-maps/maps-temperature-update.png" alt-text="Een Office-kaart met room 121 gekleurd oranje":::

## <a name="store-your-maps-information-in-azure-digital-twins"></a>Sla uw kaarten gegevens op in azure Digital Apparaatdubbels

Nu u een hardcoded oplossing hebt om uw kaarten gegevens bij te werken, kunt u de Azure Digital Apparaatdubbels-grafiek gebruiken om alle benodigde informatie op te slaan voor het bijwerken van uw binnenste kaart. Dit geldt ook voor de statusset-ID, de abonnements-ID van Maps en de onderdeel-ID van elke kaart en locatie. 

In een oplossing voor dit specifieke voor beeld moet elke ruimte op het hoogste niveau worden bijgewerkt zodat deze een statusset-ID en Maps abonnements-ID-kenmerk heeft, en elke ruimte kan worden bijgewerkt zodat deze een functie-ID heeft. U moet deze waarden eenmaal instellen tijdens het initialiseren van de dubbele grafiek en vervolgens de waarden voor elke dubbele update gebeurtenis opvragen.

Afhankelijk van de configuratie van uw topologie kunt u deze drie kenmerken opslaan op verschillende niveaus die samen hangen met de granulatie van de kaart.

## <a name="next-steps"></a>Volgende stappen

Zie de volgende verwijzingen voor meer informatie over het beheren, bijwerken en ophalen van gegevens uit de apparaatdubbels-grafiek:

* [*Instructies: digitale apparaatdubbels beheren*](./how-to-manage-twin.md)
* [*Instructies: een query uitvoeren op de dubbele grafiek*](./how-to-query-graph.md)
