---
title: Query-eenheden in azure Digital Apparaatdubbels
titleSuffix: Azure Digital Twins
description: Inzicht in het facturerings concept van query-eenheden in azure Digital Apparaatdubbels
author: baanders
ms.author: baanders
ms.date: 8/14/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 0e1c5f08c4292e4f3dfec448d8bf54d5d5601840
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/29/2021
ms.locfileid: "99050495"
---
# <a name="query-units-in-azure-digital-twins"></a>Query-eenheden in azure Digital Apparaatdubbels 

Een Azure Digital Apparaatdubbels **query Unit (qu)** is een eenheid op aanvraag die wordt gebruikt voor het uitvoeren van uw [Azure Digital apparaatdubbels-query's](how-to-query-graph.md) met behulp van de [query-API](/rest/api/digital-twins/dataplane/query). 

De systeem bronnen, zoals CPU, IOPS en geheugen die nodig zijn om query bewerkingen uit te voeren die worden ondersteund door Azure Digital Apparaatdubbels, worden abstract. Hiermee kunt u in plaats daarvan het gebruik bijhouden in query-eenheden.

De hoeveelheid query eenheden die wordt gebruikt om een query uit te voeren, wordt beïnvloed door...

* de complexiteit van de query
* de grootte van de resultatenset (dus een query die 10 resultaten retourneert, verbruikt meer QUs dan een query met vergelijk bare complexiteit die slechts één resultaat retourneert)

In dit artikel wordt uitgelegd hoe u query-eenheden begrijpt en verbruik van query eenheden kunt bijhouden.

## <a name="find-the-query-unit-consumption-in-azure-digital-twins"></a>Het verbruik van de query-eenheid in azure Digital Apparaatdubbels zoeken

Wanneer u een query uitvoert met behulp van de Azure Digital Apparaatdubbels- [query-API](/rest/api/digital-twins/dataplane/query), kunt u de reactie header controleren om het aantal QUs bij te houden dat de query verbruikt. Zoek naar "query-kosten" in het antwoord teruggestuurd vanuit Azure Digital Apparaatdubbels.

Met de Azure Digital Apparaatdubbels [sdk's](how-to-use-apis-sdks.md) kunt u de header van de query lading extra heren van het wissel bare antwoord. In deze sectie wordt beschreven hoe u een query uitvoert voor Digital apparaatdubbels en hoe u het bewissel bare antwoord kunt herhalen om de koptekst van de query-toerekenbaar uit te pakken. 

Het volgende code fragment laat zien hoe u de query kosten kunt ophalen die worden gemaakt bij het aanroepen van de query-API. De antwoord pagina's worden dan eerst herhaald voor toegang tot de koptekst van de query en vervolgens wordt de digitale dubbele resultaten binnen elke pagina herhaald. 

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/getQueryCharges.cs":::

## <a name="next-steps"></a>Volgende stappen

Ga voor meer informatie over het uitvoeren van query's in azure Digital Apparaatdubbels naar:

* [*Concepten: query taal*](concepts-query-language.md)
* [*Instructies: een query uitvoeren op de dubbele grafiek*](how-to-query-graph.md)
* [Naslag documentatie voor query-API](/rest/api/digital-twins/dataplane/query/querytwins)

U kunt Azure Digital Apparaatdubbels-query limieten vinden in [*Azure Digital apparaatdubbels-service limieten*](reference-service-limits.md).