---
title: De beschikbaarheid en reactiesnelheid van een website bewaken | Microsoft Docs
description: Stel webtests in Application Insights in. Ontvang een waarschuwing wanneer een website niet meer beschikbaar is of traag reageert.
ms.topic: conceptual
ms.date: 03/10/2021
ms.reviewer: sdash
ms.openlocfilehash: d7c610e374dcb7b97850d815ba8bb927cdebacfc
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103012561"
---
# <a name="monitor-the-availability-of-any-website"></a>Beschik baarheid van alle websites bewaken

Nadat u uw web-app/website hebt geïmplementeerd, kunt u terugkerende tests instellen om de beschik baarheid en reactie snelheid te bewaken. [Azure Application Insights](./app-insights-overview.md) verzendt regelmatig webaanvragen naar uw toepassing vanaf verschillende punten over de hele wereld. U kunt hiermee een waarschuwing ontvangen als uw toepassing niet reageert of als deze te langzaam reageert.

U kunt beschikbaarheidstests instellen voor alle HTTP- en HTTPS-eindpunten die toegankelijk zijn op het openbare internet. U hoeft geen wijzigingen aan te brengen op de website die u wilt testen. Eigenlijk hoeft het niet zelfs een eigen site te zijn. U kunt de beschik baarheid testen van een REST API waarvan uw service afhankelijk is.

### <a name="types-of-availability-tests"></a>Typen beschikbaarheids tests:

Er zijn drie soorten beschikbaarheids tests:

* [URL-pingtest](#create-a-url-ping-test): een eenvoudige test die u in Azure Portal kunt instellen.
* [Webtest met meerdere stappen](availability-multistep.md): een opname van een reeks webaanvragen, die kunnen worden afgespeeld om complexe scenario's te testen. Webtests met meerdere stappen worden gemaakt in Visual Studio Enter prise en geüpload naar de portal voor uitvoering.
* [Aangepaste beschikbaarheids testen](/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability): als u besluit een aangepaste toepassing te maken om beschikbaarheids tests uit te voeren, `TrackAvailability()` kunt u de methode gebruiken om de resultaten naar Application Insights te verzenden.

**U kunt Maxi maal 100 beschik bare beschikbaarheids tests maken per Application Insights resource.**

> [!IMPORTANT]
> Beide, [URL-ping testen](#create-a-url-ping-test) en [webtest met meerdere stappen](availability-multistep.md) zijn afhankelijk van de open bare Internet-DNS-infra structuur om de domein namen van de geteste eind punten op te lossen. Dit betekent dat als u Privé-DNS gebruikt, u ervoor moet zorgen dat elke domein naam van uw test ook kan worden omgezet door de open bare domeinnaam servers, of dat wanneer dit niet mogelijk is, u in plaats daarvan [aangepaste beschikbaarheids tests](/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability) kunt gebruiken.

## <a name="create-an-application-insights-resource"></a>Een Application Insights-resource maken

Als u een beschikbaarheids test wilt maken, moet u eerst een Application Insights resource maken. Als u al een resource hebt gemaakt, gaat u door naar de volgende sectie om [een URL-ping-test te maken](#create-a-url-ping-test).

Selecteer in de Azure Portal **een resource maken**  >  **Ontwikkelhulpprogramma's**  >  **Application Insights** en [Maak een Application Insights-resource](create-new-resource.md).

## <a name="create-a-url-ping-test"></a>Een URL-pingtest aanmaken

De naam "URL-ping testen" is een bit van een misnomer. Deze test maakt geen gebruik van ICMP (Internet Control Message Protocol) om de beschik baarheid van uw site te controleren. In plaats daarvan wordt meer geavanceerde HTTP-aanvraag functionaliteit gebruikt om te controleren of een eind punt reageert. Het meet ook de prestaties die zijn gekoppeld aan het antwoord en voegt de mogelijkheid toe om aangepaste succes criteria in te stellen, gekoppeld aan meer geavanceerde functies zoals het parseren van afhankelijke aanvragen, en het toestaan van nieuwe pogingen.

Als u uw eerste beschikbaarheids aanvraag wilt maken, opent u het deel venster Beschik baarheid en selecteert u **test maken**.

![Vul in elk geval de URL van uw website in](./media/monitor-web-app-availability/availability-create-test-001.png)

### <a name="create-a-test"></a>Een test maken

|Instelling| Uitleg
|----|----|----|
|**URL** |  De URL kan iedere webpagina zijn die u wilt testen, maar deze moet zichtbaar zijn vanaf het openbare internet. De URL kan een queryreeks bevatten. Zo kunt u bijvoorbeeld oefenen met uw database. Als de URL naar een omleiding is opgelost, kunnen we deze tot maximaal 10 omleidingen opvolgen.|
|**Afhankelijke aanvragen parseren**| Test vraagt installatie kopieën, scripts, stijl bestanden en andere bestanden die deel uitmaken van de webpagina onder testen. De opgenomen reactietijd is inclusief de tijd die nodig is om deze bestanden op te halen. De test mislukt als een van deze resources niet kan worden gedownload binnen de time-out voor de volledige test. Als de optie niet is ingeschakeld, vraagt de test alleen het bestand op van de URL die u hebt opgegeven. Als u deze optie inschakelt, resulteert dit in een strikte controle. De test kan mislukken voor cases die mogelijk niet merkbaar zijn wanneer u de site hand matig bezoekt.
|**Nieuwe pogingen inschakelen**|Wanneer de test mislukt, wordt deze na een korte interval opnieuw geprobeerd. Fouten worden pas gerapporteerd als er drie opeenvolgende pogingen mislukken. Daaropvolgende tests worden vervolgens met de gebruikelijke testfrequentie uitgevoerd. Volgende pogingen worden tijdelijk uitgesteld tot er weer een test slaagt. Deze regel wordt onafhankelijk toegepast op elke testlocatie. **We raden u aan deze optie te selecteren**. Gemiddeld verdwijnt ongeveer 80% van de fouten na het opnieuw proberen.|
|**Test frequentie**| Hiermee stelt u in hoe vaak de test wordt uitgevoerd vanaf elke test locatie. Met een standaardfrequentie van vijf minuten en vijf testlocaties wordt uw site gemiddeld per minuut getest.|
|**Testlocaties**| Zijn de locaties waar onze servers webaanvragen verzenden naar uw URL. Het **minimum aantal aanbevolen test locaties is vijf** om ervoor te zorgen dat u problemen in uw website kunt onderscheiden van netwerk problemen. U kunt maximaal 16 locaties selecteren.

**Als uw URL niet zichtbaar is via het open bare Internet, kunt u ervoor kiezen om de firewall selectief te openen, zodat alleen de test transacties via worden toegestaan**. Raadpleeg de [hand leiding voor IP-adressen](./ip-addresses.md#availability-tests)voor meer informatie over de firewall uitzonderingen voor onze beschikbaarheids test agents.

> [!NOTE]
> We raden u ten zeerste aan te testen vanaf meerdere locaties met **Mini maal vijf locaties**. Dit is om te voor komen dat er valse waarschuwingen optreden die kunnen leiden tot tijdelijke problemen met een specifieke locatie. Daarnaast hebben we vastgesteld dat de optimale configuratie het **aantal test locaties moet hebben dat gelijk is aan de drempel waarde voor de waarschuwings locatie + 2**.

### <a name="success-criteria"></a>Criteria voor geslaagde pogingen

|Instelling| Uitleg
|----|----|----|
| **Time-out testen** |Deze waarde verlagen om te worden gewaarschuwd over trage reacties. De test wordt als mislukt beschouwd als er binnen deze periode geen reactie van uw site is ontvangen. Als u **Parse onafhankelijke aanvragen** hebt geselecteerd, moeten alle afbeeldingen, stijlbestanden, scripts en andere afhankelijke resources binnen deze periode worden ontvangen.|
| **HTTP-antwoord** | De geretourneerde status code die als geslaagd is geteld. 200 is de code die aangeeft dat er een normale webpagina is geretourneerd.|
| **Inhouds overeenkomst** | Een teken reeks, bijvoorbeeld ' Welkom! ' Er wordt getest of er in elke respons een exacte (hoofdlettergevoelige) overeenkomst wordt gevonden. Het moet een eenvoudige tekenreeks zijn, zonder jokertekens. Als uw pagina-inhoud wordt gewijzigd, moet u deze tekenreeks mogelijk ook bijwerken. **Alleen Engelse tekens worden ondersteund met inhouds overeenkomsten** |

### <a name="alerts"></a>Waarschuwingen

|Instelling| Uitleg
|----|----|----|
|**Bijna realtime (preview-versie)** | We raden u aan bijna realtime waarschuwingen te gebruiken. Het configureren van dit type waarschuwing wordt uitgevoerd nadat de beschikbaarheids test is gemaakt.  |
|**Drempel waarde voor waarschuwings locatie**|We raden aan dat er mini maal 3/5 locaties zijn. De optimale relatie tussen de drempel waarde van de waarschuwings locatie en het aantal test locaties is drempel waarde voor **waarschuwings locaties**  =  **aantal test locaties-2, met een minimum van vijf test locaties.**|

### <a name="location-population-tags"></a>Locatie populatie Tags

De volgende populatie Tags kunnen worden gebruikt voor het kenmerk geo-locatie bij het implementeren van een ping-test voor de beschik baarheids-URL met Azure Resource Manager.

#### <a name="azure-gov"></a>Azure-gov

| Weergavenaam   | Naam van populatie     |
|----------------|---------------------|
| USGov Virginia | USGov-VA-AZR        |
| USGov Arizona  | USGov-PHX-AZR       |
| USGov Texas    | USGov-TX-AZR        |
| USDoD-Oost     | USGov-ddeast-AZR    |
| USDoD-centraal  | USGov-ddcentral-AZR |

#### <a name="azure"></a>Azure

| Weergavenaam                           | Naam van populatie   |
|----------------------------------------|-------------------|
| Australië - oost                         | EMEA-au-Syd-Edge  |
| Brazilië - zuid                           | latam-BR-Gru-Edge |
| VS - centraal                             | VS-FL-quote-Edge    |
| Azië - oost                              | Apac-HK-hkn-AZR   |
| VS - oost                                | US-VA-Ash-AZR     |
| Frankrijk-zuid (voorheen Frankrijk-centraal) | EMEA-CH-ZRH-Edge  |
| Frankrijk - centraal                         | EMEA-fr-pra-Edge  |
| Japan - oost                             | Apac-JP-Kaw-Edge  |
| Europa - noord                           | EMEA-GB-db3-AZR   |
| VS - noord-centraal                       | US-Il-CH1-AZR     |
| VS - zuid-centraal                       | VS-TX-SN1-AZR     |
| Azië - zuidoost                         | Apac-SG-Sin-AZR   |
| Verenigd Koninkrijk West                                | EMEA-se-waarschuwingsd-Edge  |
| Europa -west                            | EMEA-nl-AMS-AZR   |
| VS - west                                | US-CA-SJC-AZR     |
| Verenigd Koninkrijk Zuid                               | EMEA-ru-MSA-Edge  |

## <a name="see-your-availability-test-results"></a>De resultaten van de beschikbaarheidstest bekijken

De resultaten van de beschikbaarheids test kunnen worden gevisualiseerd met zowel de lijn-als spreidings tekening weergave.

Klik na een paar minuten op **vernieuwen** om de test resultaten weer te geven.

![Scherm afbeelding toont de beschikbaarheids pagina met de knop Vernieuwen gemarkeerd.](./media/monitor-web-app-availability/availability-refresh-002.png)

In de weer gave scatterplot ziet u voor beelden van de test resultaten met details over diagnostische test stappen. De testengine slaat diagnostische gegevens op voor tests met fouten. Bij geslaagde tests wordt diagnostische informatie voor een subset van de uitvoeringen opgeslagen. Beweeg de muis aanwijzer over een van de groene/rode stippen om de test, naam en locatie te bekijken.

![Regel weergave](./media/monitor-web-app-availability/availability-scatter-plot-003.png)

Selecteer een bepaalde test of locatie, of verklein de periode om meer resultaten te zien uit de periode die voor u van belang is. Gebruik Search Explorer om resultaten van alle uitvoeringen weer te geven, of gebruik Analytics-query's om aangepaste rapporten uit te voeren op deze gegevens.

## <a name="inspect-and-edit-tests"></a> Tests bekijken en bewerken

Als u een test wilt bewerken, tijdelijk wilt uitschakelen of verwijderen, klikt u op de weglatings tekens naast de naam van een test. Het kan Maxi maal 20 minuten duren voordat configuratie wijzigingen zijn door gegeven aan alle agents nadat een wijziging is aangebracht.

![Test gegevens weer geven. Een webtest bewerken en uitschakelen](./media/monitor-web-app-availability/edit.png)

Het is verstandig beschikbaarheidstests of de regels voor waarschuwingen die eraan zijn gekoppeld uit te schakelen wanneer u onderhoud uitvoert op uw service.

## <a name="if-you-see-failures"></a>Als u mislukte tests ziet

Klik op een rode punt.

![Op een rode punt klikken](./media/monitor-web-app-availability/open-instance-3.png)

Vanuit het resultaat van een beschikbaarheids test ziet u de transactie Details voor alle onderdelen. Hier kunt u het volgende doen:

* De reactie inspecteren die is ontvangen van uw server.
* Diagnose fout met gecorreleerde telemetrie aan de server zijde die is verzameld tijdens het verwerken van de mislukte beschikbaarheids test.
* Registreer een probleem of werk item in Git-of Azure-kaarten om het probleem op te sporen. De bug bevat een koppeling naar deze gebeurtenis.
* Het webtestresultaat openen in Visual Studio.

Lees [hier](./transaction-diagnostics.md)meer over de diagnostische gegevens over end-to-end trans acties.

Klik op de rij met de uitzonde ring om de details van de uitzonde ring aan de server zijde te bekijken die de synthetische beschikbaarheids test heeft veroorzaakt. U kunt ook de [moment opname van de fout opsporing](./snapshot-debugger.md) vinden voor het uitgebreidere code niveau diagnoses.

![Diagnostische gegevens aan de server zijde](./media/monitor-web-app-availability/open-instance-4.png)

Naast de onbewerkte resultaten kunt u ook twee belang rijke metrische gegevens over beschik baarheid in [Metrics Explorer](../essentials/metrics-getting-started.md)bekijken:

1. Beschikbaarheid: percentage van de tests die zijn geslaagd, bekeken over alle testuitvoeringen.
2. Testduur: gemiddelde testduur van alle testuitvoeringen.

## <a name="automation"></a>Automation

* Gebruik [PowerShell-scripts om automatisch een beschikbaarheidstest in te stellen](./powershell.md#add-an-availability-test).
* Stel een [webhook](../alerts/alerts-webhooks.md) in die wordt aangeroepen wanneer er een waarschuwing wordt gegenereerd.

## <a name="troubleshooting"></a>Problemen oplossen

Speciaal [artikel voor probleem oplossing](troubleshoot-availability.md).

## <a name="next-steps"></a>Volgende stappen

* [Beschikbaarheids waarschuwingen](availability-alerts.md)
* [Webtests met meerdere stappen](availability-multistep.md)

