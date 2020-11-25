---
title: 'Overzicht: Wat is Azure Time Series Insights Gen2? - Azure Time Series Insights Gen2 | Microsoft Docs'
description: Meer informatie over wijzigingen, verbeteringen en functies in van Azure Time Series Insights Gen2.
ms.service: time-series-insights
services: time-series-insights
author: shreyasharmamsft
ms.author: shresha
manager: dpalled
ms.workload: big-data
ms.topic: overview
ms.date: 10/02/2020
ms.custom: seodec18
ms.openlocfilehash: 8fdbb42d06f26849d14e093c340c6d2457ee6ec9
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95020092"
---
# <a name="what-is-azure-time-series-insights-gen2"></a>Wat is Azure Time Series Insights Gen2

Azure Time Series Insights Gen2 is een open en schaalbare end-to-end IoT-analyseservices met de beste gebruikerservaring en geavanceerde API's voor de integratie van de krachtige mogelijkheden in uw bestaande werkstroom of toepassing.

U kunt dit gebruiken om query's op gegevens uit te voeren en gegevens te verzamelen, te verwerken, op te slaan en te visualiseren op IoT-schaal (Internet of Things (IoT): gegevens die in hoge mate in context zijn geplaatst en geoptimaliseerd voor tijdreeksen.

Met Azure Time Series Insights Gen2 kunt u ad hoc gegevens verkennen en operationele analyses uitvoeren om verborgen trends en afwijkingen aan het licht te brengen en oorzakenanalyse uit te voeren. Het is een open en flexibele aanbieding die voldoet aan de brede behoeften van industriële IoT-implementaties.

## <a name="video"></a>Video

Meer informatie over Azure Time Series Insights Gen2.

> [!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Using-Azure-Time-Series-Insights-to-create-an-Industrial-IoT-analytics-platform/player]

## <a name="definition-of-iot-data"></a>Definitie van IoT-gegevens

Industriële IoT-gegevens in asset-intensieve organisaties ontbreekt het vaak aan structurele consistentie vanwege het gevarieerde karakter van apparaten en sensoren in een industriële setting. Gegevens die van deze stromen afkomstig zijn, worden gekenmerkt door aanzienlijke hiaten en soms beschadigde berichten en verkeerde metingen. IoT-gegevens zijn vaak zinvol in de context van de invoer van aanvullende gegevens die afkomstig zijn van eigen of externe bronnen, zoals CRM en ERP, die context toevoegen voor end-to-end werkstromen. Invoer van externe gegevensbronnen, zoals weergegevens, kunnen de telemetriestromen in een bepaalde installatie helpen verbeteren.

Dit houdt in dat slechts een fractie van de gegevens voor operationele en zakelijke doeleinden wordt gebruikt, en dat voor de analyse meer context nodig is. Industriële gegevens worden voor een diepgaande analyse vaak over een lange tijdspanne historisch gemaakt om trends te begrijpen en te correleren. Voor het omzetten van verzamelde IoT-gegevens in bruikbare inzichten zijn de volgende zaken nodig:

* Gegevensverwerking voor het opschonen, filteren, interpoleren, transformeren en voorbereiden van gegevens voor analyse.
* Een structuur om doorheen te navigeren en de gegevens te begrijpen (om de gegevens te normaliseren en context te geven).
* Voordelige opslag voor lange/oneindige bewaarperioden aan verwerkte (of afgeleide) gegevens, alsmede onbewerkte gegevens.

Deze gegevens bieden consistente, uitgebreide, actuele en juiste informatie voor zakelijke rapportage en analyse.

In de volgende afbeelding wordt een typische IoT-gegevensstroom getoond.

[![IoT-gegevensstroom](media/v2-update-overview/overview-one.png)](media/v2-update-overview/overview-one.png#lightbox)

## <a name="azure-time-series-insights-gen2-for-industrial-iot"></a>Azure Time Series Insights Gen2 voor industriële IoT

Het IoT-landschap bestaat uit diverse klanten uit een groot aantal verschillende takken van industrie, waaronder de productie-industrie, auto-industrie, energiebedrijven, nutsbedrijven, slimme gebouwen en consulting. In dit brede scala aan industriële IoT-markten zijn cloudeigen oplossingen die uitgebreide analyses bieden voor grootschalige IoT-gegevens nog steeds in ontwikkeling.

Azure Time Series Insights Gen2 voorziet in deze marktbehoefte door een kant-en-klare, end-to-end, analytische IoT-oplossing te bieden met rijke semantische modellering voor contextualisatie van tijdreeksgegevens, op assets gebaseerde inzichten en een optimale gebruikerservaring voor detectie, trending, anomaliedetectie en operationele intelligentie.

Dankzij een uitgebreid, operationeel analyseplatform met de mogelijkheid van interactieve gegevensverkenning stelt Azure Time Series Insights Gen2 u in staat meer waardevolle informatie af te leiden uit gegevens die verzameld zijn van IoT-assets. De Gen2-aanbieding biedt ondersteuning voor:

* Een meerlaags opslagmogelijkheid met 'warme' en 'koude' analytische ondersteuning die klanten de mogelijkheid biedt om gegevens te routeren tussen 'warm' en 'koud' voor interactieve analyse over warme gegevens en operationele intelligentie over decennia aan historische gegevens.

  * Een zeer interactieve methode voor 'warme' analyse om veelvuldig grote hoeveel heden query's te verrichten over kortere tijdspannen
  * Een data lake aan tijdreeksen in Azure Storage die schaalbaar is, uitstekend functioneert en kostenbesparend is, waardoor klanten binnen enkele seconden de trend kunnen zien uit jaren aan tijdreeksgegevens.

* Ondersteuning van semantische modellen voor het beschrijven van het domein en de metagegevens die gekoppeld zijn aan de afgeleide en onbewerkte signalen van assets en apparaten.

* Flexibel analyseplatform voor het opslaan van historische tijdreeksgegevens in het Azure Storage-account van de klant, waardoor klanten eigenaar kunnen zijn van hun IoT-gegevens. Gegevens worden opgeslagen in een opensource-indeling van Apache Parquet die connectiviteit en samenwerking mogelijk maakt in diverse gegevensscenario's, waaronder predictive analytics, machine learning en andere aangepaste berekeningen, die worden uitgevoerd met behulp van bekende technologieën, waaronder Spark en Databricks.

* Uitgebreide analyse met verbeterde query-API's en gebruikerservaring, waarbij gegevensinzichten op basis van assets worden gecombineerd met uitgebreide, ad-hocgegevensanalyse met ondersteuning voor interpolatie, scalaire en statistische functies, categorische variabelen, spreidingsgrafieken en in de tijd verschuivende tijdreeksen voor uitgebreide analyse.

* Platform voor grote ondernemingen ter ondersteuning van de behoeften aan schaalvergroting, betere prestaties, beveiliging en betrouwbaarheid van onze IoT-klanten.

* Ondersteuning van de uitbreidbaarheid en integratie voor end-to-end-analyse. Azure Time Series Insights Gen2 biedt een uitbreidbaar analyseplatform voor diverse gegevensscenario's. Met de Power BI-connector kunnen klanten de query's die ze in Azure Time Series Insights Gen2 uitvoeren rechtstreeks in Power BI invoeren om een uniforme weergave van hun BI- en tijdreeksanalyses op één plek te integreren.

In dit diagram op wordt de gegevensstroom op hoog niveau getoond.

  [![Belangrijkste mogelijkheden](media/v2-update-overview/overview-two.png)](media/v2-update-overview/overview-two.png#lightbox)

Azure Time Series Insights Gen2 biedt een schaalbaar prijsmodel voor betalen per gebruik voor gegevensverwerking, gegevensopslag (gegevens en metagegevens) en het uitvoeren van query's op gegevens, zodat klanten hun gebruik kunnen afstemmen op hun bedrijfsbehoeften.

Met de introductie van deze belangrijke, industriële IoT-functionaliteiten biedt Azure Time Series Insights Gen2 tevens de volgende belangrijke voordelen:  

| Mogelijkheid | Voordeel |
| ---| ---|
| Opslag met meerdere lagen voor tijdreeksgegevens op IoT-schaal | Met een pijplijn voor de verwerking van gedeelde gegevens voor het opnemen van gegevens kunt u gegevens opnemen in zowel warme als koude archieven. Gebruik warme opslag voor interactieve query's en koude opslag voor het opslaan van grote hoeveelheden gegevens. Zie [query's](./concepts-query-overview.md) als u wilt profiteren van hoogwaardige, op assets gebaseerde query's. |
| Time Series-model voor het contextualiseren van onbewerkte telemetriegegevens en het afleiden van inzichten op basis van assets | U kunt het tijdreeksmodel gebruiken om instanties, hiërarchieën, typen en variabelen voor uw tijdreeksgegevens te maken. Zie [Time Series-model](./concepts-model-overview.md) voor meer informatie over het Time Series-model.  |
| Probleemloze en continue integratie met andere gegevensoplossingen | Gegevens in de koude opslag van Azure Time Series Insights Gen2 worden [opgeslagen](./concepts-storage.md) in opensource Apache Parquet-bestanden. Dit maakt gegevensintegratie mogelijk met andere gegevensoplossingen (intern of extern), voor scenario's met bijvoorbeeld business intelligence, geavanceerde machine learning en predictive analytics. |
| Gegevens worden bijna in realtime verkend | De [Azure Time Series Insights Gen2](./concepts-ux-panels.md) biedt gebruikers visualisatie voor alle gegevens die door de opnamepijplijn worden geleid. Nadat u verbinding hebt gemaakt met een gebeurtenisbron, kunt u gebeurtenisgegevens bekijken, verkennen en er query's op uitvoeren. Op deze manier kunt u nagaan of een apparaat gegevens naar verwachting verzendt. U kunt IoT-apparaten ook controleren op status, productiviteit en algehele effectiviteit. |
| Uitbreidbaarheid en integratie | De Power BI Connector-integratie is via de optie **Exporteren** rechtstreeks beschikbaar in de Time Series Explorer-gebruikerservaring. Hierdoor kunnen klanten de tijdreeksquery's die ze in onze gebruikerservaring maken, rechtstreeks naar het Power BI-bureaublad exporteren en de tijdreeksgrafieken naast andere BI Analytics weergeven. Hiermee wordt een nieuwe klasse van scenario's mogelijk voor industriële IoT-ondernemingen die in Power BI hebben geïnvesteerd door in plaats van analyses afkomstig van verschillende gegevensbronnen één plek te bieden waar alles geïntegreerd is, waaronder IoT-tijdreeksen. |
| Aangepaste toepassingen gebouwd op het Azure Time Series Insights Gen2-platform | Azure Time Series Insights Gen2 biedt ondersteuning voor de [JavaScript SDK](https://github.com/microsoft/tsiclient/blob/master/docs/API.md). De SDK biedt uitgebreide besturingselementen en vereenvoudigde toegang tot query's. Gebruik de SDK voor het bouwen van aangepaste IoT-toepassingen op basis van Azure Time Series Insights Gen2, die aansluiten op uw zakelijke behoeften. U kunt de [query-API's](./concepts-query-overview.md) van Azure Time Series Insights Gen2 ook rechtstreeks gebruiken om gegevens naar aangepaste IoT-toepassingen te sturen. |

## <a name="next-steps"></a>Volgende stappen

Aan de slag met Azure Time Series Insights Gen2:

> [!div class="nextstepaction"]
> [Snelstartgids](./quickstart-explore-tsi.md)

Informatie over use cases:

> [!div class="nextstepaction"]
> [Gebruikscases van Azure Time Series Insights Gen2](./overview-use-cases.md)