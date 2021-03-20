---
title: Logische apps verplaatsen tussen abonnementen, resource groepen of regio's
description: Logische apps of integratie accounts migreren naar andere Azure-abonnementen, resource groepen of locaties (regio's)
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: aca2c51ff14b99ba41b159cf32e59dc861de7a53
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87826208"
---
# <a name="move-logic-app-resources-to-other-azure-resource-groups-regions-or-subscriptions"></a>Logische app-resources verplaatsen naar andere Azure-resource groepen,-regio's of-abonnementen

Als u uw logische app of gerelateerde resources wilt migreren naar een andere Azure-resource groep,-regio of-abonnement, hebt u verschillende manieren om deze taken uit te voeren, zoals de Azure Portal, Azure PowerShell, Azure CLI en REST API. Lees de volgende overwegingen voordat u resources verplaatst: 

* U kunt alleen [specifieke logische app-resource typen](../azure-resource-manager/management/move-support-resources.md#microsoftlogic) verplaatsen tussen Azure-resource groepen of-abonnementen.

* Controleer de [limieten](../logic-apps/logic-apps-limits-and-config.md) voor het aantal logische app-resources dat u kunt hebben in uw Azure-abonnement en in elke Azure-regio. Deze beperkingen bepalen of u specifieke resource typen kunt verplaatsen wanneer de regio hetzelfde blijft in abonnementen of resource groepen. U kunt bijvoorbeeld slechts één integratie account voor de gratis laag hebben voor elke Azure-regio in elk Azure-abonnement.

* Wanneer u resources verplaatst, worden er nieuwe resource-Id's gemaakt door Azure. Zorg er daarom voor dat u de nieuwe Id's gebruikt en eventuele scripts of hulpprogram ma's die aan de verplaatste resources zijn gekoppeld, bijwerkt.

* Nadat u logische apps hebt gemigreerd tussen abonnementen, resource groepen of regio's, moet u alle verbindingen waarvoor open verificatie (OAuth) vereist is, opnieuw maken of autoriseren.

* U kunt een [Integration service-omgeving (ISE)](connect-virtual-network-vnet-isolated-environment-overview.md) alleen verplaatsen naar een andere resource groep die bestaat in dezelfde Azure-regio of Azure-abonnement. U kunt een ISE niet verplaatsen naar een resource groep die bestaat in een andere Azure-regio of een Azure-abonnement. Na een dergelijke verplaatsing moet u ook alle verwijzingen naar de ISE bijwerken in uw logische app-werk stromen, integratie accounts, verbindingen, enzovoort.

## <a name="prerequisites"></a>Vereisten

* Hetzelfde Azure-abonnement dat is gebruikt voor het maken van de logische app of het integratie account dat u wilt verplaatsen

* Resource-eigenaar machtigingen om de gewenste resources te verplaatsen en in te stellen. Meer informatie over [toegangs beheer op basis van rollen (Azure RBAC)](../role-based-access-control/built-in-roles.md#owner).

<a name="move-subscription"></a>

## <a name="move-resources-between-subscriptions"></a>Resources verplaatsen van het ene naar het andere abonnement

Als u een resource, zoals een logische app of een integratie account, wilt verplaatsen naar een ander Azure-abonnement, kunt u de Azure Portal, Azure PowerShell, Azure CLI of REST API gebruiken. Deze stappen omvatten de Azure Portal, die u kunt gebruiken wanneer de resource regio hetzelfde blijft. Zie [resources verplaatsen naar een nieuwe resource groep of een nieuw abonnement](../azure-resource-manager/management/move-resource-group-and-subscription.md)voor andere stappen en algemene voor bereiding.

1. Zoek in het [Azure Portal](https://portal.azure.com)de logische app-resource die u wilt verplaatsen en selecteer deze.

1. Op de **overzichts** pagina van de resource, naast **abonnement**, selecteert u de **wijzigings** koppeling.

1. Selecteer op de pagina **resources verplaatsen** de logische app-resource en eventuele gerelateerde resources die u wilt verplaatsen.

1. Selecteer in de lijst **abonnement** het doel abonnement.

1. Selecteer de doel resource groep in de lijst **resource groep** . Als u een andere resource groep wilt maken, selecteert u **een nieuwe groep maken**.

1. Als u wilt weten dat scripts of hulpprogram ma's die zijn gekoppeld aan de verplaatste resources, niet werken totdat u ze bijwerkt met de nieuwe resource-Id's, selecteert u het bevestigings venster en selecteert u **OK**.

<a name="move-resource-group"></a>

## <a name="move-resources-between-resource-groups"></a>Resources verplaatsen tussen resource groepen

Als u een resource, zoals een logische app, een integratie account of een [integratie service omgeving (ISE)](connect-virtual-network-vnet-isolated-environment-overview.md), wilt verplaatsen naar een andere Azure-resource groep, kunt u de Azure Portal, Azure PowerShell, Azure CLI of rest API gebruiken. Deze stappen omvatten de Azure Portal, die u kunt gebruiken wanneer de resource regio hetzelfde blijft. Zie [resources verplaatsen naar een nieuwe resource groep of een nieuw abonnement](../azure-resource-manager/management/move-resource-group-and-subscription.md)voor andere stappen en algemene voor bereiding.

Voordat u resources tussen groepen verplaatst, kunt u testen of u uw resource kunt verplaatsen naar een andere groep. Zie [uw verhuizing valideren](../azure-resource-manager/management/move-resource-group-and-subscription.md#validate-move)voor meer informatie.

1. Zoek in het [Azure Portal](https://portal.azure.com)de logische app-resource die u wilt verplaatsen en selecteer deze.

1. Selecteer op de **overzichts** pagina van de resource naast **resource groep** de **wijzigings** koppeling.

1. Selecteer op de pagina **resources verplaatsen** de logische app-resource en eventuele gerelateerde resources die u wilt verplaatsen.

1. Selecteer de doel resource groep in de lijst **resource groep** . Als u een andere resource groep wilt maken, selecteert u **een nieuwe groep maken**.

1. Als u wilt weten dat scripts of hulpprogram ma's die zijn gekoppeld aan de verplaatste resources, niet werken totdat u ze bijwerkt met de nieuwe resource-Id's, selecteert u het bevestigings venster en selecteert u **OK**.

<a name="move-location"></a>

## <a name="move-resources-between-regions"></a>Resources verplaatsen tussen regio's

Wanneer u een logische app naar een andere regio wilt verplaatsen, zijn uw opties afhankelijk van de manier waarop u uw logische app hebt gemaakt. Op basis van de optie die u kiest, moet u de verbindingen in uw logische app opnieuw maken of autoriseren.

* In de Azure Portal maakt u de logische app opnieuw in de nieuwe regio en configureert u de werk stroom instellingen opnieuw. Om tijd te besparen, kunt u de onderliggende werk stroom definitie en verbindingen van de bron-app kopiëren naar de doel-app. Als u de ' code ' achter een logische app wilt weer geven, selecteert u in de werk balk van de Logic app-ontwerp functie de **code weergave**.

* Met Visual Studio en de Azure Logic Apps-Hulpprogram Ma's voor Visual Studio kunt u [uw logische app openen en downloaden](../logic-apps/manage-logic-apps-with-visual-studio.md) vanuit de Azure portal als een [Azure Resource Manager-sjabloon](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md). Deze sjabloon is voornamelijk gereed voor implementatie en bevat de resource definities voor uw logische app, inclusief de werk stroom zelf en verbindingen. De sjabloon declareert ook para meters voor de waarden die tijdens de implementatie moeten worden gebruikt. Op die manier kunt u gemakkelijker wijzigen waar en hoe u de logische app implementeert, op basis van uw behoeften. Als u de locatie en andere benodigde informatie voor de implementatie wilt opgeven, kunt u een afzonderlijk parameter bestand gebruiken.

* Als u uw logische app hebt gemaakt en geïmplementeerd met behulp van doorlopende integratie (CI) en continue levering (CD)-hulpprogram ma's, zoals Azure-pijp lijnen in azure DevOps, kunt u uw app implementeren in een andere regio met behulp van deze hulpprogram ma's.

Zie de volgende onderwerpen voor meer informatie over implementatie sjablonen voor Logic apps:

* [Overzicht: de implementatie voor Azure Logic Apps automatiseren met behulp van Azure Resource Manager sjablonen](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)
* [Uw logische app zoeken, openen en downloaden van de Azure Portal in Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md)
* [Azure Resource Manager sjablonen maken voor Azure Logic Apps](../logic-apps/logic-apps-create-azure-resource-manager-templates.md)
* [Azure Resource Manager-sjablonen inzetten voor Azure Logic Apps](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md)

### <a name="related-resources"></a>Gerelateerde resources

Sommige Azure-resources, zoals on-premises gegevens gateway resources in azure, kunnen bestaan in een regio die verschilt van de Logic apps die gebruikmaken van deze resources. Andere Azure-resources, zoals gekoppelde integratie accounts, moeten zich echter in dezelfde regio bevinden als uw logische apps. Zorg er op basis van uw scenario voor dat uw Logic apps toegang hebben tot de resources die door uw apps in dezelfde regio worden verwacht.

Als u bijvoorbeeld een logische app wilt koppelen aan een integratie account, moeten beide resources zich in dezelfde regio bevinden. In scenario's als herstel na nood gevallen wilt u doorgaans integratie accounts met dezelfde configuratie en artefacten. In andere scenario's hebt u mogelijk integratie accounts met verschillende configuraties en artefacten nodig.

Aangepaste connectors in Azure Logic Apps zijn zichtbaar voor de auteurs van de connectors en gebruikers die hetzelfde Azure-abonnement hebben en dezelfde Azure Active Directory Tenant. Deze connectors zijn beschikbaar in dezelfde regio waar Logic apps worden geïmplementeerd. Zie voor meer informatie [Share custom connectors in your organization](/connectors/custom-connectors/share) (Aangepaste connectors delen in uw organisatie).

De sjabloon die u in Visual Studio krijgt, bevat alleen de resource definities voor uw logische app en de bijbehorende verbindingen. Als uw logische app bijvoorbeeld gebruikmaakt van andere resources, zoals een integratie account en B2B-artefacten, zoals partners, overeenkomsten en schema's, moet u de sjabloon van dat integratie account exporteren met behulp van de Azure Portal. Deze sjabloon bevat de resource definities voor het integratie account en de artefacten. De sjabloon heeft echter geen volledige para meters. Daarom moet u de waarden die u wilt gebruiken voor implementatie hand matig para meters.

### <a name="export-templates-for-integration-accounts"></a>Sjablonen voor integratie accounts exporteren

1. Zoek en open uw integratie account in de [Azure Portal](https://portal.azure.com).

1. Selecteer in het menu van het integratie account onder **instellingen** de optie **sjabloon exporteren**.

1. Selecteer op de werk balk de optie **downloaden** en sla de sjabloon op.

1. Open en bewerk de sjabloon om de vereiste waarden voor de implementatie te para meters.

## <a name="next-steps"></a>Volgende stappen

[Azure-resources verplaatsen naar nieuwe resource groepen of-abonnementen](../azure-resource-manager/management/move-resource-group-and-subscription.md)
