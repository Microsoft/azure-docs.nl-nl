---
title: De beschikbaarheid en reactiesnelheid van websites bewaken - Azure Monitor
description: Pingtests instellen in Application Insights. Ontvang een waarschuwing wanneer een website niet meer beschikbaar is of traag reageert.
ms.topic: conceptual
ms.date: 04/15/2021
ms.reviewer: sdash
ms.openlocfilehash: 60698862e26175425221940a4b69867cb414fe86
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/18/2021
ms.locfileid: "107598870"
---
# <a name="monitor-the-availability-of-any-website"></a>Beschikbaarheid van websites bewaken

De naam 'URL ping test' is een beetje een verkeerde naam. Voor de duidelijkheid: deze tests maken geen gebruik van ICMP (Internet Control Message Protocol) om de beschikbaarheid van uw site te controleren. In plaats daarvan gebruiken ze geavanceerdere HTTP-aanvraagfunctionaliteit om te valideren of een eindpunt reageert. Ze meten ook de prestaties die aan dat antwoord zijn gekoppeld en bieden de mogelijkheid om aangepaste succescriteria in te stellen in combinatie met geavanceerdere functies, zoals het parseren van afhankelijke aanvragen, en het toestaan van nieuwe aanvragen.

Als u een beschikbaarheidstest wilt maken, moet u een bestaande Application Insight-resource gebruiken of een [Application Insights maken.](create-new-resource.md)

Als u uw eerste beschikbaarheidsaanvraag wilt maken, opent u het deelvenster Beschikbaarheid en selecteert  **u Test maken.**

:::image type="content" source="./media/monitor-web-app-availability/availability-create-test-001.png" alt-text="Schermopname van het maken van een test.":::

## <a name="create-a-test"></a>Een test maken

|Instelling| Uitleg
|----|----|----|
|**URL** |  De URL kan iedere webpagina zijn die u wilt testen, maar deze moet zichtbaar zijn vanaf het openbare internet. De URL kan een queryreeks bevatten. Zo kunt u bijvoorbeeld oefenen met uw database. Als de URL naar een omleiding is opgelost, kunnen we deze tot maximaal 10 omleidingen opvolgen.|
|**Afhankelijke aanvragen parseren**| Test aanvragen voor afbeeldingen, scripts, stijlbestanden en andere bestanden die deel uitmaken van de webpagina die wordt getest. De opgenomen reactietijd is inclusief de tijd die nodig is om deze bestanden op te halen. De test mislukt als een van deze resources niet binnen de time-out voor de hele test kan worden gedownload. Als de optie niet is ingeschakeld, vraagt de test alleen het bestand op van de URL die u hebt opgegeven. Het inschakelen van deze optie resulteert in een strengere controle. De test kan mislukken in gevallen, die mogelijk niet zichtbaar zijn wanneer u handmatig op de site bladert.
|**Nieuwe proberen inschakelen**|wanneer de test mislukt, wordt deze na een kort interval opnieuw uitgevoerd. Fouten worden pas gerapporteerd als er drie opeenvolgende pogingen mislukken. Daaropvolgende tests worden vervolgens met de gebruikelijke testfrequentie uitgevoerd. Volgende pogingen worden tijdelijk uitgesteld tot er weer een test slaagt. Deze regel wordt onafhankelijk toegepast op elke testlocatie. **We raden deze optie aan.** Gemiddeld verdwijnt ongeveer 80% van de fouten na het opnieuw proberen.|
|**Frequentie testen**| Hiermee stelt u in hoe vaak de test wordt uitgevoerd vanaf elke testlocatie. Met een standaardfrequentie van vijf minuten en vijf testlocaties wordt uw site gemiddeld per minuut getest.|
|**Testlocaties**| Zijn de plaatsen van waar onze servers webaanvragen naar uw URL verzenden. **Ons minimum aantal aanbevolen testlocaties is vijf** om ervoor te zorgen dat u problemen op uw website kunt onderscheiden van netwerkproblemen. U kunt maximaal 16 locaties selecteren.

Als uw URL niet zichtbaar is vanaf het openbare internet, kunt u ervoor kiezen om uw firewall selectief te openen zodat alleen **de testtransacties via worden toegestaan.** Raadpleeg de IP-adreshandleiding voor meer informatie over de firewall-uitzonderingen voor onze [beschikbaarheidstestagents.](./ip-addresses.md#availability-tests)

> [!NOTE]
> We raden u ten zeerste aan om te testen vanaf meerdere locaties **met minimaal vijf locaties.** Dit is om valse alarmen te voorkomen die kunnen ontstaan door tijdelijke problemen met een specifieke locatie. Bovendien hebben we geconstateerd dat de optimale configuratie is om het aantal testlocaties gelijk te laten zijn aan de drempelwaarde voor de waarschuwingslocatie **+ 2**.

## <a name="success-criteria"></a>Succescriteria

|Instelling| Uitleg
|----|----|----|
| **Time-out testen** |Verklein deze waarde om te worden gewaarschuwd bij trage reacties. De test wordt als mislukt beschouwd als er binnen deze periode geen reactie van uw site is ontvangen. Als u **Parse onafhankelijke aanvragen** hebt geselecteerd, moeten alle afbeeldingen, stijlbestanden, scripts en andere afhankelijke resources binnen deze periode worden ontvangen.|
| **HTTP-antwoord** | De geretourneerde statuscode die als geslaagd wordt geteld. 200 is de code die aangeeft dat er een normale webpagina is geretourneerd.|
| **Inhoudsmatch** | Een tekenreeks, zoals 'Welkom!' Er wordt getest of er in elke respons een exacte (hoofdlettergevoelige) overeenkomst wordt gevonden. Het moet een eenvoudige tekenreeks zijn, zonder jokertekens. Als uw pagina-inhoud wordt gewijzigd, moet u deze tekenreeks mogelijk ook bijwerken. **Alleen Engelse tekens worden ondersteund met inhoudsmatch** |

## <a name="alerts"></a>Waarschuwingen

|Instelling| Uitleg
|----|----|----|
|**Bijna realtime (preview)** | We raden u aan near-realtime waarschuwingen te gebruiken. Het configureren van dit type waarschuwing wordt uitgevoerd nadat uw beschikbaarheidstest is gemaakt.  |
|**Drempelwaarde voor waarschuwingslocatie**|We raden minimaal 3/5 locaties aan. De optimale relatie tussen de drempelwaarde voor de waarschuwingslocatie en het aantal testlocaties **is** het drempelwaardenummer van de waarschuwingslocatie voor  =  **testlocaties - 2, met minimaal vijf testlocaties.**|

## <a name="location-population-tags"></a>Locatiepopulatietags

De volgende populatietags kunnen worden gebruikt voor het kenmerk geolocatie bij het implementeren van een pingtest voor beschikbaarheids-URL's met behulp van Azure Resource Manager.

### <a name="azure-gov"></a>Azure Gov

| Weergavenaam   | Naam van populatie     |
|----------------|---------------------|
| USGov Virginia | usgov-va-azr        |
| USGov Arizona  | usgov-phx-azr       |
| USGov Texas    | usgov-tx-azr        |
| USDoD - oost     | usgov-ddeast-azr    |
| USDoD Central  | usgov-ddcentral-azr |

#### <a name="azure"></a>Azure

| Weergavenaam                           | Naam van populatie   |
|----------------------------------------|-------------------|
| Australië - oost                         | emea-au-syd-edge  |
| Brazilië - zuid                           | latam-br-gru-edge |
| Central US                             | us-fl-mia-edge    |
| Azië - oost                              | apac-hk-hkn-azr   |
| VS - oost                                | us-va-uur-azr     |
| Frankrijk - zuid (voorheen Frankrijk - centraal) | emea-ch-zrh-edge  |
| Frankrijk - centraal                         | emea-fr-pra-edge  |
| Japan - oost                             | apac-jp-kaw-edge  |
| Europa - noord                           | emea-gb-db3-azr   |
| VS - noord-centraal                       | us-il-ch1-azr     |
| VS - zuid-centraal                       | us-tx-sn1-azr     |
| Azië - zuidoost                         | apac-sg-sin-azr   |
| Verenigd Koninkrijk West                                | emea-se-sto-edge  |
| Europa -west                            | emea-nl-ams-azr   |
| VS - west                                | us-ca-sjc-azr     |
| Verenigd Koninkrijk Zuid                               | emea-ru-msa-edge  |

## <a name="see-your-availability-test-results"></a>De resultaten van de beschikbaarheidstest bekijken

Resultaten van beschikbaarheidstests kunnen worden gevisualiseerd met zowel lijn- als spreidingsdiagramweergaven.

Klik na een paar minuten op **Vernieuwen om** de testresultaten te bekijken.

:::image type="content" source="./media/monitor-web-app-availability/availability-refresh-002.png" alt-text="Schermopname van de pagina Beschikbaarheid met de knop Vernieuwen gemarkeerd.":::

In de spreidingsdiagramweergave ziet u voorbeelden van de testresultaten met diagnostische teststapdetails. De testengine slaat diagnostische gegevens op voor tests met fouten. Bij geslaagde tests wordt diagnostische informatie voor een subset van de uitvoeringen opgeslagen. Beweeg de muisaanwijzer over een van de groene/rode punten om de test, testnaam en locatie te zien.

:::image type="content" source="./media/monitor-web-app-availability/availability-scatter-plot-003.png" alt-text="Lijnweergave." border="false":::

Selecteer een bepaalde test of locatie, of verklein de periode om meer resultaten te zien uit de periode die voor u van belang is. Gebruik Search Explorer om resultaten van alle uitvoeringen weer te geven, of gebruik Analytics-query's om aangepaste rapporten uit te voeren op deze gegevens.

## <a name="inspect-and-edit-tests"></a> Tests bekijken en bewerken

Als u een test wilt bewerken, tijdelijk wilt uitschakelen of verwijderen, klikt u op het beletselletselvenster naast een testnaam. Het kan tot 20 minuten duren voordat configuratiewijzigingen zijn doorgegeven aan alle testagents nadat een wijziging is aangebracht.

:::image type="content" source="./media/monitor-web-app-availability/edit.png" alt-text="Testdetails weergeven. Een webtest bewerken en uitschakelen." border="false":::

Het is verstandig beschikbaarheidstests of de regels voor waarschuwingen die eraan zijn gekoppeld uit te schakelen wanneer u onderhoud uitvoert op uw service.

## <a name="if-you-see-failures"></a>Als u mislukte tests ziet

Selecteer een rode stip.

:::image type="content" source="./media/monitor-web-app-availability/end-to-end.png" alt-text="Schermopname van het tabblad End-to-end-transactiedetails." border="false":::

In een resultaat van een beschikbaarheidstest ziet u de transactiedetails voor alle onderdelen. Hier kunt u het volgende doen:

* Bekijk het probleemoplossingsrapport om te bepalen waarom de test is mislukt, maar uw toepassing nog steeds beschikbaar is.
* De reactie inspecteren die is ontvangen van uw server.
* Diagnose van fouten met gecorreleerde telemetrie aan serverzijde die is verzameld tijdens het verwerken van de mislukte beschikbaarheidstest.
* Registreer een probleem of werkitem in Git of Azure Boards om het probleem bij te houden. De bug bevat een koppeling naar deze gebeurtenis.
* Het webtestresultaat openen in Visual Studio.

Ga naar de documentatie voor transactiediagnose voor meer informatie over de end-to-end-ervaring met [transactiediagnose.](./transaction-diagnostics.md)

Klik op de uitzonderingsrij om de details weer te geven van de uitzondering aan de serverzijde waardoor de synthetische beschikbaarheidstest is mislukt. U kunt ook de momentopname voor [foutopsporing krijgen](./snapshot-debugger.md) voor uitgebreidere diagnostische gegevens op codeniveau.

:::image type="content" source="./media/monitor-web-app-availability/open-instance-4.png" alt-text="Diagnostische gegevens aan de serverzijde.":::

Naast de onbewerkte resultaten kunt u ook twee belangrijke metrische gegevens over beschikbaarheid weergeven in [Metrics Explorer:](../essentials/metrics-getting-started.md)

1. Beschikbaarheid: percentage van de tests die zijn geslaagd, bekeken over alle testuitvoeringen.
2. Testduur: gemiddelde testduur van alle testuitvoeringen.

## <a name="automation"></a>Automation

* Gebruik [PowerShell-scripts om automatisch een beschikbaarheidstest in te stellen](./powershell.md#add-an-availability-test).
* Stel een [webhook](../alerts/alerts-webhooks.md) in die wordt aangeroepen wanneer er een waarschuwing wordt gegenereerd.


## <a name="next-steps"></a>Volgende stappen

* [Beschikbaarheidswaarschuwingen](availability-alerts.md)
* [Webtests met meerdere stappen](availability-multistep.md)
* [Problemen oplossen](troubleshoot-availability.md)
