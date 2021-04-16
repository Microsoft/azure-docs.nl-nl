---
title: De beschikbaarheid en reactiesnelheid van websites bewaken - Azure Monitor
description: Pingtests instellen in Application Insights. Ontvang een waarschuwing wanneer een website niet meer beschikbaar is of traag reageert.
ms.topic: conceptual
ms.date: 04/15/2021
ms.reviewer: sdash
ms.openlocfilehash: ecfd4ffee3582ff37411e59c75d8be8fca5e945f
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107516597"
---
# <a name="monitor-the-availability-of-any-website"></a>Beschikbaarheid van websites bewaken

De naam 'URL ping test' is een beetje een verkeerde naam. Voor de duidelijkheid: deze tests maken geen gebruik van ICMP (Internet Control Message Protocol) om de beschikbaarheid van uw site te controleren. In plaats daarvan gebruiken ze geavanceerdere HTTP-aanvraagfunctionaliteit om te valideren of een eindpunt reageert. Ze meten ook de prestaties die aan dat antwoord zijn gekoppeld en bieden de mogelijkheid om aangepaste succescriteria in te stellen in combinatie met geavanceerdere functies, zoals het parseren van afhankelijke aanvragen, en het toestaan van nieuwe aanvragen.

Er zijn twee soorten URL-pingtests die u kunt maken: eenvoudige en standaard pingtests.

> [!NOTE]
> Basic- en Standard-pingtests zijn momenteel beschikbaar als openbare preview. Deze preview-versies worden aangeboden zonder service level agreement. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.

Basic versus Standard:

- Basic is beperkt tot vijf locaties per test.
- Standaardtests kunnen aangepaste headers of aanvraagteksten hebben.
- Standaardtests kunnen elke HTTP-aanvraagmethode gebruiken, terwijl basic alleen kan `GET` gebruiken.
- De controle van de levensduur van het SSL-certificaat waarschuwt u voor een bepaalde periode voordat uw certificaat verloopt.
- Standaardtests zijn een betaalde functie.

> [!NOTE]
> Er worden momenteel geen extra kosten in rekening gebracht voor de preview-functie Standard Ping-tests. Prijzen voor functies die in preview zijn, worden in de toekomst aangekondigd en er wordt een kennisgeving vóór de start van de facturering weergegeven. Als u na de kennisgevingsperiode standaard pingtests wilt blijven gebruiken, wordt u gefactureerd tegen het toepasselijke tarief.

## <a name="create-a-url-ping-test"></a>Een URL-pingtest aanmaken

Als u een beschikbaarheidstest wilt maken, moet u een bestaande Application Insight-resource gebruiken of een [Application Insights maken.](create-new-resource.md)

Als u uw eerste beschikbaarheidsaanvraag wilt maken, opent u het deelvenster Beschikbaarheid en selecteert u Test maken om & test-SKU te kiezen.

:::image type="content" source="./media/monitor-web-app-availability/create-basic-test.png" alt-text="Schermopname van het maken van een eenvoudige URL-pingtest in De Azure-portal":::

|Instelling | Uitleg |
|--------|-------------|
|**URL** |  De URL kan iedere webpagina zijn die u wilt testen, maar deze moet zichtbaar zijn vanaf het openbare internet. De URL kan een queryreeks bevatten. Zo kunt u bijvoorbeeld oefenen met uw database. Als de URL naar een omleiding is opgelost, kunnen we deze tot maximaal 10 omleidingen opvolgen.|
|**Afhankelijke aanvragen parseren**| Test aanvragen voor afbeeldingen, scripts, stijlbestanden en andere bestanden die deel uitmaken van de webpagina die wordt getest. De opgenomen reactietijd is inclusief de tijd die nodig is om deze bestanden op te halen. De test mislukt als een van deze resources niet kan worden gedownload binnen de time-out voor de hele test. Als de optie niet is ingeschakeld, vraagt de test alleen het bestand op van de URL die u hebt opgegeven. Het inschakelen van deze optie resulteert in een strengere controle. De test kan mislukken voor gevallen, die mogelijk niet zichtbaar zijn wanneer u handmatig op de site bladert. |
|**Nieuwe proberen inschakelen**| Wanneer de test mislukt, wordt deze na een kort interval opnieuw uitgevoerd. Fouten worden pas gerapporteerd als er drie opeenvolgende pogingen mislukken. Daaropvolgende tests worden vervolgens met de gebruikelijke testfrequentie uitgevoerd. Volgende pogingen worden tijdelijk uitgesteld tot er weer een test slaagt. Deze regel wordt onafhankelijk toegepast op elke testlocatie. **We raden deze optie aan.** Gemiddeld verdwijnt ongeveer 80% van de fouten na het opnieuw proberen.|
| **Validatietest voor SSL-certificaat** | U kunt het SSL-certificaat op uw website controleren om er zeker van te zijn dat het correct is geïnstalleerd, geldig en vertrouwd en geen fouten aan uw gebruikers geeft. |
| **Proactieve levensduurcontrole** | Hiermee kunt u een bepaalde periode definiëren voordat uw SSL-certificaat verloopt. Zodra de test is verlopen, mislukt deze. |
|**Frequentie testen**| Hiermee stelt u in hoe vaak de test wordt uitgevoerd vanaf elke testlocatie. Met een standaardfrequentie van vijf minuten en vijf testlocaties wordt uw site gemiddeld per minuut getest.|
|**Testlocaties**| Zijn de plaatsen van waar onze servers webaanvragen naar uw URL verzenden. **Ons minimum aantal aanbevolen testlocaties is vijf** om ervoor te zorgen dat u problemen op uw website kunt onderscheiden van netwerkproblemen. U kunt meer dan vijf locaties met standaardtests en maximaal 16 locaties selecteren.|

Als uw URL niet zichtbaar is vanaf het openbare internet, kunt u ervoor kiezen om uw firewall selectief te openen zodat alleen **de testtransacties via worden toegestaan.** Raadpleeg de IP-adreshandleiding voor meer informatie over de firewall-uitzonderingen voor onze [beschikbaarheidstestagents.](./ip-addresses.md#availability-tests)

> [!NOTE]
> We raden u ten zeerste aan om te testen vanaf meerdere locaties **met minimaal vijf locaties.** Dit is om valse alarmen te voorkomen die kunnen leiden tot tijdelijke problemen met een specifieke locatie. Bovendien hebben we geconstateerd dat de optimale configuratie is om het aantal testlocaties gelijk te laten zijn aan de drempelwaarde voor de waarschuwingslocatie **+ 2**.

## <a name="standard-test"></a>Standaardtest

:::image type="content" source="./media/monitor-web-app-availability/standard-test-post.png" alt-text="Schermopname van het tabblad Standaardtestgegevens." border="false":::

|Instelling | Uitleg |
|--------|-------------|
| **Aangepaste headers** | Sleutelwaardeparen die de operationele parameters definiëren. |
| **HTTP-aanvraagwerkwoord** | Geef aan welke actie u wilt ondernemen met uw aanvraag. Als uw gekozen werkwoord niet beschikbaar is in de gebruikersinterface, kunt u een standaardtest implementeren met behulp van Azure Broncontrole met de gewenste keuze. |
| **Aanvraagbody** | Aangepaste gegevens die zijn gekoppeld aan uw HTTP-aanvraag. U kunt het type eigen bestandstype in uw inhoud uploaden of deze functie uitschakelen. Voor onbewerkte inhoud van de body ondersteunen we TEXT, JSON, HTML, XML en JavaScript. |

## <a name="success-criteria"></a>Succescriteria

|Instelling| Uitleg
|----|----|----|
| **Time-out testen** |Verklein deze waarde om te worden gewaarschuwd bij trage reacties. De test wordt als mislukt beschouwd als er binnen deze periode geen reactie van uw site is ontvangen. Als u **Parse onafhankelijke aanvragen** hebt geselecteerd, moeten alle afbeeldingen, stijlbestanden, scripts en andere afhankelijke resources binnen deze periode worden ontvangen.|
| **HTTP-antwoord** | De geretourneerde statuscode die wordt geteld als geslaagd. 200 is de code die aangeeft dat er een normale webpagina is geretourneerd.|
| **Inhoudsmatch** | Een tekenreeks, zoals 'Welkom!' Er wordt getest of er in elke respons een exacte (hoofdlettergevoelige) overeenkomst wordt gevonden. Het moet een eenvoudige tekenreeks zijn, zonder jokertekens. Als uw pagina-inhoud wordt gewijzigd, moet u deze tekenreeks mogelijk ook bijwerken. **Alleen Engelse tekens worden ondersteund met inhoudsmatch** |

## <a name="alerts"></a>Waarschuwingen

|Instelling| Uitleg
|----|----|----|
|**Bijna realtime (preview)** | We raden u aan near-realtime waarschuwingen te gebruiken. Het configureren van dit type waarschuwing wordt uitgevoerd nadat uw beschikbaarheidstest is gemaakt.  |
|**Drempelwaarde voor waarschuwingslocatie**|We raden minimaal 3/5 locaties aan. De optimale relatie tussen de drempelwaarde voor de waarschuwingslocatie en het aantal testlocaties **is** het drempelwaardenummer van de waarschuwingslocatie van  =  **testlocaties - 2, met minimaal vijf testlocaties.**|

## <a name="location-population-tags"></a>Locatiepopulatietags

De volgende populatietags kunnen worden gebruikt voor het kenmerk geolocatie bij het implementeren van een pingtest voor beschikbaarheids-URL's met behulp van Azure Resource Manager.

#### <a name="azure-gov"></a>Azure Gov

| Weergavenaam   | Naam van populatie     |
|----------------|---------------------|
| USGov Virginia | usgov-va-azr        |
| USGov Arizona  | usgov-phx-azr       |
| USGov Texas    | usgov-tx-azr        |
| USDoD East     | usgov-ddeast-azr    |
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

Klik na enkele minuten op Vernieuwen **om de** testresultaten te bekijken.

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

* Bekijk het probleemoplossingsrapport om te bepalen waarom uw test is mislukt, maar uw toepassing nog steeds beschikbaar is.
* De reactie inspecteren die is ontvangen van uw server.
* Diagnosefout met gecorreleerde telemetrie op de server die is verzameld tijdens het verwerken van de mislukte beschikbaarheidstest.
* Registreer een probleem of werkitem in Git of Azure Boards om het probleem bij te houden. De bug bevat een koppeling naar deze gebeurtenis.
* Het webtestresultaat openen in Visual Studio.

Ga naar de documentatie voor transactiediagnose voor meer informatie over de end-to-end-ervaring voor [transactiediagnose.](./transaction-diagnostics.md)

Klik op de uitzonderingsrij om de details weer te geven van de uitzondering aan de serverzijde waardoor de synthetische beschikbaarheidstest is mislukt. U kunt ook de momentopname voor [foutopsporing krijgen](./snapshot-debugger.md) voor uitgebreidere diagnostische gegevens op codeniveau.

:::image type="content" source="./media/monitor-web-app-availability/open-instance-4.png" alt-text="Diagnostische gegevens aan de serverzijde.":::

Naast de onbewerkte resultaten kunt u ook twee belangrijke metrische beschikbaarheidsgegevens weergeven in [Metrics Explorer:](../essentials/metrics-getting-started.md)

1. Beschikbaarheid: percentage van de tests die zijn geslaagd, bekeken over alle testuitvoeringen.
2. Testduur: gemiddelde testduur van alle testuitvoeringen.

## <a name="automation"></a>Automation

* Gebruik [PowerShell-scripts om automatisch een beschikbaarheidstest in te stellen](./powershell.md#add-an-availability-test).
* Stel een [webhook](../alerts/alerts-webhooks.md) in die wordt aangeroepen wanneer er een waarschuwing wordt gegenereerd.


## <a name="next-steps"></a>Volgende stappen

* [Beschikbaarheidswaarschuwingen](availability-alerts.md)
* [Webtests met meerdere stappen](availability-multistep.md)
* [Problemen oplossen](troubleshoot-availability.md)
