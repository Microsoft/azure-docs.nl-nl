---
title: Veelgestelde vragen over Azure Resource Health
description: Overzicht van Azure Resource Health
ms.topic: conceptual
ms.date: 01/29/2019
ms.openlocfilehash: a5ad8867b110039e4dcc34b20c8a61fccfd39eaf
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2020
ms.locfileid: "97032861"
---
# <a name="azure-resource-health-faq"></a>Veelgestelde vragen over Azure Resource Health
Meer informatie over de antwoorden op veelgestelde vragen over Azure Resource Health.

## <a name="what-is-azure-resource-health"></a>Wat is Azure Resource Health?
Resource Health helpt u bij het diagnosticeren en krijgen van ondersteuning wanneer een Azure-probleem van invloed is op uw resources. Het biedt u informatie over de huidige en eerdere status van uw resources en helpt u problemen verhelpen. Resource Health biedt technische ondersteuning als u hulp nodig heeft bij problemen met Azure-services.  

## <a name="what-is-the-resource-health-intended-for"></a>Wat is de Resource Health bedoeld voor?
Als er een probleem met een bron is gedetecteerd, kunt u met Resource Health u de hoofd oorzaak van de fout vaststellen. Het biedt hulp bij het oplossen van het probleem en technische ondersteuning als u meer hulp nodig hebt bij problemen met Azure-Services.

## <a name="what-health-checks-are-performed-by-resource-health"></a>Welke status controles worden uitgevoerd door Resource Health?
Resource Health voert verschillende controles uit op basis van het [resource type](resource-health-checks-resource-types.md). Deze controles zijn ontworpen om drie soorten problemen te implementeren: 
- Niet-geplande gebeurtenissen, bijvoorbeeld een onverwachte host opnieuw opstarten
- Geplande gebeurtenissen, zoals geplande updates van host-besturings systemen
- Gebeurtenissen die worden geactiveerd door gebruikers acties, bijvoorbeeld een gebruiker die een virtuele machine opnieuw opstart

## <a name="what-does-each-of-the-health-status-mean"></a>Wat betekent elk van de integriteits status?
Er zijn drie verschillende statussen:
- Beschikbaar: er zijn geen bekende problemen in het Azure-platform die van invloed kunnen zijn op deze resource
- Niet beschikbaar: resource status heeft problemen gedetecteerd die van invloed zijn op de resource
- Onbekend: de resource status kan de status van een resource niet bepalen omdat deze geen informatie meer ontvangt. 

## <a name="what-does-the-unknown-status-mean-is-something-wrong-with-my-resource"></a>Wat betekent de onbekende status? Is er iets mis met mijn resource?
De status wordt ingesteld op onbekend wanneer Resource Health stopt met het ontvangen van informatie over een specifieke resource. Hoewel deze status geen definitieve indicatie is van de status van de resource, in gevallen waarin u problemen ondervindt, kan dit erop wijzen dat er een probleem is met Azure.

## <a name="how-can-i-get-help-for-a-resource-that-is-unavailable"></a>Hoe kan ik hulp krijgen bij een resource die niet beschikbaar is?
U kunt een ondersteunings aanvraag indienen via de Blade Resource Health. U hebt geen ondersteunings overeenkomst met micro soft nodig om een aanvraag te openen wanneer de resource niet beschikbaar is omdat er platform gebeurtenissen zijn.

## <a name="does-resource-health-differentiate-between-unavailability-caused-by-platform-problems-versus-something-i-did"></a>Is Resource Health onderscheid gemaakt tussen niet-beschik baarheid die is veroorzaakt door platform problemen en wat ik heb gedaan?
Ja, wanneer een bron niet beschikbaar is, Resource Health de hoofd oorzaak in een van deze categorieën identificeren: 
-   Door gebruiker gestarte actie
-   Geplande gebeurtenis 
-   Niet-geplande gebeurtenis

In de portal worden door de gebruiker gestarte acties weer gegeven met behulp van een blauw meldings pictogram, terwijl geplande en niet-geplande gebeurtenissen worden weer gegeven met een rood waarschuwings pictogram. Meer informatie vindt u in het [overzicht van resource Health](Resource-health-overview.md).  

## <a name="can-i-integrate-resource-health-with-my-monitoring-tools"></a>Kan Ik Resource Health integreren met mijn controle hulpprogramma's?
Resource Health [ondersteunt](resource-health-alert-arm-template-guide.md) waarschuwingen op basis van activiteiten Logboeken. Waarschuwingen voor activiteiten Logboeken gebruiken [actie groepen](../azure-monitor/platform/action-groups.md) om gebruikers ervan op de hoogte te stellen dat een waarschuwing is geactiveerd. Actie groepen bieden ondersteuning voor diverse meldings kanalen, zoals e-mail, SMS-, webhook-en ITSM-acties.

## <a name="where-do-i-find-resource-health"></a>Waar vind Ik Resource Health?
Nadat u zich hebt aangemeld bij de Azure Portal, zijn er meerdere manieren om toegang te krijgen tot Resource Health:
- Navigeer naar uw resource. Selecteer in de navigatie balk aan de linkerkant de optie **resource status**
- Ga naar de Blade Azure Service Health.  Selecteer in de navigatie balk aan de linkerkant de optie **resource status**.

U kunt ook de Resource Health-API gebruiken om informatie te verkrijgen over de status van uw resources.

## <a name="is-resource-health-available-for-all-resource-types"></a>Is Resource Health beschikbaar voor alle resource typen?
De lijst met status controles en resource typen die via Resource Health worden ondersteund, kunt u [hier](resource-health-checks-resource-types.md)vinden.

## <a name="what-should-i-do-if-my-resource-is-showing-available-but-i-believe-it-is-not"></a>Wat moet ik doen als mijn resource beschikbaar wordt weer gegeven, maar ik geloof het niet? "
Bij het controleren van de status van een resource kunt u met de rechter muisknop op **rapporteren onjuiste** status. Voordat u het rapport verzendt, hebt u de mogelijkheid om aanvullende informatie te verstrekken over waarom u denkt dat de huidige status onjuist is.

## <a name="is-resource-health-available-for-all-azure-regions"></a>Is Resource Health beschikbaar voor alle Azure-regio's? 
De resource status is beschikbaar in alle Azure-geografische gebieden.

## <a name="how-is-resource-health-different-from-azure-status-or-the-service-health-dashboard"></a>Hoe wijkt Resource Health af van de Azure-status of het Service Health-dash board?
De gegevens die worden verstrekt door Resource Health, zijn specifieker dan de status van Azure of het dash board Service Health.

Terwijl de [Azure-status](https://status.azure.com) en het service Health-dash board u informeren over Service problemen die van invloed zijn op een breed scala aan klanten (bijvoorbeeld een Azure-regio), worden er door resource Health meer gedetailleerde gebeurtenissen weer gegeven die alleen relevant zijn voor de specifieke resource. Als een host bijvoorbeeld onverwacht opnieuw wordt opgestart, wordt door Resource Health alleen de klanten gewaarschuwd waarvan de virtuele machines op die host werden uitgevoerd.

## <a name="do-i-need-to-activate-resource-health-for-each-resource"></a>Moet Ik Resource Health voor elke resource activeren?
Nee, status informatie is beschikbaar voor alle resource typen die beschikbaar zijn via Resource Health. 

## <a name="do-we-need-to-enable-resource-health-for-my-organization"></a>Moeten we Resource Health voor mijn organisatie inschakelen?
Nee.  Azure Resource Health is toegankelijk binnen de Azure Portal zonder installatie vereisten.

## <a name="is-resource-health-available-free-of-charge"></a>Is Resource Health gratis beschikbaar?
Ja.  Azure Resource Health is gratis.

## <a name="what-are-the-recommendations-that-resource-health-provides"></a>Wat zijn de aanbevelingen die Resource Health biedt?
Op basis van de status Resource Health biedt u aanbevelingen met betrekking tot het verminderen van de tijd die u hebt besteed aan het oplossen van problemen. De aanbevelingen voor beschik bare bronnen zijn gericht op het oplossen van de meest voorkomende problemen die klanten tegen komen. Als de resource niet beschikbaar is vanwege een niet-geplande Azure-gebeurtenis, wordt u geholpen tijdens en na het herstel proces. 

## <a name="next-steps"></a>Volgende stappen

Meer informatie over Resource Health:
-  [Overzicht van Azure Resource Health](Resource-health-overview.md)
-  [Resourcetypen en statuscontroles die beschikbaar zijn via Azure Resource Health](resource-health-checks-resource-types.md)
