---
title: Een Azure Blockchain Service-lid maken met behulp van een Azure Resource Manager-sjabloon
description: Meer informatie over het maken van een lid van Azure Blockchain Service met behulp van een Azure Resource Manager-sjabloon.
services: azure-resource-manager
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs, references_regions
ms.date: 09/16/2020
ms.openlocfilehash: e9893336f2e6633519853aceecc945ee6bf0bf4b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91292758"
---
# <a name="quickstart-create-an-azure-blockchain-service-member-using-an-arm-template"></a>Quickstart: Een Azure Blockchain Service-lid maken met behulp van een ARM-sjabloon

In deze quickstart implementeert u een nieuw blockchain-lid en -consortium in Azure Blockchain Service met behulp van een Azure Resource Manager-sjabloon (ARM-sjabloon). Een lid van de Azure Blockchain Service is een blockchain-knooppunt in een privé-blockchain-netwerk van een consortium. Bij het inrichten van een lid kunt u een consortium-netwerk maken of het lid aan een netwerk toevoegen. U hebt voor een consortium-netwerk ten minste één lid nodig. Het aantal blockchain-leden dat deelnemers nodig hebben, is afhankelijk van uw scenario. Consortium-deelnemers kunnen een of meer blockchain-leden hebben of ze kunnen leden delen met andere deelnemers. Zie [Azure Blockchain Service-consortium](consortium.md) voor meer informatie over consortiums.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Als uw omgeving voldoet aan de vereisten en u benkend bent met het gebruik van ARM-sjablonen, selecteert u de knop **Implementeren naar Azure**. De sjabloon wordt in Azure Portal geopend.

[![Implementeren in Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-blockchain-asaservice%2Fazuredeploy.json)

## <a name="prerequisites"></a>Vereisten

Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="review-the-template"></a>De sjabloon controleren

De sjabloon die in deze quickstart wordt gebruikt, komt uit [Azure-snelstartsjablonen](https://azure.microsoft.com/resources/templates/201-blockchain-asaservice/).

:::code language="json" source="~/quickstart-templates/201-blockchain-asaservice/azuredeploy.json":::

Azure-resources die in de sjabloon zijn gedefinieerd:

* [**Microsoft.Blockchain/blockchainMembers**](/azure/templates/microsoft.blockchain/blockchainmembers)

## <a name="deploy-the-template"></a>De sjabloon implementeren

1. Selecteer de volgende koppeling om u aan te melden bij Azure en open een sjabloon.

    [![Implementeren in Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-blockchain-asaservice%2Fazuredeploy.json)

1. Geef de instellingen voor het lid van de Azure Blockchain-service op.

    Instelling | Beschrijving
    --------|------------
    Abonnement | Selecteer het Azure-abonnement dat u wilt gebruiken voor uw service. Als u meerdere abonnementen hebt, kiest u het abonnement waarin u wordt gefactureerd voor de resource.
    Resourcegroep | U kunt een nieuwe resourcegroepnaam maken of een bestaande naam uit uw abonnement kiezen.
    Regio | Kies een regio om de resourcegroep te maken. Alle leden van het consortium moeten zich op dezelfde locatie bevinden. Beschikbare locaties voor de implementatie zijn *westeurope, eastus, southeastasia, westeurope, northeurope, westus2* en *japaneast*. Functies zijn mogelijk niet in alle regio's beschikbaar. Azure Blockchain Data Manager is beschikbaar in de volgende Azure-regio's: VS - oost en Europa - west.
    Naam van Bc Lid | Kies een unieke naam voor het lid van de Azure Blockchain Service. De blockchain-naam mag alleen kleine letters en cijfers bevatten. Het eerste teken moet een letter zijn. De waarde moet minimaal 2 en maximaal 20 tekens lang zijn.
    Consortium-naam | Voer een unieke naam in. Zie [Azure Blockchain Service-consortium](consortium.md) voor meer informatie over consortiums.
    Wachtwoord voor lid | Het wachtwoord voor het standaardtransactieknooppunt van het lid. Gebruik het wachtwoord voor basisverificatie als u verbinding maakt met het openbare eindpunt van het standaardtransactieknooppunt van het blockchain-lid.
    Consortium-management-account-password | Het wachtwoord van het consortium-account wordt gebruikt voor het versleutelen van de persoonlijke sleutel voor het Ethereum-account dat voor het lid wordt gemaakt. Het wordt gebruikt voor consortiumbeheer.
    SKU-categorie | De prijscategorie voor de nieuwe service. Kies tussen de categorieën **Standard** en **Basic**. Gebruik de categorie *Basic* voor ontwikkeling, tests en het testen van concepten. Gebruik de categorie *Standard* voor implementaties van productiekwaliteit. U moet de categorie *Standaard* ook gebruiken als u Blockchain Data Manager gebruikt of een groot aantal privétransacties verzendt. Wanneer een lid is gemaakt, kan de prijscategorie niet meer worden gewijzigd van Basic in Standard en andersom.
    SKU-naam | De knooppuntconfiguratie en kosten voor uw nieuwe service. Gebruik **B0** voor Basic en **S0** voor Standard.
    Locatie | Kies een locatie om het lid te maken. De locatie van de resourcegroep wordt standaard gebruikt `[resourceGroup().location]`. Alle leden van het consortium moeten zich op dezelfde locatie bevinden. Beschikbare locaties voor de implementatie zijn *westeurope, eastus, southeastasia, westeurope, northeurope, westus2* en *japaneast*. Functies zijn mogelijk niet in alle regio's beschikbaar. Azure Blockchain Data Manager is beschikbaar in de volgende Azure-regio's: VS - oost en Europa - west.

1. Selecteer **Beoordelen en maken** om de sjabloon te controleren en te implementeren.

  Hier wordt de Azure Portal gebruikt om de sjabloon te implementeren. U kunt ook Azure PowerShell, Azure CLI en REST API gebruiken. Zie [Sjablonen implementeren](../../azure-resource-manager/templates/deploy-powershell.md) voor meer informatie over andere implementatiemethoden.

## <a name="review-deployed-resources"></a>Geïmplementeerde resources bekijken

U kunt de Azure-portal gebruiken om de details van het geïmplementeerde lid van Azure Blockchain Service weer te geven. Ga in de portal naar de resourcegroep met uw Azure Blockchain Service-lid. Selecteer het blockchain-lid dat u hebt gemaakt.

![Overzichtsdetails van geïmplementeerd Azure Blockchain-lid in de Azure-portal](./media/create-member-template/deployed-member.png)

## <a name="clean-up-resources"></a>Resources opschonen

U kunt voor de volgende quickstart of zelfstudie het blockchain-lid gebruiken dat u hebt gemaakt. Als u ze niet meer nodig hebt, kunt u alle resources die u voor de quickstart hebt gemaakt, verwijderen door de resourcegroep te verwijderen.

Zo verwijdert u de resourcegroep:

1. Ga in de Azure Portal naar **Resourcegroep** in het linkernavigatievenster en selecteer de resourcegroep die u wilt verwijderen.
2. Selecteer **Resourcegroep verwijderen**. Controleer de verwijdering door de naam van de resourcegroep in te voeren en **Verwijderen** te selecteren.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een lid van de Azure Blockchain Service en een nieuw consortium geïmplementeerd. Probeer de volgende quickstart om de Azure Blockchain Development Kit voor Ethereum te gebruiken om een koppeling te maken met een lid van de Azure Block Chain Service.

> [!div class="nextstepaction"]
> [Visual Studio Code gebruiken om verbinding te maken met Azure Blockchain Service](connect-vscode.md)