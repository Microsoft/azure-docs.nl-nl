---
title: Meer informatie over de functies van zakelijke woorden lijst in azure controle sfeer liggen (preview)
description: In dit artikel wordt uitgelegd wat een zakelijke woorden lijst is in azure controle sfeer liggen.
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/13/2020
ms.openlocfilehash: 8b391438d8d6605e7ef493a6552af634db840ad5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96553254"
---
# <a name="understand-business-glossary-features-in-azure-purview"></a>Meer informatie over de functies van zakelijke woorden lijst in azure controle sfeer liggen

Dit artikel bevat een overzicht van de functie voor zakelijke woorden lijst in azure controle sfeer liggen. 

## <a name="business-glossary"></a>Zakelijke woordenlijst

Een woorden lijst geeft een vocabulaire voor zakelijke gebruikers.  Het bestaat uit zakelijke voor waarden die aan elkaar kunnen worden gerelateerd en kunnen worden gecategoriseerd zodat ze in verschillende contexten kunnen worden begrepen. Deze voor waarden kunnen vervolgens worden toegewezen aan assets zoals een Data Base, tabellen, kolommen, enzovoort. Dit helpt bij het samen stellen van het technische jargon dat is gekoppeld aan de gegevensopslag plaatsen en biedt de zakelijke gebruiker de mogelijkheid om gegevens in de vocabulaire te ontdekken en ermee te werken.


Een zakelijke woorden lijst is een verzameling voor waarden. Elke term vertegenwoordigt een object in een organisatie. het is zeer waarschijnlijk dat er meerdere termen zijn die hetzelfde object vertegenwoordigen. Een klant kan ook worden aangeduid als client, verkoper of koper. Deze meerdere termen hebben een relatie met elkaar. De relatie tussen deze termen kan een van de volgende zijn:

- synoniemen-verschillende termen met dezelfde definitie
- Verwante-andere naam met soort gelijke definitie

Dezelfde term kan ook meerdere zakelijke objecten impliceren. Het is belang rijk dat elke term goed is gedefinieerd en duidelijk wordt begrepen binnen de organisatie.

## <a name="custom-attributes"></a>Aangepaste kenmerken

Azure controle sfeer liggen ondersteunt acht out-of-the-box-kenmerken voor elke bedrijfs woordenlijst term:
- Name
- Definitie
- Data-webbronnen
- Data experts
- Acroniem
- Synoniemen
- Gerelateerde voor waarden
- Resources

Deze kenmerken kunnen niet worden bewerkt of verwijderd. Deze kenmerken zijn echter niet voldoende voor een volledige definitie van een term in een organisatie. Controle sfeer liggen biedt een functie waarmee u aangepaste kenmerken voor uw woorden lijst kunt definiëren om dit probleem op te lossen.

## <a name="term-templates"></a>Term sjablonen

Term sjablonen bieden aangepaste kenmerken van een woorden lijst die logisch kunnen worden gegroepeerd in de catalogus. Met de functie kunt u alle relevante aangepaste kenmerken groeperen in een sjabloon en vervolgens de sjabloon Toep assen tijdens het maken van de woordenlijst term. Alle aan de financiën gerelateerde aangepaste kenmerken, zoals kosten centrum, profit center, kunnen bijvoorbeeld worden gegroepeerd in een sjabloon voor sjabloon financiering en de sjabloon Financiën kan worden gebruikt om financiële woordenlijst termen te maken.

Alle standaard kenmerken zijn gegroepeerd in een systeem standaard sjabloon. Elke term sjabloon die u maakt, bevat deze kenmerken samen met eventuele aanvullende aangepaste kenmerken die zijn gemaakt als onderdeel van het maken van een sjabloon.

## <a name="glossary-vs-classification-vs-sensitivity-labels"></a>Verklarende woorden lijst versus classificatie labels versus gevoeligheid

Hoewel de termen van een woorden lijst, classificaties en labels aantekeningen zijn naar een gegevensasset, heeft elk daarvan een andere betekenis in de context van de catalogus. 

### <a name="glossary"></a>Woordenlijst

Zoals hierboven vermeld, definieert de term bedrijfs woordenlijst de zakelijke woorden lijst voor een organisatie en helpt het om de kloof tussen de verschillende afdelingen in uw bedrijf te overbruggen.

### <a name="classifications"></a>Classificaties

Classificaties zijn aantekeningen die kunnen worden toegewezen aan entiteiten. Dankzij de flexibiliteit van classificaties kunt u deze gebruiken voor meerdere scenario's, zoals:

- informatie over de aard van de gegevens die zijn opgeslagen in de gegevensassets
- Toegangs beheer beleid definiëren

Controle sfeer liggen heeft vandaag meer dan 100 systeem classificaties en u kunt uw eigen classificaties definiëren in de catalogus. Als onderdeel van het scan proces worden deze classificaties automatisch gedetecteerd en toegepast op gegevensassets en schema's. U kunt ze echter op elk gewenst moment onderdrukken. De menselijke onderdrukkingen worden nooit vervangen door geautomatiseerde scans.

### <a name="sensitivity-labels"></a>Vertrouwelijkheidslabels

Gevoeligheids labels zijn een type aantekening waarmee u de gegevens van uw organisatie kunt classificeren en beveiligen, zonder de productiviteit en samen werking te belemmeren. Gevoeligheids labels worden gebruikt voor het identificeren van de categorieën van classificatie typen in uw organisatie gegevens en het groeperen van de beleids regels die u op elke categorie wilt Toep assen. Controle sfeer liggen maakt gebruik van dezelfde gevoelige informatie typen als Microsoft 365, waarmee u uw bestaande beveiligings beleid en-beveiliging kunt uitrekken in uw hele inhoud en gegevens. Dezelfde labels kunnen worden gedeeld tussen Microsoft Office producten en gegevensassets in controle sfeer liggen.

## <a name="next-steps"></a>Volgende stappen

- [Term sjablonen beheren](how-to-manage-term-templates.md)
- [Bladeren in de gegevens catalogus in azure controle sfeer liggen](how-to-browse-catalog.md)
