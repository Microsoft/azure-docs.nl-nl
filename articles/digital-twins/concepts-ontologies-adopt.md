---
title: Ontologies voor de industrie standaard vaststellen
titleSuffix: Azure Digital Twins
description: Meer informatie over bestaande industrie Ontologies die kunnen worden goedgekeurd voor Azure Digital Apparaatdubbels
author: baanders
ms.author: baanders
ms.date: 2/26/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: c4f003fc9e418501af76281c10277fce3606e24c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102124224"
---
# <a name="adopting-an-industry-ontology"></a>Een industrie Ontology aannemen

Omdat het eenvoudiger is om te beginnen met een open-source DTDL-Ontology dan vanaf een lege pagina, wordt micro soft door gegeven aan domein experts voor het publiceren van Ontologies, die veel geaccepteerde branche conventies vertegenwoordigen en ondersteuning biedt voor diverse klant gebruiks voorbeelden. 

Het resultaat is een set open-source DTDL-Ontologies, die leren van, bouwen op, leren van of rechtstreeks gebruik maakt van industrie normen. De Ontologies zijn ontworpen om te voldoen aan de behoeften van downstream-ontwikkel aars, met de mogelijkheid om veel te worden goedgekeurd en/of uitgebreid door de branche.

Op dit moment heeft micro soft met partners gewerkt aan het ontwikkelen van een Ontology voor [Smart gebouwen](#realestatecore-smart-building-ontology) en een Ontology voor [Smart steden](#smart-cities-ontology). Dit biedt een gemeen schappelijke grond slag voor model lering op basis van standaarden in deze branches om Reinvention te voor komen. 

## <a name="realestatecore-smart-building-ontology"></a>RealEstateCore slimme buil ding Ontology

*Zoek het Ontology hier: [**Digital Apparaatdubbels Definition Language RealEstateCore Ontology for Smart gebouwen**](https://github.com/Azure/opendigitaltwins-building)*.

Micro soft heeft een partnerschap geleverd met [RealEstateCore](https://www.realestatecore.io/), een Zweeds consortium van onroerend goed eigen aren, software leveranciers en onderzoek instituten, om deze open-source DTDL-Ontology te leveren voor de echte bedrijfs wereld.

Deze Ontology voor Smart gebouwen biedt gemeen schappelijke grond voor het maken van Smart-gebouwen, met behulp van industrie normen (zoals [Brick schema](https://brickschema.org/ontology/) of [W3C buil ding topologie Ontology](https://w3c-lbd-cg.github.io/bot/index.html)) om Reinvention te voor komen. De Ontology wordt ook geleverd met aanbevolen procedures voor het gebruik en de uitbrei ding ervan. 

Ga voor meer informatie over de structuur-en model conventies van de Ontology, hoe u deze kunt gebruiken, hoe u deze wilt uitbreiden en hoe u bijdragen kunt over de opslag plaats van Ontology op GitHub: [Azure/opendigitaltwins-buil ding](https://github.com/Azure/opendigitaltwins-building). 

Meer informatie over de samen werking met RealEstateCore en doel stellingen voor dit initiatief vindt u in dit blog bericht en begeleidende video: [RealEstateCore, een Smart buil ding Ontology for Digital apparaatdubbels, is nu beschikbaar](https://techcommunity.microsoft.com/t5/internet-of-things/realestatecore-a-smart-building-ontology-for-digital-twins-is/ba-p/1914794).

## <a name="smart-cities-ontology"></a>Smart steden Ontology

*Zoek de Ontology hier: [**Digital Apparaatdubbels Definition Language (DTDL) Ontology voor Smart steden**](https://github.com/Azure/opendigitaltwins-smartcities)*.

Micro soft heeft gewerkt met [Open Agile Smart steden (OASC)](https://oascities.org/) en [SIRUS](https://sirus.be/) om een DTDL Ontology te bieden voor Smart steden, vanaf [ETSI CIM NGSI-LD](https://www.etsi.org/committee/cim). Naast ETSI NGSI-LD hebben we ook Saref4City, CityGML, ISO en anderen geëvalueerd.

De huidige release van de Ontology is gericht op een initiële set modellen. De Ontology-auteurs helpen u bij het uitbreiden van de eerste set use cases en de bestaande modellen te verbeteren. 

Voor meer informatie over de Ontology, hoe u deze kunt gebruiken en hoe u kunt bijdragen, gaat u naar de opslag plaats van Ontology op GitHub: [Azure/opendigitaltwins-smartcities](https://github.com/Azure/opendigitaltwins-smartcities). 

U kunt ook meer lezen over de partnerschappen en benadering voor Smart steden in dit blog bericht en de bijbehorende video: [Smart steden Ontology for Digital apparaatdubbels](https://techcommunity.microsoft.com/t5/internet-of-things/smart-cities-ontology-for-digital-twins/ba-p/2166585).

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het uitbreiden van de industrie standaard Ontologies om te voldoen aan uw specificaties: [*concepten: uitbrei ding van industrie Ontologies*](concepts-ontologies-extend.md).

* U kunt ook door gaan met het pad voor het ontwikkelen van modellen op basis van Ontologies: [*het gebruik van Ontology-strategieën in een model ontwikkelings pad*](concepts-ontologies.md#using-ontology-strategies-in-a-model-development-path).