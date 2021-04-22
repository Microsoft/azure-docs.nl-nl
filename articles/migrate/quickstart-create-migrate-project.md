---
title: Snelstart voor het maken van een Azure Migrate project met behulp van Azure Resource Manager sjabloon.
description: In deze quickstart leert u hoe u een nieuw project Azure Migrate maken met behulp van Azure Resource Manager arm-sjabloon (ARM-sjabloon).
ms.date: 04/23/2021
author: rahulg1190
manager: bsiva
ms.author: rahugup
ms.topic: quickstart
ms.custom:
- subject-armqs
- mode-arm
ms.openlocfilehash: 5129a414c6b27e28d1821b72e7e0cfb744dc3452
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107893055"
---
# <a name="quickstart-create-an-azure-migrate-project-using-an-arm-template"></a>Quickstart: Een Azure Migrate maken met behulp van een ARM-sjabloon

In deze quickstart wordt beschreven hoe u een herstelproject Azure Migrate met behulp van een Azure Resource Manager sjabloon (ARM-sjabloon). Azure Migrate is een gecentraliseerde hub voor het beoordelen en migreren naar on-premises servers, infrastructuur, toepassingen en gegevens in Azure. Azure Migrate ondersteunt evaluatie en migratie van on-premises VMware-VM's, Hyper-V-VM's, fysieke servers, andere gevirtualiseerde VM's, databases, web-apps en virtuele bureaubladen.

Met deze sjabloon maakt u Azure Migrate project dat verder wordt gebruikt voor het evalueren en migreren van uw on-premises Servers, infrastructuur, toepassingen en gegevens van Azure.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Als uw omgeving voldoet aan de vereisten en u benkend bent met het gebruik van ARM-sjablonen, selecteert u de knop **Implementeren naar Azure**. De sjabloon wordt in Azure Portal geopend.

[![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-migrate-project-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Vereisten

Als u nog geen actief abonnement op Azure hebt, kunt u een [gratis account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="review-the-template"></a>De sjabloon controleren

De sjabloon die in deze quickstart wordt gebruikt, komt uit [Azure-quickstartsjablonen](https://azure.microsoft.com/en-us/resources/templates/101-migrate-project-create/).

:::code language="json" source="~/quickstart-templates/101-migrate-project-create/azuredeploy.json":::



## <a name="deploy-the-template"></a>De sjabloon implementeren

Voor het implementeren van de sjabloon **zijn abonnement,** **resourcegroep,** **projectnaam** en **locatie** vereist.

1. Selecteer **Implementeren in Azure** om u aan te melden bij Azure en de sjabloon te openen.

   [![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-migrate-project-create%2Fazuredeploy.json)

2. Typ of selecteer de volgende waarden:

   :::image type="content" source="media/quickstart-create-migrate-project-template/create-migrate-project.png" alt-text="Sjabloon voor het maken van een Azure Migrate project.":::

   - **Abonnement**: Selecteer uw Azure-abonnement.
   - **Resourcegroep:** selecteer een bestaande groep of selecteer **Nieuwe maken om** een groep toe te voegen.
   - **Locatie:** de standaardinstelling is de locatie van de resourcegroep en is niet meer beschikbaar nadat een resourcegroep is geselecteerd.
   - **Projectnaam migreren:** geef een naam op voor de kluis.
   - **Locatie:** selecteer de locatie waar u het Azure Migrate project en de resources ervan wilt implementeren. 

3. Klik **op de knop Controleren en** maken om de implementatie te starten. 
   
## <a name="validate-the-deployment"></a>De implementatie valideren

Als u wilt controleren of Azure Migrate project is gemaakt, gebruikt u de Azure Portal.


1. Navigeer naar Azure Migrate door te zoeken **Azure Migrate** in de zoekbalk op de Azure Portal. 
2. Klik op **de knop Ontdekken, evalueren en migreren** onder de tegel Windows, Linux en SQL Server migratie.
3. Selecteer het **Azure-abonnement** en **project** volgens de waarden die zijn opgegeven in de implementatie. 


## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een Azure Migrate gemaakt. Voor meer informatie over Azure Migrate en de mogelijkheden ervan gaat u verder met het Azure Migrate overzicht.

> [!div class="nextstepaction"]
> [Overzicht van Azure Migrate](migrate-services-overview.md)
> 