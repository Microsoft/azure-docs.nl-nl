---
title: 'Quickstart: Een Resource Manager-sjabloon gebruiken om een virtuele Ubuntu Linux-machine te maken'
description: In deze snelstart leert u hoe u een Resource Manager-sjabloon gebruikt om een virtuele Linux-machine te maken
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.topic: quickstart
ms.workload: infrastructure
ms.date: 06/04/2020
ms.author: cynthn
ms.custom: subject-armqs
ms.openlocfilehash: 97a9d3632cb0e78b899844b1aaa84a030c6da23e
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102549614"
---
# <a name="quickstart-create-an-ubuntu-linux-virtual-machine-using-an-arm-template"></a>Quickstart: Een virtuele Ubuntu Linux-machine maken met een ARM-sjabloon

In deze quickstart wordt beschreven hoe u een Azure Resource Manager-sjabloon (ARM-sjabloon) gebruikt voor het implementeren van een virtuele Ubuntu Linux-machine (VM) in Azure.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Als uw omgeving voldoet aan de vereisten en u benkend bent met het gebruik van ARM-sjablonen, selecteert u de knop **Implementeren naar Azure**. De sjabloon wordt in Azure Portal geopend.

[![Implementeren in Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-vm-simple-linux%2fazuredeploy.json)

## <a name="prerequisites"></a>Vereisten

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="review-the-template"></a>De sjabloon controleren

De sjabloon die in deze snelstart wordt gebruikt, komt uit [Azure Quick Start-sjablonen](https://azure.microsoft.com/resources/templates/101-vm-simple-linux/).

:::code language="json" source="~/quickstart-templates/101-vm-simple-linux/azuredeploy.json":::


Verschillende resources worden in de sjabloon gedefinieerd:

- [**Microsoft.Network/virtualNetworks/subnets**](/azure/templates/Microsoft.Network/virtualNetworks/subnets): een subnet maken.
- [**Microsoft.Storage/storageAccounts**](/azure/templates/Microsoft.Storage/storageAccounts): een opslagaccount maken.
- [**Microsoft.Network/networkInterfaces**](/azure/templates/Microsoft.Network/networkInterfaces): een NIC maken.
- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/Microsoft.Network/networkSecurityGroups): een netwerkbeveiligingsgroep maken.
- [**Microsoft.Network/virtualNetworks**](/azure/templates/Microsoft.Network/virtualNetworks): een virtueel netwerk maken.
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/Microsoft.Network/publicIPAddresses): een openbaar IP-adres maken.
- [**Microsoft.Compute/virtualMachines**](/azure/templates/Microsoft.Compute/virtualMachines) : een virtuele machine maken.

## <a name="deploy-the-template"></a>De sjabloon implementeren

1. Selecteer de volgende afbeelding om u aan te melden bij Azure en een sjabloon te openen. Met de sjabloon worden een sleutelkluis en een geheim gemaakt.

    [![Implementeren in Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-vm-simple-linux%2fazuredeploy.json)

1. Typ of selecteer de volgende waarden. Gebruik waar mogelijk de standaardwaarden.

    - **Abonnement**: selecteer een Azure-abonnement.
    - **Resourcegroep**: selecteer een bestaande resourcegroep in de vervolgkeuzelijst of selecteer **Nieuwe maken**, geef een unieke naam op voor de nieuwe resourcegroep en klik vervolgens op **OK**.
    - **Locatie**: selecteer een locatie.  Bijvoorbeeld **VS - centraal**.
    - **Gebruikersnaam van beheerder**: geef een gebruikersnaam op, zoals *azureuser*.
    - **Verificatietype**: U kunt kiezen tussen het gebruik van een SSH-sleutel of een wachtwoord.
    - **Beheerderswachtwoord of sleutel**, afhankelijk van wat u kiest als verificatietype:
        - Als u **Wachtwoord** kiest moet het wachtwoord minstens 12 tekens lang zijn en voldoen aan de [gedefinieerde complexiteitsvereisten](faq.md#what-are-the-password-requirements-when-creating-a-vm).
        - Als u **sshPublicKey** kiest, plakt u hier uw openbare sleutel.
    - **DNS-labelvoorvoegsel**: voer een unieke ID in om te gebruiken als onderdeel van het DNS label.
    - **Versie van besturingssysteem van Ubuntu**: selecteer welke versie van Ubuntu u wilt uitvoeren op de VM.
    - **Locatie**: de standaardlocatie is hetzelfde als van de resourcegroep als die al bestaat.
    - **VM-grootte**: selecteer de [grootte](../sizes.md) die kan worden gebruikt voor de VM.
    - **Naam van virtueel netwerk**: naam van het vNet.
    - **Subnetnaam**: naam van het subnet dat de VM moet gebruiken.
    - **Naam van de netwerkbeveiligingsgroep**: naam van de netwerkbeveiligingsgroep.
1. Selecteer **Controleren + maken**. Selecteer nadat de validatie is afgerond **Maken** om de VM te maken en te implementeren.


Voor het implementeren van de sjabloon wordt de Azure-portal gebruikt. Naast de Azure-portal kunt u ook Azure CLI, Azure PowerShell en REST API gebruiken. Zie [Sjablonen implementeren](../../azure-resource-manager/templates/deploy-cli.md) voor meer informatie over andere implementatiemethoden.

## <a name="review-deployed-resources"></a>Geïmplementeerde resources bekijken

U kunt de Azure-portal ook gebruiken om de VM en andere resources die zijn gemaakt te controleren. Selecteer wanneer de implementatie is voltooid **Naar de resourcegroep gaan** om de VM en andere resources te bekijken.


## <a name="clean-up-resources"></a>Resources opschonen

Als u de resourcegroep niet meer nodig hebt, verwijdert u deze. Hierdoor worden ook de VM en alle resources in de resourcegroep verwijderd. 

1. Selecteer de **Resourcegroep**.
1. Selecteer **Verwijderen** op de pagina van de resourcegroep.
1. Typ ter bevestiging de naam van de resourcegroep. Selecteer vervolgens **Verwijderen**.


## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een eenvoudige virtuele machine geïmplementeerd met behulp van een ARM-sjabloon. Voor meer informatie over virtuele machines in Azure, gaat u verder met de zelfstudie voor virtuele Linux-machines.


> [!div class="nextstepaction"]
> [Zelfstudies over virtuele Linux-machines](./tutorial-manage-vm.md)