---
title: 'Snelstart: Een Azure Synapse-werkruimte maken met behulp van een Azure Resource Manager-sjabloon'
description: Ontdek hoe u een Synapse-werkruimte maakt met behulp van een ARM-sjabloon (Azure Resource Manager-sjabloon).
services: azure-resource-manager
author: julieMSFT
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: jrasnick
ms.date: 08/07/2020
ms.openlocfilehash: 2b05220081dde4db6f07f21fb9978d9e466235ca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92740378"
---
# <a name="quickstart-create-an-azure-synapse-workspace-using-an-arm-template"></a>Quickstart: een Azure Synapse-werkruimte maken met behulp van een ARM-sjabloon

Met deze Azure Resource Manager-sjabloon (ARM-sjabloon) wordt een Azure Synapse-werkruimte met onderliggende Data Lake Storage gemaakt. De Azure Synapse-werkruimte is een beveiligbare samenwerkingsgrens voor analyseprocessen in Azure Synapse Analytics.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Als uw omgeving voldoet aan de vereisten en u benkend bent met het gebruik van ARM-sjablonen, selecteert u de knop **Implementeren naar Azure**. De sjabloon wordt in Azure Portal geopend.

[![Implementeren in Azure 1](../media/template-deployments/deploy-to-azure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FSynapse%2Fmaster%2FManage%2FDeployWorkspace%2Fazuredeploy.json)

## <a name="prerequisites"></a>Vereisten

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="review-the-template"></a>De sjabloon controleren

U kunt de sjabloon controleren door de koppeling **Visualiseren** te selecteren. Selecteer vervolgens **Sjabloon bewerken**.

[![Visualiseren](../media/template-deployments/template-visualize-button.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FSynapse%2Fmaster%2FManage%2FDeployWorkspace%2Fazuredeploy.json)

In de sjabloon zijn twee resources gedefinieerd:

- Storage-account
- Werkruimte

## <a name="deploy-the-template"></a>De sjabloon implementeren

1. Selecteer de volgende afbeelding om u aan te melden bij Azure en de sjabloon te openen. Met deze sjabloon maakt u een Synapse-werkruimte.

   [![Implementeren in Azure 2](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FSynapse%2Fmaster%2FManage%2FDeployWorkspace%2Fazuredeploy.json)

1. Voer de volgende waarden in of werk deze bij:

   - **Abonnement**: Selecteer een Azure-abonnement.
   - **Resourcegroep**: Selecteer **Nieuwe maken**, geef een unieke naam op voor de resourcegroep en selecteer **OK**. Met een nieuwe resourcegroep kunt u makkelijker resources opschonen.
   - **Regio**: Selecteer een regio.  Bijvoorbeeld **VS - centraal**.
   - **Naam**: Voer een naam in voor uw werkruimte.
   - **Aanmeldgegevens van SQL-beheerder**: Geef de gebruikersnaam van de beheerder voor de SQL-server op.
   - **Wachtwoord van SQL-beheerder**: Geef het beheerderswachtwoord voor de SQL-server op.
   - **Tagwaarden**: Accepteer de standaardwaarde.
   - **Controleren en maken**: Selecteren.
   - **Maken**: Selecteren.

## <a name="next-steps"></a>Volgende stappen

Als u meer wilt weten over Azure Synapse Analytics en Azure Resource Manager, vindt u meer informatie in de onderstaande artikelen.

- Lees een [Overzicht van Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)
- Meer informatie over [Azure Resource Manager](../azure-resource-manager/management/overview.md)
- [Uw eerste ARM-sjabloon maken en implementeren](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
