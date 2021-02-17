---
title: De verbinding met Azure IoT Hub controleren en problemen oplossen
description: Meer informatie over het controleren en oplossen van veelvoorkomende fouten met connectiviteit van apparaten voor Azure IoT Hub
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 11/06/2020
ms.author: jlian
ms.custom:
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
- 'Role: Technical Support'
- fasttrack-edit
- iot
ms.openlocfilehash: 8bd20e3c7207c75e87a2132fca89906885de2676
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100579264"
---
# <a name="monitor-diagnose-and-troubleshoot-disconnects-with-azure-iot-hub"></a>De verbinding met Azure IoT Hub controleren, vaststellen en problemen oplossen

Connectiviteits problemen voor IoT-apparaten kunnen lastig zijn om problemen op te lossen omdat er veel mogelijke storings punten zijn. Toepassings logica, fysieke netwerken, protocollen, hardware, IoT Hub en andere Cloud Services kunnen problemen veroorzaken. De mogelijkheid om de bron van een probleem te detecteren en te lokaliseren is essentieel. Een IoT-oplossing op schaal kan echter duizenden apparaten hebben, waardoor het niet praktisch is om afzonderlijke apparaten hand matig te controleren. IoT Hub integreert met twee Azure-Services om u te helpen:

* **Azure monitor** Om u te helpen deze problemen op schaal te detecteren, op te sporen en op te lossen, gebruikt u de bewakings mogelijkheden IoT Hub via Azure Monitor. Dit omvat het instellen van waarschuwingen voor het activeren van meldingen en acties wanneer de verbinding wordt verbroken en logboeken worden geconfigureerd die u kunt gebruiken om de voor waarden te ontdekken waarmee de verbinding wordt verbroken.

* **Azure Event grid** Gebruik Azure Event Grid voor het maken van een abonnement op een apparaat en het verbreken van gebeurtenissen die worden gegenereerd door IoT Hub voor een kritieke infra structuur en verbinding per apparaat.

In beide gevallen zijn deze mogelijkheden beperkt tot wat IoT Hub kan observeren, dus we raden u ook aan de aanbevolen procedures voor uw apparaten en andere Azure-Services te volgen.

## <a name="event-grid-vs-azure-monitor"></a>Event Grid versus Azure Monitor

Event Grid biedt een bewakings oplossing met lage latentie per apparaat die u kunt gebruiken om de verbindingen van apparaten en infra structuur te controleren. Azure Monitor biedt een metrische, *verbonden apparaten* die u kunt gebruiken om het aantal apparaten dat is verbonden met uw IOT hub te bewaken en een waarschuwing te activeren wanneer dat nummer onder een statische drempel waarde daalt.

Houd rekening met het volgende wanneer u beslist of u Event Grid of Azure Monitor voor een bepaald scenario wilt gebruiken:

* Waarschuwings latentie: IoT Hub verbindings gebeurtenissen worden veel sneller via Event Grid afgeleverd. Dit maakt Event Grid een betere keuze voor scenario's waarin snelle meldingen gewenst zijn.

* Meldingen per apparaat: Event Grid biedt de mogelijkheid om verbindingen op te sporen en de verbinding met afzonderlijke apparaten te verbreken. Dit maakt Event Grid een betere keuze voor scenario's waarin u de verbindingen voor kritieke apparaten moet bewaken.

* Licht gewicht instellen: Azure Monitor metrische waarschuwingen bieden een lichte installatie-ervaring die niet nodig is om te integreren met andere services voor het leveren van meldingen via E-mail, SMS, spraak en andere meldingen.  Met Event Grid moet u integreren met andere Azure-Services om meldingen te kunnen leveren. Beide services kunnen worden geïntegreerd met andere services om complexere acties te activeren.

Als gevolg van de mogelijkheden voor de lage latentie per apparaat voor productie omgevingen, raden we u aan Event Grid te gebruiken om verbindingen te bewaken. De keuze is natuurlijk niet exclusief, u kunt zowel Azure Monitor metrische waarschuwingen als Event Grid gebruiken. Ongeacht uw keuze voor het bijhouden van de verbinding, gebruikt u waarschijnlijk Azure Monitor bron Logboeken om de oorzaak van het verbreken van een onverwacht apparaat te helpen oplossen. In de volgende secties vindt u meer informatie over elk van deze opties.

## <a name="event-grid-monitor-device-connect-and-disconnect-events"></a>Event Grid: verbinding met apparaat controleren en gebeurtenissen verbreken

Voor het bewaken van het apparaat verbinding maken en het verbreken van gebeurtenissen in de productie, kunt u het beste abonneren op de [ **DeviceConnected** -en **DeviceDisconnected** -gebeurtenissen](iot-hub-event-grid.md#event-types) in Event grid om waarschuwingen te activeren en de verbindings status van een apparaat te controleren Event Grid biedt veel lagere gebeurtenis latentie dan Azure Monitor, en u kunt bewaken per apparaat, in plaats van het totale aantal verbonden apparaten. Deze factoren maken Event Grid de voorkeurs methode voor het bewaken van kritieke apparaten en infra structuur.

Wanneer u Event Grid gebruikt om waarschuwingen te bewaken of te activeren op het verbreken van apparaten, moet u ervoor zorgen dat u de periodieke verbindingen die worden verbroken door SAS-token vernieuwing op apparaten die gebruikmaken van de Azure IoT Sdk's. Zie [MQTT-gedrag bij het verbreken van apparaten met Azure IOT sdk's](#mqtt-device-disconnect-behavior-with-azure-iot-sdks)voor meer informatie.

Bekijk de volgende onderwerpen voor meer informatie over het bewaken van verbindings gebeurtenissen voor apparaten met Event Grid:

* Zie [reageren op IOT hub gebeurtenissen met Event grid](iot-hub-event-grid.md)voor een overzicht van het gebruik van Event Grid met IOT hub. Let vooral op de [beperkingen voor de sectie met verbonden apparaten en gebeurtenissen voor het ontkoppelen van apparaten](iot-hub-event-grid.md#limitations-for-device-connected-and-device-disconnected-events) .

* Zie voor een zelf studie over het best Ellen van apparaat verbindings gebeurtenissen de [verbindings gebeurtenissen van het apparaat best Ellen van Azure IOT hub met behulp van Azure Cosmos DB](iot-hub-how-to-order-connection-state-events.md).

* Voor een zelf studie over het verzenden van e-mail meldingen raadpleegt [u e-mail meldingen over Azure IOT hub-gebeurtenissen verzenden met behulp van Event grid en Logic apps](../event-grid/publish-iot-hub-events-to-logic-apps.md) in de Event grid documentatie.

## <a name="azure-monitor-route-connection-events-to-logs"></a>Azure Monitor: verbindings gebeurtenissen naar Logboeken routeren

IoT hub verzendt voortdurend resource logboeken voor verschillende categorieën bewerkingen. Als u deze logboek gegevens wilt verzamelen, moet u echter een diagnostische instelling maken om deze te routeren naar een bestemming waar deze kan worden geanalyseerd of gearchiveerd. Een dergelijke bestemming is Azure Monitor logboeken via een Log Analytics-werk ruimte ([Zie prijzen](https://azure.microsoft.com/pricing/details/log-analytics/)), waar u de gegevens kunt analyseren met behulp van Kusto-query's.

De [categorie verbindingen met IOT hub bron logboeken](monitor-iot-hub-reference.md#connections) verzendt bewerkingen en fouten die moeten worden uitgevoerd met verbindingen met apparaten. De volgende scherm afbeelding ziet u een diagnostische instelling voor het routeren van deze logboeken naar een Log Analytics-werk ruimte:

:::image type="content" source="media/iot-hub-troubleshoot-connectivity/create-diagnostic-setting.png" alt-text="Aanbevolen instelling voor het verzenden van connectiviteits logboeken naar Log Analytics werk ruimte.":::

We raden u aan om zo snel mogelijk een diagnostische instelling te maken nadat u uw IoT-hub hebt gemaakt, omdat IoT Hub altijd resource logboeken meeneemt, worden ze niet verzameld door Azure Monitor totdat u ze naar een bestemming stuurt.

Zie [verzameling en route ring](monitor-iot-hub.md#collection-and-routing)voor meer informatie over route logboeken naar een bestemming. Zie de [zelf studie metrische gegevens en Logboeken gebruiken](tutorial-use-metrics-and-diags.md)voor gedetailleerde instructies voor het maken van een diagnostische instelling.

## <a name="azure-monitor-set-up-metric-alerts-for-device-disconnect-at-scale"></a>Azure Monitor: metrische waarschuwingen instellen voor het verbreken van het apparaat op schaal

U kunt waarschuwingen instellen op basis van de platform gegevens die door IoT Hub worden verzonden. Met metrische waarschuwingen kunt u personen informeren dat er een voor waarde is opgetreden en ook acties activeren die automatisch op die voor waarde kunnen reageren.

De metrische gegevens [*voor verbonden apparaten (preview)*](monitor-iot-hub-reference.md#device-metrics) vertelt u hoeveel apparaten er zijn verbonden met uw IOT hub. U kunt waarschuwingen maken om te activeren als deze metriek onder een drempel waarde valt:

:::image type="content" source="media/iot-hub-troubleshoot-connectivity/configure-alert-logic.png" alt-text="Waarschuwings logica instellingen voor de metrische gegevens van verbonden apparaten.":::

U kunt metrische waarschuwings regels gebruiken om te controleren op het verbreken van de verbreking van apparaten op schaal. Dat wil zeggen, wanneer een groot aantal apparaten onverwacht wordt verbroken. Wanneer een dergelijk voorval wordt gedetecteerd, kunt u de logboeken bekijken om het probleem op te lossen. Voor het bewaken van de verbindingen per apparaat en voor het verbreken van de verbinding met essentiële apparaten; u moet echter Event Grid gebruiken. Event Grid biedt ook een meer real-time ervaring dan de metrische gegevens van Azure.

Zie [waarschuwingen in Monitor IOT hub](monitor-iot-hub.md#alerts)voor meer informatie over waarschuwingen met IOT hub. Zie de [zelf studie metrische gegevens en Logboeken gebruiken](tutorial-use-metrics-and-diags.md)voor een overzicht van het maken van waarschuwingen in IOT hub. Zie [overzicht van waarschuwingen in Microsoft Azure](../azure-monitor/alerts/alerts-overview.md) in de Azure monitor documentatie voor een gedetailleerd overzicht van waarschuwingen.

## <a name="azure-monitor-use-logs-to-resolve-connectivity-errors"></a>Azure Monitor: Logboeken gebruiken om verbindings fouten op te lossen

Wanneer u het verbreken van het apparaat detecteert, of het nu werkt met Azure Monitor metrische waarschuwingen of met Event Grid, kunt u Logboeken gebruiken om de oorzaak van het probleem op te lossen. In deze sectie wordt beschreven hoe u veelvoorkomende problemen in Azure Monitor Logboeken kunt zoeken. In de onderstaande stappen wordt ervan uitgegaan dat u al een [Diagnostische instelling](#azure-monitor-route-connection-events-to-logs) hebt gemaakt voor het verzenden van Logboeken voor IOT hub verbindingen naar een log Analytics-werk ruimte.

Nadat u een diagnostische instelling hebt gemaakt om IoT Hub bron logboeken naar Azure Monitor-logboeken te routeren, volgt u deze stappen om de logboeken in Azure Portal weer te geven.

1. Ga in [Azure Portal](https://portal.azure.com)naar uw IOT-hub.

1. Selecteer **Logboeken** onder **bewaking** in het linkerdeel venster van uw IOT-hub.

1. Als u verbindings fouten logboeken voor IoT Hub wilt isoleren, voert u de volgende query in de query-editor in en selecteert u vervolgens **uitvoeren**:

    ```kusto
    AzureDiagnostics
    | where ( ResourceType == "IOTHUBS" and Category == "Connections" and Level == "Error")
    ```

1. Als er resultaten zijn, zoekt `OperationName` u naar, `ResultType` (fout code) en `ResultDescription` (fout bericht) om meer details te krijgen over de fout.

   ![Voor beeld van een fouten logboek](./media/iot-hub-troubleshoot-connectivity/diag-logs.png)

Wanneer u de fout hebt vastgesteld, volgt u de richt lijnen voor probleem oplossing voor hulp met de meest voorkomende fouten:

* [400027 ConnectionForcefullyClosedOnNewConnection](iot-hub-troubleshoot-error-400027-connectionforcefullyclosedonnewconnection.md)

* [404104 DeviceConnectionClosedRemotely](iot-hub-troubleshoot-error-404104-deviceconnectionclosedremotely.md)

* [401003 IoTHubUnauthorized](iot-hub-troubleshoot-error-401003-iothubunauthorized.md)

* [409002 LinkCreationConflict](iot-hub-troubleshoot-error-409002-linkcreationconflict.md)

* [500001 ServerError](iot-hub-troubleshoot-error-500xxx-internal-errors.md)

* [500008 GenericTimeout](iot-hub-troubleshoot-error-500xxx-internal-errors.md)

## <a name="mqtt-device-disconnect-behavior-with-azure-iot-sdks"></a>MQTT apparaat verbreekt de verbinding met Azure IoT Sdk's

De Sdk's van Azure IoT-apparaat worden van IoT Hub losgekoppeld en vervolgens opnieuw verbonden wanneer ze SAS-tokens van het MQTT-Protocol (en MQTT over websockets) vernieuwen. In Logboeken wordt dit weer gegeven als apparaat voor het verbreken van de verbinding en verbindings gebeurtenissen, soms vergezeld van fout gebeurtenissen.

De levens duur van het token is standaard 60 minuten voor alle Sdk's; het kan echter door ontwikkel aars in een aantal Sdk's worden gewijzigd. De volgende tabel geeft een overzicht van de levens duur van tokens, het vernieuwen van tokens en het gedrag van het vernieuwen van tokens voor elk van de Sdk's:

| SDK | Levens duur van token | Token vernieuwing | Gedrag bij vernieuwing |
|-----|----------|---------------------|---------|
| .NET | 60 minuten, configureerbaar | 85% levens duur, configureerbaar | SDK verbindt en verbreekt de verbinding met de levens duur van tokens plus een respijt periode van 10 minuten. Informatieve gebeurtenissen en fouten die in Logboeken zijn gegenereerd. |
| Java | 60 minuten, configureerbaar | 85% levens duur, kan niet worden geconfigureerd | SDK verbindt en verbreekt de verbinding met de levens duur van tokens plus een respijt periode van 10 minuten. Informatieve gebeurtenissen en fouten die in Logboeken zijn gegenereerd. |
| Node.js | 60 minuten, configureerbaar | Configureer bare | SDK verbindt en verbreekt de verbinding met het vernieuwen van tokens. In Logboeken worden alleen informatieve gebeurtenissen gegenereerd. |
| Python | 60 minuten, niet configureerbaar | -- | SDK verbindt en verbreekt de verbinding met de levens duur van tokens. |

In de volgende scherm afbeeldingen ziet u het gedrag voor het vernieuwen van tokens in Azure Monitor logboeken voor verschillende Sdk's. De levens duur van het token en de vernieuwings drempel zijn gewijzigd ten opzichte van de standaard waarden zoals vermeld.

* .NET-apparaat-SDK met een 1200 seconde (20 minuten) levens duur van tokens en verlenging is ingesteld op 90% van de levens duur. de verbinding wordt om de 30 minuten verbroken:

    :::image type="content" source="media/iot-hub-troubleshoot-connectivity/net-mqtt.png" alt-text="Fout gedrag voor het vernieuwen van tokens via MQTT in Azure Monitor-logboeken met .NET SDK.":::

* Java SDK met een 300 seconde (5 minuten) levens duur van tokens en standaard 85% van de levens duur vernieuwen. De verbinding wordt om de 15 minuten verbroken:

    :::image type="content" source="media/iot-hub-troubleshoot-connectivity/java-mqtt.png" alt-text="Fout gedrag voor het vernieuwen van tokens via MQTT in Azure Monitor-logboeken met Java SDK.":::

* Knoop punt-SDK met een 300 seconde (5 minuten) de levens duur van het token en het vernieuwen van het token is ingesteld op 3 minuten. De verbinding wordt verbroken bij het vernieuwen van het token. Er zijn ook geen fouten, alleen informatieve verbindings-en verbrekings gebeurtenissen worden verzonden:

    :::image type="content" source="media/iot-hub-troubleshoot-connectivity/node-mqtt.png" alt-text="Fout gedrag voor het vernieuwen van tokens via MQTT in Azure Monitor-logboeken met knooppunt-SDK.":::

De volgende query is gebruikt om de resultaten te verzamelen. De query extraheert de SDK-naam en-versie van de eigenschappen verzameling; Zie [SDK-versie in IOT hub-logboeken](monitor-iot-hub.md#sdk-version-in-iot-hub-logs)voor meer informatie.

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
| where Category == "Connections"
| extend parsed_json = parse_json(properties_s)
| extend SDKVersion = tostring(parsed_json.sdkVersion) , DeviceId = tostring(parsed_json.deviceId) , Protocol =  tostring(parsed_json.protocol)
| distinct TimeGenerated, OperationName, Level, ResultType, ResultDescription, DeviceId, Protocol, SDKVersion

```

Als webontwikkelaar of-operator van IoT-oplossingen moet u op de hoogte zijn van dit gedrag om het verbinden/verbreken van gebeurtenissen en gerelateerde fouten in Logboeken te interpreteren. Als u de levens duur van het token of het vernieuwings gedrag voor apparaten wilt wijzigen, controleert u of het apparaat een dubbele instelling voor een apparaat of een apparaat-methode implementeert waardoor dit mogelijk is.

Als u de verbindingen met Event hub bewaken, moet u ervoor zorgen dat u de periodieke verbreken van de verbinding door de vernieuwing van SAS-tokens uitfiltert. bijvoorbeeld: door geen acties te activeren op basis van het verbreken van de verbinding, zolang de gebeurtenis Disconnect wordt gevolgd door een verbindings gebeurtenis binnen een bepaalde periode.

> [!NOTE]
> IoT Hub ondersteunt slechts één actieve MQTT-verbinding per apparaat. Een nieuwe MQTT-verbinding namens dezelfde apparaat-ID leidt ertoe IoT Hub de bestaande verbinding te verwijderen.
>
> 400027 ConnectionForcefullyClosedOnNewConnection wordt aangemeld bij IoT Hub-logboeken

## <a name="i-tried-the-steps-but-they-didnt-work"></a>Ik heb de stappen uitgevoerd, maar deze zijn niet uitgevoerd

Als de vorige stappen niet zijn uitgevoerd, probeert u het volgende:

* Als u toegang hebt tot de problematische apparaten, fysiek of extern (zoals SSH), volgt u de [probleem Oplosser](https://github.com/Azure/azure-iot-sdk-node/wiki/Troubleshooting-Guide-Devices) voor het oplossen van problemen door te gaan met het oplossen van het probleem.

* Controleer of uw apparaten zijn **ingeschakeld** in de Azure Portal > uw IOT-hub > IOT-apparaten.

* Als uw apparaat gebruikmaakt van het MQTT-protocol, controleert u of poort 8883 is geopend. Zie [verbinding maken met IOT hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)voor meer informatie.

* Krijg hulp van [micro soft Q&een vraag pagina voor azure IOT hub](/answers/topics/azure-iot-hub.html), [stack overflow](https://stackoverflow.com/questions/tagged/azure-iot-hub)of [ondersteuning voor Azure](https://azure.microsoft.com/support/options/).

Als u de documentatie voor iedereen wilt verbeteren, kunt u in de sectie feedback hieronder een opmerking plaatsen als deze hand leiding u niet heeft geholpen.

## <a name="next-steps"></a>Volgende stappen

* Zie [tijdelijke fout afhandeling](/azure/architecture/best-practices/transient-faults)voor meer informatie over het oplossen van tijdelijke problemen.

* Zie [connectiviteit en betrouw bare berichten beheren met azure IOT hub apparaat-sdk's](iot-hub-reliability-features-in-sdks.md#connection-and-retry)voor meer informatie over de Azure IOT SDK en het beheren van nieuwe pogingen.