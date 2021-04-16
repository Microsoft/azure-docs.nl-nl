---
title: Een Azure Blockchain Service-lid maken - Azure PowerShell
description: Maak een Azure Blockchain Service-lid voor een blockchain-consortium behulp van Azure PowerShell.
ms.reviewer: ravastra
ms.date: 9/22/2020
ms.topic: quickstart
ms.custom:
- references_regions
- devx-track-azurepowershell
- mode-api
ms.openlocfilehash: 9087bda9c1979a98f4bbf9f8343d012c4cf3098c
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107529635"
---
# <a name="quickstart-create-an-azure-blockchain-service-blockchain-member-using-azure-powershell"></a>Quickstart: Een Azure Blockchain Service-blockchainlid maken met behulp van Azure PowerShell

In deze quickstart implementeert u een nieuw blockchainlid en -consortium in Azure Blockchain Service met behulp van Azure PowerShell.

## <a name="prerequisites"></a>Vereisten

Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

Als u PowerShell lokaal wilt gebruiken, moet u voor dit artikel de Az-module van PowerShell installeren en verbinding maken met uw Azure-account met behulp van de cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount). Zie [Azure PowerShell installeren](/powershell/azure/install-az-ps) voor meer informatie over het installeren van de Az-module van PowerShell.

> [!IMPORTANT]
> Zo lang de PowerShell-module **Az.Blockchain** in preview is, moet u deze afzonderlijk van de Az-module van PowerShell installeren met de cmdlet `Install-Module`. Zodra de PowerShell-module algemeen beschikbaar komt, wordt het onderdeel van toekomstige releases van Az PowerShell en is de module systeemeigen beschikbaar vanuit Azure Cloud Shell.

```azurepowershell-interactive
Install-Module -Name Az.Blockchain
```

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

## <a name="register-resource-provider"></a>Resourceprovider registreren

Als dit de eerste keer is dat u de Azure Blockchain-service gebruikt, moet u de resourceprovider van **Microsoft.Blockchain** registreren.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Blockchain
```

## <a name="choose-a-specific-azure-subscription"></a>Een specifiek Azure-abonnement kiezen

Als u meerdere Azure-abonnementen hebt, kiest u het juiste abonnement waarin de resource moet worden gefactureerd. Selecteer een specifiek abonnement met de cmdlet [set-AzContext](/powershell/module/az.accounts/set-azcontext).

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="define-variables"></a>Variabelen definiëren

U gebruikt herhaaldelijk verschillende gegevens. Maak variabelen om de gegevens op te slaan.

```azurepowershell-interactive
# Name of resource group used throughout this article
$resourceGroupName = 'myResourceGroup'

# Azure region
$location = 'eastus'
```

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Maak een [Azure-resourcegroep](../../azure-resource-manager/management/overview.md) met de cmdlet [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Een resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en groepsgewijs worden beheerd.

In het volgende voorbeeld wordt een resourcegroep gemaakt op basis van de naam in de variabele `$resourceGroupName` in de regio die is opgegeven in de variabele `$location`.

```azurepowershell-interactive
New-AzResourceGroup -Name $resourceGroupName -Location $location
```

## <a name="create-a-blockchain-member"></a>Een blockchain-lid maken

Een lid van de Azure Blockchain Service is een blockchain-knooppunt in een privé-blockchain-netwerk van een consortium.
Bij het inrichten van een lid kunt u een consortium-netwerk maken of het lid aan een netwerk toevoegen. U hebt voor een consortium-netwerk ten minste één lid nodig. Het aantal blockchain-leden dat deelnemers nodig hebben, is afhankelijk van uw scenario. Consortium-deelnemers kunnen een of meer blockchain-leden hebben of ze kunnen leden delen met andere deelnemers. Zie [Azure Blockchain Service-consortium](consortium.md) voor meer informatie over consortiums.

Er zijn verschillende parameters en eigenschappen die u moet doorgeven. Vervang de voorbeeldparameters met uw waarden.

```azurepowershell-interactive
$passwd = Read-Host -Prompt 'Enter the members default transaction node password' -AsSecureString
$csPasswd = Read-Host -Prompt 'Enter  the consortium account password' -AsSecureString

$memberParams = @{
  Name = 'myblockchainmember'
  ResourceGroupName = $resourceGroupName
  Consortium = 'myconsortium'
  ConsortiumManagementAccountPassword = $csPasswd
  Location = $location
  Password = $passwd
  Protocol = 'Quorum'
  Sku = 'S0'
}
New-AzBlockchainMember @memberParams
```

| Parameter | Beschrijving |
|---------|-------------|
| **ResourceGroupName** | De naam van de resourcegroep waarin Azure Blockchain Service-resources worden gemaakt. Gebruik de resourcegroep die u in de vorige sectie hebt gemaakt.
| **Naam** | A unieke naam waarmee uw Azure Blockchain Service-blockchainlid wordt geïdentificeerd. De naam wordt gebruikt voor het openbare adres van het eindpunt. Bijvoorbeeld `myblockchainmember.blockchain.azure.com`.
| **Locatie** | Azure-regio waarin het blockchainlid wordt gemaakt. Bijvoorbeeld `westus2`. Kies de locatie die zich het dichtst bij uw gebruikers of uw andere Azure-toepassingen bevindt. Functies zijn mogelijk niet in alle regio's beschikbaar. Azure Blockchain Data Manager is beschikbaar in de volgende Azure-regio's: VS - oost en Europa - west.
| **Wachtwoord** | Het wachtwoord voor het standaardtransactieknooppunt van het lid. Gebruik het wachtwoord voor basisverificatie als u verbinding maakt met het openbare eindpunt van het standaardtransactieknooppunt van het blockchain-lid.
| **Protocol** | Het blockchainprotocol. Momenteel wordt het _Quorum_-protocol ondersteund.
| **Consortium** | De naam van het consortium waaraan u kunt deelnemen of dat u kunt maken. Zie [Azure Blockchain Service-consortium](consortium.md) voor meer informatie over consortiums.
| **ConsortiumManagementAccountPassword** | Het wachtwoord voor het consortiumaccount wordt ook wel het lidaccountwachtwoord genoemd. Het wachtwoord van het lidaccount wordt gebruikt voor het versleutelen van de persoonlijke sleutel voor het Ethereum-account dat voor het lid wordt gemaakt. U gebruikt het lidaccount en het wachtwoord van het lidaccount voor het beheer van consortiums.
| **SKU** | Het type servicelaag. **S0** voor Standard of **B0** voor Basic. Gebruik de categorie _Basic_ voor ontwikkeling, tests en het testen van concepten. Gebruik de categorie _Standard_ voor implementaties van productiekwaliteit. U moet de categorie _Standard_ ook gebruiken als u Blockchain Data Manager gebruikt of een groot aantal privé transacties verzendt. Wanneer een lid is gemaakt, kan de prijscategorie niet meer worden gewijzigd van Basic in Standard en andersom.

Het duurt meer dan 10 minuten om het blockchainlid en de ondersteunende resources te maken.

## <a name="clean-up-resources"></a>Resources opschonen

U kunt voor de volgende quickstart of zelfstudie het blockchain-lid gebruiken dat u hebt gemaakt. Als u ze niet meer nodig hebt, kunt u alle resources die u voor de snelstart hebt gemaakt, verwijderen door resourcegroep `myResourceGroup` te verwijderen.

> [!CAUTION]
> In het volgende voorbeeld worden de opgegeven resourcegroep en alle resources erin verwijderd.
> Als resources buiten het bereik van dit artikel in de opgegeven resourcegroep bestaan, worden ze ook verwijderd.

```azurepowershell-interactive
Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een lid van de Azure Blockchain Service en een nieuw consortium geïmplementeerd. Probeer de volgende quickstart om de Azure Blockchain Development Kit voor Ethereum te gebruiken om een koppeling te maken met een lid van de Azure Block Chain Service.

> [!div class="nextstepaction"]
> [Visual Studio Code gebruiken om verbinding te maken met Azure Blockchain Service](connect-vscode.md)
