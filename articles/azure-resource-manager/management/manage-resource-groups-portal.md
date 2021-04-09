---
title: Resource groepen beheren-Azure Portal
description: Gebruik Azure Portal om uw resource groepen te beheren via Azure Resource Manager. Laat zien hoe u resource groepen kunt maken, weer geven en verwijderen.
author: mumian
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: jgao
ms.openlocfilehash: aabaad54dce52d57aaa4689f591351e290b0f9a3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101093693"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-the-azure-portal"></a>Azure Resource Manager resource groepen beheren met behulp van de Azure Portal

Meer informatie over het gebruik van de [Azure Portal](https://portal.azure.com) met [Azure Resource Manager](overview.md) voor het beheren van uw Azure-resource groepen. Zie [Azure-resources beheren met behulp van de Azure portal voor het](manage-resources-portal.md)beheer van Azure-resources.

Andere artikelen over het beheren van resource groepen:

- [Azure-resource groepen beheren met behulp van Azure CLI](manage-resources-cli.md)
- [Azure-resource groepen beheren met behulp van Azure PowerShell](manage-resources-powershell.md)

[!INCLUDE [Handle personal data](../../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-resource-group"></a>Wat is een resource groep?

Een resourcegroep is een container met gerelateerde resources voor een Azure-oplossing. De resourcegroep kan alle resources voor de oplossing bevatten of enkel de resources die u als groep wilt beheren. U bepaalt hoe resources worden toegewezen aan resourcegroepen op basis van wat voor uw organisatie het meest zinvol is. Over het algemeen voegt u resources die dezelfde levens cyclus delen, toe aan dezelfde resource groep, zodat u deze eenvoudig kunt implementeren, bijwerken en verwijderen als groep.

De resourcegroep slaat metagegevens op over de resources. Dat is de reden waarom u moet aangeven waar die metagegevens moeten worden opgeslagen als u een locatie voor de resourcegroep opgeeft. In verband met nalevingsvereisten moet u er mogelijk voor zorgen dat uw gegevens worden opgeslagen in een bepaalde regio.


## <a name="create-resource-groups"></a>Resource groepen maken

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. **Resource groepen** selecteren

    ![resource groep toevoegen](./media/manage-resource-groups-portal/manage-resource-groups-add-group.png)
3. Selecteer **Toevoegen**.
4. Voer de volgende waarden in:

   - **Abonnement**: Selecteer uw Azure-abonnement. 
   - **Resourcegroep**: Voer een nieuwe resourcegroepsnaam in. 
   - **Regio**: Selecteer een Azure-locatie, zoals **VS-centraal**.

     ![resource groep maken](./media/manage-resource-groups-portal/manage-resource-groups-create-group.png)
5. Selecteer **Beoordelen en maken**
6. Selecteer **Maken**. Het duurt een paar seconden om een resource groep te maken.
7. Selecteer **vernieuwen** in het bovenste menu om de lijst met resource groepen te vernieuwen en selecteer vervolgens de zojuist gemaakte resource groep om deze te openen. Of selecteer de **melding**(het klok pictogram) bovenaan en selecteer vervolgens **Ga naar resource groep** om de zojuist gemaakte resource groep te openen

    ![Ga naar resource groep](./media/manage-resource-groups-portal/manage-resource-groups-add-group-go-to-resource-group.png)

## <a name="list-resource-groups"></a>Resource groepen weer geven

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Als u de resource groepen wilt weer geven, selecteert u **resource groepen**

    ![Bladeren in resource groepen](./media/manage-resource-groups-portal/manage-resource-groups-list-groups.png)

3. Als u de informatie die wordt weer gegeven voor de resource groepen wilt aanpassen, selecteert u **kolommen bewerken**. De volgende scherm afbeelding toont de toegevoegde kolommen die u aan de weer gave kunt toevoegen:

## <a name="open-resource-groups"></a>Resource groepen openen

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
2. Selecteer **Resourcegroepen**.
3. Selecteer de resource groep die u wilt openen.

## <a name="delete-resource-groups"></a>Resourcegroepen verwijderen

1. Open de resource groep die u wilt verwijderen.  Zie [resource groepen openen](#open-resource-groups).
2. Selecteer **Resourcegroep verwijderen**.

    ![Azure-resource groep verwijderen](./media/manage-resource-groups-portal/delete-group.png)

Zie [Azure Resource Manager resource groep verwijderen](delete-resource-group.md)voor meer informatie over de manier waarop Azure Resource Manager het verwijderen van resources ordent.

## <a name="deploy-resources-to-a-resource-group"></a>Resources implementeren in een resource groep

Nadat u een resource manager-sjabloon hebt gemaakt, kunt u de Azure Portal gebruiken om uw Azure-resources te implementeren. Zie [Snelstartgids: Azure Resource Manager sjablonen maken en implementeren met behulp van de Azure Portal](../templates/quickstart-create-templates-use-the-portal.md)voor het maken van een sjabloon. Zie [resources implementeren met Resource Manager-sjablonen en Azure Portal](../templates/deploy-portal.md)voor meer informatie over het implementeren van een sjabloon met behulp van de portal.

## <a name="move-to-another-resource-group-or-subscription"></a>Verplaatsen naar een andere resource groep of een ander abonnement

U kunt de resources in de groep verplaatsen naar een andere resource groep. Zie voor meer informatie [Resources verplaatsen naar een nieuwe resourcegroep of een nieuw abonnement](move-resource-group-and-subscription.md).

## <a name="lock-resource-groups"></a>Resource groepen vergren delen

Vergren delen voor komt dat andere gebruikers in uw organisatie per ongeluk essentiële resources verwijderen of wijzigen, zoals een Azure-abonnement, resource groep of resource. 

1. Open de resource groep die u wilt vergren delen.  Zie [resource groepen openen](#open-resource-groups).
2. Selecteer in het linkerdeel venster de optie **vergren** delen.
3. Selecteer **toevoegen** om een vergren deling aan de resource groep toe te voegen.
4. Voer de **vergrendelings naam**, het **vergrendelings type** en de **notities** in. De vergrendelings typen bevatten **alleen-lezen** en **verwijderen**.

    ![Azure-resource groep vergren delen](./media/manage-resource-groups-portal/manage-resource-groups-add-lock.png)

Zie voor meer informatie [bronnen vergren delen om onverwachte wijzigingen te voor komen](lock-resources.md).

## <a name="tag-resource-groups"></a>Label resource groepen

U kunt Tags Toep assen op resource groepen en resources om uw assets logisch te organiseren. Zie [Tags gebruiken om uw Azure-resources te organiseren](tag-resources.md#portal)voor meer informatie.

## <a name="export-resource-groups-to-templates"></a>Resource groepen exporteren naar sjablonen

Zie voor meer informatie over het exporteren van sjablonen [één en meerdere resources exporteren naar sjabloon-Portal](../templates/export-template-portal.md).

## <a name="manage-access-to-resource-groups"></a>Toegang tot resource groepen beheren

[Toegangs beheer op basis van rollen (Azure RBAC) van Azure](../../role-based-access-control/overview.md) is de manier waarop u de toegang tot resources in azure beheert. Zie [Azure-rollen toewijzen met behulp van de Azure Portal](../../role-based-access-control/role-assignments-portal.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- Zie [overzicht van Azure Resource Manager](overview.md)voor meer informatie Azure Resource Manager.
- Zie [inzicht krijgen in de structuur en de syntaxis van Azure Resource Manager sjablonen](../templates/template-syntax.md)voor meer informatie over de syntaxis van de Resource Manager-sjabloon.
- Zie [Stapsgewijze zelf studies](../index.yml)voor meer informatie over het ontwikkelen van sjablonen.
- Zie [sjabloon verwijzing](/azure/templates/)voor het weer geven van de Azure Resource Manager sjabloon schema's.
