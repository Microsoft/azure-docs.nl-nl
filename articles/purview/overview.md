---
title: Inleiding tot Azure Purview (preview)
description: Dit artikel bevat een overzicht van Azure Purview, met inbegrip van de functies ervan en de problemen die met Azure Purview kunnen worden opgelost. Met Azure Purview kan elke gebruiker gegevensbronnen registreren, detecteren, begrijpen en gebruiken.
author: hophan
ms.author: hophan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: overview
ms.date: 11/30/2020
ms.openlocfilehash: 9ead9a564c11901775ac7c471cd53fe65b3fdef9
ms.sourcegitcommit: 48cb2b7d4022a85175309cf3573e72c4e67288f5
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/08/2020
ms.locfileid: "96855104"
---
# <a name="what-is-azure-purview"></a>Wat is Azure Purview?

> [!IMPORTANT]
> Azure Purview is momenteel in PREVIEW. De [Aanvullende voorwaarden voor gebruik van Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) omvatten aanvullende juridische voorwaarden die van toepassing zijn op Azure-functies die in bèta of preview zijn of die anders nog niet algemeen beschikbaar zijn.

Azure Purview is een geïntegreerde service voor gegevensbeheer die u helpt bij het beheren van uw on-premises gegevens, gegevens in meerdere clouds of SaaS-gegevens (Software-as-a-Service). Maak eenvoudig een holistische, actuele kaart van uw gegevenslandschap met behulp van geautomatiseerde gegevensdetectie, classificatie van gevoelige gegevens en end-to-end gegevensherkomst. Zorg dat gebruikers de mogelijkheid hebben om waardevolle, betrouwbare gegevens te vinden.

Azure Purview Data Map biedt de basis voor gegevensdetectie en effectief gegevensbeheer. Purview Data Map is een cloudeigen PaaS-service voor het vastleggen van metagegevens van bedrijfsgegevens die zich in analyse- en operationele systemen on-premises en in de cloud bevinden. Purview Data Map wordt automatisch bijgewerkt door de ingebouwde scan- en classificatiesystemen. Zakelijke gebruikers kunnen Purview Data Map configureren en gebruiken via een intuïtieve gebruikersinterface, en ontwikkelaars kunnen via programma's, zoals opensource Apache Atlas 2.0-API's met Data Map communiceren.

Azure Purview Data Map zorgt ervoor dat de Purview-gegevenscatalogus en Purview-gegevensinzichten als geïntegreerde ervaring binnen Studio kunnen worden gebruikt.
 
Met de Purview-gegevenscatalogus kunnen zakelijke en technische gebruikers op dezelfde manier eenvoudig relevante gegevens vinden via een zoekervaring met verschillende filters, zoals woordenlijsttermen, classificaties, gevoeligheidslabels en meer. Voor specialisten, gegevensbeheerders en -managers biedt de Purview-gegevenscatalogus functies voor de curatie van gegevens, zoals het beheer van bedrijfsterminologie en de mogelijkheid om de codering van gegevensassets te automatiseren met woordenlijsttermen. Gegevensgebruikers en -producenten kunnen de herkomst van gegevensassets ook visueel volgen, beginnende bij de on-premises operationele systemen, via verplaatsing, transformatie en verrijking met verschillende systemen voor gegevensopslag en verwerking in de cloud, tot aan het moment dat ze door een analysesysteem zoals Power BI worden gebruikt.

Via Purview-gegevensinzichten krijgen datamanagers en security officers een vogelperspectief geboden en zien ze in een oogopslag duidelijk welke gegevens er op dat moment worden gescand, waar gevoelige gegevens zich bevinden en hoe deze zich verplaatsen.

## <a name="discovery-challenges-for-data-consumers"></a>Problemen met detectie voor gegevensgebruikers

Detectie van zakelijke gegevensbronnen is traditioneel een organisch proces op basis van algemeen aanwezige kennis. Voor bedrijven die het meeste uit hun gegevensassets willen halen, leidt deze benadering tot talloze uitdagingen:

* Omdat er geen centrale locatie is waar gegevensbronnen worden geregistreerd, weten gebruikers mogelijk niet dat er gegevensbronnen bestaan, tenzij ze ermee in aanraking komen als onderdeel van een ander proces.
* Als gebruikers de locatie van een gegevensbron niet weten, kunnen ze de gegevens niet benaderen met behulp van een clienttoepassing. Vanwege dataverbruik moeten gebruikers de verbindingsreeks of het pad kennen.
* Tenzij gebruikers de locatie van de documentatie voor een gegevensbron weten, weten ze niet wat het beoogde gebruik van de gegevens is. Gegevensbronnen en documentatie bevinden zich mogelijk op diverse plaatsen en worden op verschillende manieren gebruikt.
* Als gebruikers vragen over een gegevensasset hebben, moeten ze de expert die of het team dat verantwoordelijk is voor de gegevens zien te vinden en ze offline benaderen. Er is geen expliciete relatie tussen gegevens en de deskundigen met een diep inzicht in het beoogde gebruik ervan.
* Tenzij gebruikers het proces voor het aanvragen van toegang tot de gegevensbron begrijpen, helpt de detectie van de gegevensbron en de bijbehorende documentatie hen niet om toegang krijgen tot de gegevens.

## <a name="discovery-challenges-for-data-producers"></a>Problemen met detectie voor gegevensproducenten

Terwijl gegevensgebruikers voor deze eerder vermelde uitdagingen staan, hebben gebruikers die verantwoordelijk zijn voor het maken en onderhouden van gegevensassets hun eigen uitdagingen:

* Het annoteren van gegevensbronnen met beschrijvende metagegevens is vaak verspilde moeite. Clienttoepassingen negeren doorgaans beschrijvingen die zijn opgeslagen in de gegevensbron.
* Het maken van documentatie voor gegevensbronnen kan lastig zijn, en het is een constante verantwoordelijkheid om documentatie synchroon te houden met gegevensbronnen. Gebruikers hebben mogelijk geen vertrouwen in documentatie die als verouderd wordt ervaren.
* Het maken en onderhouden van documentatie voor gegevensbronnen is complex en tijdrovend. Dit geldt des te meer voor het beschikbaar maken van die documentatie voor iedereen die gebruikmaakt van de gegevensbron.
* Het is van groot belang om toegang tot gegevensbronnen te beperken en ervoor te zorgen dat gegevensgebruikers weten hoe ze om toegang kunnen vragen.

Gezamenlijk vormen deze uitdagingen een aanzienlijke belemmering voor bedrijven die gebruik van en kennis over zakelijke gegevens willen aansporen en promoten.

## <a name="discovery-challenges-for-security-administrators"></a>Problemen met detectie voor beveiligingsbeheerders

Gebruikers die verantwoordelijk zijn voor het waarborgen van de beveiliging van de gegevens van hun organisatie, kunnen als gegevensgebruikers en -producenten met de hierboven genoemde uitdagingen worden geconfronteerd, maar daarnaast ook nog eens met de volgende uitdagingen:

* De hoeveelheid gegevens van een organisatie groeit voortdurend in nieuwe richtingen. Ook worden gegevens in alle mogelijke richtingen opgeslagen en gedeeld. De taak voor het detecteren, beveiligen en beheren van uw gevoelige gegevens is een taak die nooit eindigt. U wilt er zeker van zijn dat de inhoud van uw organisatie wordt gedeeld met de juiste personen en toepassingen, en via de juiste machtigingen.
* Inzicht in de risiconiveaus van de gegevens van uw organisatie vereist dat u goed naar uw inhoud kijkt en daarbij vooral let op trefwoorden, RegEx-patronen en/of gevoelige gegevenstypen. Voorbeelden van gevoelige gegevenstypen zijn creditcardnummers, sociaal-fiscale nummers of bankrekeningnummers. U moet voortdurend alle gegevensbronnen voor gevoelige inhoud bewaken, omdat zelfs het minste geringste gegevensverlies uw organisatie onherstelbare schade kan berokkenen.
* Ervoor zorgen dat uw organisatie aan het beveiligingsbeleid blijft voldoen is een uitdagende taak, omdat uw inhoud groeit en verandert, en omdat deze vereisten en beleidsregels worden bijgewerkt om aan de steeds veranderende digitale werkelijkheid te blijven voldoen. Beveiligingsbeheerders krijgen vaak de taak toebedeeld om zo snel mogelijk voor gegevensbeveiliging te zorgen.

## <a name="azure-purview-advantages"></a>Voordelen van Azure Purview

Azure Purview is ontworpen om de problemen die in de vorige secties werden genoemd, te verhelpen en ervoor te zorgen dat ondernemingen het meeste uit hun bestaande gegevensassets halen. De catalogus helpt door te zorgen dat gegevensbronnen gemakkelijk kunnen worden gedetecteerd en begrijpelijk zijn voor de gebruikers die met de gegevens omgaan.

Azure Purview levert een cloudservice waarin gegevensbronnen kunnen worden geregistreerd. Tijdens de registratie blijven de gegevens op de huidige locatie, maar een kopie van de metagegevens wordt toegevoegd aan Azure Purview, samen met een verwijzing naar de locatie van de gegevensbron. Deze metagegevens worden ook geïndexeerd zodat elke gegevensbron gemakkelijk kan worden gedetecteerd via zoekopdrachten, en begrijpelijk is voor gebruikers die de gegevensbron detecteren.

Nadat u een gegevensbron hebt geregistreerd, kunt u de metagegevens ervan verrijken. De metagegevens worden toegevoegd door de gebruiker die de gegevensbron heeft geregistreerd of door een andere gebruiker in de onderneming. Elke gebruiker kan aantekeningen toevoegen aan een gegevensbron door beschrijvingen, tags of andere metagegevens in te voeren voor het aanvragen van toegang tot gegevensbronnen. Deze beschrijvende metagegevens vormen een aanvulling op de structurele metagegevens (zoals kolomnamen en gegevenstypen) die zijn geregistreerd vanuit de gegevensbron.

Het primaire doel van het registreren van de bronnen is het detecteren en begrijpen van gegevensbronnen en het gebruik ervan. Zakelijke gebruikers hebben mogelijk gegevens nodig voor bedrijfsinformatie, ontwikkeling van toepassingen, data science of een andere taak waarbij de juiste gegevens vereist zijn. Ze gebruiken de detectiefunctie van de gegevenscatalogus om snel te zoeken naar gegevens die ze nodig hebben, de gegevens te beoordelen op geschiktheid. Ze gebruiken de gegevens door het openen van de gegevensbron in een hulpprogramma naar keuze.

Tegelijkertijd kunnen gebruikers bijdragen aan de catalogus door tags, documentatie en aantekeningen te maken voor gegevensbronnen die al zijn geregistreerd. Ze kunnen ook nieuwe gegevensbronnen registreren, die vervolgens worden gevonden, begrepen en gebruikt door de community van catalogusgebruikers.

## <a name="next-steps"></a>Volgende stappen

Zie [Een Azure Purview-account maken](create-catalog-portal.md) als u aan de slag wilt met Azure Purview.
