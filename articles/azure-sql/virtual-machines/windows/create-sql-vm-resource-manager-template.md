---
title: SQL Server-VM maken met behulp van een ARM-sjabloon
description: Meer informatie over het maken van een SQL-server op een Azure-VM (virtuele machine) met behulp van een ARM-sjabloon (Azure Resource Manager).
author: MashaMSFT
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: mathoma
ms.date: 06/29/2020
ms.service: virtual-machines-sql
ms.openlocfilehash: 9d0dd8ee1b99ddd2abf4fad154c70315a3d33c83
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/12/2020
ms.locfileid: "94556417"
---
# <a name="quickstart-create-sql-server-vm-using-an-arm-template"></a>Quickstart: SQL Server-VM maken met behulp van een ARM-sjabloon

Gebruik deze ARM-sjabloon (Azure Resource Manager) om een SQL-server te implementeren op een Azure-VM (virtuele machine). 

[!INCLUDE [About Azure Resource Manager](../../../../includes/resource-manager-quickstart-introduction.md)]

Als uw omgeving voldoet aan de vereisten en u benkend bent met het gebruik van ARM-sjablonen, selecteert u de knop **Implementeren naar Azure**. De sjabloon wordt in Azure Portal geopend.

[![Implementeren in Azure](../../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-sql-vm-new-storage%2fazuredeploy.json)

## <a name="prerequisites"></a>Vereisten

Voor de ARM-sjabloon van SQL Server-VM is het volgende vereist:

- De nieuwste versie van de [Azure CLI](/cli/azure/install-azure-cli) en/of [PowerShell](/powershell/scripting/install/installing-powershell). 
- Een vooraf geconfigureerde [resourcegroep](../../../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups) met een voorbereid [virtueel netwerk](../../../virtual-network/quick-create-portal.md) en [subnet](../../../virtual-network/virtual-network-manage-subnet.md#add-a-subnet).
- Een Azure-abonnement. Als u nog geen abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.


## <a name="review-the-template"></a>De sjabloon controleren

De sjabloon die in deze quickstart wordt gebruikt, komt uit [Azure-quickstartsjablonen](https://azure.microsoft.com/resources/templates/101-sql-vm-new-storage/).

:::code language="json" source="~/quickstart-templates/101-sql-vm-new-storage/azuredeploy.json":::

Er worden vijf Azure-resources gedefinieerd in de sjabloon: 

- [Microsoft.Network/publicIpAddresses](/azure/templates/microsoft.network/publicipaddresses): Hiermee maakt u een openbaar IP-adres. 
- [Microsoft.Network/networkSecurityGroups](/azure/templates/microsoft.network/networksecuritygroups): Hiermee maakt u een netwerkbeveiligingsgroep. 
- [Microsoft.Network/networkInterfaces](/azure/templates/microsoft.network/networkinterfaces): Configureert de netwerkinterface. 
- [Microsoft.Compute/virtualMachines](/azure/templates/microsoft.compute/virtualmachines): Maakt een virtuele machine in Azure. 
- [Microsoft.SqlVirtualMachine/SqlVirtualMachines](/azure/templates/microsoft.sqlvirtualmachine/sqlvirtualmachines): registreert de virtuele machine bij de SQL IaaS-agentextensie. 

Meer SQL Server op Azure VM-sjablonen vindt u in de [galerie met quickstart-sjablonen](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Sqlvirtualmachine&pageNumber=1&sort=Popular).


## <a name="deploy-the-template"></a>De sjabloon implementeren

1. Selecteer de volgende afbeelding om u aan te melden bij Azure en een sjabloon te openen. Met de sjabloon wordt een virtuele machine gemaakt waarop de beoogde SQL Server-versie is geïnstalleerd en die is geregistreerd bij de SQL IaaS-agentextensie. 

   [![Implementeren in Azure](../../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-sql-vm-new-storage%2fazuredeploy.json)

2. Typ of selecteer de volgende waarden.

    * **Abonnement**: Selecteer een Azure-abonnement.
    * **Resourcegroep**: De voorbereide resourcegroep voor uw SQL Server-VM. 
    * **Regio**: Selecteer een regio.  Bijvoorbeeld **VS - centraal**.
    * **Naam van virtuele machine**: Voer een naam in voor de SQL Server-VM. 
    * **Grootte van de virtuele machine**: Kies de juiste grootte voor uw virtuele machine in de vervolgkeuzelijst.
    * **Naam bestaand virtueel netwerk**: Voer de naam in van het voorbereide virtuele netwerk voor uw SQL Server-VM. 
    * **Bestaande Vnet-resourcegroep**: Voer de resourcegroep in waar het virtuele netwerk is voorbereid. 
    * **Naam bestaand subnet**: De naam van het voorbereide subnet. 
    * **Aanbieding voor installatiekopie**: Kies de SQL Server- en Windows Server-installatiekopie die het beste bij uw bedrijfsbehoeften past. 
    * **SQL Sku**: Kies de editie van SQL Server SKU die het beste bij uw bedrijfsbehoeften past. 
    * **Gebruikersnaam van beheerder**: De gebruikersnaam voor de beheerder van de virtuele machine. 
    * **Wachtwoord voor beheerder**: Het wachtwoord dat wordt gebruikt door het VM-beheerdersaccount. 
    * **Type werkbelasting voor opslag**:  Het type opslag voor de werkbelasting die het beste bij uw bedrijf past. 
    * **Aantal SQL-gegevensschijven**:  Het aantal schijven dat SQL Server gebruikt voor gegevensbestanden.  
    * **Gegevenspad**:  Het pad voor de SQL Server-gegevensbestanden. 
    * **Aantal SQL-logboekschijven**:  Het aantal schijven dat SQL Server gebruikt voor logboekbestanden. 
    * **Pad naar logboek**:  Het pad naar de SQL Server-logboekbestanden. 
    * **Locatie**:  De locatie voor alle resources; deze waarde moet de standaardinstelling van `[resourceGroup().location]` blijven. 

3. Selecteer **Controleren + maken**. Nadat de SQL Server-VM is geïmplementeerd, ontvangt u een melding.

Voor het implementeren van de sjabloon wordt de Azure-portal gebruikt. Naast Azure Portal kunt u ook Azure PowerShell, Azure CLI en REST API gebruiken. Zie [Sjablonen implementeren](../../../azure-resource-manager/templates/deploy-powershell.md) voor meer informatie over andere implementatiemethoden.

## <a name="review-deployed-resources"></a>Geïmplementeerde resources bekijken

U kunt de Azure CLI gebruiken om geïmplementeerde resources te controleren. 


```azurecli-interactive
echo "Enter the resource group where your SQL Server VM exists:" &&
read resourcegroupName &&
az resource list --resource-group $resourcegroupName 
```

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u deze niet meer nodig hebt, verwijdert u de resourcegroep met Azure CLI of Azure PowerShell:

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>Volgende stappen

Zie voor een stapsgewijze zelfstudie die u door het proces van het maken van een sjabloon leidt:

> [!div class="nextstepaction"]
> [Zelfstudie: Uw eerste ARM-sjabloon maken en implementeren](../../../azure-resource-manager/templates/template-tutorial-create-first-template.md)

Raadpleeg de volgende bronnen voor andere manieren om een SQL Server-VM te implementeren: 
- [Azure-portal](create-sql-vm-portal.md)
- [PowerShell](create-sql-vm-powershell.md)

Zie [een overzicht van SQL Server op Azure VM's](sql-server-on-azure-vm-iaas-what-is-overview.md)voor meer informatie.