---
title: Integreren met Azure Time Series Insights
titleSuffix: Azure Digital Twins
description: Zie gebeurtenis routes van Azure Digital Apparaatdubbels instellen op Azure Time Series Insights.
author: alexkarcher-msft
ms.author: alkarche
ms.date: 4/7/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 256bb80687ffedba9718303710335cce1a582c1d
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107303997"
---
# <a name="integrate-azure-digital-twins-with-azure-time-series-insights"></a>Azure Digital Apparaatdubbels integreren met Azure Time Series Insights

In dit artikel leert u hoe u Azure Digital Apparaatdubbels integreert met behulp van [Azure time series Insights (TSI)](../time-series-insights/overview-what-is-tsi.md).

Met de oplossing die in dit artikel wordt beschreven, kunt u historische gegevens over uw IoT-oplossing verzamelen en analyseren. Azure Digital Twins is zeer geschikt voor het verzenden van gegevens naar Time Series Insights, omdat u hiermee meerdere gegevensstreams kunt samenbrengen en uw gegevens uniform kunt maken voordat u deze naar Time Series Insights verzendt.

## <a name="prerequisites"></a>Vereisten

Voordat u een relatie met Time Series Insights kunt instellen, moet u de volgende resources instellen:
* Een **IOT-hub**. Zie de sectie [*een IOT hub maken*](../iot-hub/quickstart-send-telemetry-cli.md#create-an-iot-hub) van de snelstartgids voor *IOT hub het verzenden van telemetrie* voor instructies.
* Een **Azure Digital apparaatdubbels-exemplaar**.
Zie [*How to: een Azure Digital apparaatdubbels-exemplaar en-verificatie instellen*](./how-to-set-up-instance-portal.md)voor instructies.
* Een **model en een dubbele in het Azure Digital apparaatdubbels-exemplaar**.
U moet dubbele gegevens een paar keer bijwerken om te zien dat de gegevens die in Time Series Insights worden bijgehouden. Zie voor instructies de sectie [*een model en dubbele toevoegen*](how-to-ingest-iot-hub-data.md#add-a-model-and-twin) in het artikel een *IOT-hub* opnemen.

> [!TIP]
> In dit artikel worden de veranderende digitale dubbele waarden die worden weer gegeven in Time Series Insights, hand matig bijgewerkt voor eenvoud. Als u dit artikel echter wilt volt ooien met Live gesimuleerde gegevens, kunt u een Azure-functie instellen waarmee Digital apparaatdubbels wordt bijgewerkt op basis van IoT-telemetriegegevens van een gesimuleerd apparaat. Voor instructies, volgt u [*procedure: opname van IOT hub gegevens*](how-to-ingest-iot-hub-data.md), met inbegrip van de laatste stappen voor het uitvoeren van de Device Simulator en valideren dat de gegevens stroom werkt.
>
> Ga later naar een andere TIP om te laten zien waar u de Device Simulator kunt gaan uitvoeren en laat uw Azure functions automatisch de apparaatdubbels bijwerken, in plaats van hand matige Digital-dubbele update-opdrachten te verzenden.


## <a name="solution-architecture"></a>Architectuur voor de oplossing

U gaat Time Series Insights toevoegen aan Azure Digital Apparaatdubbels via het onderstaande pad.

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-integrate-time-series-insights/diagram-simple.png" alt-text="Diagram van Azure-Services in een end-to-end-scenario, waarbij Time Series Insights worden gemarkeerd." lightbox="media/how-to-integrate-time-series-insights/diagram-simple.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="create-event-hub-namespace"></a>Event hub-naamruimte maken

Voordat u de Event hubs maakt, maakt u eerst een Event Hub naam ruimte waarmee gebeurtenissen worden ontvangen van uw Azure Digital Apparaatdubbels-exemplaar. U kunt de onderstaande Azure CLI-instructies gebruiken of de Azure Portal: Quick Start [*: een event hub maken met behulp van Azure Portal*](../event-hubs/event-hubs-create.md). Ga naar [*Azure-producten beschikbaar per regio*](https://azure.microsoft.com/global-infrastructure/services/?products=event-hubs)om te zien welke regio's Event hubs ondersteunen.

```azurecli-interactive
az eventhubs namespace create --name <name-for-your-event-hubs-namespace> --resource-group <your-resource-group> -l <region>
```

> [!TIP]
> Als er een fout melding wordt weer gegeven `BadRequest: The specified service namespace is invalid.` , moet u ervoor zorgen dat de naam die u hebt gekozen voor uw naam ruimte voldoet aan de naamgevings vereisten die in dit referentie document worden beschreven: [naam ruimte maken](/rest/api/servicebus/create-namespace).

U gebruikt deze event hubs-naam ruimte om de twee Event hubs te bewaren die nodig zijn voor dit artikel:

  1. **Apparaatdubbels hub** -Event hub voor het ontvangen van twee wijzigings gebeurtenissen
  2. **Time Series hub** -Event hub gebeurtenissen naar Time Series Insights streamen

In de volgende secties vindt u instructies voor het maken en configureren van deze hubs in uw Event Hub naam ruimte.

## <a name="create-twins-hub"></a>Apparaatdubbels hub maken

De eerste Event Hub u in dit artikel maakt, is de **apparaatdubbels-hub**. Deze Event Hub ontvangt twee wijzigings gebeurtenissen van Azure Digital Apparaatdubbels.
Als u de apparaatdubbels-hub wilt instellen, voert u de volgende stappen uit in deze sectie:

1. De apparaatdubbels-hub maken
2. Een autorisatie regel maken om machtigingen voor de hub te beheren
3. Een eind punt maken in azure Digital Apparaatdubbels die gebruikmaakt van de autorisatie regel voor toegang tot de hub
4. Een route maken in azure Digital Apparaatdubbels die gebeurtenis met dubbele updates verzendt naar het eind punt en de verbonden apparaatdubbels hub
5. De apparaatdubbels hub ophalen connection string

Maak de **apparaatdubbels-hub** met de volgende CLI-opdracht. Geef een naam op voor uw apparaatdubbels-hub.

```azurecli-interactive
az eventhubs eventhub create --name <name-for-your-twins-hub> --resource-group <your-resource-group> --namespace-name <your-event-hubs-namespace-from-above>
```

### <a name="create-twins-hub-authorization-rule"></a>Apparaatdubbels hub-autorisatie regel maken

Maak een [autorisatie regel](/cli/azure/eventhubs/eventhub/authorization-rule#az-eventhubs-eventhub-authorization-rule-create) met machtigingen voor verzenden en ontvangen. Geef een naam op voor de regel.

```azurecli-interactive
az eventhubs eventhub authorization-rule create --rights Listen Send --name <name-for-your-twins-hub-auth-rule> --resource-group <your-resource-group> --namespace-name <your-event-hubs-namespace-from-earlier> --eventhub-name <your-twins-hub-from-above>
```

### <a name="create-twins-hub-endpoint"></a>Apparaatdubbels hub-eind punt maken

Maak een Azure Digital Apparaatdubbels- [eind punt](concepts-route-events.md#create-an-endpoint) dat uw event hub koppelt aan uw Azure Digital apparaatdubbels-exemplaar. Geef een naam op voor het eind punt van de apparaatdubbels-hub.

```azurecli-interactive
az dt endpoint create eventhub -n <your-Azure-Digital-Twins-instance-name> --eventhub-resource-group <your-resource-group> --eventhub-namespace <your-event-hubs-namespace-from-earlier> --eventhub <your-twins-hub-name-from-above> --eventhub-policy <your-twins-hub-auth-rule-from-earlier> --endpoint-name <name-for-your-twins-hub-endpoint>
```

### <a name="create-twins-hub-event-route"></a>Een apparaatdubbels hub-gebeurtenis route maken

Azure Digital Apparaatdubbels-instanties kunnen [dubbele update gebeurtenissen](how-to-interpret-event-data.md) verzenden wanneer de status van een twee is bijgewerkt. In deze sectie maakt u een Azure Digital Apparaatdubbels- **gebeurtenis route** waarmee deze update gebeurtenissen worden doorgestuurd naar de apparaatdubbels-hub voor verdere verwerking.

Maak een [route](concepts-route-events.md#create-an-event-route) in azure Digital apparaatdubbels om dubbele update gebeurtenissen naar uw eind punt te verzenden. Met het filter in deze route worden alleen dubbele update berichten door gegeven aan uw eind punt. Geef een naam op voor de route van de apparaatdubbels hub-gebeurtenis.

```azurecli-interactive
az dt route create -n <your-Azure-Digital-Twins-instance-name> --endpoint-name <your-twins-hub-endpoint-from-above> --route-name <name-for-your-twins-hub-event-route> --filter "type = 'Microsoft.DigitalTwins.Twin.Update'"
```

### <a name="get-twins-hub-connection-string"></a>Apparaatdubbels hub-connection string ophalen

Haal de [apparaatdubbels-Event Hub Connection String](../event-hubs/event-hubs-get-connection-string.md)op met behulp van de autorisatie regels die u hierboven hebt gemaakt voor de apparaatdubbels hub.

```azurecli-interactive
az eventhubs eventhub authorization-rule keys list --resource-group <your-resource-group> --namespace-name <your-event-hubs-namespace-from-earlier> --eventhub-name <your-twins-hub-from-above> --name <your-twins-hub-auth-rule-from-earlier>
```
Noteer de **primaryConnectionString** -waarde van het resultaat om de instelling van de apparaatdubbels hub-app verderop in dit artikel te configureren.

## <a name="create-time-series-hub"></a>Time Series hub maken

De tweede Event Hub u in dit artikel maakt, is de **Time Series hub**. Dit is een Event Hub waarmee de Azure Digital Apparaatdubbels-gebeurtenissen worden gestreamd naar Time Series Insights.
Als u de time series hub wilt instellen, voert u de volgende stappen uit:

1. De time series hub maken
2. Een autorisatie regel maken om machtigingen voor de hub te beheren
3. De time series hub-connection string ophalen

Wanneer u later het Time Series Insights-exemplaar maakt, verbindt u deze time series hub als de gebeurtenis bron voor het Time Series Insights exemplaar.

Maak de **Time Series-hub** met behulp van de volgende opdracht. Geef een naam op voor de time series hub.

```azurecli-interactive
 az eventhubs eventhub create --name <name-for-your-time-series-hub> --resource-group <your-resource-group> --namespace-name <your-event-hub-namespace-from-earlier>
```

### <a name="create-time-series-hub-authorization-rule"></a>Een time series hub-autorisatie regel maken

Maak een [autorisatie regel](/cli/azure/eventhubs/eventhub/authorization-rule#az-eventhubs-eventhub-authorization-rule-create) met machtigingen voor verzenden en ontvangen. Geef een naam op voor de time series hub auth-regel.

```azurecli-interactive
az eventhubs eventhub authorization-rule create --rights Listen Send --name <name-for-your-time-series-hub-auth-rule> --resource-group <your-resource-group> --namespace-name <your-event-hub-namespace-from-earlier> --eventhub-name <your-time-series-hub-name-from-above>
```

### <a name="get-time-series-hub-connection-string"></a>connection string voor time series-hub ophalen

Haal de [Time Series hub Connection String](../event-hubs/event-hubs-get-connection-string.md)op met de autorisatie regels die u hierboven hebt gemaakt voor de time series hub:

```azurecli-interactive
az eventhubs eventhub authorization-rule keys list --resource-group <your-resource-group> --namespace-name <your-event-hub-namespace-from-earlier> --eventhub-name <your-time-series-hub-name-from-earlier> --name <your-time-series-hub-auth-rule-from-earlier>
```
Noteer de **primaryConnectionString** -waarde van het resultaat om de instelling van de time series hub-app later in dit artikel te configureren.

Houd ook rekening met de volgende waarden om deze later te gebruiken om een Time Series Insights-exemplaar te maken.
* Event hub-naamruimte
* Naam van de time series-hub
* Regel voor time series hub auth

## <a name="create-a-function"></a>Een functie maken

In deze sectie maakt u een Azure-functie waarmee dubbele update gebeurtenissen van hun oorspronkelijke formulier worden geconverteerd als JSON-patch-documenten naar JSON-objecten, die alleen bijgewerkte en toegevoegde waarden van uw apparaatdubbels bevatten.

### <a name="step-1-create-function-app"></a>Stap 1: functie-app maken

Maak eerst een nieuw functie-app-project in Visual Studio. Zie de sectie [**een functie-app maken in Visual Studio**](how-to-create-azure-function.md#create-a-function-app-in-visual-studio) van het artikel *How to: een functie instellen voor het verwerken van gegevens* voor instructies over hoe u dit doet.

### <a name="step-2-add-a-new-function"></a>Stap 2: een nieuwe functie toevoegen

Maak een nieuwe Azure-functie met de naam *ProcessDTUpdatetoTSI. cs* om de telemetrie van apparaten bij te werken naar de time series Insights. Het functie type is **Event hub-trigger**.

:::image type="content" source="media/how-to-integrate-time-series-insights/create-event-hub-trigger-function.png" alt-text="Scherm opname van Visual Studio voor het maken van een nieuwe Azure-functie van het type Event Hub-trigger.":::

### <a name="step-3-fill-in-function-code"></a>Stap 3: functie code invullen

Voeg de volgende pakketten toe aan uw project:
* [Micro soft. Azure. webjobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs/)
* [Micro soft. Azure. webjobs. Extensions. Event hubs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs/)
* [Micro soft. NET. SDK. functions](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/)

Vervang de code in het bestand *ProcessDTUpdatetoTSI. cs* door de volgende code:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/updateTSI.cs":::

Sla uw functie code op.

### <a name="step-4-publish-the-function-app-to-azure"></a>Stap 4: de functie-app publiceren in azure

Publiceer het project met de functie *ProcessDTUpdatetoTSI. cs* naar een functie-app in Azure.

Zie de sectie [**de functie-app publiceren in azure**](how-to-create-azure-function.md#publish-the-function-app-to-azure) van het artikel *How to: een functie voor het verwerken van gegevens* voor meer informatie over hoe u dit doet.

Sla de naam van de functie-app op die u later wilt gebruiken om de app-instellingen voor de twee Event hubs te configureren.

### <a name="step-5-security-access-for-the-function-app"></a>Stap 5: beveiligings toegang voor de functie-app

Wijs vervolgens **een Access-rol toe** voor de functie en **Configureer de toepassings instellingen** zodat deze toegang krijgen tot uw Azure Digital apparaatdubbels-exemplaar. Voor instructies over hoe u dit doet, zie de sectie [**beveiligings toegang instellen voor de functie-app**](how-to-create-azure-function.md#set-up-security-access-for-the-function-app) van het artikel *een functie voor het verwerken van gegevens* .

### <a name="step-6-configure-app-settings-for-the-two-event-hubs"></a>Stap 6: de app-instellingen voor de twee Event hubs configureren

Vervolgens voegt u omgevings variabelen toe aan de instellingen van de functie-app waarmee deze toegang kan krijgen tot de apparaatdubbels hub en time series hub.

Gebruik de apparaatdubbels hub **primaryConnectionString** -waarde die u eerder hebt opgeslagen om een app-instelling te maken in de functie-app die de apparaatdubbels hub bevat Connection String:

```azurecli-interactive
az functionapp config appsettings set --settings "EventHubAppSetting-Twins=<your-twins-hub-primaryConnectionString>" -g <your-resource-group> -n <your-App-Service-(function-app)-name>
```

Gebruik de time series hub **primaryConnectionString** -waarde die u eerder hebt opgeslagen om een app-instelling te maken in de functie-app die de tijdreeks hub bevat Connection String:

```azurecli-interactive
az functionapp config appsettings set --settings "EventHubAppSetting-TSI=<your-time-series-hub-primaryConnectionString>" -g <your-resource-group> -n <your-App-Service-(function-app)-name>
```

## <a name="create-and-connect-a-time-series-insights-instance"></a>Een Time Series Insights-instantie maken en verbinden

In deze sectie stelt u Time Series Insights instantie in om gegevens van uw time series hub te ontvangen. Zie [*zelf studie: een Azure time series Insights GEN2 payg-omgeving instellen*](../time-series-insights/tutorial-set-up-environment.md)voor meer informatie over dit proces. Volg de onderstaande stappen om een time series Insights-omgeving te maken.

1. Zoek in het [Azure Portal](https://portal.azure.com)naar *Time Series Insights omgevingen* en selecteer de knop **toevoegen** . Kies de volgende opties om de time series-omgeving te maken.

    * **Abonnement** : Kies uw abonnement.
        - **Resource groep** : Kies uw resource groep.
    * **Omgevings naam** : Geef een naam op voor uw time series-omgeving.
    * **Locatie** : Kies een locatie.
    * **Laag** -Kies de prijs categorie **Gen2 (L1)** .
    * **Eigenschaps naam** : Voer **$dtId** in (meer informatie over het selecteren van een id-waarde in [*Best practices voor het kiezen van een time series-id*](../time-series-insights/how-to-select-tsid.md)).
    * **Naam van opslag account** : Geef een naam op voor het opslag account.
    * **Warme Store inschakelen** : verlaat dit veld ingesteld op *Ja*.

    U kunt de standaard waarden voor andere eigenschappen op deze pagina laten staan. Selecteer de knop **volgende: gebeurtenis bron >** .

    :::image type="content" source="media/how-to-integrate-time-series-insights/create-time-series-insights-environment-1.png" alt-text="Scherm opname van de Azure Portal om Time Series Insights omgeving te maken. Selecteer uw abonnement, resource groep en locatie uit de respectieve vervolg keuzelijsten en kies een naam voor uw omgeving." lightbox="media/how-to-integrate-time-series-insights/create-time-series-insights-environment-1.png":::
        
    :::image type="content" source="media/how-to-integrate-time-series-insights/create-time-series-insights-environment-2.png" alt-text="Scherm opname van de Azure Portal om Time Series Insights omgeving te maken. De prijs categorie Gen2 (L1) is geselecteerd en de eigenschaps naam van de tijd reeks-ID is $dtId." lightbox="media/how-to-integrate-time-series-insights/create-time-series-insights-environment-2.png":::

2. Kies op het tabblad *gebeurtenis bron* de volgende velden:

   * **Een gebeurtenisbron maken** -Kies *Ja*.
   * **Bron type** : Selecteer *Event hub*.
   * **Naam** : Geef een naam op voor de bron van de gebeurtenis.
   * **Abonnement** : Kies uw Azure-abonnement.
   * **Event hub-naam ruimte** : Kies de naam ruimte die u eerder in dit artikel hebt gemaakt.
   * **Event hub-naam** : Kies de naam van de **Time Series-hub** die u eerder in dit artikel hebt gemaakt.
   * **Naam van toegangs beleid voor Event hub** -Kies de *Time Series hub auth-regel* die u eerder in dit artikel hebt gemaakt.
   * **Event hub-consumenten groep** : Selecteer *Nieuw* en geef een naam op voor uw event hub Consumer groep. Selecteer vervolgens *toevoegen*.
   * **Eigenschaps naam** : laat dit veld leeg.
    
    Klik op de knop **beoordeling + maken** om alle details te bekijken. Selecteer vervolgens de knop **evalueren + maken** opnieuw om de tijdreeks omgeving te maken.

    :::image type="content" source="media/how-to-integrate-time-series-insights/create-tsi-environment-event-source.png" alt-text="Scherm opname van de Azure Portal om Time Series Insights omgeving te maken. U maakt een gebeurtenis bron met de Event Hub informatie hierboven. U maakt ook een nieuwe consumenten groep." lightbox="media/how-to-integrate-time-series-insights/create-tsi-environment-event-source.png":::

## <a name="send-iot-data-to-azure-digital-twins"></a>IoT-gegevens verzenden naar Azure Digital Apparaatdubbels

Als u wilt beginnen met het verzenden van gegevens naar Time Series Insights, moet u beginnen met het bijwerken van de digitale twee eigenschappen in azure Digital Apparaatdubbels met het wijzigen van gegevens waarden.

Gebruik de volgende CLI-opdracht voor het bijwerken van de eigenschap *Tempe ratuur* op de *thermostat67* -dubbele waarde die u hebt toegevoegd aan uw instantie in de sectie [vereisten](#prerequisites) .

```azurecli-interactive
az dt twin update -n <your-azure-digital-twins-instance-name> --twin-id thermostat67 --json-patch '{"op":"replace", "path":"/Temperature", "value": 20.5}'
```

**Herhaal de opdracht mini maal vier keer met verschillende temperatuur waarden** om verschillende gegevens punten te maken die later in de time series Insights omgeving kunnen worden waargenomen.

> [!TIP]
> Als u dit artikel wilt volt ooien met Live gesimuleerde gegevens in plaats van de digitale dubbele waarden hand matig bij te werken, moet u eerst controleren of u de TIP van de sectie [*vereisten*](#prerequisites) hebt voltooid voor het instellen van een Azure-functie waarmee apparaatdubbels van een gesimuleerd apparaat wordt bijgewerkt.
Daarna kunt u het apparaat nu uitvoeren om te beginnen met het verzenden van gesimuleerde gegevens en het bijwerken van uw digitale bron via die gegevens stroom.

## <a name="visualize-your-data-in-time-series-insights"></a>Uw gegevens in Time Series Insights weergeven

Gegevens moeten nu worden gestroomd naar uw Time Series Insights-exemplaar, die klaar zijn om te worden geanalyseerd. Volg de onderstaande stappen om de gegevens in te verkennen.

1. Zoek in de [Azure Portal](https://portal.azure.com)de naam van uw time series-omgeving die u eerder hebt gemaakt. Selecteer in de menu opties aan de linkerkant de optie *overzicht* om de *URL van Time Series Insights Explorer* weer te geven. Selecteer de URL om de temperatuur wijzigingen weer te geven die in de Time Series Insights omgeving worden weer gegeven.

    :::image type="content" source="media/how-to-integrate-time-series-insights/view-environment.png" alt-text="Scherm afbeelding van de Azure Portal om de URL van Time Series Insights Explorer te selecteren op het tabblad Overzicht van uw Time Series Insights omgeving." lightbox="media/how-to-integrate-time-series-insights/view-environment.png":::

2. In de Explorer ziet u de apparaatdubbels in de Azure Digital Apparaatdubbels-instantie die aan de linkerkant wordt weer gegeven. Selecteer de *thermostat67* dubbele, kies de eigenschaps *temperatuur* en druk op **toevoegen**.

    :::image type="content" source="media/how-to-integrate-time-series-insights/add-data.png" alt-text="Scherm afbeelding van de Time Series Insights Explorer om thermostat67 te selecteren, selecteert u de eigenschaps temperatuur en klikt u op toevoegen." lightbox="media/how-to-integrate-time-series-insights/add-data.png":::

3. U ziet nu de oorspronkelijke temperatuur aflezingen uit uw Thermo staat, zoals hieronder wordt weer gegeven. 

    :::image type="content" source="media/how-to-integrate-time-series-insights/initial-data.png" alt-text="Scherm afbeelding van de TSI-Verkenner voor het weer geven van de oorspronkelijke temperatuur gegevens. Het is een regel met wille keurige waarden tussen 68 en 85" lightbox="media/how-to-integrate-time-series-insights/initial-data.png":::

Als u toestaat dat een simulatie veel langer wordt uitgevoerd, ziet uw visualisatie er ongeveer als volgt uit:

:::image type="content" source="media/how-to-integrate-time-series-insights/day-data.png" alt-text="Scherm afbeelding van de TSI-Verkenner waar de gegevens van de Tempe ratuur voor elke dubbele kleur in drie parallelle lijnen van verschillende kleuren worden genoteerd." lightbox="media/how-to-integrate-time-series-insights/day-data.png":::

## <a name="next-steps"></a>Volgende stappen

De digitale apparaatdubbels worden standaard opgeslagen als een platte hiërarchie in Time Series Insights, maar ze kunnen worden verrijkt met model gegevens en een hiërarchie met meerdere niveaus voor de organisatie. Lees voor meer informatie over dit proces: 

* [*Zelf studie: een model definiëren en Toep assen*](../time-series-insights/tutorial-set-up-environment.md#define-and-apply-a-model) 

U kunt aangepaste logica schrijven om deze informatie automatisch te verstrekken met behulp van het model en de grafiek gegevens die al zijn opgeslagen in azure Digital Apparaatdubbels. Zie de volgende verwijzingen voor meer informatie over het beheren, bijwerken en ophalen van gegevens uit de apparaatdubbels-grafiek:

* [*Instructies: een digitale twee richtings beheer beheren*](./how-to-manage-twin.md)
* [*Instructies: een query uitvoeren op de dubbele grafiek*](./how-to-query-graph.md)