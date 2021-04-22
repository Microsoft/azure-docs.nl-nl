---
title: Een toepassing Service Fabric Mesh verplaatsen naar een andere regio
description: U kunt uw Service Fabric Mesh verplaatsen door een kopie van uw huidige sjabloon te implementeren in een nieuwe Azure-regio.
author: erikadoyle
ms.author: edoyle
ms.topic: how-to
ms.date: 01/14/2020
ms.custom: subject-moving-resources
ms.openlocfilehash: bce61a00ae1b6b451927b43dbcf19ddb615f79a5
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107861171"
---
# <a name="move-a-service-fabric-mesh-application-to-another-azure-region"></a>Een toepassing Service Fabric Mesh verplaatsen naar een andere Azure-regio

> [!IMPORTANT]
> De preview van Azure Service Fabric Mesh is niet meer beschikbaar. Nieuwe implementaties zijn niet langer toegestaan via de Service Fabric Mesh API. Ondersteuning voor bestaande implementaties wordt voortgezet tot en met 28 april 2021.
> 
> Zie preview-Azure Service Fabric Mesh [voor meer informatie.](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/)

In dit artikel wordt beschreven hoe u uw Service Fabric Mesh en de resources ervan naar een andere Azure-regio verplaatst. U kunt uw resources om een aantal redenen verplaatsen naar een andere regio. Bijvoorbeeld als reactie op uitval, om functies of services te verkrijgen die alleen beschikbaar zijn in specifieke regio's, om te voldoen aan interne beleids- en governancevereisten, of als reactie op capaciteitsplanningsvereisten.

 [Service Fabric Mesh biedt geen ondersteuning voor de](../azure-resource-manager/management/move-support-resources.md#microsoftservicefabricmesh) mogelijkheid om resources rechtstreeks tussen Azure-regio's te verplaatsen. U kunt resources echter indirect verplaatsen door een kopie van uw huidige Azure Resource Manager-sjabloon te implementeren naar de nieuwe doelregio en vervolgens binnenkomend verkeer en afhankelijkheden om te leiden naar de zojuist gemaakte Service Fabric Mesh-toepassing.

## <a name="prerequisites"></a>Vereisten

* Controller voor ingress [(zoals Application Gateway](../application-gateway/index.yml)) als intermediair voor het routeren van verkeer tussen clients en uw Service Fabric Mesh toepassing
* Service Fabric Mesh (preview)-beschikbaarheid in de Azure-doelregio ( `westus` `eastus` , of `westeurope` )

## <a name="prepare"></a>Voorbereiden

1. Maak een 'momentopname' van de huidige status van uw Service Fabric Mesh-toepassing door de sjabloon Azure Resource Manager parameters van de meest recente implementatie te exporteren. Volg hiervoor de stappen [in](../azure-resource-manager/templates/export-template-portal.md#export-template-after-deployment) Sjabloon exporteren na implementatie met behulp van de Azure Portal. U kunt ook [Azure CLI,](../azure-resource-manager/management/manage-resource-groups-cli.md#export-resource-groups-to-templates) [Azure PowerShell](../azure-resource-manager/management/manage-resource-groups-powershell.md#export-resource-groups-to-templates)of [REST API.](/rest/api/resources/resourcegroups/exporttemplate)

2. Exporteert, indien [van toepassing, andere resources in dezelfde resourcegroep](../azure-resource-manager/templates/export-template-portal.md#export-template-from-a-resource-group) voor herdeployment in de doelregio.

3. Bekijk (en bewerk, indien nodig) de geëxporteerde sjabloon om ervoor te zorgen dat de bestaande eigenschapswaarden de waarden zijn die u wilt gebruiken in de doelregio. De nieuwe `location` (Azure-regio) is een parameter die u tijdens het opnieuw implementeren opgeeft.

## <a name="move"></a>Verplaatsen

1. Maak een nieuwe resourcegroep (of gebruik een bestaande) in de doelregio.

2. Met uw geëxporteerde sjabloon volgt u de stappen in [Resources implementeren vanuit een aangepaste sjabloon](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template) met behulp van de Azure Portal. U kunt ook [Azure CLI,](../azure-resource-manager/templates/deploy-cli.md) [Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md)of [REST API.](../azure-resource-manager/templates/deploy-rest.md)

3. Raadpleeg de richtlijnen voor afzonderlijke services in het onderwerp Moving Azure resources [across regions (Azure-resources](../azure-resource-manager/management/move-resources-overview.md#move-resources-across-regions)verplaatsen tussen [regio's)](../storage/common/storage-account-move.md)voor hulp bij het verplaatsen van gerelateerde resources, zoals Azure Storage-accounts.

## <a name="verify"></a>Verifiëren

1. Wanneer de implementatie is voltooid, test u de eindpunten van de toepassing om de functionaliteit van uw toepassing te controleren.

2. U kunt de status van uw toepassing ook controleren door de toepassingsstatus te controleren[(az mesh app show](/cli/azure/mesh/app#az_mesh_app_show)) en de toepassingslogboeken en ([az mesh code-package-log](/cli/azure/mesh/code-package-log)) opdrachten te controleren met behulp van [de Azure Service Fabric Mesh CLI](./service-fabric-mesh-quickstart-deploy-container.md#set-up-service-fabric-mesh-cli).

## <a name="commit"></a>Doorvoeren

Zodra u de equivalente functionaliteit van uw Service Fabric Mesh-toepassing in de doelregio hebt bevestigd, configureert u de controller voor binnenkomend verkeer (bijvoorbeeld [Application Gateway](../application-gateway/redirect-overview.md)) om verkeer om te leiden naar de nieuwe toepassing.

## <a name="clean-up-source-resources"></a>Bronbronnen ops schonen

Verwijder de brontoepassing [en/of](../azure-resource-manager/management/delete-resource-group.md)de bovenliggende resourcegroep om de Service Fabric Mesh te voltooien.

## <a name="next-steps"></a>Volgende stappen

* [Azure-resources verplaatsen tussen regio's](../azure-resource-manager/management/move-resources-overview.md#move-resources-across-regions)
* [Ondersteuning voor het verplaatsen van Azure-resources tussen regio's](../azure-resource-manager/management/move-support-resources.md)
* [Resources verplaatsen naar een nieuwe resourcegroep of een nieuw abonnement](../azure-resource-manager/management/move-resource-group-and-subscription.md)
* [Ondersteuning voor het verplaatsen van resources](../azure-resource-manager/management/move-support-resources.md
)
