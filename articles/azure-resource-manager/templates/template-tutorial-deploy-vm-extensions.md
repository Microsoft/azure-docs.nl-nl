---
title: Extensies voor virtuele machines implementeren met sjablonen
description: Informatie over het implementeren van extensies voor virtuele machines met ARM-sjablonen (Azure Resource Manager).
author: mumian
ms.date: 03/26/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 320908ab4b24c43e8bd5209d4f32ef0211036958
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2021
ms.locfileid: "105628421"
---
# <a name="tutorial-deploy-virtual-machine-extensions-with-arm-templates"></a>Zelfstudie: Extensies voor virtuele machines implementeren met ARM-sjablonen

Meer informatie over het gebruik van [extensies voor virtuele Azure-machines](../../virtual-machines/extensions/features-windows.md) voor het uitvoeren van configuratie- en automatiseringstaken na de implementatie op virtuele Azure-machines. Er zijn veel verschillende VM-extensies beschikbaar voor gebruik met Azure-VM's. In deze zelfstudie implementeert u een aangepaste scriptextensie van een ARM-sjabloon (Azure Resource Manager) om een PowerShell-script uit te voeren op een Windows-VM. Met het script wordt een webserver op de virtuele machine geïnstalleerd.

Deze zelfstudie bestaat uit de volgende taken:

> [!div class="checklist"]
> * Een PowerShell-script voorbereiden
> * Een snelstartsjabloon openen
> * De sjabloon bewerken
> * De sjabloon implementeren

Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u dit artikel wilt voltooien, hebt u het volgende nodig:

* Visual Studio Code met de extensie Resource Manager Tools. Zie [Quickstart: ARM-sjablonen maken met Visual Studio Code](quickstart-create-templates-use-visual-studio-code.md).
* Gebruik een gegenereerd wachtwoord voor het beheerdersaccount van de virtuele machine om de beveiliging te verhogen. Hier volgt een voorbeeld voor het genereren van een wachtwoord:

    ```console
    openssl rand -base64 32
    ```

    Azure Key Vault is ontworpen om cryptografische sleutels en andere geheimen te beveiligen. Zie [Zelfstudie: Azure Key Vault integreren in ARM-sjabloonimplementatie](./template-tutorial-use-key-vault.md). We raden u ook aan om uw wachtwoord elke drie maanden te wijzigen.

## <a name="prepare-a-powershell-script"></a>Een PowerShell-script voorbereiden

U kunt een inline PowerShell-script of een scriptbestand gebruiken. In deze zelfstudie ziet u hoe u een scriptbestand gebruikt. Een PowerShell-script met de volgende inhoud wordt gedeeld vanuit [GitHub](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-vm-extension/installWebServer.ps1):

```azurepowershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

Als u ervoor kiest het bestand naar uw eigen locatie te publiceren, moet u het element `fileUri` in de sjabloon later in de zelfstudie bijwerken.

## <a name="open-a-quickstart-template"></a>Een snelstartsjabloon openen

Snelstartsjablonen voor Azure is een opslagplaats voor ARM-sjablonen. In plaats van een sjabloon helemaal vanaf de basis te maken, kunt u een voorbeeldsjabloon zoeken en aanpassen. De sjabloon die in deze zelfstudie wordt gebruikt, heet [Deploy a simple Windows VM](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/) (Een eenvoudige Windows-VM implementeren).

1. Selecteer in Visual Studio Code **File** > **Open File**.
1. Plak de volgende URL in het vak **File name**:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```

1. Selecteer **Open** om het bestand te openen.
    In de sjabloon zijn vijf resources gedefinieerd:

   * [**Microsoft.Storage/storageAccounts**](/azure/templates/Microsoft.Storage/storageAccounts).
   * [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses).
   * [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups).
   * [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks).
   * [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces).
   * [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines).

     Het is handig om enige basiskennis te hebben van de sjabloon voordat u deze gaat aanpassen.

1. Selecteer **File** > **Save As** om een kopie van het bestand op uw lokale computer op te slaan als *azuredeploy.json*.

## <a name="edit-the-template"></a>De sjabloon bewerken

Voeg een VM-extensieresource toe aan de bestaande sjabloon met de volgende inhoud:

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "apiVersion": "2020-12-01",
  "name": "[concat(variables('vmName'),'/', 'InstallWebServer')]",
  "location": "[parameters('location')]",
  "dependsOn": [
      "[concat('Microsoft.Compute/virtualMachines/',variables('vmName'))]"
  ],
  "properties": {
      "publisher": "Microsoft.Compute",
      "type": "CustomScriptExtension",
      "typeHandlerVersion": "1.7",
      "autoUpgradeMinorVersion":true,
      "settings": {
        "fileUris": [
          "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-vm-extension/installWebServer.ps1"
        ],
        "commandToExecute": "powershell.exe -ExecutionPolicy Unrestricted -File installWebServer.ps1"
      }
  }
}
```

Zie de [extensieverwijzing](/azure/templates/microsoft.compute/virtualmachines/extensions) voor meer informatie over deze resourcedefinitie. Hier volgen enkele belangrijke elementen:

* `name`: omdat de extensiebron een onderliggende resource van het virtuele-machineobject is, moet de naam het voorvoegsel van de naam van de virtuele machine hebben. Zie [Setnaam en -type voor onderliggende resources](child-resource-name-type.md).
* `dependsOn`: de extensiebron maken nadat u de virtuele machine hebt gemaakt.
* `fileUris`: dit zijn de locaties waar de scriptbestanden worden opgeslagen. Als u ervoor kiest om de meegeleverde locaties niet te gebruiken, moet u de waarden bijwerken.
* `commandToExecute`: met deze opdracht wordt het script aangeroepen.

Als u een inline script wilt gebruiken, verwijdert u `fileUris` en werkt u `commandToExecute` bij naar:

```powershell
powershell.exe Install-WindowsFeature -name Web-Server -IncludeManagementTools && powershell.exe remove-item 'C:\\inetpub\\wwwroot\\iisstart.htm' && powershell.exe Add-Content -Path 'C:\\inetpub\\wwwroot\\iisstart.htm' -Value $('Hello World from ' + $env:computername)
```

Met dit inline script wordt ook de inhoud van _iisstart.html_ bijgewerkt.

U moet ook de HTTP-poort openen zodat u toegang hebt tot de webserver.

1. Zoek `securityRules` in de sjabloon.
1. Voeg de volgende regel toe naast **default-allow-3389**.

    ```json
    {
      "name": "AllowHTTPInBound",
      "properties": {
        "priority": 1010,
        "access": "Allow",
        "direction": "Inbound",
        "destinationPortRange": "80",
        "protocol": "Tcp",
        "sourcePortRange": "*",
        "sourceAddressPrefix": "*",
        "destinationAddressPrefix": "*"
      }
    }
    ```

## <a name="deploy-the-template"></a>De sjabloon implementeren

Informatie over de implementatieprocedure vindt u in de sectie **De sjabloon implementeren** van [Zelfstudie: ARM-sjablonen met afhankelijke resources maken](./template-tutorial-create-templates-with-dependent-resources.md#deploy-the-template). Het wordt aanbevolen een gegenereerd wachtwoord te gebruiken voor het beheerdersaccount van de virtuele machine. Zie de sectie [Vereisten](#prerequisites) van dit artikel voor meer informatie.

Voer de volgende opdracht uit vanuit Cloud Shell om het openbare IP-adres van de VM op te halen:

```azurepowershell
(Get-AzPublicIpAddress -ResourceGroupName $resourceGroupName).IpAddress
```

Plak het IP-adres in een webbrowser. De gebruikelijke welkomstpagina van Internet Information Services (IIS) wordt geopend:

![De welkomstpagina van Internet Information Services](./media/template-tutorial-deploy-vm-extensions/resource-manager-template-deploy-extensions-customer-script-web-server.png)

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u de geïmplementeerd Azure-resources niet meer nodig hebt, kunt u deze opschonen door de resourcegroep te verwijderen.

1. Selecteer **Resourcegroepen** in het linkerdeelvenster van de Azure-portal.
2. Typ de naam van de resourcegroep in het vak **Filteren op naam**.
3. Selecteer de naam van de resourcegroep.
    De resourcegroep bevat zes resources.
4. Selecteer **Resourcegroep verwijderen** in het bovenste menu.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie gebruikt u een virtuele machine en een VM-extensie. Door de extensie is de IIS-webserver op de virtuele machine geïnstalleerd. Zie dit artikel voor meer informatie over het gebruik van de Azure SQL Database-extensie om een BACPAC-bestand te importeren:

> [!div class="nextstepaction"]
> [SQL-extensies implementeren](./template-tutorial-deploy-sql-extensions-bacpac.md)
