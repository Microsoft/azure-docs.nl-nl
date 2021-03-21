---
title: Resources implementeren met Power shell en sjabloon
description: Gebruik Azure Resource Manager en Azure PowerShell om resources te implementeren in Azure. De resources worden gedefinieerd in een resource manager-sjabloon of een Bicep-bestand.
ms.topic: conceptual
ms.date: 03/04/2021
ms.openlocfilehash: 784f17566ce4fb19a7ec5e3fd4a504d7c25f90fe
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102521625"
---
# <a name="deploy-resources-with-arm-templates-and-azure-powershell"></a>Resources implementeren met ARM-sjablonen en Azure PowerShell

In dit artikel wordt uitgelegd hoe u Azure PowerShell kunt gebruiken met Azure Resource Manager sjablonen (ARM-sjablonen) of Bicep bestanden om uw resources te implementeren in Azure. Als u niet bekend bent met de concepten van het implementeren en beheren van uw Azure-oplossingen, raadpleegt u overzicht van [sjabloon implementatie](overview.md) of [Bicep overzicht](bicep-overview.md).

Voor het implementeren van Bicep-bestanden hebt u [Azure PowerShell versie 5.6.0 of hoger](/powershell/azure/install-az-ps)nodig.

## <a name="prerequisites"></a>Vereisten

U hebt een sjabloon nodig om te implementeren. Als u er nog geen hebt, kunt u een [voorbeeld sjabloon](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json) downloaden en opslaan vanuit de Azure Quick Start-sjablonen opslag plaats. De lokale bestands naam die in dit artikel wordt gebruikt, is _C:\MyTemplates\azuredeploy.jsop_.

U moet Azure PowerShell installeren en verbinding maken met Azure:

- **Installeer Azure PowerShell-cmdlets op de lokale computer.** Zie [Aan de slag met Azure PowerShell](/powershell/azure/get-started-azureps) voor meer informatie.
- **Maak verbinding met Azure via [Connect-AZAccount](/powershell/module/az.accounts/connect-azaccount)**. Als u meerdere Azure-abonnementen hebt, moet u mogelijk ook [set-AzContext](/powershell/module/Az.Accounts/Set-AzContext)uitvoeren. Zie [meerdere Azure-abonnementen gebruiken](/powershell/azure/manage-subscriptions-azureps)voor meer informatie.

Als Power shell niet is geïnstalleerd, kunt u Azure Cloud Shell gebruiken. Zie [arm-sjablonen implementeren vanaf Azure Cloud shell](deploy-cloud-shell.md)voor meer informatie.

## <a name="deployment-scope"></a>Implementatie bereik

U kunt uw implementatie richten op een resource groep, een abonnement, een beheer groep of een Tenant. Afhankelijk van het bereik van de implementatie, gebruikt u verschillende opdrachten.

- Als u wilt implementeren in een **resource groep**, gebruikt u [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment):

  ```azurepowershell
  New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateFile <path-to-template-or-bicep>
  ```

- Als u wilt implementeren in een **abonnement**, gebruikt u [New-AzSubscriptionDeployment](/powershell/module/az.resources/new-azdeployment) . Dit is een alias van de `New-AzDeployment` cmdlet:

  ```azurepowershell
  New-AzSubscriptionDeployment -Location <location> -TemplateFile <path-to-template-or-bicep>
  ```

  Zie [resource groepen en-resources op abonnements niveau maken](deploy-to-subscription.md)voor meer informatie over implementaties op abonnements niveau.

- Als u wilt implementeren in een **beheer groep**, gebruikt u [New-AzManagementGroupDeployment](/powershell/module/az.resources/New-AzManagementGroupDeployment).

  ```azurepowershell
  New-AzManagementGroupDeployment -Location <location> -TemplateFile <path-to-template-or-bicep>
  ```

  Zie [resources maken op het niveau van de beheer groep](deploy-to-management-group.md)voor meer informatie over implementaties op het niveau van beheer groepen.

- Gebruik [New-AzTenantDeployment](/powershell/module/az.resources/new-aztenantdeployment)om te implementeren in een **Tenant**.

  ```azurepowershell
  New-AzTenantDeployment -Location <location> -TemplateFile <path-to-template-or-bicep>
  ```

  Zie [resources maken op Tenant niveau](deploy-to-tenant.md)voor meer informatie over implementaties op Tenant niveau.

Voor elk bereik moet de gebruiker die de sjabloon implementeert, over de vereiste machtigingen beschikken om resources te maken.

## <a name="deployment-name"></a>Naam van implementatie

Bij het implementeren van een ARM-sjabloon kunt u een naam opgeven voor de implementatie. Deze naam kan u helpen de implementatie op te halen uit de implementatie geschiedenis. Als u geen naam opgeeft voor de implementatie, wordt de naam van het sjabloon bestand gebruikt. Als u bijvoorbeeld een sjabloon implementeert `azuredeploy.json` met de naam en u geen implementatie naam opgeeft, wordt de implementatie een naam genoemd `azuredeploy` .

Telkens wanneer u een implementatie uitvoert, wordt een item toegevoegd aan de implementatie geschiedenis van de resource groep met de naam van de implementatie. Als u een andere implementatie uitvoert en deze dezelfde naam geeft, wordt de vorige vermelding vervangen door de huidige implementatie. Als u de unieke vermeldingen in de implementatie geschiedenis wilt behouden, geeft u elke implementatie een unieke naam.

Als u een unieke naam wilt maken, kunt u een wille keurig getal toewijzen.

```azurepowershell-interactive
$suffix = Get-Random -Maximum 1000
$deploymentName = "ExampleDeployment" + $suffix
```

U kunt ook een datum waarde toevoegen.

```azurepowershell-interactive
$today=Get-Date -Format "MM-dd-yyyy"
$deploymentName="ExampleDeployment"+"$today"
```

Als u gelijktijdige implementaties uitvoert naar dezelfde resource groep met dezelfde implementatie naam, wordt alleen de laatste implementatie voltooid. Implementaties met dezelfde naam die nog niet zijn voltooid, worden vervangen door de laatste implementatie. Als u bijvoorbeeld een implementatie uitvoert met de naam `newStorage` die een opslag account implementeert `storage1` en tegelijkertijd een andere implementatie uitvoert met de naam `newStorage` die een opslag account implementeert `storage2` , implementeert u slechts één opslag account. De naam van het resulterende opslag account is `storage2` .

Als u echter een implementatie uitvoert met de naam `newStorage` die een opslag account implementeert `storage1` en direct nadat de implementatie is voltooid, voert u `newStorage` `storage2` twee opslag accounts uit met een naam die een opslag account implementeert. Een heeft `storage1` de naam en de andere heet `storage2` . Maar u hebt slechts één vermelding in de implementatie geschiedenis.

Wanneer u een unieke naam voor elke implementatie opgeeft, kunt u deze gelijktijdig zonder conflict uitvoeren. Als u een implementatie uitvoert met de naam `newStorage1` die een opslag account implementeert `storage1` en tegelijkertijd een andere implementatie uitvoert met de naam `newStorage2` die een opslag account implementeert `storage2` , hebt u twee opslag accounts en twee vermeldingen in de implementatie geschiedenis.

Geef elke implementatie een unieke naam om conflicten met gelijktijdige implementaties te voor komen en te zorgen voor unieke vermeldingen in de implementatie geschiedenis.

## <a name="deploy-local-template-or-bicep-file"></a>Lokale sjabloon of Bicep-bestand implementeren

U kunt een sjabloon implementeren vanaf uw lokale computer of een die extern is opgeslagen. In deze sectie wordt het implementeren van een lokale sjabloon beschreven.

Als u implementeert in een resource groep die niet bestaat, maakt u de resource groep. De naam van de resource groep mag alleen alfanumerieke tekens, punten, onderstrepings teken, afbreek streepjes en haakjes bevatten. Het kan Maxi maal 90 tekens lang zijn. De naam kan niet eindigen met een punt.

```azurepowershell
New-AzResourceGroup -Name ExampleGroup -Location "Central US"
```

Als u een lokaal sjabloon-of Bicep-bestand wilt implementeren, gebruikt u de `-TemplateFile` para meter in de implementatie opdracht. In het volgende voor beeld ziet u ook hoe u een parameter waarde instelt die afkomstig is uit de sjabloon.

```azurepowershell
New-AzResourceGroupDeployment `
  -Name ExampleDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateFile <path-to-template-or-bicep>
```

Het volt ooien van de implementatie kan enkele minuten duren.

## <a name="deploy-remote-template"></a>Externe sjabloon implementeren

> [!NOTE]
> Momenteel biedt Azure PowerShell geen ondersteuning voor het implementeren van externe Bicep-bestanden. Als u een extern Bicep-bestand wilt implementeren, gebruikt u CLI Bicep om eerst het Bicep-bestand te compileren naar een JSON-sjabloon.

In plaats van ARM-sjablonen op uw lokale computer op te slaan, kunt u ze beter opslaan op een externe locatie. U kunt sjablonen opslaan in een opslagplaats voor broncodebeheer (zoals GitHub). U kunt de sjablonen ook opslaan in een Azure-opslagaccount voor gedeelde toegang in uw organisatie.

Als u implementeert in een resource groep die niet bestaat, maakt u de resource groep. De naam van de resource groep mag alleen alfanumerieke tekens, punten, onderstrepings teken, afbreek streepjes en haakjes bevatten. Het kan Maxi maal 90 tekens lang zijn. De naam kan niet eindigen met een punt.

```azurepowershell
New-AzResourceGroup -Name ExampleGroup -Location "Central US"
```

Als u een externe sjabloon wilt implementeren, gebruikt u de `-TemplateUri`-parameter.

```azurepowershell
New-AzResourceGroupDeployment `
  -Name remoteTemplateDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

In het voor gaande voor beeld is een openbaar toegankelijke URI vereist voor de sjabloon, die voor de meeste scenario's werkt, omdat uw sjabloon geen gevoelige gegevens mag bevatten. Als u gevoelige gegevens (zoals een beheerders wachtwoord) moet opgeven, geeft u die waarde als een beveiligde para meter door. Als u echter de toegang tot de sjabloon wilt beheren, kunt u de [sjabloon specificaties](#deploy-template-spec)gebruiken.

Voor het implementeren van extern gekoppelde sjablonen met een relatief pad dat is opgeslagen in een opslag account, gebruikt `QueryString` u om het SAS-token op te geven:

```azurepowershell
New-AzResourceGroupDeployment `
  -Name linkedTemplateWithRelativePath `
  -ResourceGroupName "myResourceGroup" `
  -TemplateUri "https://stage20210126.blob.core.windows.net/template-staging/mainTemplate.json" `
  -QueryString $sasToken
```

Zie [relatief pad gebruiken voor gekoppelde sjablonen](./linked-templates.md#linked-template)voor meer informatie.

## <a name="deploy-template-spec"></a>Sjabloonspecificatie implementeren

> [!NOTE]
> Momenteel biedt Azure PowerShell geen ondersteuning voor het maken van sjabloon specificaties door Bicep-bestanden op te geven. U kunt echter een Bicep-bestand maken met de resource [micro soft. resources/templateSpecs](/azure/templates/microsoft.resources/templatespecs) voor het implementeren van een sjabloon specificatie. Hier volgt een [voor beeld](https://github.com/Azure/azure-docs-json-samples/blob/master/create-template-spec-using-template/azuredeploy.bicep).
In plaats van een lokale of externe sjabloon te implementeren, kunt u een [sjabloon specificatie](template-specs.md)maken. De sjabloon specificatie is een resource in uw Azure-abonnement die een ARM-sjabloon bevat. Het is eenvoudig om de sjabloon veilig te delen met gebruikers in uw organisatie. U gebruikt op rollen gebaseerd toegangs beheer van Azure (Azure RBAC) om toegang te verlenen tot de sjabloon specificatie. Deze functie is momenteel beschikbaar als preview-versie.

In de volgende voor beelden ziet u hoe u een sjabloon specificatie maakt en implementeert.

Maak eerst de sjabloon specificatie door de ARM-sjabloon op te geven.

```azurepowershell
New-AzTemplateSpec `
  -Name storageSpec `
  -Version 1.0 `
  -ResourceGroupName templateSpecsRg `
  -Location westus2 `
  -TemplateJsonFile ./mainTemplate.json
```

Vervolgens haalt u de ID op voor de sjabloon specificatie en implementeert u deze.

```azurepowershell
$id = (Get-AzTemplateSpec -Name storageSpec -ResourceGroupName templateSpecsRg -Version 1.0).Version.Id

New-AzResourceGroupDeployment `
  -ResourceGroupName demoRG `
  -TemplateSpecId $id
```

Zie [Azure Resource Manager-sjabloon specificaties (preview-versie)](template-specs.md)voor meer informatie.

## <a name="preview-changes"></a>Preview-wijzigingen

Voordat u uw sjabloon implementeert, kunt u een voor beeld bekijken van de wijzigingen die door de sjabloon in uw omgeving worden aangebracht. Gebruik de [bewerking What-if](template-deploy-what-if.md) om te controleren of de sjabloon de wijzigingen aanbrengt die u verwacht. Wat-als ook de sjabloon voor fouten valideert.

## <a name="pass-parameter-values"></a>Parameter waarden door geven

Als u parameter waarden wilt door geven, kunt u inline-para meters of een parameter bestand gebruiken.

### <a name="inline-parameters"></a>Inline-para meters

Als u inline-para meters wilt door geven, geeft u de namen van de para meter op met de `New-AzResourceGroupDeployment` opdracht. Als u bijvoorbeeld een teken reeks en een matrix wilt door geven aan een sjabloon, gebruikt u:

```powershell
$arrayParam = "value1", "value2"
New-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile <path-to-template-or-bicep> `
  -exampleString "inline string" `
  -exampleArray $arrayParam
```

U kunt ook de inhoud van het bestand ophalen en deze inhoud als een inline-para meter opgeven.

```powershell
$arrayParam = "value1", "value2"
New-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile <path-to-template-or-bicep> `
  -exampleString $(Get-Content -Path c:\MyTemplates\stringcontent.txt -Raw) `
  -exampleArray $arrayParam
```

Het ophalen van een parameter waarde uit een bestand is handig wanneer u configuratie waarden moet opgeven. U kunt bijvoorbeeld [Cloud-init-waarden opgeven voor een virtuele Linux-machine](../../virtual-machines/linux/using-cloud-init.md).

Als u een matrix met objecten wilt door geven, maakt u hash-tabellen in Power shell en voegt u deze toe aan een matrix. Geef deze matrix als een para meter door tijdens de implementatie.

```powershell
$hash1 = @{ Name = "firstSubnet"; AddressPrefix = "10.0.0.0/24"}
$hash2 = @{ Name = "secondSubnet"; AddressPrefix = "10.0.1.0/24"}
$subnetArray = $hash1, $hash2
New-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile <path-to-template-or-bicep> `
  -exampleArray $subnetArray
```

### <a name="parameter-files"></a>Parameter bestanden

In plaats van parameters als inline waarden door te geven in uw script, is het wellicht eenvoudiger een JSON-bestand te gebruiken dat de parameterwaarden bevat. Het parameter bestand kan een lokaal bestand of een extern bestand met een toegankelijke URI zijn. Zowel de ARM-sjabloon als het Bicep-bestand gebruiken JSON-parameter bestanden.

Zie [Een Resource Manager-parameterbestand maken](parameter-files.md) voor meer informatie over het parameterbestand.

Als u een lokaal parameter bestand wilt door geven, gebruikt u de `TemplateParameterFile` para meter:

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile <path-to-template-or-bicep> `
  -TemplateParameterFile c:\MyTemplates\storage.parameters.json
```

Als u een extern parameter bestand wilt door geven, gebruikt u de `TemplateParameterUri` para meter:

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json `
  -TemplateParameterUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.parameters.json
```

## <a name="next-steps"></a>Volgende stappen

- Als u wilt terugkeren naar een geslaagde implementatie wanneer u een fout krijgt, raadpleegt u [herstellen bij fout naar geslaagde implementatie](rollback-on-error.md).
- Zie [Azure Resource Manager implementatie modi](deployment-modes.md)om op te geven hoe u resources wilt afhandelen die in de resource groep aanwezig zijn, maar die niet zijn gedefinieerd in de sjabloon.
- Zie [inzicht in de structuur en syntaxis van arm-sjablonen](template-syntax.md)voor informatie over het definiëren van para meters in uw sjabloon.
- Zie voor meer informatie over het implementeren van een sjabloon waarvoor een SAS-token is vereist een [persoonlijke arm-sjabloon implementeren met SAS-token](secure-template-with-sas-token.md).
