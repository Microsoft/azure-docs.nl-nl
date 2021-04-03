---
title: 'Veelgestelde vragen: SRE en code ring | Microsoft Docs'
titleSuffix: Azure
description: 'Veelgestelde vragen: informatie over de relatie tussen SRE en code ring'
author: dnblankedelman
manager: ScottCa
ms.service: site-reliability-engineering
ms.topic: article
ms.date: 09/14/2020
ms.author: dnb
ms.openlocfilehash: b8865fdd53f4947b17a3621a128fc83f3d93d3e2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "90089068"
---
# <a name="frequently-asked-questions-do-i-need-to-know-how-to-code-to-get-involved-with-sre"></a>Veelgestelde vragen: Ik wil graag weten hoe ze kunnen worden gebruikt om aan de slag te gaan met SRE?

Wanneer een persoon overweegt om aan de slag te gaan met de SRE en teams, is het raadzaam om de SRE-procedures uit te voeren.

Het korte antwoord: Ja. 

Het volledige antwoord is echter nog iets complexer. Laten we eens kijken naar drie locaties waar de code ring in de site betrouw baarheid speelt, samen met het niveau van de benodigde coderings expertise. Deze lijst is niet compleet, maar deze scenario's zijn een aantal van de meest voorkomende gebruiks voorbeelden.

## <a name="scenario-1-removing-toil-through-automation"></a>Scenario 1: toil verwijderen via Automation

Site betrouw baarheid van technici en andere die gebruikmaken van SRE-procedures proberen waar mogelijk om toil te verwijderen. "Toil" betekent een specifiek ding in SRE. Toil verwijst naar bewerkingen die worden uitgevoerd door mensen die bepaalde kenmerken hebben. Toil heeft geen meerwaarde op de lange termijn. Het helpt de service niet op een betekenisvolle manier vooruit. Het is vaak repetitief en grotendeels handmatig (hoewel het kan worden geautomatiseerd). Naarmate de service of het systeem groter wordt na verloop van tijd, neemt het aantal aanvragen voor dat systeem waarschijnlijk ook evenredig toe en is er zelfs nog meer handmatig werk vereist.

Als een service bijvoorbeeld vereist dat het team van SRE elke week opnieuw moet worden ingesteld, of om nieuwe accounts en schijf ruimte hand matig in te richten of door de hand matig opnieuw moet worden opgestart, is dit een operationele belasting die toil is. Het uitvoeren van deze acties heeft de service niet op een langdurige, permanente manier beter gemaakt. Deze acties moeten waarschijnlijk steeds worden herhaald.

SRE's hebben een hekel aan toil. Ze proberen om het zoveel en indien mogelijk te voorkomen. Dit is een van de gevallen waarin automatisering een rol gaat spelen in SRE. Als deze aanvragen automatisch kunnen worden verwerkt, wordt het team vrijgemaakt om te werken aan meer beloningen en nadelige dingen.

*Codeer expertise*: Automation vereist enige coderings expertise, maar hoeft geen volledige software engineering-vaardig heden te vereisen. Als u kleine scripts kunt schrijven (mogelijk in Power shell of Bourne shell) of zelfs als u een [https://docs.microsoft.com/azure/logic-apps/logic-apps-overview](Azure Logic app) met een wille keurige code maakt, kan deze app nog steeds helpen bij het elimineren van toil.

## <a name="scenario-2-control-through-apisdomain-specific-languages-dslstemplates"></a>Scenario 2: Control through Api's/Domain Specific talen (DSLs)/templates

Hoewel het niet strikt nood zakelijk is voor SRE, is het mogelijk om omgevingen te beheren via Api's, DSLs en sjablonen (met name Cloud omgevingen) zodat SREs hun werk kan opschalen. Het inrichten/ongedaan maken van de inrichting van de infra structuur, het configureren van bewaking en het integreren van verschillende services wordt veel efficiënter via code ring.

*Coderings kennis*: zoals in het vorige scenario, hebt u een aantal coderings expertise nodig, maar hoeft u geen volledige software engineering-vaardig heden te vereisen. Naast de hierboven genoemde scripts en logische apps, [https://docs.microsoft.com/azure/azure-resource-manager/templates/overview](Azure Resource Manager Templates) kan ook worden gebruikt met minimale coderings ervaring.

## <a name="scenario-3-fixing-the-code"></a>Scenario 3: de code herstellen

Site betrouwbaarheids technici kijken om de betrouw baarheid van een systeem te verbeteren. Deze doel stelling vereist soms Blijf spitten in de bron code van een systeem, het bepalen van het probleem en het oplossen van een correctie in de code basis. Hoewel het niveau van verfijning van deze werk soort groot kan variëren op basis van de situatie, is het coderen van expertise een afdoende vereiste.

*Codeer kennis*: volledige software techniek is vaak vereist in dit scenario.


## <a name="next-steps"></a>Volgende stappen

Wilt u meer weten over de betrouw baarheid en de hoeveelheid werk van de site? Bekijk onze [site betrouwbaarheids techniek hub](../index.yml), de bovenstaande product documentatie.
