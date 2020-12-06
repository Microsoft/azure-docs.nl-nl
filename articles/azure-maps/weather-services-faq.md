---
title: Veelgestelde vragen over Microsoft Azure Maps-Services (FAQ)
description: Vind antwoord op veelgestelde vragen over de gegevens en functies van Azure Maps weer Services.
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/04/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 2a5a58c1515c647bb76bf35f3a5eaade3d00588a
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/06/2020
ms.locfileid: "96747323"
---
# <a name="azure-maps-weather-services-frequently-asked-questions-faq"></a>Veelgestelde vragen over Azure Maps-Services

In dit artikel vindt u antwoorden op veelgestelde vragen over de gegevens en functies van [Azure Maps weer Services](https://docs.microsoft.com/rest/api/maps/weather) . De volgende onderwerpen komen aan bod:

* Gegevens bronnen en gegevens modellen
* Dekking en beschik baarheid van weer Services
* Gegevens update frequentie
* Ontwikkelen met Azure Maps Sdk's
* Opties voor het visualiseren van weer gegevens, waaronder integratie met micro soft Power BI

## <a name="data-sources-and-data-models"></a>Gegevens bronnen en gegevens modellen

**Hoe worden er Azure Maps bron gegevens weer gegeven?**

Azure Maps is gebouwd met de samen werking van mobiliteits-en locatie technologie partners van wereld klasse, waaronder AccuWeather, die de onderliggende weer gegevens levert. Als u de aankondiging van Azure Maps ' samen werking met AccuWeather wilt lezen, gaat u naar [regen of glanzend: Azure Maps-weer services bieden inzicht in uw onderneming](https://azure.microsoft.com/blog/rain-or-shine-azure-maps-weather-services-will-bring-insights-to-your-enterprise/).

AccuWeather heeft realtime weer en omgevings informatie overal ter wereld verkrijgbaar, voornamelijk door hun partnerschappen met talloze nationale overheids instellingen en andere eigen voorzieningen. Hieronder vindt u een lijst met deze basis informatie.

* Openbaar beschik bare mondiale oppervlakte waarnemingen van overheids instellingen
* Gegevens sets van eigen oppervlakte waarnemingen van overheden en particuliere bedrijven
* Radar gegevens met hoge resolutie voor meer dan 40 landen/regio's
* Beste real-time wereld wijde bliksem gegevens
* Government-uitgegeven weer waarschuwingen voor meer dan 60 landen/regio's en gebieden
* Satelliet gegevens van geostationaire weers satellieten die de hele wereld bedekken
* Meer dan 150 numerieke prognose modellen met inbegrip van interne, bedrijfsspecifieke modellen, Government models, zoals het Amerikaanse Global Forecast-systeem (GFS) en unieke downscaled modellen van particuliere bedrijven
* Opmerkingen over lucht kwaliteit
* Waarnemingen van de departementen vervoer

Tien duizenden oppervlakte waarnemingen, samen met andere gegevens, zijn opgenomen om de huidige voor waarden die voor gebruikers beschikbaar zijn, te maken en te beïnvloeden. Dit omvat niet alleen vrij beschik bare standaard gegevens sets, maar ook unieke waarnemingen die afkomstig zijn van nationale meteorologische Services in veel landen/regio's, waaronder India, Brazilië en Canada en andere eigen invoer. Deze unieke gegevens sets verhogen de ruimtelijke en tijdelijke resolutie van huidige voor waarden gegevens voor onze gebruikers. 

Deze gegevens sets worden in realtime gecontroleerd op nauw keurigheid van het Digital Forecast-systeem. Dit maakt gebruik van de eigen kunst matige intelligentie algoritmen van AccuWeather om de prognoses continu te wijzigen, zodat ze altijd de meest recente gegevens bevatten en zodoende de nauw keurigheid van de continuheid optimaliseren.

**Met welke modellen worden er weer prognose gegevens gemaakt?**

Een groot aantal voor weers pellen-hulp systemen wordt gebruikt voor het formuleren van globale prognoses. Meer dan 150 numerieke prognose modellen worden elke dag gebruikt, zowel externe als interne gegevens sets. Dit omvat overheids modellen, zoals de ECMWF van het Euro pees centrum en het Amerikaanse Global Forecast-systeem (GFS). Daarnaast heeft AccuWeather in bedrijfs eigen modellen met een hoge resolutie die de voor keur heeft verkleinen voor specifieke locaties en strategische regionale domeinen om weer een grotere nauw keurigheid te voors pellen. De unieke overvloei-en wegings algoritmen van AccuWeather zijn in de laatste paar decennia ontwikkeld. Deze algoritmen maken optimaal gebruik van de talloze prognose invoer om zeer nauw keurige prognoses te bieden.

## <a name="weather-services-coverage-and-availability"></a>Dekking en beschik baarheid van weer Services

**Wat voor soort dekking kan ik verwachten voor verschillende landen/regio's?**

De weers cover service is afhankelijk van het land/de regio. Alle functies zijn niet beschikbaar in elk land/elke regio. Zie de documentatie van de [behoefte](https://docs.microsoft.com/azure/azure-maps/weather-coverage)voor meer informatie.

## <a name="data-update-frequency"></a>Gegevens update frequentie

**Hoe vaak worden de gegevens van de huidige voor waarden bijgewerkt?**

De huidige voor waarden worden ten minste één keer per uur bijgewerkt, maar kunnen vaker worden bijgewerkt met snel veranderende voor waarden, zoals grote temperatuur wijzigingen, veranderingen in de lucht, wijzigingen, enzovoort. De meeste waarnemings punten over het hele wereld rapport zijn veel keer per uur als de omstandigheden veranderen. Enkele gebieden zullen echter nog steeds slechts één keer, twee keer of vier keer per uur worden bijgewerkt op basis van de geplande intervallen.  

Azure Maps de gegevens van de huidige voor waarden Maxi maal tien minuten in de cache opslaat om de bijna realtime update frequentie van de gegevens te vastleggen wanneer deze optreedt. Als u wilt zien wanneer het antwoord in de cache verloopt en wilt voor komen dat verouderde gegevens worden weer gegeven, kunt u gebruikmaken van de verlopen header gegevens in de HTTP-header van de Azure Maps API-reactie.

**Hoe vaak dagelijks en prognose gegevens per uur worden bijgewerkt?**

Dagelijkse en Uurprognose-gegevens worden meerdere keren per dag bijgewerkt, wanneer bijgewerkte waarnemingen worden ontvangen.  Als bijvoorbeeld een geraamde hoge/lage Tempe ratuur wordt overschreden, worden de prognose gegevens tijdens de volgende update cyclus aangepast. Dit kan gebeuren met verschillende intervallen, maar gebeurt meestal binnen een uur. Veel onverwachte weers omstandigheden kunnen een wijziging van de prognose gegevens veroorzaken. Met een hot-zomer middag kan een geïsoleerde Thunderstorm zich bijvoorbeeld plotseling voordoen, waardoor de Cloud dekking en de regen zwaarder wordt. De geïsoleerde Storm kan in feite de Tempe ratuur met maar liefst 10 graden verwijderen. Deze nieuwe waarde voor de Tempe ratuur is van invloed op de prognoses per uur en dagelijks voor de rest van de dag, en zal als zodanig worden bijgewerkt in onze gegevens sets.

Azure Maps Forecast-Api's worden opgeslagen in de cache van Maxi maal 30 minuten. Als u wilt zien wanneer het antwoord in de cache verloopt en wilt voor komen dat verouderde gegevens worden weer gegeven, kunt u gebruikmaken van de verlopen header gegevens in de HTTP-header van de Azure Maps API-reactie. U wordt aangeraden om indien nodig een update uit te voeren op basis van een specifieke product use case en gebruikers interface.

## <a name="developing-with-azure-maps-sdks"></a>Ontwikkelen met Azure Maps Sdk's

**Ondersteunt Azure Maps Web-SDK systeem eigen integratie van weer Services?**

De Azure Maps Web-SDK biedt een Services-module. De Services-module is een helper-bibliotheek waarmee u gemakkelijk de Azure Maps REST-services in web-of Node.js toepassingen kunt gebruiken. met behulp van Java script of type script. Zie onze [documentatie](https://docs.microsoft.com/azure/azure-maps/how-to-use-services-module)als u aan de slag wilt gaan.

**Ondersteunt Azure Maps Android SDK systeem eigen integratie van weer Services?**

De Azure Maps Android-Sdk's bieden ondersteuning voor Mercator-tegel lagen. Dit kan x/y/zoom-notatie, een Quad-sleutel notatie of een EPSG 3857-begrenzingsvak bevatten.

We gaan een service module voor Java/Android maken, vergelijkbaar met de Web SDK-module. Met de module Android-Services kunt u eenvoudig toegang krijgen tot alle Azure Maps-Services in een Java-of Android-app.  

## <a name="data-visualizations"></a>Gegevensvisualisaties  

**Biedt Azure Maps Power BI visuele ondersteuning Azure Maps weer tegels?**

Ja. Zie [een laag toevoegen aan Power bi Visual](https://docs.microsoft.com/azure/azure-maps/power-bi-visual-add-tile-layer)voor meer informatie over het migreren van radar-en infra rood satelliet tegels naar het micro soft power BIe-element. 

**Hoe kan ik interpreteert kleuren die worden gebruikt voor radar-en satelliet tegels?**

Het Azure Maps [weer geven concept artikel](https://docs.microsoft.com/azure/azure-maps/weather-services-concepts#radar-and-satellite-imagery-color-scale) bevat een hand leiding voor het interpreteren van kleuren die worden gebruikt voor radar-en satelliet tegels. In het artikel worden kleur voorbeelden en HEXADECIMALe kleur codes beschreven.
 
**Kan ik tegel animaties van radar en satelliet maken?**

Ja. Naast de tegels in realtime radar en satellieten kunnen Azure Maps klanten eerdere en toekomstige tegels aanvragen om gegevens visualisaties met kaart-overlays te verbeteren. Dit kan worden gedaan door direct Calling [kaarten tegel v2 API](https://aka.ms/AzureMapsWeatherTiles ) of door tegels aan te vragen via Azure Maps Web SDK. Radar tegels zijn in het verleden tot 1,5 uur en Maxi maal 2 uur in de toekomst. De tegels en zijn beschikbaar in intervallen van vijf minuten. De infra rood tegels zijn in het verleden Maxi maal drie uur lang en zijn beschikbaar in intervallen van tien minuten. Zie voor meer informatie het voor beeld van een open source weer tegel animatie [code](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Animated%20tile%20layer).  

**Biedt u pictogrammen voor verschillende weers omstandigheden?**

Ja. U kunt [hier](https://docs.microsoft.com/azure/azure-maps/weather-services-concepts#weather-icons)pictogrammen en de bijbehorende codes vinden. U ziet dat slechts enkele van de weer service Api's, zoals de  [huidige voor waarden van de API ophalen](https://aka.ms/azuremapsweathercurrentconditions), de *iconCode* in het antwoord retour neren. Zie het huidige WeatherConditions open source-voor [beeld](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Get%20current%20weather%20at%20a%20location)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Als deze veelgestelde vragen niet beantwoordt aan uw vraag, kunt u contact met ons opnemen via de volgende kanalen (in de volg orde waarin ze worden doorzocht):

* De sectie opmerkingen van dit artikel.
* [MSFT Q&een pagina voor Azure Maps](https://docs.microsoft.com/answers/topics/azure-maps.html).
* Microsoft Ondersteuning. Als u een nieuwe ondersteunings aanvraag wilt maken, selecteert u in het [Azure Portal](https://portal.azure.com/)op het tabblad Help de knop **Help** en ondersteuning en selecteert u vervolgens **nieuwe ondersteunings aanvraag**.
* [Azure Maps UserVoice](https://feedback.azure.com/forums/909172-azure-maps) om functie aanvragen te verzenden.

Meer informatie over het aanvragen van real-time en geraamde weer gegevens met Azure Maps weer Services:
> [!div class="nextstepaction"]
> [Real-time gegevens opvragen ](how-to-request-weather-data.md)

Het artikel over Azure Mapses van weer Services:
> [!div class="nextstepaction"]
> [Concepten van weer Services](weather-coverage.md)

Bekijk de documentatie voor de Azure Maps weer Services API:

> [!div class="nextstepaction"]
> [Azure Maps weer Services](/rest/api/maps/weather)
