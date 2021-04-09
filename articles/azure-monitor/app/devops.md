---
title: Bewaking van web-app-prestaties-Azure-toepassing Insights
description: Hoe Application Insights past in de devOps-cyclus
ms.topic: conceptual
ms.date: 12/21/2018
ms.openlocfilehash: bc897cc0259894964e0b6164219b1e5459e824b6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100579563"
---
# <a name="deep-diagnostics-for-web-apps-and-services-with-application-insights"></a>Diepe diagnostische gegevens voor web-apps en services met Application Insights
## <a name="why-do-i-need-application-insights"></a>Waarom heb ik Application Insights nodig?
Application Insights bewaakt uw actieve web-app. U krijgt informatie over fouten en prestatie problemen en helpt u bij het analyseren van hoe klanten uw app gebruiken. Het werkt voor apps die worden uitgevoerd op een groot aantal platformen (ASP.NET, Java EE, Node.js,...) en wordt gehost in de Cloud of on-premises. 

![Aspecten van de complexiteit van het leveren van web-apps](./media/devops/010.png)

Het is essentieel om een moderne toepassing te bewaken terwijl deze wordt uitgevoerd. Het belangrijkste is dat u storingen wilt detecteren voordat de meeste klanten doen. U wilt ook prestatie problemen ontdekken en oplossen die, hoewel het niet mogelijk is om de prestaties te vertragen of een ongemak voor uw gebruikers veroorzaken. En wanneer het systeem op uw tevredenheid presteert, wilt u weten wat de gebruikers doen: zijn ze de nieuwste functie? Slagen ze ermee?

Moderne webtoepassingen zijn ontwikkeld in een cyclus van continue levering: een nieuwe functie of verbetering in te brengen. Bekijk hoe goed het werkt voor de gebruikers. plan de volgende toename van de ontwikkeling op basis van die kennis. Een belang rijk onderdeel van deze cyclus is de observatie fase. Application Insights biedt de hulp middelen voor het bewaken van een webtoepassing voor prestaties en gebruik.

Het belangrijkste aspect van dit proces is diagnose en diagnose. Als de toepassing mislukt, gaat het bedrijf verloren. De hoofd rol van een bewakings raamwerk is daarom om fouten betrouwbaar te detecteren, u onmiddellijk op de hoogte te stellen en te presen teren de informatie die nodig is om de oorzaak van het probleem te achterhalen. Dit is precies wat Application Insights doet.

### <a name="where-do-bugs-come-from"></a>Waar komen de bugs vandaan?
Storingen in websystemen ontstaan doorgaans door configuratie problemen of onjuiste interacties tussen de vele onderdelen. De eerste taak bij het aanpakken van een live site-incident is daarom het identificeren van de Locus van het probleem: welk onderdeel of relatie is de oorzaak?

Een deel van ons, dat wil zeggen, kan een eenvoudig tijd perk zijn waarin een computer programma op één computer wordt uitgevoerd. De ontwikkel aars testen deze zorgvuldig voordat ze ze verzenden; en die het heeft verzonden, zou er zelden op worden getwijfeld of te zien zijn. De gebruikers moeten de resterende bugs voor veel jaren samen stellen. 

Er zijn nu zeer verschillende dingen. Uw app heeft een verdwaald van verschillende apparaten om uit te voeren, en het kan lastig zijn om precies hetzelfde gedrag te garanderen. Als host voor apps in de Cloud, betekent dit dat fouten snel kunnen worden opgelost, maar dit betekent ook continue competitie en de verwachte nieuwe functies met regel matige tussen pozen. 

In deze omstandigheden is de enige manier om een vast beheer van het aantal fouten te houden, automatische eenheids testen. Het is niet mogelijk om alles hand matig opnieuw te testen op elke levering. Eenheids test is nu een gebruikelijke onderdeel van het bouw proces. Hulpprogram ma's als de Xamarin-Test Cloud helpen bij het bieden van automatische UI-tests op meerdere browser versies. Met deze test regimes kunt u er voor zorgen dat de frequentie van fouten die in een app worden gevonden, tot een minimum wordt beperkt.

Typische webtoepassingen hebben veel live-onderdelen. Naast de client (in een browser-of apparaat-app) en de webserver is er waarschijnlijk een aanzienlijke back-upverwerking. Misschien is de back-end een pijp lijn van onderdelen of een losser verzameling van samen werkende delen. En veel hiervan zijn niet in uw besturings element. Dit zijn de externe services waarvan u afhankelijk bent.

In configuraties als deze kan het lastig en economisch niet economische zijn om te testen op of aan de voor waarde, in tegens telling tot de mogelijke storings modus, met uitzonde ring van het live-systeem zelf. 

### <a name="questions-"></a>Vragen...
Enkele vragen die we stellen wanneer we een websysteem ontwikkelen:

* Loopt mijn app vast? 
* Wat is er precies gebeurd? -Als de aanvraag is mislukt, wil ik weten hoe deze is ontvangen. Er is een tracering van gebeurtenissen vereist...
* Is mijn app snel genoeg? Hoe lang duurt het om standaard aanvragen te beantwoorden?
* Kan de server de belasting verwerken? Wanneer het aantal aanvragen toeneemt, loopt de reactie tijd stabiel?
* Hoe reageert mijn afhankelijkheden: de REST Api's, data bases en andere onderdelen die mijn app aanroept. Met name als het systeem langzaam is, is dit mijn onderdeel of ontvang ik trage reacties van iemand anders?
* Is mijn app up of down? Is dit van over de hele wereld te zien? Laat het me weten als het stopt....
* Wat is de hoofd oorzaak? Is de fout aangetroffen in mijn onderdeel of afhankelijkheid? Is het een communicatie probleem?
* Hoeveel gebruikers zijn van invloed op het probleem? Als ik meer dan een probleem voor de aanpak, wat het belangrijkste is?

## <a name="what-is-application-insights"></a>Wat is Application Insights?
![Basis werk stroom van Application Insights](./media/devops/020.png)

1. Application Insights uw app te instrumenteren en er telemetrie over te sturen terwijl de app wordt uitgevoerd. U kunt de Application Insights SDK bouwen in de app of u kunt instrumentatie Toep assen tijdens runtime. De voormalige methode is flexibeler, omdat u uw eigen telemetrie kunt toevoegen aan de normale modules.
2. De telemetrie wordt verzonden naar de Application Insights-Portal, waar deze wordt opgeslagen en verwerkt. (Hoewel Application Insights wordt gehost in Microsoft Azure, kunnen alle web-apps worden bewaakt, niet alleen Azure-apps.)
3. De telemetrie wordt weer gegeven in de vorm van grafieken en tabellen met gebeurtenissen.

Er zijn twee hoofd typen telemetrie: geaggregeerde en RAW-exemplaren. 

* Exemplaar gegevens bevatten bijvoorbeeld een rapport van een aanvraag die is ontvangen door uw web-app. U kunt de details van een aanvraag zoeken en controleren met behulp van de zoek functie in de Application Insights Portal. Het exemplaar bevat gegevens, zoals hoe lang uw app heeft gereageerd op de aanvraag, evenals de aangevraagde URL, de geschatte locatie van de client en andere gegevens.
* Samengevoegde gegevens omvatten aantallen gebeurtenissen per eenheids tijd, zodat u het aantal aanvragen kunt vergelijken met de reactie tijden. Het bevat ook gemiddelden van metrische gegevens, zoals reactie tijden van aanvragen.

De belangrijkste gegevens categorieën zijn:

* Aanvragen voor uw app (meestal HTTP-aanvragen) met gegevens over URL, reactie tijd, geslaagd of mislukt.
* Afhankelijkheden: REST-en SQL-aanroepen van uw app, ook met URI, reactie tijden en geslaagde pogingen
* Uitzonde ringen, inclusief stack traceringen.
* Pagina weergave gegevens die afkomstig zijn uit de browser van de gebruikers.
* Metrische gegevens, zoals prestatie meter items, en metrische gegevens die u zelf schrijft. 
* Aangepaste gebeurtenissen die u kunt gebruiken om zakelijke gebeurtenissen bij te houden
* Logboek traceringen die worden gebruikt voor fout opsporing.

## <a name="case-study-real-madrid-fc"></a>Casestudy: Real Madrid F.C.
De webservice van [Real Madrid voetbal club](https://www.realmadrid.com/) dient ongeveer 450.000.000 ventilatoren over de hele wereld. Ventilatoren hebben toegang tot de webbrowsers en de mobiele apps van de Club. Ventilatoren kunnen alleen tickets boeken, maar ook toegang krijgen tot informatie en video clips op resultaten, spelers en aanstaande games. Ze kunnen zoeken met filters zoals het aantal gescoorde doelen. Er zijn ook koppelingen naar sociale media. De gebruikers ervaring is zeer persoonlijk en is ontworpen als communicatie in twee richtingen om ventilatoren te benaderen.

De oplossing [is een systeem van services en toepassingen op Microsoft Azure](https://www.microsoft.com/inculture/sports/real-madrid/). Schaal baarheid is een belang rijke vereiste: verkeer is variabel en kan zeer grote volumes bereiken tijdens en rond overeenkomsten.

Voor Real Madrid is het essentieel om de prestaties van het systeem te bewaken. Azure-toepassing Insights biedt een uitgebreid overzicht van het systeem, waardoor een betrouwbaar en hoog service niveau wordt gegarandeerd. 

De Club krijgt ook diep gaande informatie over de ventilatoren: waar ze zich bevinden (slechts 3% in Spanje), wat belang rijk is in spelers, historische resultaten en aanstaande games, en hoe ze reageren op resultaten van treffers.

De meeste van deze telemetriegegevens worden automatisch verzameld zonder toegevoegde code, waarmee de oplossing wordt vereenvoudigd en de operationele complexiteit werd gereduceerd.  Voor Real Madrid hebben Application Insights elke maand een telemetrie-punt van 3.800.000.000.

Real Madrid gebruikt de Power BI-module om hun telemetrie weer te geven.

![Power BI weer gave van Application Insights telemetrie](./media/devops/080.png)

## <a name="smart-detection"></a>Slimme detectie
[Proactieve diagnoses](./proactive-diagnostics.md) is een recente functie. Als u geen speciale configuratie door u hebt, Application Insights detecteert en waarschuwt u automatisch over ongebruikelijke stijgingen in fout tarieven in uw app. Het is slim genoeg om een achtergrond van af en toe fouten te negeren en is ook groter dan een toename in aanvragen. Als er bijvoorbeeld een fout is opgetreden in een van de services waarvan u afhankelijk bent, of als de nieuwe build die u zojuist hebt geïmplementeerd, niet goed werkt, weet u zeker dat u deze ontvangt zodra u uw e-mail bericht bekijkt. (En er zijn webhooks, zodat u andere apps kunt activeren.)

Een ander aspect van deze functie voert een dagelijkse diep gaande analyse uit van uw telemetrie en zoekt naar ongebruikelijke prestatie patronen die moeilijk te detecteren zijn. Het is bijvoorbeeld mogelijk dat er trage prestaties worden gevonden die zijn gekoppeld aan een bepaald geografisch gebied of met een bepaalde browser versie.

In beide gevallen vertelt de waarschuwing niet alleen de symptomen die worden gedetecteerd, maar hebt u ook gegevens die u nodig hebt om het probleem op te lossen, zoals relevante uitzonderings rapporten.

![E-mail van proactieve diagnostische gegevens](./media/devops/030.png)

SAMTEC van de klant: ' tijdens een recente functie cutover hebben we een onder-schaal bare data base gevonden die de resource limieten heeft bereikt en time-outs veroorzaakt. Proactieve detectie waarschuwingen bevonden zich letterlijk naarmate we het probleem uitsorterenen, wat bijna in realtime werd geadverteerd. Deze waarschuwing, gekoppeld aan de waarschuwingen van het Azure-platform, hielp ons het probleem bijna onmiddellijk op te lossen. Totale uitval tijd < tien minuten. "

## <a name="live-metrics-stream"></a>Live Metrics Stream
Het implementeren van de nieuwste build kan een bezorgde-ervaring zijn. Als er problemen zijn, wilt u deze meteen weten, zodat u indien nodig een back-up kunt maken. Live Metrics Stream geeft u belang rijke metrische gegevens met een latentie van ongeveer één seconde.

![Live Metrics](./media/devops/0040.png)

En kunt u onmiddellijk een voor beeld van fouten of uitzonde ringen controleren.

![Live-fout gebeurtenissen](./media/devops/002-live-stream-failures.png)

## <a name="application-map"></a>Toepassingskaart
Toepassings overzicht detecteert automatisch de topologie van uw toepassing, waarbij de prestatie gegevens erop worden geplaatst, zodat u gemakkelijk prestatie knelpunten en problematische stromen in uw gedistribueerde omgeving kunt identificeren. Hiermee kunt u toepassings afhankelijkheden detecteren op Azure-Services. U kunt het probleem sorteren door te weten te komen of het een code-gerelateerde of afhankelijkheid is en van één plaatsings analyse in een gerelateerde diagnose-ervaring. Uw toepassing kan bijvoorbeeld mislukken als gevolg van verminderde prestaties in de SQL-laag. Met toepassings toewijzing kunt u het direct bekijken en inzoomen op de SQL Index Advisor-of query Insights-ervaring.

![Toepassingskaart](./media/devops/0050.png)

## <a name="application-insights-analytics"></a>Application Insights Analytics
Met [Analytics](../logs/log-query-overview.md)kunt u wille keurige query's schrijven in een krachtige, op SQL gebaseerde taal.  Het is eenvoudig om problemen met de hele app-stack op te lossen omdat verschillende perspectieven aansluiten en u de juiste vragen kunt stellen om service prestaties te correleren met metrische gegevens van uw bedrijf en de ervaring van de klant. 

U kunt een query uitvoeren voor al uw telemetrie-exemplaar en metrische gegevens die zijn opgeslagen in de portal. De taal bevat filter, samen voegen, aggregatie en andere bewerkingen. U kunt velden berekenen en statistische analyses uitvoeren. Er zijn zowel tabellaire als grafische visualisaties.

![Analyse query en resultaten grafiek](./media/devops/0025.png)

U kunt bijvoorbeeld het volgende doen:

* Segmenteer de prestatie gegevens van uw toepassing door de klant lagen om inzicht te krijgen in hun ervaring.
* Zoek naar specifieke fout codes of aangepaste gebeurtenis namen tijdens het onderzoeken van Live-sites.
* Zoom in op het app-gebruik van specifieke klanten om te begrijpen hoe de functies zijn verkregen en aangenomen.
* Volg sessies en reactie tijden voor specifieke gebruikers om ondersteuning en operationele teams in te scha kelen om onmiddellijke klant ondersteuning te bieden.
* Bepaal de meest gebruikte app-functies voor het beantwoorden van vragen over de prioriteiten van functies.

DNN van de klant: "Application Insights heeft ons het ontbrekende deel van de vergelijking verschaft zodat gegevens naar behoefte kunnen worden gecombineerd, gesorteerd en gefilterd. Ons team in staat te stellen hun eigen Ingenuity en ervaring te gebruiken om gegevens te vinden met een krachtige query taal heeft ons toestemming gegeven om inzichten op te sporen en problemen op te lossen die we nog niet wisten. Een groot aantal interessante antwoorden is afkomstig uit de vragen die beginnen met *' ik wonder als.*.. '.

## <a name="development-tools-integration"></a>Integratie van ontwikkel hulpprogramma's
### <a name="configuring-application-insights"></a>Application Insights configureren
Visual Studio en intereclips hebben hulpprogram ma's voor het configureren van de juiste SDK-pakketten voor het project dat u ontwikkelt. Er is een menu opdracht om Application Insights toe te voegen.

Als u een framework voor traceer logboek registratie wilt gebruiken, zoals Log4N, NLog of System. Diagnostics. trace, krijgt u de optie om de logboeken te verzenden naar Application Insights samen met de andere telemetrie, zodat u eenvoudig de traceringen kunt correleren met aanvragen, afhankelijkheids aanroepen en uitzonde ringen.

### <a name="search-telemetry-in-visual-studio"></a>Telemetrie doorzoeken in Visual Studio
Tijdens het ontwikkelen en opsporen van fouten in een functie kunt u de telemetrie rechtstreeks in Visual Studio bekijken en doorzoeken met dezelfde zoek functies als in de webportal.

En wanneer Application Insights een uitzonde ring registreert, kunt u het gegevens punt bekijken in Visual Studio en direct naar de relevante code gaan.

![Visual Studio Search](./media/devops/060.png)

Tijdens de fout opsporing hebt u de mogelijkheid om de telemetrie in uw ontwikkel machine te houden en deze weer te geven in Visual Studio, maar niet naar de portal te verzenden. Deze lokale optie voor komt het combi neren van fout opsporing met productie-telemetrie.

### <a name="work-items"></a>Werk items
Wanneer een waarschuwing wordt gegenereerd, kan Application Insights automatisch een werk item in uw werk systeem bijhouden.

## <a name="but-what-about"></a>Maar wat over...?
* [Privacy en opslag](./data-retention-privacy.md) : uw telemetrie wordt bewaard op beveiligde Azure-servers.
* Prestaties: de impact is zeer laag. Telemetrie is gebatcheerd.
* [Prijzen](./pricing.md) : u kunt gratis aan de slag, en dat gaat zo door als u een laag volume hebt.


## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>Volgende stappen
Het is eenvoudig om aan de slag te gaan met Application Insights. De belangrijkste opties zijn:

* [IIS-servers](./monitor-performance-live-website-now.md)en ook voor [Azure app service](./app-insights-overview.md).
* Instrumenteer uw project tijdens de ontwikkeling. U kunt dit doen voor [ASP.net](./asp-net.md) -of [Java](./java-get-started.md) -apps, evenals [Node.js](./nodejs.md) en een host van [andere typen](./platforms.md). 
* Een [wille keurige webpagina](./javascript.md) instrumenteren door een korte code fragment toe te voegen.

