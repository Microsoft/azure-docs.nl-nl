---
title: Wat is een Ontology?
titleSuffix: Azure Digital Twins
description: Meer informatie over DTDL Industry Ontologies voor het model leren van een bepaald domein
author: baanders
ms.author: baanders
ms.date: 2/12/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 3393856b25040cff603ea2ef51e8adbcba78dc26
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102034690"
---
# <a name="what-is-an-ontology"></a>Wat is een Ontology? 

De woorden lijst van een Azure Digital Apparaatdubbels-oplossing is gedefinieerd met behulp van [modellen](concepts-models.md), waarin de typen entiteiten worden beschreven die in uw omgeving aanwezig zijn.

Als uw oplossing is gekoppeld aan een bepaalde branche, is het soms eenvoudiger en effectiever om te beginnen met een set modellen voor die branche die al bestaat, in plaats van uw eigen modelset helemaal zelf te ontwerpen. Deze vooraf bestaande model sets worden **Ontologies** genoemd. 

Over het algemeen is een Ontology een set modellen voor een bepaald domein, zoals een bouw structuur, IoT-systeem, Smart City, het energie raster, webinhoud, enzovoort. Ontologies worden vaak gebruikt als schema's voor kennis grafieken, aangezien ze het volgende kunnen inschakelen:
* Harmonisatie van software onderdelen, Documentatie, query Bibliotheken, enzovoort.
* Gereduceerde investeringen in conceptuele modellen en systeem ontwikkeling
* Eenvoudiger gegevens interoperabiliteit op een semantisch niveau
* Best practices opnieuw gebruiken, in plaats van helemaal te beginnen of ' het wiel opnieuw te onderbrengen '

In dit artikel wordt uitgelegd waarom en hoe u Ontologies kunt gebruiken voor uw Azure Digital Apparaatdubbels-modellen, en welke Ontologies en hulpprogram ma's voor hen beschikbaar zijn.

## <a name="using-ontologies-for-azure-digital-twins"></a>Ontologies voor Azure Digital Apparaatdubbels gebruiken

Ontologies bieden een geweldig uitgangs punt voor digitale dubbele oplossingen. Ze omvatten een set domein-specifieke modellen en relaties tussen entiteiten voor het ontwerpen, maken en parseren van een digitale dubbele grafiek. Ontologies kunnen ontwikkel aars van oplossingen in staat stellen een digitale apparaatdubbels-oplossing te starten vanaf een bewezen begin punt en zich richten op het oplossen van zakelijke problemen. De Ontologies van micro soft is ook ontworpen om eenvoudig te worden uitgebreid, zodat u ze kunt aanpassen voor uw oplossing. 

Daarnaast kunt u met deze ontologies in uw oplossingen ze instellen voor een naadloze integratie tussen verschillende partners en leveranciers, omdat Ontologies een gemeen schappelijke vocabulaire voor alle oplossingen kan bieden.

Omdat modellen in azure Digital Apparaatdubbels worden weer gegeven in de [Digital Apparaatdubbels Definition Language (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md), worden Ontologies voor gebruik met Azure Digital apparaatdubbels ook geschreven in DTDL. 

## <a name="strategies-for-integrating-ontologies"></a>Strategieën voor het integreren van Ontologies

Er zijn drie mogelijke strategieën voor het integreren van industrie-standaard Ontologies met DTDL. U kunt de naam kiezen die voor u het beste werkt, afhankelijk van uw behoeften:

| Strategie | Description | Resources |
| --- | --- | --- |
| **Adopteren** | U kunt uw oplossing starten met een open-source DTDL-Ontology dat is gebouwd op veel aangenomen industrie normen. U kunt deze model sets out-of-the-box gebruiken of ze uitbreiden met uw eigen toevoegingen voor een aangepaste oplossing. | [*Concepten: &nbsp; Ontologies van de &nbsp; industrie &nbsp; standaard aannemen*](concepts-ontologies-adopt.md)<br><br>[*Concepten: &nbsp; uitbrei ding van &nbsp; Ontologies*](concepts-ontologies-extend.md) |
| **Noten** | Als u al bestaande modellen in een andere standaard indeling hebt weer gegeven, kunt u deze converteren naar DTDL om ze te gebruiken met Azure Digital Apparaatdubbels. | [*Concepten: &nbsp; &nbsp; Ontologies converteren*](concepts-ontologies-convert.md)<br><br>[*Concepten: &nbsp; uitbrei ding van &nbsp; Ontologies*](concepts-ontologies-extend.md) |
| **Auteur** | U kunt uw eigen aangepaste DTDL-modellen altijd helemaal zelf ontwikkelen met behulp van toepasselijke industrie normen als inspiratie. | [*Concepten: DTDL-modellen*](concepts-models.md) |

### <a name="using-ontology-strategies-in-a-model-development-path"></a>Ontology-strategieën gebruiken in een ontwikkel traject voor modellen

Ongeacht de strategie die u kiest voor het integreren van een Ontology in azure Digital Apparaatdubbels, kunt u het volledige pad hieronder volgen om u te helpen bij het maken en uploaden van uw Ontology als DTDL-modellen.

1. Begin met het controleren en begrijpen van [DTDL-modellen in azure Digital apparaatdubbels](concepts-models.md).
1. Ga verder met de gekozen strategie voor Ontology-integratie van boven: uw modellen op basis van uw Ontology [**aannemen**](concepts-ontologies-adopt.md), [**converteren**](concepts-ontologies-convert.md)of [**ontwerpen**](concepts-models.md) .
    1. Als dat nodig is, [breidt](concepts-ontologies-extend.md) u uw Ontology uit om deze aan uw behoeften aan te passen.
1. [Valideer](how-to-parse-models.md) uw modellen om te controleren of ze werken met DTDL documenten.
1. Upload uw voltooide modellen naar Azure Digital Apparaatdubbels, met behulp van de [api's](how-to-manage-model.md#upload-models) of een voor beeld zoals het [Azure Digital apparaatdubbels model Uploader](https://github.com/Azure/opendigitaltwins-building-tools/tree/master/ModelUploader).

Daarna kunt u uw modellen gebruiken in uw Azure Digital Apparaatdubbels-exemplaar. 

U kunt ze visualiseren met voor beelden zoals [Azure Digital Apparaatdubbels Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) of [Azure Digital apparaatdubbels model visualer](https://github.com/Azure/opendigitaltwins-building-tools/tree/master/AdtModelVisualizer), of door over te stappen om [digitale apparaatdubbels](concepts-twins-graph.md)te maken.

## <a name="next-steps"></a>Volgende stappen

Lees meer over de strategieën voor het aannemen, converteren en ontwerpen van Ontologies:
* [*Concepten: industrie standaard Ontologies*](concepts-ontologies-adopt.md)
* [*Concepten: Ontologies converteren*](concepts-ontologies-convert.md)
* [*Procedure: DTDL-modellen beheren*](how-to-manage-model.md)

Of leer hoe modellen worden gebruikt voor het maken van Digital apparaatdubbels: [*concepten: Digital apparaatdubbels en het dubbele diagram*](concepts-twins-graph.md).