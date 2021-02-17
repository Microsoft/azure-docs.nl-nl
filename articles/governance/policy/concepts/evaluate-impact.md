---
title: De impact van een nieuwe Azure Policy definitie evalueren
description: Inzicht in het proces dat moet worden gevolgd wanneer een nieuwe beleids definitie wordt geïntroduceerd in uw Azure-omgeving.
ms.date: 10/05/2020
ms.topic: conceptual
ms.openlocfilehash: cf52d25aa846388bc387430913a733d5206df82e
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100590805"
---
# <a name="evaluate-the-impact-of-a-new-azure-policy-definition"></a>De impact van een nieuwe Azure Policy definitie evalueren

Azure Policy is een krachtig hulp programma voor het beheer van uw Azure-resources op bedrijfs standaarden en om te voldoen aan de vereisten voor naleving. Als mensen, processen of pijp lijnen resources maken of bijwerken, Azure Policy de aanvraag beoordeelt. Wanneer het effect van de beleids definitie [wijzigt](./effects.md#modify), [toevoegen](./effects.md#deny) of [DeployIfNotExists](./effects.md#deployifnotexists), wordt de aanvraag door het beleid gewijzigd of toegevoegd. Wanneer het beleid van de beleids definitie wordt [gecontroleerd](./effects.md#audit) of [AuditIfNotExists](./effects.md#auditifnotexists), wordt een vermelding in het activiteiten logboek gemaakt voor nieuwe en bijgewerkte resources. En wanneer het effect van de beleids definitie is [geweigerd](./effects.md#deny), stopt het beleid het maken of de wijziging van de aanvraag.

Deze resultaten zijn precies zo nodig als u weet dat het beleid correct is gedefinieerd. Het is echter belang rijk om een nieuw beleid te valideren, zoals bedoeld voordat het werk kan worden gewijzigd of geblokkeerd. De validatie moet ervoor zorgen dat alleen de beoogde resources niet-compatibel zijn en dat er geen resources zijn die onjuist zijn opgenomen (ook wel een _valse waarde-positief_) in de resultaten.

De aanbevolen benadering voor het valideren van een nieuwe beleids definitie is door de volgende stappen te volgen:

- Uw beleid nauw keurig definiëren
- Uw bestaande resources controleren
- Nieuwe of bijgewerkte resource aanvragen controleren
- Uw beleid implementeren voor resources
- Doorlopende bewaking

## <a name="tightly-define-your-policy"></a>Uw beleid nauw keurig definiëren

Het is belang rijk om te begrijpen hoe het bedrijfs beleid wordt geïmplementeerd als een beleids definitie en de relatie van Azure-resources met andere Azure-Services. Deze stap wordt uitgevoerd door [de vereisten te identificeren](../tutorials/create-custom-policy-definition.md#identify-requirements) en [de bron eigenschappen te bepalen](../tutorials/create-custom-policy-definition.md#determine-resource-properties).
Maar het is ook belang rijk dat u verder gaat dan de smalle definitie van uw bedrijfs beleid. Is de beleids status bijvoorbeeld ' alle Virtual Machines moet... '? Wat is de informatie over andere Azure-Services die gebruikmaken van Vm's, zoals HDInsight of AKS? Wanneer u een beleid definieert, moeten we overwegen hoe dit beleid gevolgen heeft voor de resources die door andere services worden gebruikt.

Daarom moeten uw beleids definities net zo nauw keurig worden gedefinieerd en zijn gericht op de resources en de eigenschappen die u moet evalueren voor naleving.

## <a name="audit-existing-resources"></a>Bestaande resources controleren

Voordat u nieuwe of bijgewerkte resources met de nieuwe beleids definitie gaat beheren, is het raadzaam om te zien hoe een beperkte subset van bestaande resources, zoals een test resource groep, wordt geëvalueerd. Gebruik de [afdwingings modus](./assignment-structure.md#enforcement-mode) _uitgeschakeld_ (DoNotEnforce) op uw beleids toewijzing om te voor komen dat het [effect](./effects.md) van trigger-of activiteiten logboek vermeldingen wordt gemaakt.

Met deze stap kunt u de nalevings resultaten van het nieuwe beleid evalueren op bestaande resources zonder dat dit van invloed is op de werk stroom. Controleer of er geen compatibele resources zijn gemarkeerd als niet-compatibel (fout _positief_) en dat alle resources die u verwacht niet-compatibel zijn, correct zijn gemarkeerd.
Nadat de eerste subset van resources op de verwachte manier wordt gevalideerd, wordt de evaluatie van alle bestaande resources langzaam uitgebreid.

Door bestaande resources op deze manier te evalueren, kunt u niet-compatibele resources herstellen voordat de volledige implementatie van het nieuwe beleid wordt toegepast. Deze opschoning kan hand matig of via een [herstel taak](../how-to/remediate-resources.md) worden uitgevoerd als het effect van de beleids definitie _DeployIfNotExists_ is.

## <a name="audit-new-or-updated-resources"></a>Nieuwe of bijgewerkte resources controleren

Zodra u hebt gecontroleerd of de nieuwe beleids definitie correct wordt gerapporteerd voor bestaande resources, is het tijd om te kijken naar de impact van het beleid wanneer resources worden gemaakt of bijgewerkt. Als de beleids definitie effect parameterisering ondersteunt, gebruikt u [controle](./effects.md#audit). Met deze configuratie kunt u het maken en bijwerken van resources bewaken om te controleren of de nieuwe beleids definitie een vermelding in het Azure-activiteiten logboek activeert voor een resource die niet compatibel is, zonder dat dit van invloed is op bestaande werk of aanvragen.

Het wordt aanbevolen om nieuwe bronnen bij te werken en te maken die overeenkomen met uw beleids definitie om te zien of het _controle_ -effect correct wordt geactiveerd wanneer dit wordt verwacht. Bevindt zich op de Lookout voor resource aanvragen die niet van invloed moeten zijn op de nieuwe beleids definitie die het _controle_ -effect triggert.
Deze resources die van invloed zijn op een ander voor beeld van fout- _positieven_ en moeten worden opgelost in de beleids definitie voordat de volledige implementatie kan worden geïmplementeerd.

In het geval dat de beleids definitie in deze fase van de test wordt gewijzigd, wordt het aanbevolen het validatie proces te starten met de controle van bestaande resources. Een wijziging in de beleids definitie voor een _onjuiste positieve waarde_ op nieuwe of bijgewerkte resources is waarschijnlijk ook van invloed op bestaande resources.

## <a name="deploy-your-policy-to-resources"></a>Uw beleid implementeren voor resources

Nadat u de validatie van de nieuwe beleids definitie met zowel bestaande resources als nieuwe of bijgewerkte resource aanvragen hebt voltooid, begint u met het proces van de implementatie van het beleid. Het is raadzaam om de beleids toewijzing voor de nieuwe beleids definitie eerst aan een subset van alle resources toe te voegen, zoals een resource groep. Nadat de eerste implementatie is gevalideerd, breidt u het bereik van het beleid uit naar bredere en bredere niveaus, zoals abonnementen en beheer groepen. Deze uitbrei ding wordt bereikt door de toewijzing te verwijderen en een nieuwe te maken in de doel scopes totdat deze is toegewezen aan het volledige bereik van resources die worden gedekt door uw nieuwe beleids definitie.

Als er tijdens de implementatie resources worden gevonden die van de nieuwe beleids definitie moeten worden uitgesloten, moet u deze op een van de volgende manieren aanpakken:

- De beleids definitie bijwerken zodat deze beter is om onbedoelde gevolgen te verminderen
- Het bereik van de beleids toewijzing wijzigen (door een nieuwe toewijzing te verwijderen en te maken)
- De groep resources toevoegen aan de uitsluitings lijst voor de beleids toewijzing

Wijzigingen in het bereik (niveau of uitsluitingen) moeten volledig worden gevalideerd en worden gecommuniceerd met uw beveiligings-en nalevings organisaties om ervoor te zorgen dat er geen hiaten in de dekking zijn.

## <a name="monitor-your-policy-and-compliance"></a>Uw beleid en naleving controleren

Het implementeren en toewijzen van de beleids definitie is niet de laatste stap. Bewaak voortdurend het [compatibiliteits](../how-to/get-compliance-data.md) niveau van resources naar uw nieuwe beleids definitie en Setup die geschikt is [Azure monitor waarschuwingen en meldingen](../../../azure-monitor/alerts/alerts-overview.md) voor wanneer niet-compatibele apparaten worden geïdentificeerd. Het is ook raadzaam om de beleids definitie en gerelateerde toewijzingen te evalueren volgens een geplande basis voor het valideren van de beleids definitie, het bedrijfs beleid en de vereisten voor naleving. U moet beleids regels verwijderen als u deze niet meer nodig hebt. Beleids regels moeten ook van tijd tot tijd worden bijgewerkt, omdat de onderliggende Azure-resources worden ontwikkeld en nieuwe eigenschappen en mogelijkheden kunnen worden toegevoegd.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de structuur van de [beleids definitie](./definition-structure.md).
- Meer informatie over de structuur van de [beleids toewijzing](./assignment-structure.md).
- Meer informatie over het [programmatisch maken van beleids regels](../how-to/programmatically-create.md).
- Meer informatie over het [ophalen van compatibiliteits gegevens](../how-to/get-compliance-data.md).
- Ontdek hoe u [niet-compatibele resources kunt herstellen](../how-to/remediate-resources.md).
- Bekijk wat een beheer groep is met [het organiseren van uw resources met Azure-beheer groepen](../../management-groups/overview.md).