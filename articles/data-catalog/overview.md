---
title: Inleiding tot Azure Data Catalog
description: Dit artikel bevat een overzicht van Microsoft Azure Data Catalog, met inbegrip van de functies ervan en de problemen die Data Catalog oplost. Met Data Catalog kan elke gebruiker gegevensbronnen registreren, detecteren, begrijpen en gebruiken.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: overview
ms.date: 08/01/2019
ms.openlocfilehash: e128e9f7515e572fc0be4b92ef03d8d98529ac66
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104674900"
---
# <a name="what-is-azure-data-catalog"></a>Wat is Azure Data Catalog?
[!INCLUDE [Azure Purview redirect](../../includes/data-catalog-use-purview.md)]

Azure Data Catalog is een volledig beheerde cloudservice. Hiermee kunnen gebruikers de gegevensbronnen ontdekken die ze nodig hebben en inzicht krijgen in de gegevensbronnen die ze hebben gevonden. Tegelijkertijd helpt Data Catalog organisaties meer waarde te halen uit hun bestaande investeringen.

Met Data Catalog kan elke gebruiker (analist, gegevenswetenschapper of ontwikkelaar) gegevensbronnen opzoeken, begrijpen en gebruiken. Data Catalog bevat een crowdsourcingmodel met metagegevens en aantekeningen. Het is een centrale locatie waar alle gebruikers van een organisatie hun kennis kunnen bijdragen en kunnen bouwen aan een community en cultuur van gegevens.

## <a name="discovery-challenges-for-data-consumers"></a>Problemen met detectie voor gegevensgebruikers

Detectie van zakelijke gegevensbronnen is traditioneel een organisch proces op basis van specifieke kennis. Voor bedrijven die het meeste uit hun gegevensassets willen halen, leidt deze benadering tot talloze uitdagingen:

* Gebruikers weten mogelijk niet dat een gegevensbron bestaat, tenzij ze ermee in aanraking komen als onderdeel van een ander proces. Er is geen centrale locatie waar gegevensbronnen zijn geregistreerd.
* Als gebruikers de locatie van een gegevensbron weten, kunnen ze de gegevens niet benaderen met behulp van een clienttoepassing. Vanwege dataverbruik moeten gebruikers de verbindingsreeks of het pad kennen.
* Tenzij gebruikers de locatie van de documentatie voor een gegevensbron weten, weten ze niet wat het beoogde gebruik van de gegevens is. Gegevensbronnen en documentatie bevinden zich mogelijk op allerlei plaatsen en worden op verschillende manieren gebruikt.
* Als gebruikers vragen over een gegevensasset hebben, moeten ze de expert die of het team dat verantwoordelijk is voor de gegevens zien te vinden en ze offline benaderen. Er is geen expliciete relatie tussen gegevens en de deskundigen met een diep inzicht in het beoogde gebruik ervan.
* Tenzij een gebruiker het proces voor het aanvragen van toegang tot de gegevensbron begrijpt, kan hij met detectie van de gegevensbron en de bijbehorende documentatie nog steeds geen toegang krijgen tot de gegevens.

## <a name="discovery-challenges-for-data-producers"></a>Problemen met detectie voor gegevensproducenten

Terwijl gegevensgebruikers voor deze eerder vermelde uitdagingen staan, hebben gebruikers die verantwoordelijk zijn voor het maken en onderhouden van gegevensassets hun eigen uitdagingen:

* Het annoteren van gegevensbronnen met beschrijvende metagegevens is vaak verspilde moeite. Clienttoepassingen negeren doorgaans beschrijvingen die zijn opgeslagen in de gegevensbron.
* Het maken van documentatie voor gegevensbronnen is vaak verspilde moeite. Op het gesynchroniseerd houden van de documentatie met de gegevensbronnen, rust een grote en continue verantwoordelijkheid. Gebruikers hebben mogelijk geen vertrouwen in documentatie die wordt als verouderd wordt ervaren.
* Het maken en onderhouden van documentatie voor gegevensbronnen is complex en tijdrovend. Dit geldt des te meer voor het beschikbaar maken van die documentatie voor iedereen die gebruikmaakt van de gegevensbron.
* Het is van groot belang om toegang tot gegevensbronnen te beperken en ervoor te zorgen dat gegevensgebruikers weten hoe ze om toegang kunnen vragen.

Gezamenlijk vormen deze uitdagingen een aanzienlijke belemmering voor bedrijven die gebruik van en kennis over zakelijke gegevens willen aansporen en promoten.

## <a name="azure-data-catalog-can-help"></a>Azure Data Catalog kan helpen

Data Catalog is ontworpen om deze problemen te verhelpen en ervoor te zorgen dat ondernemingen het meeste uit hun bestaande gegevensassets kunnen halen. Data Catalog helpt door te zorgen dat gegevensbronnen gemakkelijk kunnen worden gedetecteerd en begrijpelijk zijn voor de gebruikers die met de gegevens omgaan.

Data Catalog levert een cloudservice waarin een gegevensbron kan worden geregistreerd. De gegevens blijven op de bestaande locatie, maar een kopie van de metagegevens wordt toegevoegd aan Data Catalog, samen met een verwijzing naar de locatie van de gegevensbron. Deze metagegevens worden ook geïndexeerd zodat elke gegevensbron gemakkelijk kan worden gedetecteerd via zoekopdrachten, en begrijpelijk is voor gebruikers die de gegevensbron detecteren.

Als een gegevensbron is geregistreerd, kunnen de metagegevens worden uitgebreid. De metagegevens kunnen worden toegevoegd door de gebruiker die ze heeft geregistreerd of door andere gebruikers in de onderneming. Elke gebruiker kan aantekeningen toevoegen aan een gegevensbron door beschrijvingen, tags of andere metagegevens in te voeren, zoals documentatie en processen voor het aanvragen van toegang tot gegevensbronnen. Deze beschrijvende metagegevens vormen een aanvulling op de structurele metagegevens (zoals kolomnamen en gegevenstypen) die zijn geregistreerd vanuit de gegevensbron.

Het primaire doel van het registreren van de bronnen is het detecteren en begrijpen van gegevensbronnen en het gebruik ervan. Zakelijke gebruikers hebben mogelijk gegevens nodig voor bedrijfsinformatie, ontwikkeling van toepassingen, data science of een andere taak waarbij de juiste gegevens vereist zijn. Ze kunnen de detectiefunctie van Data Catalog gebruiken om te snel zoeken naar gegevens die ze nodig hebben, de gegevens te beoordelen op geschiktheid en de gegevens gebruiken door het openen van de gegevensbron in een hulpprogramma naar keuze. 

Tegelijkertijd kunnen gebruikers bijdragen aan de catalogus door tags, documentatie en aantekeningen te maken voor gegevensbronnen die al zijn geregistreerd. Ze kunnen ook nieuwe gegevensbronnen registreren, die vervolgens kunnen worden gevonden, begrepen en gebruikt door de community van catalogusgebruikers.

![Mogelijkheden van Data Catalog](./media/data-catalog-what-is-data-catalog/data-catalog-capabilities.png)

## <a name="learn-more-about-data-catalog"></a>Meer informatie over Data Catalog

Zie voor meer informatie over de mogelijkheden van Data Catalog:

* [Gegevensbronnen registreren](data-catalog-how-to-register.md)
* [Gegevensbronnen detecteren](data-catalog-how-to-discover.md)
* [Aantekeningen toevoegen aan gegevensbronnen](data-catalog-how-to-annotate.md)
* [Gegevensbronnen documenteren](data-catalog-how-to-documentation.md)
* [Verbinding maken met gegevensbronnen](data-catalog-how-to-connect.md)
* [Werken met big data](data-catalog-how-to-big-data.md)
* [Gegevensassets beheren](data-catalog-how-to-manage.md)
* [De zakelijke woordenlijst instellen](data-catalog-how-to-business-glossary.md)
* [Veelgestelde vragen](data-catalog-frequently-asked-questions.md)

## <a name="next-steps"></a>Volgende stappen

Aan de slag met Data Catalog:

* [Quickstart: Een Azure Data Catalog maken](data-catalog-get-started.md)
* [Open your Azure Data Catalog](https://www.azuredatacatalog.com) (Azure Data Catalog openen)