---
title: Problemen diagnosticeren, problemen oplossen en oplossen-Azure Time Series Insights
description: In dit artikel wordt beschreven hoe u veelvoorkomende problemen in uw Azure Time Series Insights omgeving kunt vaststellen en oplossen.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.reviewer: v-mamcge
ms.workload: big-data
ms.topic: troubleshooting
ms.date: 09/29/2020
ms.custom: seodec18
ms.openlocfilehash: 15f2ff5aaa1d731c13125d0a3ab4ac32acb9276c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "95023272"
---
# <a name="diagnose-and-solve-issues-in-your-azure-time-series-insights-gen1-environment"></a>Problemen vaststellen en oplossen in uw Azure Time Series Insights gen1-omgeving

> [!CAUTION]
> Dit is een Gen1-artikel.

In dit artikel worden de problemen beschreven die kunnen optreden in uw Azure Time Series Insights omgeving. Het artikel bevat mogelijke oorzaken en oplossingen voor oplossing.

## <a name="video"></a>Video

### <a name="learn-about-common-azure-time-series-insights-challenges-and-mitigationsbr"></a>Meer informatie over algemene Azure Time Series Insights uitdagingen en oplossingen</br>

> [!VIDEO https://www.youtube.com/embed/7U0SwxAVSKw]

## <a name="problem-no-data-is-shown"></a>Probleem: er worden geen gegevens weer gegeven

Als er geen gegevens worden weer gegeven in de [Azure time series Insights Explorer](https://insights.timeseries.azure.com), moet u rekening houden met deze veelvoorkomende oorzaken.

### <a name="cause-a-event-source-data-isnt-in-json-format"></a>Oorzaak van een: gebeurtenis bron gegevens bevindt zich niet in JSON-indeling

Azure Time Series Insights ondersteunt alleen JSON-gegevens. Zie [ondersteunde JSON-shapes](./concepts-json-flattening-escaping-rules.md)voor json-voor beelden.

### <a name="cause-b-the-event-source-key-is-missing-a-required-permission"></a>Oorzaak B: er ontbreekt een vereiste machtiging voor de bron sleutel van de gebeurtenis

* Voor een IoT-hub in azure IoT Hub moet u de sleutel opgeven die service Connect-machtigingen heeft. Selecteer het **iothubowner** -of **service** beleid. Beide hebben service Connect-machtigingen.

   [![Verbindings machtigingen voor IoT Hub-service](media/diagnose-and-solve-problems/iothub-serviceconnect-permissions.png)](media/diagnose-and-solve-problems/iothub-serviceconnect-permissions.png#lightbox)

* Voor een Event Hub in azure Event Hubs moet u de sleutel opgeven met de machtiging Luis teren. Het beleid voor **lezen** en **beheren** werkt allebei, omdat beide over Luister machtigingen beschikken.

   [![Event hub-Luister machtigingen](media/diagnose-and-solve-problems/eventhub-listen-permissions.png)](media/diagnose-and-solve-problems/eventhub-listen-permissions.png#lightbox)

### <a name="cause-c-the-provided-consumer-group-isnt-exclusive-to-azure-time-series-insights"></a>Oorzaak C: de door de gebruiker aangelegde groep is niet exclusief Azure Time Series Insights

Wanneer u een IoT-hub registreert of een Event Hub, is het belang rijk om de Consumer groep in te stellen die u wilt gebruiken voor het lezen van de gegevens. Deze consumenten groep *kan niet worden gedeeld*. Als de Consumer groep wordt gedeeld, wordt de onderliggende IoT-hub of Event Hub automatisch en wille keurig losgekoppeld van een van de lezers. Geef een unieke consumenten groep op voor de Azure Time Series Insights om te lezen.

### <a name="cause-d-the-environment-has-just-been-provisioned"></a>Oorzaak D: de omgeving is zojuist ingericht

De gegevens worden binnen een paar minuten nadat de omgeving en de gegevens voor het eerst zijn gemaakt in uw Azure Time Series Insights Explorer weer gegeven.

## <a name="problem-some-data-is-shown-but-data-is-missing"></a>Probleem: sommige gegevens worden weer gegeven, maar er ontbreken gegevens

Houd rekening met de volgende mogelijke problemen wanneer gegevens slechts gedeeltelijk worden weer gegeven en de gegevens lijken te zijn.

### <a name="cause-a-your-environment-is-being-throttled"></a>Oorzaak van een: uw omgeving wordt beperkt

[Beperking](time-series-insights-environment-mitigate-latency.md) is een veelvoorkomend probleem bij het inrichten van omgevingen nadat u een gebeurtenis bron hebt gemaakt die gegevens bevat. Azure IoT Hub-en Azure Events-gegevens opslag zijn Maxi maal zeven dagen. Azure Time Series Insights begint altijd met de oudste gebeurtenis in de bron van de gebeurtenis (First-in, first-out of *FIFO*).

Als u bijvoorbeeld 5.000.000 gebeurtenissen in een gebeurtenis bron hebt wanneer u verbinding maakt met een S1-Azure Time Series Insights omgeving met één eenheid, wordt door Azure Time Series Insights ongeveer 1.000.000 gebeurtenissen per dag gelezen. Het lijkt erop dat Azure Time Series Insights vijf latenties ondervindt. Maar wat er gebeurt, is dat de omgeving wordt beperkt.

Als u oude gebeurtenissen in uw gebeurtenis bron hebt, kunt u op een van de volgende twee manieren beperken:

* Wijzig de Bewaar limieten van uw gebeurtenis bron om oude gebeurtenissen die u niet wilt weer geven in Azure Time Series Insights te verwijderen.
* Richt een grotere omgevings grootte (aantal eenheden) in om de door Voer van oude gebeurtenissen te verg Roten. Als u in het voor gaande voor beeld dezelfde S1-omgeving tot vijf eenheden voor één dag verhoogt, moet de omgeving binnen een dag worden opgevangen. Als uw gebeurtenis productie van de vaste toestand 1.000.000 of minder gebeurtenissen per dag is, kunt u de capaciteit van de gebeurtenis beperken tot één eenheid nadat Azure Time Series Insights is opgevangen.

De afgedwongen beperkings limiet is gebaseerd op het SKU-type en de capaciteit van de omgeving. Alle gebeurtenis bronnen in de omgeving delen deze capaciteit. Als de gebeurtenis bron voor uw IoT-hub of Event Hub gegevens na de afgedwongen limieten pusht, treden er vertragingen en vertraging op.

In de volgende afbeelding ziet u een Azure Time Series Insights omgeving met een SKU van S1 en een capaciteit van 3. Het kan 3.000.000 gebeurtenissen per dag inkomen.

[![Omgevings capaciteit](media/diagnose-and-solve-problems/environment-sku-current-capacity.png)](media/diagnose-and-solve-problems/environment-sku-current-capacity.png#lightbox)

Stel dat er een omgeving is waarin berichten van een Event Hub worden opgenomen. Het heeft een dagelijks ingangs tempo van ongeveer 67.000 berichten. Dit aantal wordt elke minuut op ongeveer 46 berichten omgezet.

* Als elke Event Hub-bericht wordt afgevlakt tot één Azure Time Series Insights gebeurtenis, treedt er geen beperking op.
* Als elke Event Hub-bericht wordt afgevlakt tot 100 Azure Time Series Insights gebeurtenissen, moeten 4.600-gebeurtenissen elke minuut worden opgenomen.

Een S1-SKU-omgeving met een capaciteit van 3 kan per minuut slechts 2.100 gebeurtenissen binnenkomen (1.000.000 gebeurtenissen per dag = 700 gebeurtenissen per minuut bij drie eenheden = 2.100 gebeurtenissen per minuut).

Zie [ondersteunde JSON-shapes](./concepts-json-flattening-escaping-rules.md)voor meer informatie over hoe afvlakking logisch werkt.

#### <a name="recommended-resolutions-for-excessive-throttling"></a>Aanbevolen oplossingen voor buitensporige beperking

Als u de vertraging wilt herstellen, verg root u de SKU-capaciteit van uw omgeving. Lees [uw Azure time series Insights-omgeving schalen](time-series-insights-how-to-scale-your-environment.md)voor meer informatie.

### <a name="cause-b-initial-ingestion-of-historical-data-slows-ingress"></a>Oorzaak B: de eerste opname van historische gegevens vertraagt de ingang

Als u een bestaande gebeurtenis bron verbindt, is het waarschijnlijk dat uw IoT-hub of Event Hub al gegevens bevat. De omgeving begint met het ophalen van gegevens uit het begin van de Bewaar periode van de gebeurtenis bron. Deze standaard verwerking kan niet worden overschreven. U kunt bandbreedte beperking inschakelen. Het kan even duren voordat de beperking is opgetreden tijdens het opnemen van historische gegevens.

#### <a name="recommended-resolutions-for-large-initial-ingestion"></a>Aanbevolen oplossingen voor grote eerste opname

De vertraging oplossen:

1. Verhoog de capaciteit van de SKU tot de Maxi maal toegestane waarde (10, in dit geval). Nadat u de capaciteit hebt verhoogd, begint het ingangs proces veel sneller. Er worden kosten in rekening gebracht voor de toegenomen capaciteit. Als u wilt visualiseren hoe snel u opvangt, kunt u de beschikbaarheids grafiek weer geven in de [Azure time series Insights Explorer](https://insights.timeseries.azure.com).

2. Wanneer de vertraging is opgevangen, verlaagt u de SKU-capaciteit tot uw normale ingangs rente.

## <a name="problem-data-was-showing-previously-but-is-no-longer-showing"></a>Probleem: gegevens worden eerder weer gegeven, maar worden niet meer weer gegeven

Als Azure Time Series Insights geen gegevens meer opneemt, maar gebeurtenissen nog steeds streamen naar IOT hub of event hub, kunt u deze mogelijke oorzaak overwegen.

### <a name="cause-a-your-hub-access-key-was-regenerated-and-your-environment-needs-to-be-updated"></a>Oorzaak van een: de toegangs sleutel voor de hub is opnieuw gegenereerd en uw omgeving moet worden bijgewerkt

Dit probleem treedt op wanneer de sleutel die u hebt gemaakt bij het maken van uw gebeurtenis bron niet langer geldig is. U ziet telemetrie in uw hub, maar er zijn geen inkomende berichten ontvangen in Azure Time Series Insights. Als u niet zeker weet of de sleutel opnieuw is gegenereerd, kunt u het activiteiten logboek van uw Event Hub doorzoeken op het maken of bijwerken van naam ruimte autorisatie regels. Voor een IoT-hub zoekt u naar ' IotHub-resource maken of bijwerken '.

Als u uw Azure Time Series Insights-omgeving met de nieuwe sleutel wilt bijwerken, opent u de resource van de hub in de Azure Portal en kopieert u de nieuwe sleutel. Ga naar uw Azure Time Series Insights-resource en selecteer **gebeurtenis bronnen**:

   [![Gebeurtenis bronnen selecteren](media/diagnose-and-solve-problems/update-hub-key-step-1.png)](media/diagnose-and-solve-problems/update-hub-key-step-1.png#lightbox)

Selecteer de bron van de gebeurtenis of bronnen waaruit de opname is gestopt, plak de nieuwe sleutel en selecteer vervolgens **Opslaan**:

   [![Plakken in de nieuwe sleutel](media/diagnose-and-solve-problems/update-hub-key-step-2.png)](media/diagnose-and-solve-problems/update-hub-key-step-2.png#lightbox)

## <a name="problem-the-event-sources-timestamp-property-name-setting-doesnt-work"></a>Probleem: het instellen van de eigenschaps naam van de gebeurtenis bron is niet mogelijk

Zorg ervoor dat de waarde van de tijds tempel eigenschap die afkomstig is van uw gebeurtenis bron als JSON-teken reeks, de notatie _jjjj-mm-ddTuu: mm: SS heeft. FFFFFFFK_. Hier volgt een voor beeld: **2008-04-12T12:53Z**.

Houd er rekening mee dat de naam van de tijds tempel eigenschap hoofdletter gevoelig is.

De eenvoudigste manier om ervoor te zorgen dat de naam van de tijds tempel eigenschap wordt vastgelegd en correct werkt, is door de Azure Time Series Insights Explorer te gebruiken. Selecteer in de Azure Time Series Insights Explorer, in de grafiek, een periode nadat u de naam van de tijds tempel eigenschap hebt ingevoerd. Klik met de rechter muisknop op de selectie en selecteer vervolgens **gebeurtenissen verkennen**.

De eerste kolomkop moet de naam van de eigenschap time stamp zijn. Naast het **tijds tempel** van het woord **($TS)** wordt weer gegeven.

De volgende waarden worden niet weer gegeven:

* *(ABC)*: geeft aan dat Azure time series Insights de gegevens waarden leest als teken reeksen.
* *Kalender pictogram*: geeft aan dat Azure time series Insights de gegevens waarden als datum/tijd-waarden leest.
* *#*: Hiermee wordt aangegeven dat Azure Time Series Insights de gegevens waarden leest als gehele getallen.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [het oplossen van latentie in azure time series Insights](time-series-insights-environment-mitigate-latency.md).

* Meer informatie [over het schalen van uw Azure time series Insights-omgeving](time-series-insights-how-to-scale-your-environment.md).