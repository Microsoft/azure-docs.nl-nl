---
title: Overzicht van Azure Resource Graph
description: Meer informatie over hoe u met de Azure Resource Graph-service complexe query's op schaal kunt uitvoeren op resources in abonnementen en tenants.
ms.date: 01/27/2021
ms.topic: overview
ms.openlocfilehash: b5df124d07b8ecfb20f5dec08830d8156e8df2cd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98919138"
---
# <a name="what-is-azure-resource-graph"></a>Wat is Azure Resource Graph?

Azure Resource Graph is een service in Azure die is ontworpen om Azure Resource Management uit te breiden met efficiënte en krachtige resourceverkenning en de mogelijkheid om query's op schaal uit te voeren binnen een bepaalde groep abonnementen, zodat u uw omgeving effectief kunt beheren. Deze query's bieden de volgende mogelijkheden:

- De mogelijkheid om resources op te vragen met geavanceerde opties voor filteren, groeperen en sorteren op resource-eigenschappen.
- De mogelijkheid om resources iteratief te verkennen op basis van beheervereisten.
- De mogelijkheid om de impact van het toepassen van een beleid in een grote cloudomgeving te beoordelen.
- De mogelijkheid om [wijzigingen te beschrijven die in de resource-eigenschappen zijn aangebracht](./how-to/get-resource-changes.md) (preview).

In deze documentatie komt elke mogelijkheid gedetailleerd aan bod.

> [!NOTE]
> Azure Resource Graph geeft functionaliteit aan de zoekbalk en de nieuwe bladerervaring Alle resources van Azure Portal en de [Wijzigingsgeschiedenis](../policy/how-to/determine-non-compliance.md#change-history)
> _visual diff_ van Azure Policy. Deze is ontworpen voor klanten die grootschalige omgevingen beheren.

[!INCLUDE [azure-lighthouse-supported-service](../../../includes/azure-lighthouse-supported-service.md)]

## <a name="how-does-resource-graph-complement-azure-resource-manager"></a>Hoe Resource Graph Azure Resource Manager aanvult

Resource Manager biedt momenteel ondersteuning voor query's op velden van elementaire resources, met name Resourcenaam, Id, Type, Resourcegroep, Abonnement en Locatie. Resource Manager biedt ook voorzieningen voor het per resource aanroepen van afzonderlijke resourceproviders voor gedetailleerde eigenschappen.

Met Azure Resource Graph hebt u toegang tot de eigenschappen die de resourceproviders retourneren zonder dat u elke resourceprovider afzonderlijk moet aanroepen. Voor een lijst met ondersteunde resourcetypen raadpleegt u de [verwijzing naar tabel- en resourcetype](./reference/supported-tables-resources.md). Een alternatieve manier om ondersteunde resourcetypen te bekijken, is via de [ schemabrowser van Azure Resource Graph Explorer](./first-query-portal.md#schema-browser).

U kunt het volgende doen met Azure Resource Graph:

- U hebt toegang tot de eigenschappen die door de resourceproviders worden geretourneerd zonder dat u elke resourceprovider afzonderlijk hoeft aan te roepen.
- U kunt de laatste 14 dagen bekijken van de wijzigingsgeschiedenis van de resource om na te gaan welke eigenschappen zijn gewijzigd en wanneer. (preview)

> [!NOTE]
> Als _preview_-functie zijn er voor sommige `type`-objecten extra niet-Resource Manager-eigenschappen beschikbaar. Zie [Uitgebreide eigenschappen (preview)](./concepts/query-language.md#extended-properties) voor meer informatie.

## <a name="how-resource-graph-is-kept-current"></a>Hoe de Resource Graph actueel blijft

Wanneer een Azure-resource wordt bijgewerkt, krijgt Resource Graph een melding van de wijziging door Resource Manager.
De database van Resource Graph wordt vervolgens bijgewerkt. Er wordt in Resource Graph ook een periodieke _volledige scan_ uitgevoerd. Deze scan zorgt ervoor dat de gegevens van Resource Graph worden bijgewerkt als er gemiste meldingen zijn of wanneer een resource buiten Resource Manager wordt bijgewerkt.

> [!NOTE]
> Resource Graph maakt gebruik van een `GET` voor de laatste niet-preview-API van elke resourceprovider voor het verzamelen van eigenschappen en waarden. Als gevolg hiervan is de verwachte eigenschap mogelijk niet beschikbaar. In sommige gevallen is de gebruikte API-versie onderdrukt om meer actuele of veelgebruikte eigenschappen te kunnen bieden in de resultaten. Zie het voorbeeld [API-versie weergeven voor elk resourcetype](./samples/advanced.md#apiversion) voor een volledige lijst in uw omgeving.

## <a name="the-query-language"></a>De querytaal

Nu u weet wat Azure Resource Graph is, leert u hoe u query's kunt maken.

Het is belangrijk te weten dat de querytaal van Azure Resource Graph is gebaseerd op de [querytaal van Kusto](/azure/data-explorer/data-explorer-overview) die wordt gebruikt door Azure Data Explorer.

Voor meer informatie over bewerkingen en functies die kunnen worden gebruikt met Azure Resource Graph raadpleegt u eerst [De querytaal van Resource Graph](./concepts/query-language.md). Zie [Resources verkennen](./concepts/explore-resources.md) als u de resources wilt bekijken.

## <a name="permissions-in-azure-resource-graph"></a>Machtigingen in Azure Resource Graph

Om Resource Graph te kunnen gebruiken, moet u over de juiste machtigingen beschikken in [Op rollen gebaseerd toegangsbeheer (RBAC)](../../role-based-access-control/overview.md), met op zijn minst leestoegang tot de resources die u wilt zoeken. Zonder ten minste `read`-machtigingen voor het Azure-object of de objectgroep worden er geen resultaten geretourneerd.

> [!NOTE]
> Resource Graph gebruikt de voor een principal beschikbare abonnementen tijdens de aanmelding. Als u resources van een nieuw abonnement wilt zien die tijdens een actieve sessie zijn toegevoegd, moet de principal de context vernieuwen. Deze actie wordt automatisch uitgevoerd wanneer u zich afmeldt en opnieuw aanmeldt.

Azure CLI en Azure PowerShell gebruiken abonnementen waartoe de gebruiker toegang heeft. Wanneer u REST API rechtstreeks gebruikt, wordt de lijst met abonnementen aangeboden door de gebruiker. Als de gebruiker toegang heeft tot een van de abonnementen in de lijst, worden de queryresultaten geretourneerd voor de abonnementen waartoe de gebruiker toegang heeft. Dit gedrag is hetzelfde als u bij het aanroepen van [Resourcegroepen - Lijst](/rest/api/resources/resourcegroups/list) \- resourcegroepen krijgt waartoe u toegang hebt, zonder enige indicatie dat het resultaat gedeeltelijk kan zijn. Als er geen abonnementen zijn in de lijst abonnementen waarvoor de gebruiker de juiste rechten heeft, is het antwoord een _403_ (verboden).

> [!NOTE]
> In de **preview** van de REST API versie `2020-04-01-preview` kan de lijst met abonnementen zijn weggelaten.
> Wanneer zowel de eigenschap `subscriptions` als de eigenschap `managementGroupId` niet in de aanvraag zijn gedefinieerd, wordt het _bereik_ ingesteld op de tenant. Zie [Bereik van de query](./concepts/query-language.md#query-scope) voor meer informatie.

## <a name="throttling"></a>Beperking

Als gratis service worden query's voor Resource Graph beperkt om de beste ervaring en responstijd te bieden aan alle klanten. Als uw organisatie de Resource Graph-API wil gebruiken voor grootschalige en regelmatige query's, gebruikt u de feedback van de portal vanaf de [Resource Graph-portalpagina](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/ResourceGraph).
Geef uw bedrijfsscenario op en schakel het selectievakje Microsoft mag per e-mail contact met u opnemen over uw feedback in, zodat het team contact met u kan opnemen.

Resource Graph beperkt query's op gebruikersniveau. De servicereactie bevat de volgende HTTP-headers:

- `x-ms-user-quota-remaining` (int): Het resterende resourcequotum voor de gebruiker. Deze waarde wordt toegewezen aan het aantal query's.
- `x-ms-user-quota-resets-after` (uu:mm:ss): De tijdsduur totdat het quotumverbruik van een gebruiker opnieuw wordt ingesteld

Zie de [richtlijnen voor beperkte aanvragen](./concepts/guidance-for-throttled-requests.md) voor meer informatie.

## <a name="running-your-first-query"></a>Uw eerste query uitvoeren

Met Azure Resource Graph Explorer een onderdeel van Azure Portal, kunt u uw Resource Graph-query's rechtstreeks in de Azure Portal uitvoeren. Maak de resultaten vast als dynamische grafieken om realtime dynamische informatie te bieden aan de werkstroom van uw portal. Zie [Eerste query met Azure Resource Graph Explorer](./first-query-portal.md) voor meer informatie.

Resource Graph ondersteunt Azure CLI, Azure PowerShell, Azure SDK voor Python en meer. De query is voor elke taal hetzelfde gestructureerd. Meer informatie over het inschakelen van Resource Graph met:

- [Azure Portal en Resource Graph Explorer](./first-query-portal.md) 
- [Azure-CLI](./first-query-azurecli.md#add-the-resource-graph-extension)
- [Azure PowerShell](./first-query-powershell.md#add-the-resource-graph-module)
- [Python](./first-query-python.md#add-the-resource-graph-library)

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [querytaal](./concepts/query-language.md).
- Zie de taal die wordt gebruikt in [Starter-query's](./samples/starter.md).
- Zie [Geavanceerde query's](./samples/advanced.md) voor geavanceerde gebruikswijzen.
