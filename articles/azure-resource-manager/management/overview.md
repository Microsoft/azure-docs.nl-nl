---
title: Overzicht van Azure Resource Manager
description: Hierin wordt beschreven hoe u Azure Resource Manager kunt gebruiken voor implementatie, beheer, en beheer van toegang tot resources in Azure.
ms.topic: overview
ms.date: 09/01/2020
ms.custom: contperfq1
ms.openlocfilehash: 317d7cae9c0febcc99831b20525b9d9d2cc96651
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96903187"
---
# <a name="what-is-azure-resource-manager"></a>Wat is Azure Resource Manager?

Azure Resource Manager is de implementatie- en beheersservice voor Azure. Het biedt een beheerlaag waarmee u resources in uw Azure-account kunt maken, bijwerken en verwijderen. U kunt beheerfuncties gebruiken, zoals toegangscontrole, vergrendelingen en tags, om uw resources te beveiligen en te organiseren na de implementatie.

Bekijk het [overzicht van sjabloonimplementatie](../templates/overview.md) voor meer informatie over ARM-sjablonen (Azure Resource Manager).

## <a name="consistent-management-layer"></a>Consistente beheerlaag

Wanneer een gebruiker een aanvraag verzendt vanuit een van de hulpprogramma's, API's of SDK's van Azure, ontvangt Resource Manager de aanvraag. De aanvraag wordt geverifieerd en geautoriseerd. Resource Manager verzendt de aanvraag naar de Azure-service, en de aangevraagde actie wordt uitgevoerd. Omdat alle aanvragen worden verwerkt via dezelfde API, ziet u consistente resultaten en mogelijkheden in de verschillende hulpprogramma's.

In de volgende afbeelding ziet u welke rol Azure Resource Manager speelt bij het verwerken van Azure-aanvragen.

![Resource Manager-aanvraagmodel](./media/overview/consistent-management-layer.png)

Alle mogelijkheden die beschikbaar zijn in de Azure-portal zijn ook beschikbaar via Azure PowerShell, Azure CLI, de Azure REST API's en client-SDK's. Functionaliteit die oorspronkelijk wordt uitgegeven via API's, is binnen 180 dagen na de eerste release beschikbaar in de portal.

## <a name="terminology"></a>Terminologie

Als u nog geen ervaring hebt met de Azure Resource Manager, zijn er enkele termen die u mogelijk niet kent.

* **resource** - een beheerbaar item dat beschikbaar is via Azure. Virtuele machines, opslagaccounts, web-apps, databases en virtuele netwerken zijn voorbeelden van resources. Resourcegroepen, abonnementen, beheergroepen en tags zijn ook voorbeelden van resources.
* **resourcegroep** - een container met gerelateerde resources voor een Azure-oplossing. De resourcegroep bevat die resources die u als groep wilt beheren. U bepaalt welke resources in een resourcegroep horen op basis van wat voor uw organisatie het meest zinvol is. Zie [Resourcegroepen](#resource-groups).
* **resourceprovider** - een service die zorgt voor Azure-resources. Voorbeeld: een veelgebruikte resourceprovider is Microsoft.Compute, die de VM-resource levert. Microsoft.Storage is een andere algemene resourceprovider. Zie [Resourceproviders en -typen](resource-providers-and-types.md).
* **Resource Manager-sjabloon**: een JSON-bestand (JavaScript Object Notation) waarmee één of meer resources worden gedefinieerd voor implementatie in een resourcegroep, abonnement, beheergroep of tenant. De sjabloon kan worden gebruikt om de resources consistent en herhaaldelijk te implementeren. Zie [Overzicht van sjabloonimplementatie](../templates/overview.md).
* **declaratieve syntaxis** - een syntaxis waarmee u kunt aangeven 'Dit is wat ik wil maken' zonder hiervoor de nodige reeks programmeeropdrachten te hoeven maken. De sjabloon Resource Manager is een voorbeeld van een declaratieve syntaxis. In het bestand definieert u de eigenschappen voor de infrastructuur te implementeren naar Azure.  Zie [Overzicht van sjabloonimplementatie](../templates/overview.md).

## <a name="the-benefits-of-using-resource-manager"></a>De voordelen van Resource Manager

Met Resource Manager kunt u het volgende doen:

* Uw infrastructuur beheren via declaratieve sjablonen in plaats van scripts.

* Alle resources voor uw oplossing implementeren, beheren en bewaken als groep, in plaats van deze resources afzonderlijk te verwerken.

* Uw oplossing gedurende de ontwikkelingscyclus herhaaldelijk implementeren en erop vertrouwen dat de resources consistent worden geïmplementeerd.

* De afhankelijkheden tussen resources zo definiëren dat deze in de juiste volgorde worden geïmplementeerd.

* Toegangsbeheer toepassen op alle services omdat op rollen gebaseerde toegangscontrole van Azure (Azure RBAC) is geïntegreerd in het beheerplatform.Toegangsbeheer toepassen op alle services omdat op rollen gebaseerd toegangsbeheer (RBAC) is geïntegreerd in het beheerplatform.

* Tags toepassen op de resources om alle resources in uw abonnement op een logische manier te organiseren.

* De facturering van uw organisatie transparanter maken door te kijken naar de kosten voor een groep resources met dezelfde tag.

## <a name="understand-scope"></a>Bereik

Azure biedt vier bereikniveaus: [beheergroepen](../../governance/management-groups/overview.md), abonnementen, [resourcegroepen](#resource-groups) en resources. In de volgende afbeelding ziet u een voorbeeld van deze lagen.

![Beheerniveaus](./media/overview/scope-levels.png)

U past beheerinstellingen toe op een of meer bereikniveaus. Het niveau dat u kiest, bepaalt de reikwijdte van de instelling. Lagere niveaus nemen instellingen over van hogere niveaus. Als u een [beleid](../../governance/policy/overview.md) bijvoorbeeld toepast op het abonnement, wordt het beleid toegepast op alle resourcegroepen en resources in het abonnement. Wanneer u een beleid toepast op de resourcegroep, wordt dit beleid toegepast op de resourcegroep en alle bijbehorende resources. Een andere resourcegroep heeft deze beleidstoewijzing echter niet.

U kunt sjablonen implementeren in tenants, beheergroepen, abonnementen of resourcegroepen.

## <a name="resource-groups"></a>Resourcegroepen

Er zijn een aantal belangrijke factoren waarmee u rekening moet houden bij het definiëren van de resourcegroep:

* Alle resources in uw groep moeten dezelfde levenscyclus hebben. U implementeert ze samen, werkt ze samen bij en verwijdert ze samen. Als een bepaalde resource, zoals een server, onderdeel moet zijn van een andere implementatiecyclus, moet deze in een andere resourcegroep worden geplaatst.

* Elke resource kan maar in één resourcegroep voorkomen.

* U kunt een resource op elk gewenst moment toevoegen aan of verwijderen uit een resourcegroep.

* U kunt een resource van de ene naar de andere resourcegroep verplaatsen. Zie voor meer informatie [Resources verplaatsen naar een nieuwe resourcegroep of een nieuw abonnement](move-resource-group-and-subscription.md).

* De resources in een resourcegroep kunnen zich in andere regio's bevinden dan de resource groep.

* Als u een resourcegroep maakt, moet u voor die resourcegroep een locatie opgeven. U vraagt zich misschien af: 'Waarom heeft een resourcegroep een locatie nodig? En als de resources andere locaties kunnen hebben dan de resourcegroep, wat is dan het nut van een locatie voor de resourcegroep?' De resourcegroep slaat metagegevens op over de resources. Als u een locatie voor de resourcegroep opgeeft, geeft u op waar deze metagegevens worden opgeslagen. In verband met nalevingsvereisten moet u er mogelijk voor zorgen dat uw gegevens worden opgeslagen in een bepaalde regio.

   Als de regio van de resourcegroep tijdelijk niet beschikbaar is, kunt u resources in de resourcegroep niet bijwerken, omdat de metagegevens niet beschikbaar zijn. De resources in andere regio's werken nog steeds zoals verwacht, maar u kunt ze niet bijwerken. Zie [Betrouwbare Azure-toepassingen ontwerpen](/azure/architecture/checklist/resiliency-per-service) voor meer informatie over het bouwen van betrouwbare toepassingen.

* Een resourcegroep kan worden gebruikt voor het bepalen van de mate van toegangsbeheer voor beheertaken. Als u een resourcegroep wilt beheren, kunt u [Azure-beleidsregels](../../governance/policy/overview.md), [Azure-rollen](../../role-based-access-control/role-assignments-portal.md)of [resourcevergrendelingen](lock-resources.md) toewijzen.

* U kunt [tags toepassen](tag-resources.md) op een resourcegroep. De resources in de resourcegroep nemen deze tags niet over.

* Een resource kan verbinding maken met resources in andere resourcegroepen. Dit scenario is gebruikelijk wanneer de twee resources gerelateerd zijn, maar niet dezelfde levenscyclus hebben. U kunt bijvoorbeeld een web-app hebben die verbinding maakt met een database in een andere resourcegroep.

* Wanneer u een resourcegroep verwijdert, worden ook alle resources in de resourcegroep verwijderd. Zie [Azure Resource Manager-resourcegroep en het verwijderen van resources](delete-resource-group.md)voor meer informatie over hoe Azure Resource Manager deze verwijdert.

* U kunt maximaal 800 exemplaren van een resourcetype implementeren in elke resourcegroep. Sommige resourcetypen worden [uitgesloten van de limiet van 800 exemplaren](resources-without-resource-group-limit.md).

* Sommige resources kunnen buiten een resourcegroep bestaan. Deze resources worden geïmplementeerd in het [abonnement](../templates/deploy-to-subscription.md), de [beheergroep](../templates/deploy-to-management-group.md) of de [tenant](../templates/deploy-to-tenant.md). In deze bereiken worden alleen specifieke resourcetypen ondersteund.

* Als u een resourcegroep wilt maken, kunt u de [portal](manage-resource-groups-portal.md#create-resource-groups), [PowerShell](manage-resource-groups-powershell.md#create-resource-groups), [Azure CLI](manage-resource-groups-cli.md#create-resource-groups) of een [ARM-sjabloon](../templates/deploy-to-subscription.md#resource-groups) gebruiken.

## <a name="resiliency-of-azure-resource-manager"></a>Tolerantie van Azure Resource Manager

De Azure Resource Manager-service is ontworpen voor tolerantie en voortdurende beschikbaarheid. Resource Manager- en besturingsvlakbewerkingen (aanvragen die zijn verzonden naar management.azure.com) in de REST API zijn:

* Gedistribueerd over verschillende regio's. Sommige services zijn niet regionaal.

* Gedistribueerd over verschillende Beschikbaarheidszones (en over regio's) op locaties met meerdere Beschikbaarheidszones.

* Niet afhankelijk van één logisch datacentrum.

* Nooit offline gezet voor onderhoudsactiviteiten.

Deze tolerantie is van toepassing op services die aanvragen ontvangen via Resource Manager. Key Vault heeft bijvoorbeeld voordelen vanwege deze tolerantie.

## <a name="next-steps"></a>Volgende stappen

* Zie [Resources verplaatsen naar een nieuwe resourcegroep of een nieuw abonnement](move-resource-group-and-subscription.md) voor meer informatie over het verplaatsen van resources.

* Zie [Tags gebruiken om uw Azure-resources te organiseren](tag-resources.md) voor meer informatie over het taggen van resources.

* Zie [Resources vergrendelen om onverwachte wijzigingen te voorkomen](lock-resources.md) voor meer informatie over het vergrendelen van resources.
