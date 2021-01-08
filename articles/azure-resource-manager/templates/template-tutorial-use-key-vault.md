---
title: Azure Key Vault gebruiken in sjablonen
description: Ontdek hoe u Azure Key Vault gebruikt om veilig parameterwaarden door te geven tijdens het implementeren van ARM-sjablonen (Azure Resource Manager).
author: mumian
ms.date: 04/23/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: seodec18
ms.openlocfilehash: 44a5131a7ad90feeeeff56e95b64e65f3f18855c
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/18/2020
ms.locfileid: "97674154"
---
# <a name="tutorial-integrate-azure-key-vault-in-your-arm-template-deployment"></a>Zelfstudie: Azure Key Vault integreren in ARM-sjabloonimplementatie

Ontdek hoe u geheime waarden uit Azure Key Vault ophaalt en deze als parameters doorgeeft tijdens de implementatie van een ARM-sjabloon (Azure Resource Manager). De parameterwaarde wordt nooit weergegeven, omdat u alleen naar de sleutelkluis-ID verwijst. U kunt een statische of dynamische ID gebruiken om naar de geheime waarden in de sleutelkluis te verwijzen. In deze zelfstudie wordt een statische ID gebruikt. Met de statische ID verwijst u naar de sleutelkluis in het parameterbestand van de sjabloon, niet het sjabloonbestand. Zie [Azure Key Vault gebruiken om veilige parameterwaarden door te geven tijdens de implementatie](./key-vault-parameter.md) voor meer informatie over beide methoden.

In de zelfstudie[Implementatievolgorde van resources instellen](./template-tutorial-create-templates-with-dependent-resources.md) maakt u een virtuele machine. U moet de gebruikersnaam en het wachtwoord voor de VM-beheerder opgeven. In plaats van het wachtwoord op te geven, kunt u het wachtwoord vooraf opslaan in een Azure-sleutelkluis en vervolgens de sjabloon aanpassen om het wachtwoord van de sleutelkluis tijdens de implementatie op te halen.

![Diagram waarin de integratie van een resource manager-sjabloon met een sleutelkluis wordt weergegeven](./media/template-tutorial-use-key-vault/resource-manager-template-key-vault-diagram.png)

Deze zelfstudie bestaat uit de volgende taken:

> [!div class="checklist"]
> * Een sleutelkluis voorbereiden
> * Een snelstartsjabloon openen
> * Het parameterbestand bewerken
> * De sjabloon implementeren
> * De implementatie valideren
> * Resources opschonen

Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

Raadpleeg [Complexe cloudimplementaties beheren met behulp van geavanceerde functies voor ARM-sjablonen](/learn/modules/manage-deployments-advanced-arm-template-features/) voor een Microsoft Learn-module waarin wordt gebruikgemaakt van een beveiligde waarde uit een sleutelkluis.

## <a name="prerequisites"></a>Vereisten

Als u dit artikel wilt voltooien, hebt u het volgende nodig:

* Visual Studio Code met de extensie Resource Manager Tools. Zie [Quickstart: ARM-sjablonen maken met Visual Studio Code](quickstart-create-templates-use-visual-studio-code.md).
* Gebruik een gegenereerd wachtwoord voor het beheerdersaccount van de virtuele machine om de beveiliging te verbeteren. Hier volgt een voorbeeld voor het genereren van een wachtwoord:

    ```console
    openssl rand -base64 32
    ```

    Controleer of het gegenereerde wachtwoord voldoet aan de wachtwoordvereisten voor virtuele machines. Elke Azure-service heeft eigen wachtwoordvereisten. Zie [Wat zijn de wachtwoordvereisten bij het maken van een virtuele machine?](../../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm) voor meer informatie over wachtwoordvereisten voor virtuele machines.

## <a name="prepare-a-key-vault"></a>Een sleutelkluis voorbereiden

In deze sectie maakt u een sleutelkluis en voegt u er een geheime waarde aan toe, zodat u de geheime waarde kunt ophalen wanneer u de sjabloon implementeert. Er zijn verschillende manieren om een sleutelkluis te maken. In deze zelfstudie gebruikt u Azure PowerShell om een [ARM-sjabloon](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorials-use-key-vault/CreateKeyVault.json) te implementeren. Deze sjabloon doet het volgende:

* Hiermee maakt u een sleutelkluis waarvoor de eigenschap `enabledForTemplateDeployment` is ingeschakeld. Deze eigenschap moet de waarde *true* hebben, anders kan tijdens het sjabloonimplementatieproces geen toegang worden verkregen tot de geheime waarden in de sleutelkluis.
* Hiermee voegt u een geheim toe aan de sleutelkluis. In het geheim wordt het wachtwoord van de VM-beheerder opgeslagen.

> [!NOTE]
> Als de gebruiker die de virtuele-machinesjabloon gaat implementeren niet de eigenaar of inzender is van de sleutelkluis, moet de eigenaar of inzender u toegang verlenen tot de machtiging `Microsoft.KeyVault/vaults/deploy/action` voor de sleutelkluis. Zie [Azure Key Vault gebruiken om veilige parameterwaarden door te geven tijdens de implementatie](./key-vault-parameter.md) voor meer informatie.

Als u het volgende Azure PowerShell-script wilt uitvoeren, selecteert u **Proberen** om Azure Cloud Shell te openen. Plak het script door met de rechtermuisknop op het shell-venster te klikken en **Plakken** te selecteren.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$upn = Read-Host -Prompt "Enter your user principal name (email address) used to sign in to Azure"
$secretValue = Read-Host -Prompt "Enter the virtual machine administrator password" -AsSecureString

$resourceGroupName = "${projectName}rg"
$keyVaultName = $projectName
$adUserId = (Get-AzADUser -UserPrincipalName $upn).Id
$templateUri = "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorials-use-key-vault/CreateKeyVault.json"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -keyVaultName $keyVaultName -adUserId $adUserId -secretValue $secretValue

Write-Host "Press [ENTER] to continue ..."
```

> [!IMPORTANT]
> * De naam van de resourcegroep is de naam van het project, maar met **rg** eraan toegevoegd. Als u de resources die u in deze zelfstudie hebt gemaakt, eenvoudig wilt [opschonen](#clean-up-resources), gebruikt u dezelfde projectnaam en resourcegroepsnaam wanneer u [de volgende sjabloon implementeert](#deploy-the-template).
> * De standaardnaam voor de geheime waarde is **vmAdminPassword**. Deze is vastgelegd in de sjabloon.
> * Als u de sjabloon de geheime waarde op wilt laten halen, moet u voor de sleutelkluis een toegangsbeleid met de naam **Toegang tot Azure Resource Manager inschakelen voor sjabloonimplementatie** inschakelen. Dit beleid wordt ingeschakeld in de sjabloon. Zie [Sleutelkluizen en geheime waarden implementeren](./key-vault-parameter.md#deploy-key-vaults-and-secrets)voor meer informatie over het toegangsbeleid.

De sjabloon heeft één uitvoerwaarde genaamd `keyVaultId`. U gebruikt deze ID en de geheime naam later in deze zelfstudie om de geheime waarde op te halen. De indeling van de resource-ID is:

```json
/subscriptions/<SubscriptionID>/resourceGroups/mykeyvaultdeploymentrg/providers/Microsoft.KeyVault/vaults/<KeyVaultName>
```

Wanneer u de ID kopieert en plakt, kan deze over meerdere regels worden verdeeld. Voeg de regels samen en verwijder extra spaties.

Als u de implementatie wilt valideren, voert u de volgende Power shell-opdracht uit in hetzelfde shell-deelvenster om de geheime waarde als leesbare tekst op te halen. De opdracht werkt alleen in dezelfde shell-sessie, omdat deze de variabele `$keyVaultName` gebruikt, die in het vorige PowerShell-script is gedefinieerd.

```azurepowershell
(Get-AzKeyVaultSecret -vaultName $keyVaultName  -name "vmAdminPassword").SecretValueText
```

Nu heeft u de voorbereidende stappen voor de sleutelkluis en geheime waarde uitgevoerd. In de volgende secties ziet u hoe u een bestaande sjabloon kunt aanpassen om de geheime waarde op te halen tijdens de implementatie.

## <a name="open-a-quickstart-template"></a>Een snelstartsjabloon openen

Snelstartsjablonen voor Azure is een opslagplaats voor ARM-sjablonen. In plaats van een sjabloon helemaal vanaf de basis te maken, kunt u een voorbeeldsjabloon zoeken en aanpassen. De sjabloon die in deze zelfstudie wordt gebruikt, heet [Een eenvoudige Windows-VM implementeren](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/).

1. Selecteer in Visual Studio Code **File** > **Open File**.

1. Plak de volgende URL in het vak **Bestandsnaam**:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```

1. Selecteer **Openen** om het bestand te openen. Het scenario is hetzelfde als de versie die wordt gebruikt in [Zelfstudie: ARM-sjablonen met afhankelijke resources maken](./template-tutorial-create-templates-with-dependent-resources.md).
   In de sjabloon zijn zes resources gedefinieerd:

   * [**Microsoft.Storage/storageAccounts**](/azure/templates/Microsoft.Storage/storageAccounts).
   * [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses).
   * [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups).
   * [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks).
   * [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces).
   * [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines).

   Het is handig om enige basiskennis te hebben van de sjabloon voordat u deze gaat aanpassen.

1. Selecteer **Bestand** > **Opslaan als** en sla het bestand vervolgens op uw lokale computer op als *azuredeploy.json*.

1. Herhaal stap 1-3 om de volgende URL te openen en sla het bestand dan op als *azuredeploy.parameters.json*.

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.parameters.json
    ```

## <a name="edit-the-parameters-file"></a>Het parameterbestand bewerken

Als u een statische ID gebruikt, hoeft u geen wijzigingen aan te brengen in het sjabloonbestand. De geheime waarde kan worden opgehaald door het parameterbestand voor de sjabloon te configureren.

1. Open in Visual Studio Code *azuredeploy.para meters.json* als deze nog niet is geopend.
1. Werk de parameter `adminPassword` bij naar:

    ```json
    "adminPassword": {
        "reference": {
            "keyVault": {
            "id": "/subscriptions/<SubscriptionID>/resourceGroups/mykeyvaultdeploymentrg/providers/Microsoft.KeyVault/vaults/<KeyVaultName>"
            },
            "secretName": "vmAdminPassword"
        }
    },
    ```

    > [!IMPORTANT]
    > Vervang de waarde voor `id` door de resource-ID van de sleutelkluis die u in de vorige stappen heeft gemaakt. De `secretName` wordt vastgelegd als **vmAdminPassword**.  Zie [Een sleutelkluis voorbereiden](#prepare-a-key-vault).

    ![Key Vault en Resource Manager-sjabloon integreren - virtuele-machine-implementatie - parameterbestand](./media/template-tutorial-use-key-vault/resource-manager-tutorial-create-vm-parameters-file.png)

1. Werk de volgende waarden bij:

    * `adminUsername`: De naam van het beheerdersaccount van de virtuele machine.
    * `dnsLabelPrefix`: Geef de waarde `dnsLabelPrefix` een naam.

    Zie de vorige afbeelding voor voorbeelden van namen.

1. Sla de wijzigingen op.

## <a name="deploy-the-template"></a>De sjabloon implementeren

1. Meld u aan bij [Azure Cloud Shell](https://shell.azure.com)

1. Kies uw favoriete omgeving door in de linkerbovenhoek **PowerShell** of **Bash** (voor CLI) te selecteren.  U moet de shell opnieuw starten wanneer u overschakelt.

    ![Bestand uploaden in Cloud Shell in de Azure-portal](./media/template-tutorial-use-template-reference/azure-portal-cloud-shell-upload-file.png)

1. Selecteer **Upload/download files** en selecteer **Uploaden**. Upload *azuredeploy.json* en *azuredeploy.para meters.json* naar Cloud Shell. Na het uploaden van het bestand kunt u de `ls`-opdracht en de `cat`-opdracht uitvoeren om te controleren of het bestand is geüpload.

1. Gebruik het volgende PowerShell-script om de sjabloon te implementeren.

    ```azurepowershell
    $projectName = Read-Host -Prompt "Enter the same project name that is used for creating the key vault"
    $location = Read-Host -Prompt "Enter the same location that is used for creating the key vault (i.e. centralus)"
    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -TemplateFile "$HOME/azuredeploy.json" `
        -TemplateParameterFile "$HOME/azuredeploy.parameters.json"

    Write-Host "Press [ENTER] to continue ..."
    ```

    Wanneer u de sjabloon implementeert, gebruikt u dezelfde resourcegroep als die van de sleutelkluis. Deze methode maakt het gemakkelijker om de resources op te schonen, omdat u slechts één resourcegroep hoeft te verwijderen in plaats van twee.

## <a name="validate-the-deployment"></a>De implementatie valideren

Wanneer u de virtuele machine heeft geïmplementeerd, test u de aanmeldingsreferenties met het wachtwoord dat is opgeslagen in de sleutelkluis.

1. Open de [Azure Portal](https://portal.azure.com).

1. Selecteer **Resourcegroepen** >  **\<*YourResourceGroupName*>**  > **simpleWinVM**.
1. Selecteer bovenaan **Verbinding maken**.
1. Selecteer **RDP-bestand downloaden** en volg de instructies voor het aanmelden bij de virtuele machine met behulp van het wachtwoord dat is opgeslagen in de sleutelkluis.

## <a name="clean-up-resources"></a>Resources opschonen

Als u uw Azure-resources niet meer nodig heeft, schoon dan de resources op die u heeft geïmplementeerd door de resourcegroep te verwijderen.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that is used for creating the key vault"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -Name $resourceGroupName

Write-Host "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u een geheime waarde uit uw Azure-sleutelkluis opgehaald. Vervolgens gebruikte u de geheime waarde in uw sjabloonimplementatie. Voor meer informatie over het gebruik van extensies van virtuele machines voor de uitvoering van post-implementatietaken raadpleegt u:

> [!div class="nextstepaction"]
> [Extensies van virtuele machines implementeren](./template-tutorial-deploy-vm-extensions.md)
