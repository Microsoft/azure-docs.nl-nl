---
title: Resources beheren-Azure Portal
description: Gebruik de Azure Portal en Azure Resource Manager om uw resources te beheren. Laat zien hoe u resources implementeert en verwijdert.
author: mumian
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: 12d704b0a3d92aa0585fb120f969000def282396
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/18/2021
ms.locfileid: "101092197"
---
# <a name="manage-azure-resources-by-using-the-azure-portal"></a>Azure-resources beheren met behulp van de Azure Portal

Meer informatie over het gebruik van de [Azure Portal](https://portal.azure.com) met [Azure Resource Manager](overview.md) voor het beheren van uw Azure-resources. Zie [Azure-resource groepen beheren met behulp van de Azure portal voor het](manage-resource-groups-portal.md)beheren van resource groepen.

Andere artikelen over het beheren van resources:

- [Azure-resources beheren met Azure CLI](manage-resources-cli.md)
- [Azure-resources beheren met behulp van Azure PowerShell](manage-resources-powershell.md)

[!INCLUDE [Handle personal data](../../../includes/gdpr-intro-sentence.md)]

## <a name="deploy-resources-to-a-resource-group"></a>Resources implementeren in een resource groep

Nadat u een resource manager-sjabloon hebt gemaakt, kunt u de Azure Portal gebruiken om uw Azure-resources te implementeren. Zie [Snelstartgids: Azure Resource Manager sjablonen maken en implementeren met behulp van de Azure Portal](../templates/quickstart-create-templates-use-the-portal.md)voor het maken van een sjabloon. Zie [resources implementeren met Resource Manager-sjablonen en Azure Portal](../templates/deploy-portal.md)voor meer informatie over het implementeren van een sjabloon met behulp van de portal.

## <a name="open-resources"></a>Open bronnen

Azure-resources zijn georganiseerd door Azure-Services en resource groepen. De volgende procedures laten zien hoe u een opslag account met de naam **mystorage0207** opent. De virtuele machine bevindt zich in een resource groep met de naam **mystorage0207rg**.

Een resource openen met het Service type:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Selecteer de Azure-service in het linkerdeel venster. In dit geval **opslag accounts**.  Als de service niet wordt weer gegeven, selecteert u **alle services** en selecteert u vervolgens het Service type.

    ![Azure-resource openen in de portal](./media/manage-resources-portal/manage-azure-resources-portal-open-service.png)

3. Selecteer de resource die u wilt openen.

    ![Scherm afbeelding die de geselecteerde resource markeert.](./media/manage-resources-portal/manage-azure-resources-portal-open-resource.png)

    Een opslag account ziet er als volgt uit:

    ![Scherm afbeelding die laat zien hoe een opslag account eruit ziet.](./media/manage-resources-portal/manage-azure-resources-portal-open-resource-storage.png)

Een resource openen per resource groep:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Selecteer **resource groepen** in het linkerdeel venster om de resource in de groep weer te geven.
3. Selecteer de resource die u wilt openen. 

## <a name="manage-resources"></a>Resources beheren

Bij het weer geven van een resource in de portal ziet u de opties voor het beheren van die specifieke resource.

![Azure-resources beheren](./media/manage-resources-portal/manage-azure-resources-portal-manage-resource.png)

In de scherm afbeelding ziet u de beheer opties voor een virtuele Azure-machine. U kunt bewerkingen uitvoeren, zoals het starten, opnieuw opstarten en het stoppen van een virtuele machine.

## <a name="delete-resources"></a>Resources verwijderen

1. Open de resource in de portal. Zie [resources openen](#open-resources)voor de stappen.
2. Selecteer **Verwijderen**. Op de volgende scherm afbeelding ziet u de beheer opties voor een virtuele machine.

    ![Azure-resource verwijderen](./media/manage-resources-portal/manage-azure-resources-portal-delete-resource.png)
3. Typ de naam van de resource om het verwijderen te bevestigen en selecteer vervolgens **verwijderen**.

Zie [Azure Resource Manager resource groep verwijderen](delete-resource-group.md)voor meer informatie over de manier waarop Azure Resource Manager het verwijderen van resources ordent.

## <a name="move-resources"></a>Resources verplaatsen

1. Open de resource in de portal. Zie [resources openen](#open-resources)voor de stappen.
2. Selecteer **verplaatsen**. De volgende scherm afbeelding toont de beheer opties voor een opslag account.

    ![Azure-resource verplaatsen](./media/manage-resources-portal/manage-azure-resources-portal-move-resource.png)
3. Selecteer **verplaatsen naar een andere resource groep** of **Ga naar een ander abonnement** , afhankelijk van uw behoeften.

Zie voor meer informatie [Resources verplaatsen naar een nieuwe resourcegroep of een nieuw abonnement](move-resource-group-and-subscription.md).

## <a name="lock-resources"></a>Resources vergrendelen

Vergren delen voor komt dat andere gebruikers in uw organisatie per ongeluk essentiële resources verwijderen of wijzigen, zoals een Azure-abonnement, resource groep of resource. 

1. Open de resource in de portal. Zie [resources openen](#open-resources)voor de stappen.
2. Selecteer **vergren delingen**. De volgende scherm afbeelding toont de beheer opties voor een opslag account.

    ![Azure-resource vergren delen](./media/manage-resources-portal/manage-azure-resources-portal-lock-resource.png)
3. Selecteer **toevoegen** en geef vervolgens de eigenschappen voor de vergren deling op.

Zie voor meer informatie [Resources vergrendelen met Azure Resource Manager](lock-resources.md).

## <a name="tag-resources"></a>Resources taggen

Door labels kunt u de resource groep en resources logisch ordenen. 

1. Open de resource in de portal. Zie [resources openen](#open-resources)voor de stappen.
2. Selecteer **Tags**. De volgende scherm afbeelding toont de beheer opties voor een opslag account.

    ![Label Azure-resource](./media/manage-resources-portal/manage-azure-resources-portal-tag-resource.png)
3. Geef de label eigenschappen op en selecteer vervolgens **Opslaan**.

Zie [Tags gebruiken om uw Azure-resources te organiseren](tag-resources.md#portal)voor meer informatie.

## <a name="monitor-resources"></a>Resources controleren

Wanneer u een resource opent, presenteert de portal standaard grafieken en tabellen voor het bewaken van het resource type. De volgende scherm afbeelding toont de grafieken voor een virtuele machine:

![Azure-resource bewaken](./media/manage-resources-portal/manage-azure-resources-portal-monitor-resource.png)

U kunt het speld pictogram in de rechter bovenhoek van de grafieken selecteren om de grafiek aan het dash board vast te maken. Zie [Dash boards maken en delen in de Azure Portal](../../azure-portal/azure-portal-dashboards.md)voor meer informatie over het werken met Dash boards.

## <a name="manage-access-to-resources"></a>Toegang tot resources beheren

[Toegangs beheer op basis van rollen (Azure RBAC) van Azure](../../role-based-access-control/overview.md) is de manier waarop u de toegang tot resources in azure beheert. Zie [Azure-rollen toewijzen met behulp van de Azure Portal](../../role-based-access-control/role-assignments-portal.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- Zie [overzicht van Azure Resource Manager](overview.md)voor meer informatie Azure Resource Manager.
- Zie [inzicht krijgen in de structuur en de syntaxis van Azure Resource Manager sjablonen](../templates/template-syntax.md)voor meer informatie over de syntaxis van de Resource Manager-sjabloon.
- Zie [Stapsgewijze zelf studies](../index.yml)voor meer informatie over het ontwikkelen van sjablonen.
- Zie [sjabloon verwijzing](/azure/templates/)voor het weer geven van de Azure Resource Manager sjabloon schema's.
