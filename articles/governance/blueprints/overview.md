---
title: Overzicht van Azure Blueprints
description: Azure Blueprints is een service in waarmee u artefacten kunt maken, definiëren en implementeren in uw Azure-omgeving.
ms.date: 01/27/2021
ms.topic: overview
ms.openlocfilehash: f4ba77f5fcb376bf600d94997b0d6ba569f04f82
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98919339"
---
# <a name="what-is-azure-blueprints"></a>Wat is Azure Blueprints?

Net zoals een blauwdruk het een ingenieur of architect mogelijk maakt om de ontwerpparameters voor een project te schetsen, stelt Azure Blueprints cloudarchitecten en centrale IT-groepen in staat om een ​​herhaalbare set Azure-resources te definiëren die voldoet aan de normen, patronen en vereisten van een organisatie en deze implementeert. Met Azure Blueprints kunnen ontwikkelteams snel nieuwe omgevingen bouwen en instellen in het volste vertrouwen dat ze voldoen aan de vereisten van de organisatie, met een set ingebouwde componenten, zoals netwerken, om de ontwikkeling en levering te versnellen.

Blauwdrukken zijn een declaratieve manier om de implementatie van diverse resourcesjablonen en andere artefacten te orkestreren, zoals:

- Roltoewijzingen
- Beleidstoewijzingen
- Azure Resource Manager-sjablonen (ARM-sjablonen)
- Resourcegroepen

De Azure Blueprints-service wordt gesteund door de wereldwijd gedistribueerde [Azure Cosmos DB](../../cosmos-db/introduction.md). Blauwdrukobjecten worden naar meerdere Azure-regio's gerepliceerd. Deze replicatie biedt lage latentie, hoge beschikbaarheid en consistente toegang tot uw blauwdrukobjecten, ongeacht in welke regio uw resources door Azure Blueprints worden geïmplementeerd.

## <a name="how-its-different-from-arm-templates"></a>Wat is het verschil met ARM-sjablonen?

De service is ontworpen om u te helpen met de _setup van de omgeving_. Deze setup bestaat vaak uit een set resourcegroepen, beleidsmaatregelen, roltoewijzingen en ARM-sjabloonimplementaties. Een blauwdruk is een pakket waarmee al deze typen _artefacten_ bij elkaar worden gebracht en u in staat wordt gesteld om dat pakket samen te stellen en versies ervan te beheren, onder andere via een CI/CD-pijplijn voor continue integratie en levering. Uiteindelijk wordt elke blauwdruk toegewezen aan een abonnement in één bewerking die kan worden gecontroleerd en bijgehouden.

Bijna alles dat u voor implementatie in Azure Blueprints wilt opnemen, kan worden bereikt met een ARM-sjabloon. Een ARM-sjabloon is echter een document dat niet in Azure zelf bestaat. Elke sjabloon wordt lokaal of in broncodebeheer opgeslagen. De sjabloon wordt gebruikt voor de implementatie van een of meer Azure-resources, maar zodra die resources zijn geïmplementeerd, is er geen actieve verbinding of relatie meer met de sjabloon.

Met Azure Blueprints blijft de relatie tussen de blauwdrukdefinitie (wat _moet worden_ geïmplementeerd) en de blauwdruktoewijzing (wat _is_ geïmplementeerd) behouden. Deze verbinding ondersteunt verbeterde tracering en controle van implementaties. Met Azure Blueprints kunt u bovendien in één keer een upgrade uitvoeren van meerdere abonnementen die zijn onderworpen aan dezelfde blauwdruk.

U hoeft niet te kiezen tussen een ARM-sjabloon en een blauwdruk. Elke blauwdruk kan bestaan uit nul of meer ARM-sjabloon _artefacten_. Deze ondersteuning betekent dat eerdere inspanningen om een ​​bibliotheek met ARM-sjablonen te ontwikkelen en te onderhouden, in Blueprints kunnen worden hergebruikt.

## <a name="how-its-different-from-azure-policy"></a>Wat is het verschil met Azure Policy?

Een blauwdruk is een pakket of container voor het samenstellen van focusspecifieke sets van standaarden, patronen en vereisten met betrekking tot de implementatie van Azure-cloudservices, beveiliging en ontwerp, die kunnen worden hergebruikt om consistentie en naleving te behouden.

Een [beleid](../policy/overview.md) is een systeem voor standaard toestaan en expliciet weigeren dat is gericht op resource-eigenschappen tijdens implementatie en voor reeds bestaande resources. Het ondersteunt cloudgovernance door te valideren dat resources binnen een abonnement voldoen aan vereisten en normen.

Door een beleid op te nemen in een blauwdruk kan het juiste patroon of ontwerp worden gemaakt tijdens het toewijzen van de blauwdruk. Door een beleid op te nemen, wordt ervoor gezorgd dat alleen goedgekeurde of verwachte wijzigingen kunnen worden aangebracht in de omgeving, om continue naleving van de intentie van de blauwdruk te beschermen.

Een beleid kan worden opgenomen als een van de vele _artefacten_ in de definitie van een blauwdruk. Blauwdrukken ondersteunen ook het gebruik van parameters in beleid en initiatieven.

## <a name="blueprint-definition"></a>Blauwdrukdefinitie

Een blauwdruk bestaat uit _artefacten_. Azure Blueprints ondersteunt momenteel de volgende resources als artefacten:

|Resource  | Hiërarchieopties| Beschrijving  |
|---------|---------|---------|
|Resourcegroepen | Abonnement | Een nieuwe resourcegroep maken voor gebruik door andere artefacten binnen de blauwdruk.  Met deze tijdelijke resourcegroepen kunt u resources precies zo indelen als u ze wilt structureren, en het bereik beperken voor opgenomen beleids- en roltoewijzingsartefacten, en ARM-sjablonen. |
|ARM-sjabloon | Abonnement, resourcegroep | Sjablonen, inclusief geneste en gekoppelde sjablonen, worden gebruikt voor het samenstellen van complexe omgevingen. Voorbeeldomgevingen: een SharePoint-farm, Azure Automation State Configuration of een Log Analytics-werkruimte. |
|Beleidstoewijzing | Abonnement, resourcegroep | Hiermee kan een beleid of initiatief worden toegewezen aan het abonnement waaraan de blauwdruk wordt toegewezen. Het beleid of initiatief moet binnen het bereik van de locatie van de blauwdrukdefinitie vallen. Als het beleid of initiatief parameters heeft, worden deze parameters toegewezen bij het maken of toewijzen van de blauwdruk. |
|Roltoewijzing | Abonnement, resourcegroep | Voeg een bestaande gebruiker of groep toe aan een ingebouwde rol om ervoor te zorgen dat de juiste mensen altijd over de juiste toegang tot uw bronnen beschikken. Roltoewijzingen kunnen voor het hele abonnement worden gedefinieerd of genest in een specifieke resourcegroep die in de blauwdruk is opgenomen. |

### <a name="blueprint-definition-locations"></a>Locaties van blauwdrukdefinities

Bij het maken van een blauwdrukdefinitie definieert u waar de blauwdruk wordt opgeslagen. Momenteel kunnen blauwdrukken worden opgeslagen in een [beheergroep](../management-groups/overview.md) of abonnement waartoe u **Inzender**-toegang hebt. Als de locatie een beheergroep is, is de blauwdruk beschikbaar voor toewijzing aan elk onderliggend abonnement van die beheergroep.

### <a name="blueprint-parameters"></a>Blauwdrukparameters

Blauwdrukken kunnen parameters doorgeven aan een beleid/initiatief of een ARM-sjabloon. Tijdens het toevoegen van een van deze _​​artefacten_ aan een blauwdruk, besluit de auteur om een ​​gedefinieerde waarde op te geven voor elke blauwdruktoewijzing, of toe te staan ​​dat elke blauwdruktoewijzing op het moment van de toewijzing een waarde opgeeft. Dankzij deze flexibiliteit hebt u de optie om een ​​vooraf vastgestelde waarde te definiëren voor elk gebruik van de blauwdruk of om die beslissing te nemen op het moment van toewijzing.

> [!NOTE]
> Een blauwdruk kan zijn eigen parameters hebben, maar deze kunnen op dit moment alleen worden gemaakt als een blauwdruk wordt gegenereerd vanuit de REST API in plaats van via de portal.

Zie [blauwdrukparameters](./concepts/parameters.md) voor meer informatie.

### <a name="blueprint-publishing"></a>Blauwdruk publiceren

Wanneer een blauwdruk wordt gemaakt, bevindt deze zich in de modus **Concept**. Wanneer de blauwdruk klaar is om te worden toegewezen, moet deze worden **Gepubliceerd**. Om een blauwdruk te publiceren moet u een **Versie**-tekenreeks definiëren (letters, cijfers en afbreekstreepjes, maximaal 20 tekens lang), plus eventuele **Wijzigingsnotities**. De **versie** onderscheidt deze blauwdruk van toekomstige gewijzigde versies ervan, en maakt het mogelijk elke versie toe te wijzen. Dit versiebeheer betekent ook dat verschillende **versies** van dezelfde blueprint kunnen worden toegewezen aan hetzelfde abonnement. Wanneer er aanvullende wijzigingen in de blauwdruk worden aangebracht, blijft de **gepubliceerde**
**versie** gewoon bestaan, net als de **niet-gepubliceerde wijzigingen**. Nadat de wijzigingen voltooid zijn, wordt de bijgewerkte blauwdruk **Gepubliceerd** met een nieuwe en unieke **versie**, en kan nu ook worden toegewezen.

## <a name="blueprint-assignment"></a>Blauwdruktoewijzing

Elke **gepubliceerde** **versie** van een blauwdruk kan worden toegewezen (met een maximale naamlengte van 90 tekens) aan een bestaande beheergroep of bestaand abonnement. In de portal wordt standaard de **versie** van de blauwdruk gebruikt die het laatst is **gepubliceerd**. Als er artefactparameters of blauwdrukparameters zijn, worden de parameters tijdens het toewijzingsproces gedefinieerd.

> [!NOTE]
> Als u een blauwdrukdefinitie toewijst aan een beheergroep, betekent dit dat het toewijzingsobject bestaat bij de beheergroep. De implementatie van artefacten is nog steeds gericht op een abonnement. Als u een toewijzing van een beheergroep wilt uitvoeren, moet u [REST API maken of bijwerken](/rest/api/blueprints/assignments/createorupdate) gebruiken. De aanvraagtekst moet een waarde bevatten voor `properties.scope` om het doelabonnement te definiëren.

## <a name="permissions-in-azure-blueprints"></a>Machtigingen in Azure Blueprints

Om blauwdrukken te kunnen gebruiken, moet u zijn gemachtigd via [op rollen gebaseerd toegangsbeheer (Azure RBAC)](../../role-based-access-control/overview.md). Als u een blauwdruk wilt lezen of bekijken in de Azure-portal, moet uw account leestoegang hebben tot het bereik waarin de definitie van de blauwdruk zich bevindt.

Om blauwdrukken te maken, moet uw account de volgende machtigingen hebben:

- `Microsoft.Blueprint/blueprints/write` - Een blauwdrukdefinitie maken
- `Microsoft.Blueprint/blueprints/artifacts/write` - Artefacten in een blauwdrukdefinitie maken
- `Microsoft.Blueprint/blueprints/versions/write` - Een blauwdruk publiceren

Om blauwdrukken te verwijderen, moet uw account de volgende machtigingen hebben:

- `Microsoft.Blueprint/blueprints/delete`
- `Microsoft.Blueprint/blueprints/artifacts/delete`
- `Microsoft.Blueprint/blueprints/versions/delete`

> [!NOTE]
> De machtigingen voor de blauwdrukdefinitie moeten worden verleend of overgenomen van het beheergroep- of abonnementbereik waarin deze is opgeslagen.

Als u een blauwdruk wilt toewijzen of de toewijzing ongedaan wilt maken, heeft uw account de volgende machtigingen nodig:

- `Microsoft.Blueprint/blueprintAssignments/write` - Een blauwdruk toewijzen
- `Microsoft.Blueprint/blueprintAssignments/delete` - De toewijzing van een blauwdruk ongedaan maken

> [!NOTE]
> Omdat blauwdruktoewijzingen op een abonnement worden gemaakt, moeten de machtigingen voor het toewijzen van blauwdrukken en het ongedaan maken van toewijzingen worden toegekend op abonnementsbereik of worden overgenomen in een abonnementsbereik.

De volgende ingebouwde rollen zijn beschikbaar:

|Azure-rol | Beschrijving |
|-|-|
|[Eigenaar](../../role-based-access-control/built-in-roles.md#owner) | Eigenaren hebben onder alle Azure Blueprint-gerelateerde machtigingen. |
|[Inzender](../../role-based-access-control/built-in-roles.md#contributor) | Inzenders kunnen onder meer blauwdrukdefinities maken en verwijderen, maar beschikken niet over machtigingen om blauwdrukken toe te wijzen. |
|[Blauwdrukinzender](../../role-based-access-control/built-in-roles.md#blueprint-contributor) | Blauwdrukinzenders kunnen blauwdrukdefinities beheren, maar deze niet toewijzen. |
|[Blauwdrukoperator](../../role-based-access-control/built-in-roles.md#blueprint-operator) | Blauwdrukoperator kunnen bestaande publicaties toewijzen, maar kunnen geen nieuwe blauwdrukdefinities maken. De toewijzing van blauwdrukken werkt alleen als de toewijzing wordt uitgevoerd met een door de gebruiker toegewezen beheerde identiteit. |

Als deze ingebouwde rollen niet aan uw beveiligingsbehoeften voldoen, kunt u een [aangepaste rol](../../role-based-access-control/custom-roles.md) maken.

> [!NOTE]
> Als u een door het systeem toegewezen beheerde identiteit gebruikt, vereist de service-principal voor Azure Blueprint de rol **Eigenaar** voor het toegewezen abonnement om implementatie mogelijk te maken. Als u de portal gebruikt, wordt deze rol automatisch verleend en ingetrokken voor de implementatie. Als u de REST API gebruikt, moet deze rol handmatig worden toegekend, maar wordt deze automatisch ingetrokken nadat de implementatie is voltooid. Als u een door de gebruiker toegewezen beheerde identiteit gebruikt, heeft alleen de gebruiker die de blauwdruk maakt de machtiging `Microsoft.Blueprint/blueprintAssignments/write` nodig. Deze machtiging maakt deel uit van de ingebouwde rollen **Eigenaar** en **Blauwdrukoperator**.

## <a name="naming-limits"></a>Naamgevingslimieten

De volgende beperkingen gelden voor bepaalde velden:

|Object|Veld|Toegestane tekens|Met maximaal Lengte|
|-|-|-|-|
|Blauwdruk|Naam|letters, cijfers, afbreekstreepjes en punten|48|
|Blauwdruk|Versie|letters, cijfers, afbreekstreepjes en punten|20|
|Blauwdruktoewijzing|Naam|letters, cijfers, afbreekstreepjes en punten|90|
|Blauwdrukartefact|Naam|letters, cijfers, afbreekstreepjes en punten|48|

## <a name="video-overview"></a>Video-overzicht

Het volgende overzicht van Azure Blueprints is afkomstig van Azure Fridays. Als u de video wilt downloaden, gaat u naar [Azure Fridays - An overview of Azure Blueprints](https://channel9.msdn.com/Shows/Azure-Friday/An-overview-of-Azure-Blueprints) op Channel 9.

> [!VIDEO https://www.youtube.com/embed/cQ9D-d6KkMY]

## <a name="next-steps"></a>Volgende stappen

- [Een blauwdruk maken - Portal](./create-blueprint-portal.md).
- [Een blauwdruk maken - PowerShell](./create-blueprint-powershell.md).
- [Een blauwdruk maken - REST API](./create-blueprint-rest-api.md).