---
author: baanders
description: bestand opnemen voor Azure Digital Twins limieten
ms.service: digital-twins
ms.topic: include
ms.date: 4/8/2021
ms.author: baanders
ms.openlocfilehash: 34fec713c3764987f07bc7fb89ecb0a0d770a840
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107728014"
---
### <a name="functional-limits"></a>Functionele limieten

De volgende tabel bevat de functionele limieten van Azure Digital Twins. 

> [!TIP]
> Zie Aanbevolen procedures voor het ontwerpen van modellen voor aanbevelingen om binnen deze functionele limieten [te werken.](../articles/digital-twins/concepts-models.md#best-practices-for-designing-models)

| Gebied | Mogelijkheid | Standaardlimiet | Verstelbare? |
| --- | --- | --- | --- |
| Azure-resource | Aantal Azure Digital Twins in een regio, per abonnement | 10 | Yes |
| Digitale tweelingen | Aantal tweelingen in een Azure Digital Twins-instantie | 200.000 | Yes |
| Digitale tweelingen | Aantal binnenkomende relaties naar één tweeling | 5\.000 | No |
| Digitale tweelingen | Aantal uitgaande relaties van één tweeling | 5\.000 | No |
| Digitale tweelingen | Maximale grootte (van JSON-body in een PUT- of PATCH-aanvraag) van één tweeling | 32 kB | No |
| Digitale tweelingen | Maximale nettoladinggrootte van aanvraag | 32 kB | No | 
| Routering | Aantal eindpunten voor één Azure Digital Twins exemplaar | 6 | No |
| Routering | Aantal routes voor één Azure Digital Twins exemplaar | 6 | Yes |
| Modellen | Aantal modellen binnen één Azure Digital Twins instantie | 10.000 | Yes |
| Modellen | Aantal modellen dat kan worden geüpload in één API-aanroep | 250 | No |
| Modellen | Maximale grootte (van JSON-body in een PUT- of PATCH-aanvraag) van één model | 1 MB | No |
| Modellen | Aantal items dat op één pagina wordt geretourneerd | 100 | No |
| Query | Aantal items dat op één pagina wordt geretourneerd | 100 | Ja |
| Query’s uitvoeren | Aantal `AND`  /  `OR` expressies in een query | 50 | Ja |
| Query’s uitvoeren | Aantal matrixitems in een `IN`  /  `NOT IN` component | 50 | Ja |
| Query’s uitvoeren | Aantal tekens in een query | 8,000 | Ja |
| Query’s uitvoeren | Aantal `JOINS` in een query | 5 | Ja |

### <a name="rate-limits"></a>Frequentielimieten

De volgende tabel geeft de snelheidslimieten van verschillende API's weer.

| API | Mogelijkheid | Standaardlimiet | Verstelbare? |
| --- | --- | --- | --- |
| Model-API | Aantal aanvragen per seconde | 100 | Yes |
| Digital Twins API | Aantal leesaanvragen per seconde | 1000 | Yes |
| Digital Twins API | Aantal patchaanvragen per seconde | 1000 | Yes |
| Digital Twins API | Aantal maak-/verwijderbewerkingen per seconde voor **alle tweelingen en relaties** | 50 | Yes |
| Digital Twins-API | Aantal bewerkingen voor maken/bijwerken/verwijderen per seconde op **één dubbel** of de relaties ervan | 10 | No |
| Query-API | Aantal aanvragen per seconde | 500 | Yes |
| Query-API | [Query-eenheden](../articles/digital-twins/concepts-query-units.md) per seconde | 4000 | Yes |
| API voor gebeurtenisroutes | Aantal aanvragen per seconde | 100 | Yes |

### <a name="other-limits"></a>Andere limieten

Limieten voor gegevenstypen en velden in DTDL-documenten voor Azure Digital Twins-modellen vindt u in de specificatiedocumentatie in GitHub: [*Digital Twins Definition Language (DTDL) - versie 2.*](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md)
 
Details van querylatentie en andere querybeperkingen vindt u in [*How-to: Query the twin graph*](../articles/digital-twins/how-to-query-graph.md)(Query uitvoeren op de tweelinggrafiek).
