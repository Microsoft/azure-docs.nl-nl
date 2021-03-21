---
title: Een Service Fabric mesh-toepassing verplaatsen naar een andere regio
description: U kunt Service Fabric netresources verplaatsen door een kopie van uw huidige sjabloon te implementeren in een nieuwe Azure-regio.
author: erikadoyle
ms.author: edoyle
ms.topic: how-to
ms.date: 01/14/2020
ms.custom: subject-moving-resources
ms.openlocfilehash: 1b59d482b8b88e37da2d61636ff3f254a46ba5c2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99626084"
---
# <a name="move-a-service-fabric-mesh-application-to-another-azure-region"></a>Een Service Fabric mesh-toepassing verplaatsen naar een andere Azure-regio

> [!IMPORTANT]
> De preview-versie van Azure Service Fabric Mesh is buiten gebruik gesteld. Nieuwe implementaties zijn niet langer toegestaan via de API van Service Fabric net. Ondersteuning voor bestaande implementaties gaat door tot 28 april 2021.
> 
> Zie [Azure service Fabric Netpreview buiten](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/)gebruik stellen voor meer informatie.

In dit artikel wordt beschreven hoe u uw Service Fabric mesh-toepassing en de bijbehorende resources kunt verplaatsen naar een andere Azure-regio. U kunt uw resources om een aantal redenen verplaatsen naar een andere regio. Als reactie op storingen is het bijvoorbeeld mogelijk om in alleen bepaalde regio's beschik bare functies of services te verkrijgen om te voldoen aan de interne beleids-en beheer vereisten, of als reactie op vereisten voor capaciteits planning.

 [Service Fabric mesh biedt geen ondersteuning](../azure-resource-manager/management/region-move-support.md#microsoftservicefabricmesh) voor het rechtstreeks verplaatsen van resources tussen Azure-regio's. U kunt resources echter indirect verplaatsen door een kopie van uw huidige Azure Resource Manager-sjabloon te implementeren in de nieuwe doel regio en vervolgens binnenkomend verkeer en afhankelijkheden te omleiden naar de zojuist gemaakte Service Fabric mesh-toepassing.

## <a name="prerequisites"></a>Vereisten

* Ingangs controller (zoals [Application Gateway](../application-gateway/index.yml)) die fungeert als intermediair voor het routeren van verkeer tussen clients en uw service Fabric mesh-toepassing
* Beschik baarheid van Service Fabric net (preview) in de Azure-doel regio ( `westus` , `eastus` of `westeurope` )

## <a name="prepare"></a>Voorbereiden

1. Maak een moment opname van de huidige status van uw Service Fabric mesh door de Azure Resource Manager sjabloon en de para meters van de meest recente implementatie te exporteren. Volg hiervoor de stappen in [sjabloon exporteren na de implementatie](../azure-resource-manager/templates/export-template-portal.md#export-template-after-deployment) met behulp van de Azure Portal. U kunt ook [Azure cli](../azure-resource-manager/management/manage-resource-groups-cli.md#export-resource-groups-to-templates), [Azure PowerShell](../azure-resource-manager/management/manage-resource-groups-powershell.md#export-resource-groups-to-templates)of [rest API](/rest/api/resources/resourcegroups/exporttemplate)gebruiken.

2. Exporteer, indien van toepassing, [andere resources in dezelfde resource groep](../azure-resource-manager/templates/export-template-portal.md#export-template-from-a-resource-group) voor een herimplementatie in de doel regio.

3. Bekijk (en bewerk indien nodig de geëxporteerde sjabloon) om ervoor te zorgen dat de bestaande eigenschaps waarden worden gebruikt die u in de doel regio wilt gebruiken. De nieuwe `location` (Azure-regio) is een para meter die u tijdens de herimplementatie kunt opgeven.

## <a name="move"></a>Verplaatsen

1. Een nieuwe resource groep maken (of een bestaande gebruiken) in de doel regio.

2. Volg de stappen in [resources implementeren vanuit aangepaste sjabloon](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template) met behulp van de Azure Portal met de geëxporteerde sjabloon. U kunt ook [Azure cli](../azure-resource-manager/templates/deploy-cli.md), [Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md)of [rest API](../azure-resource-manager/templates/deploy-rest.md)gebruiken.

3. Raadpleeg de richt lijnen voor afzonderlijke services die worden vermeld in het onderwerp [Azure-resources verplaatsen tussen regio's](../azure-resource-manager/management/move-region.md)voor hulp bij het verplaatsen van gerelateerde resources, zoals [Azure Storage accounts](../storage/common/storage-account-move.md).

## <a name="verify"></a>Verifiëren

1. Wanneer de implementatie is voltooid, test u de eind punten van de toepassing om de functionaliteit van uw toepassing te controleren.

2. U kunt ook de status van uw toepassing controleren door de status van de toepassing te controleren en de toepassings logboeken en ([AZ netcode-package-log](/cli/azure/ext/mesh/mesh/code-package-log)[)-](/cli/azure/ext/mesh/mesh/app#ext-mesh-az-mesh-app-show)opdrachten te bekijken met behulp van de [Azure service Fabric mesh cli](./service-fabric-mesh-quickstart-deploy-container.md#set-up-service-fabric-mesh-cli).

## <a name="commit"></a>Doorvoeren

Zodra u de equivalente functionaliteit van uw Service Fabric mesh-toepassing in de doel regio hebt bevestigd, configureert u de ingangs controller (bijvoorbeeld [Application Gateway](../application-gateway/redirect-overview.md)) om het verkeer om te leiden naar de nieuwe toepassing.

## <a name="clean-up-source-resources"></a>Bron resources opschonen

Als u het verplaatsen van de Service Fabric mesh-toepassing wilt volt ooien, [verwijdert u de bron toepassing en/of de bovenliggende resource groep](../azure-resource-manager/management/delete-resource-group.md).

## <a name="next-steps"></a>Volgende stappen

* [Azure-resources verplaatsen tussen regio's](../azure-resource-manager/management/move-region.md)
* [Ondersteuning voor het verplaatsen van Azure-resources in verschillende regio's](../azure-resource-manager/management/region-move-support.md)
* [Resources verplaatsen naar een nieuwe resourcegroep of een nieuw abonnement](../azure-resource-manager/management/move-resource-group-and-subscription.md)
* [Ondersteuning voor het verplaatsen van resources](../azure-resource-manager/management/move-support-resources.md
)
