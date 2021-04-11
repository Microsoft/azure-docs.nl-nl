---
title: Algemene scenario’s Azure Data Catalog
description: Een overzicht van algemene scenario's voor Azure Data Catalog, met inbegrip van de registratie en detectie van gegevens bronnen met hoge waarde, het inschakelen van selfservice business intelligence en het vastleggen van bestaande kennis over gegevens bronnen en processen.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 4f76a9696d613a4a974c9ae7c7b2578e56f2fcfa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "104674611"
---
# <a name="azure-data-catalog-common-scenarios"></a>Algemene scenario’s Azure Data Catalog

[!INCLUDE [Azure Purview redirect](../../includes/data-catalog-use-purview.md)]

In dit artikel vindt u algemene scenario's waarin Azure Data Catalog uw organisatie kan helpen meer waarde uit de bestaande gegevens bronnen te halen.

## <a name="scenario-1-registration-of-central-data-sources"></a>Scenario 1: registratie van centrale gegevens bronnen
Organisaties hebben vaak veel gegevens bronnen met hoge waarde. Deze gegevens bronnen zijn onder andere line-of-Business, OLTP-systemen (online Trans Action processing), data warehouses en business intelligence/Analytics-data bases. Het aantal systemen, en de onderlinge overlap ping, groeit doorgaans in de loop van de tijd wanneer de bedrijfs behoeften ontwikkelen en het bedrijf zelf ontwikkelt, bijvoorbeeld fusies en acquisities.

Het kan lastig zijn voor leden van de organisatie om te weten waar de gegevens zich in deze gegevens bronnen bevinden. Vragen, zoals het volgende, zijn allemaal te algemeen:

* Van de drie HR-systemen die in het bedrijf worden gebruikt, wat ik moet gebruiken voor het maken van dit type rapport?
* Waar moet ik de gecertificeerde verkoop cijfers ophalen voor het boek jaar dat net is geëindigd?
* Wie moet ik vragen of wat is het proces dat ik moet gebruiken om toegang te krijgen tot het Data Warehouse?
* Ik weet niet of deze cijfers goed zijn. Wie kan ik vragen hoe deze gegevens moeten worden gebruikt voordat ik dit dash board met mijn team deel?

Op deze en andere vragen kunt Azure Data Catalog antwoorden geven. De centrale, IT-beheerde gegevens bronnen die worden gebruikt in verschillende organisaties, zijn vaak het logische begin punt voor het invullen van de catalogus. Hoewel elke gebruiker een gegevens bron kan registreren, waardoor de catalogus kan worden gestart met de gegevens bronnen die de meest waarschijnlijke waarde geven aan het grootste aantal gebruikers, helpt het systeem te implementeren en te gebruiken. 

Als u aan de slag gaat met Azure Data Catalog, het identificeren en registreren van belang rijke gegevens bronnen die worden gebruikt door veel verschillende teams van gegevens gebruikers, kan de eerste stap worden uitgevoerd.

Dit scenario biedt u ook de mogelijkheid om aantekeningen te maken op de gegevens bronnen met hoge waarden zodat ze gemakkelijker te begrijpen en toegankelijk zijn. Een belang rijk aspect van deze inspanning is het toevoegen van informatie over de manier waarop gebruikers toegang kunnen aanvragen tot de gegevens bron. Met Azure Data Catalog kunt u het e-mail adres opgeven van de gebruiker die of het team dat verantwoordelijk is voor het beheren van toegang tot de gegevens bron, koppelingen naar bestaande hulpprogram ma's of documentatie of vrije tekst die het Access-aanvraag proces beschrijft. Deze informatie helpt leden die geregistreerde gegevens bronnen detecteren, maar die nog geen machtigingen hebben om toegang te krijgen tot de gegevens om eenvoudig toegang te vragen met behulp van de processen die worden gedefinieerd en beheerd door de eigen aren van de gegevens bron.

## <a name="scenario-2-self-service-business-intelligence"></a>Scenario 2: Self-service business intelligence
Hoewel traditionele bedrijfs oplossingen van bedrijven nog steeds een waardevol deel uitmaken van de data landschappen van veel organisaties, heeft het veranderende tempo van het bedrijf zelf meer en belang rijker. Met self-service BI kunnen informatie medewerkers en analisten hun eigen rapporten, werkmappen en dash boards maken zonder dat ze gebruikmaken van een IT-team van de IT-afdeling of door de planning en beschik baarheid van het IT-team worden beperkt.

In self-service BI-scenario's combi neren gebruikers doorgaans gegevens uit meerdere bronnen, die veel mogelijk niet eerder zijn gebruikt voor BI en analyse. Hoewel sommige van deze gegevens bronnen mogelijk al bekend zijn, kan het lastig zijn om te ontdekken wat u kunt doen om mogelijke gegevens bronnen voor een bepaalde taak te zoeken en te evalueren.

Normaal gesp roken is dit detectie proces een hand matig: analisten gebruiken hun peer-netwerk verbindingen om anderen te identificeren die samen werken met de gegevens die worden aangevraagd. Nadat een gegevens bron is gevonden en gebruikt, herhaalt het proces zichzelf opnieuw voor elke volgende selfservice BI-inspanning, waarbij meerdere gebruikers een redundant hand matig proces van detectie uitvoeren.

Met Azure Data Catalog kan uw organisatie deze cyclus van moeite verstoren. Na het detecteren van een gegevens bron via een traditionele manier kan een analist deze registreren, zodat deze in de toekomst eenvoudiger kan worden gedetecteerd door andere gebruikers. Hoewel de analist meer waarde kan toevoegen door aantekeningen te maken op de geregistreerde gegevensassets, hoeft deze aantekening niet op hetzelfde moment als registratie te worden uitgevoerd. Gebruikers kunnen na verloop van tijd bijdragen, zoals hun schema's toestaan, waarmee de waarde geleidelijk wordt toegevoegd aan de gegevens bronnen die in de catalogus zijn geregistreerd.

Deze biologische groei van de catalogus inhoud is een natuurlijke aanvulling op de registratie vooraf van centrale gegevens bronnen. Het vooraf invullen van de catalogus met gegevens die veel gebruikers nodig hebben, kan een gebruiker zijn voor het eerste gebruik en detectie. Als u gebruikers de mogelijkheid wilt geven om zich te registreren en aantekeningen aan te maken, kunnen ze een manier hebben om ze te houden en andere leden van de organisatie.

Het is belang rijk dat hoewel dit scenario specifiek gericht is op self-service BI, dezelfde patronen en uitdagingen van toepassing zijn op grootschalige zakelijke BI-projecten. Met behulp van Data Catalog kan uw organisatie alle inspanningen verbeteren die een hand matig proces van het detecteren van gegevens bronnen inhouden.

## <a name="scenario-3-capturing-tribal-knowledge"></a>Scenario 3: tribale-kennis vastleggen
Hoe weet u welke gegevens u nodig hebt om uw taak uit te voeren en waar u die gegevens kunt vinden?

Als u al enige tijd in uw taak hebt, weet u het waarschijnlijk alleen. U hebt een geleidelijk leer proces door lopen en u hebt meer informatie over de gegevens bronnen die van belang zijn voor uw dagelijkse werk.

Hoe weet deze persoon welke gegevens voor de taak zijn vereist, wanneer een nieuwe werk nemer deelneemt aan uw team?

Conflicteert zijn, de nieuwe persoon komt met deze vragen.

Deze voortdurende overdracht van tribale kennis maakt deel uit van het detectie proces voor de gegevens bron in organisaties groot en klein. Meer senioren en ervaren team leden hebben kennis gemaakt over de jaren en nieuwere team leden hebben geleerd om hen te vragen wanneer ze vragen hebben. De meest essentiële informatie bestaat vaak alleen in de hoofden van enkele belang rijke personen, en wanneer deze mensen op vakantie zijn of het team verlaten, heeft de organisatie Last.

Data experts maken het mogelijk om hun kennis te documenteren, deze te delen via e-mail of in Word-documenten op een team share point-site. Hoewel deze aanpak waardevol kan zijn, wordt er een nieuw detectie probleem geïntroduceerd: Hoe weet men welke documentatie bestaat en waar u deze kunt vinden?

Met Azure Data Catalog heeft uw organisatie één centrale locatie voor het opslaan en delen van deze tribale kennis, en om het eenvoudig te kunnen detecteren. In Data Catalog kunnen uw gegevens specialisten rechtstreeks aantekeningen toevoegen aan gegevensassets en koppelingen naar bestaande documentatie bieden. Wanneer leden van een organisatie de catalogus gebruiken om een gegevens bron te detecteren, vinden ze niet alleen de bron zelf, maar ook de kennis die voorheen alleen aanwezig was in de experts van uw organisatie.
