---
title: Implementatie scripts gebruiken in sjablonen | Microsoft Docs
description: gebruik implementatie scripts in Azure Resource Manager sjablonen.
services: azure-resource-manager
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 03/23/2021
ms.author: jgao
ms.openlocfilehash: 9f4c21a4b7e58c4eed3a62ea844eb11ccf4ecb49
ms.sourcegitcommit: a67b972d655a5a2d5e909faa2ea0911912f6a828
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104889379"
---
# <a name="use-deployment-scripts-in-arm-templates"></a>Implementatie scripts gebruiken in ARM-sjablonen

Meer informatie over het gebruik van implementatie scripts in azure-resource sjablonen (ARM-sjablonen). Met een nieuw resource type `Microsoft.Resources/deploymentScripts` , kunnen gebruikers scripts uitvoeren in sjabloon implementaties en uitvoerings resultaten controleren. Deze scripts kunnen worden gebruikt voor het uitvoeren van aangepaste stappen zoals:

- gebruikers toevoegen aan een map
- bewerkingen voor gegevens vlak uitvoeren, bijvoorbeeld blobs of een Seed-data base kopiëren
- een licentie sleutel opzoeken en valideren
- een zelfondertekend certificaat maken
- een object maken in azure AD
- IP-adres blokken zoeken op basis van aangepast systeem

De voor delen van het implementatie script:

- Eenvoudig te coderen, gebruiken en fouten opsporen. U kunt implementatie scripts ontwikkelen in uw favoriete ontwikkel omgevingen. De scripts kunnen worden inge sloten in sjablonen of in externe script bestanden.
- U kunt de script taal en het platform opgeven. Momenteel worden Azure PowerShell-en Azure CLI-implementatie scripts in de Linux-omgeving ondersteund.
- Het door geven van opdracht regel argumenten naar het script toestaan.
- Kan script uitvoer opgeven en deze weer door geven aan de implementatie.

De bron van het implementatie script is alleen beschikbaar in de regio's waar Azure container instance beschikbaar is.  Zie de [Beschik baarheid van resources voor Azure container instances in azure-regio's](../../container-instances/container-instances-region-availability.md).

> [!IMPORTANT]
> Een opslag account en een container exemplaar zijn nodig voor het uitvoeren van scripts en het oplossen van problemen. U hebt de opties om een bestaand opslag account op te geven, anders wordt het opslag account samen met het container exemplaar automatisch gemaakt door de script service. De twee automatisch gemaakte resources worden doorgaans verwijderd door de script service wanneer de uitvoering van het implementatie script wordt uitgevoerd in een Terminal status. U wordt gefactureerd voor de resources totdat de resources zijn verwijderd. Zie voor meer informatie het [opschonen van implementatie script bronnen](#clean-up-deployment-script-resources).

> [!IMPORTANT]
> De deploymentScripts resource API-versie 2020-10-01 ondersteunt [OnBehalfofTokens (OBO)](../../active-directory/develop/v2-oauth2-on-behalf-of-flow.md). Met behulp van OBO maakt de implementatie script service gebruik van het token van de implementatie-principal voor het maken van de onderliggende resources voor het uitvoeren van implementatie scripts, waaronder Azure container instance, een Azure-opslag account en roltoewijzingen voor de beheerde identiteit. In oudere API-versies wordt de beheerde identiteit gebruikt voor het maken van deze resources.
> Pogings logica voor Azure Sign in is nu ingebouwd in het wrapper-script. Als u machtigingen verleent in dezelfde sjabloon waar u implementatie scripts uitvoert. De implementatie script service probeert zich 10 minuten aan te melden met een interval van 10 seconden totdat de roltoewijzing van de beheerde identiteit wordt gerepliceerd.

## <a name="configure-the-minimum-permissions"></a>De minimale machtigingen configureren

Voor de API-versie 2020-10-01 of hoger van het implementatie script wordt de implementatie-Principal gebruikt voor het maken van onderliggende resources die vereist zijn voor het uitvoeren van de bron van het implementatie script, een opslag account en een Azure container-exemplaar. Als het script moet worden geverifieerd bij Azure en Azure-specifieke acties moeten worden uitgevoerd, raden we u aan het script aan te bieden met een door de gebruiker toegewezen beheerde identiteit. De beheerde identiteit moet de vereiste toegang hebben om de bewerking in het script te kunnen volt ooien.

Als u de machtigingen met minimale bevoegdheden wilt configureren, hebt u het volgende nodig:

- Wijs een aangepaste rol toe met de volgende eigenschappen aan de implementatie-Principal:

  ```json
  {
    "roleName": "deployment-script-minimum-privilege-for-deployment-principal",
    "description": "Configure least privilege for the deployment principal in deployment script",
    "type": "customRole",
    "IsCustom": true,
    "permissions": [
      {
        "actions": [
          "Microsoft.Storage/storageAccounts/*",
          "Microsoft.ContainerInstance/containerGroups/*",
          "Microsoft.Resources/deployments/*",
          "Microsoft.Resources/deploymentScripts/*"
        ],
      }
    ],
    "assignableScopes": [
      "[subscription().id]"
    ]
  }
  ```

  Als de Azure Storage en de resource providers van het Azure-container exemplaar niet zijn geregistreerd, moet u ook `Microsoft.Storage/register/action` en toevoegen `Microsoft.ContainerInstance/register/action` .

- Als er een beheerde identiteit wordt gebruikt, moet de implementatie-principal de rol **Managed Identity operator** (een ingebouwde rol) hebben die is toegewezen aan de beheerde ID-resource.

## <a name="sample-templates"></a>Voorbeeldsjablonen

De volgende JSON is een voor beeld. Zie het meest recente [sjabloon schema](/azure/templates/microsoft.resources/deploymentscripts)voor meer informatie.

```json
{
  "type": "Microsoft.Resources/deploymentScripts",
  "apiVersion": "2020-10-01",
  "name": "runPowerShellInline",
  "location": "[resourceGroup().location]",
  "kind": "AzurePowerShell", // or "AzureCLI"
  "identity": {
    "type": "userAssigned",
    "userAssignedIdentities": {
      "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myID": {}
    }
  },
  "properties": {
    "forceUpdateTag": "1",
    "containerSettings": {
      "containerGroupName": "mycustomaci"
    },
    "storageAccountSettings": {
      "storageAccountName": "myStorageAccount",
      "storageAccountKey": "myKey"
    },
    "azPowerShellVersion": "3.0",  // or "azCliVersion": "2.0.80",
    "arguments": "-name \\\"John Dole\\\"",
    "environmentVariables": [
      {
        "name": "UserName",
        "value": "jdole"
      },
      {
        "name": "Password",
        "secureValue": "jDolePassword"
      }
    ],
    "scriptContent": "
      param([string] $name)
      $output = 'Hello {0}. The username is {1}, the password is {2}.' -f $name,${Env:UserName},${Env:Password}
      Write-Output $output
      $DeploymentScriptOutputs = @{}
      $DeploymentScriptOutputs['text'] = $output
    ", // or "primaryScriptUri": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.ps1",
    "supportingScriptUris":[],
    "timeout": "PT30M",
    "cleanupPreference": "OnSuccess",
    "retentionInterval": "P1D"
  }
}
```

> [!NOTE]
> Het voor beeld is voor demonstratie doeleinden. De eigenschappen `scriptContent` en `primaryScriptUri` kunnen niet worden gecombineerd in een sjabloon.

> [!NOTE]
> De _scriptContent_ toont een script met meerdere regels.  Met de Azure Portal-en Azure DevOps-pijp lijn kan een implementatie script met meerdere regels niet worden geparseerd. U kunt de Power shell-opdrachten (met behulp van punt komma's of _\\ r \\ n_ of _\\ n_) koppelen aan één regel of de `primaryScriptUri` eigenschap gebruiken met een extern script bestand. Er zijn veel gratis Escape-of Escape-hulpprogram ma's voor de JSON-teken reeks beschikbaar. Bijvoorbeeld [https://www.freeformatter.com/json-escape.html](https://www.freeformatter.com/json-escape.html) .

Details van eigenschaps waarde:

- `identity`: Voor de API-versie 2020-10-01 of hoger van het implementatie script is een door de gebruiker toegewezen beheerde identiteit optioneel, tenzij u in het script specifieke acties van Azure moet uitvoeren.  Voor de API-versie 2019-10-01-preview is een beheerde identiteit vereist als de implementatie script service deze gebruikt voor het uitvoeren van de scripts. Op dit moment wordt alleen door de gebruiker toegewezen beheerde identiteit ondersteund.
- `kind`: Geef het type script op. Momenteel worden Azure PowerShell-en Azure CLI-scripts ondersteund. De waarden zijn **AzurePowerShell** en **AzureCLI**.
- `forceUpdateTag`: Als u deze waarde tussen de sjabloon implementaties wijzigt, wordt het implementatie script opnieuw uitgevoerd. Als u de `newGuid()` functies of gebruikt `utcNow()` , kunnen beide functies alleen worden gebruikt in de standaard waarde voor een para meter. Zie [Script meerdere keren uitvoeren](#run-script-more-than-once) voor meer informatie.
- `containerSettings`: Geef de instellingen op om Azure container instance aan te passen.  `containerGroupName` is voor het opgeven van de naam van de container groep. Als u niets opgeeft, wordt de groeps naam automatisch gegenereerd.
- `storageAccountSettings`: Geef de instellingen op voor het gebruik van een bestaand opslag account. Als u niets opgeeft, wordt er automatisch een opslag account gemaakt. Zie [een bestaand opslag account gebruiken](#use-existing-storage-account).
- `azPowerShellVersion`/`azCliVersion`: Geef de module versie op die moet worden gebruikt. Een lijst met [ondersteunde versies van Azure PowerShell](https://mcr.microsoft.com/v2/azuredeploymentscripts-powershell/tags/list)weer geven. Een lijst met [ondersteunde Azure cli-versies](https://mcr.microsoft.com/v2/azure-cli/tags/list)weer geven.

  >[!IMPORTANT]
  > Het implementatie script maakt gebruik van de beschik bare CLI-installatie kopieën van micro soft Container Registry (MCR). Het duurt ongeveer één maand om een CLI-installatie kopie te certificeren voor het implementatie script. Gebruik de CLI-versies die binnen 30 dagen zijn uitgebracht. Zie opmerkingen bij de [release van Azure cli](/cli/azure/release-notes-azure-cli)om de release datums voor de installatie kopieën te vinden. Als er een niet-ondersteunde versie wordt gebruikt, wordt in het fout bericht een lijst met ondersteunde versies weer gegeven.

- `arguments`: Geef de parameterwaarden op. De waarden worden gescheiden door een spatie.

  Met implementatie scripts worden de argumenten gesplitst in een matrix met teken reeksen door het aanroepen van de [CommandLineToArgvW ](/windows/win32/api/shellapi/nf-shellapi-commandlinetoargvw) -systeem aanroep. Deze stap is nodig omdat de argumenten worden door gegeven als een [opdracht eigenschap](/rest/api/container-instances/containergroups/createorupdate#containerexec) naar Azure container instance en de opdracht eigenschap een matrix van teken reeks is.

  Als de argumenten escape tekens bevatten, gebruikt u [JsonEscaper](https://www.jsonescaper.com/) om de tekens te verdubbelen. Plak de oorspronkelijke escape teken reeks in het hulp programma en selecteer vervolgens **Escape**.  Het hulp programma voert een dubbele escape teken reeks uit. In de vorige voorbeeld sjabloon is het argument bijvoorbeeld `-name \"John Dole\"` . De escape teken reeks is `-name \\\"John Dole\\\"` .

  Als u een ARM-sjabloon parameter van het type object wilt door geven als een argument, converteert u het object naar een teken reeks met behulp van de functie [String ()](./template-functions-string.md#string) en gebruikt u vervolgens de functie [replace ()](./template-functions-string.md#replace) om alle in te vervangen `\"` `\\\"` . Bijvoorbeeld:

  ```json
  replace(string(parameters('tables')), '\"', '\\\"')
  ```

  Zie voor meer informatie de [voorbeeld sjabloon](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-jsonEscape.json).

- `environmentVariables`: Geef de omgevings variabelen op die moeten worden door gegeven aan het script. Zie [implementatie scripts ontwikkelen](#develop-deployment-scripts)voor meer informatie.
- `scriptContent`: Geef de scriptinhoud op. Als u een extern script wilt uitvoeren, gebruikt u in plaats daarvan `primaryScriptUri`. Zie [inline-script gebruiken](#use-inline-scripts) en [extern script gebruiken](#use-external-scripts)voor voor beelden.
- `primaryScriptUri`: Geef een openbaar toegankelijke URL op voor het primaire implementatie script met ondersteunde bestands extensies. Zie [externe scripts gebruiken](#use-external-scripts)voor meer informatie.
- `supportingScriptUris`: Geef een matrix met openbaar toegankelijke Url's op voor de ondersteunende bestanden die worden genoemd in `scriptContent` of `primaryScriptUri` . Zie [externe scripts gebruiken](#use-external-scripts)voor meer informatie.
- `timeout`: Geef de maximale toegestane uitvoeringstijd voor het script op, opgegeven in de [ISO 8601-indeling](https://en.wikipedia.org/wiki/ISO_8601). Standaardwaarde is **P1D**.
- `cleanupPreference`. Geef de voor keur op voor het opschonen van implementatie resources wanneer de uitvoering van het script wordt uitgevoerd in een Terminal status. De standaard instelling is **altijd**, wat betekent dat de resources worden verwijderd ondanks de status van de Terminal (geslaagd, mislukt, geannuleerd). Zie [Resources van implementatiescripts opschonen](#clean-up-deployment-script-resources) voor meer informatie.
- `retentionInterval`: Geef het interval op waarvoor de service de implementatie script bronnen behoudt nadat de uitvoering van het implementatie script een Terminal status heeft bereikt. De resources van het implementatie script worden verwijderd wanneer deze duur verloopt. De duur is gebaseerd op het [ISO 8601-patroon](https://en.wikipedia.org/wiki/ISO_8601). Het retentie-interval ligt tussen 1 en 26 uur (PT26H). Deze eigenschap wordt gebruikt wanneer `cleanupPreference` is ingesteld op **OnExpiration**. Zie [Resources van implementatiescripts opschonen](#clean-up-deployment-script-resources) voor meer informatie.

### <a name="additional-samples"></a>Aanvullende voor beelden

- Voor [Beeld 1](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault.json): een sleutel kluis maken en implementatie script gebruiken om een certificaat toe te wijzen aan de sleutel kluis.
- Voor [Beeld 2](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault-subscription.json): een resource groep maken op abonnements niveau, een sleutel kluis maken in de resource groep en vervolgens het implementatie script gebruiken om een certificaat toe te wijzen aan de sleutel kluis.
- Voor [Beeld 3](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault-mi.json): een door de gebruiker toegewezen beheerde identiteit maken, de rol Inzender toewijzen aan de identiteit op het niveau van de resource groep, een sleutel kluis maken en vervolgens het implementatie script gebruiken om een certificaat toe te wijzen aan de sleutel kluis.

## <a name="use-inline-scripts"></a>Inline-scripts gebruiken

Voor de volgende sjabloon is één resource gedefinieerd met het `Microsoft.Resources/deploymentScripts` type. Het gemarkeerde deel is het inline-script.

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-helloworld.json" range="1-44" highlight="24-30":::

> [!NOTE]
> Omdat de inline-implementatie scripts tussen dubbele aanhalings tekens staan, moeten de teken reeksen in de implementatie scripts worden geescaped met behulp van een back slash (**&#92;**) of tussen enkele aanhalings tekens. U kunt ook overwegen om de teken reeks vervanging te gebruiken zoals deze wordt weer gegeven in het vorige JSON-voor beeld.

Het script neemt één para meter en de waarde van de para meter. `DeploymentScriptOutputs` wordt gebruikt voor het opslaan van uitvoer. In de sectie outputs `value` ziet u hoe u de opgeslagen waarden kunt openen. `Write-Output` wordt gebruikt voor fout opsporing. Zie [implementatie scripts bewaken en problemen oplossen](#monitor-and-troubleshoot-deployment-scripts)voor meer informatie over het openen van het uitvoer bestand. Zie [voorbeeld sjablonen](#sample-templates)voor de beschrijving van de eigenschap.

Als u het script wilt uitvoeren, selecteert u **proberen** de Cloud shell te openen en plakt u de volgende code in het deel venster shell.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the name of the resource group to be created"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location

New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.json"

Write-Host "Press [ENTER] to continue ..."
```

De uitvoer ziet er als volgt uit:

![Implementatie script van Resource Manager-sjabloon Hello wereld-uitvoer](./media/deployment-script-template/resource-manager-template-deployment-script-helloworld-output.png)

## <a name="use-external-scripts"></a>Externe scripts gebruiken

Naast inline-scripts kunt u ook externe script bestanden gebruiken. Alleen primaire Power shell-scripts met de bestands extensie _PS1_ worden ondersteund. Voor CLI-scripts kunnen de primaire scripts alle uitbrei dingen (of zonder een uitbrei ding) hebben, zolang de scripts geldige bash-scripts zijn. Als u externe script bestanden wilt gebruiken, vervangt u door `scriptContent` `primaryScriptUri` . Bijvoorbeeld:

```json
"primaryScriptUri": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.ps1",
```

Zie de [voorbeeld sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/deployment-script/deploymentscript-helloworld-primaryscripturi.json)voor meer informatie.

De externe script bestanden moeten toegankelijk zijn. Als u uw script bestanden wilt beveiligen die zijn opgeslagen in azure Storage-accounts, genereert u een SAS-token en neemt u dit op in de URI voor de sjabloon. Stel de vervaltijd zo in, dat er genoeg tijd is om de implementatie te voltooien. Zie voor meer informatie [implementeren van persoonlijke arm-sjabloon met SAS-token](./secure-template-with-sas-token.md).

U bent verantwoordelijk voor het garanderen van de integriteit van de scripts waarnaar wordt verwezen door het implementatie script, hetzij `primaryScriptUri` of `supportingScriptUris` . Alleen verwijzen naar scripts die u vertrouwt.

## <a name="use-supporting-scripts"></a>Ondersteunende scripts gebruiken

U kunt complexe logica van elkaar scheiden in een of meer ondersteunende script bestanden. `supportingScriptUris`Met de eigenschap kunt u indien nodig een matrix met uri's naar de ondersteunende script bestanden opgeven:

```json
"scriptContent": "
    ...
    ./Create-Cert.ps1
    ...
"

"supportingScriptUris": [
  "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/create-cert.ps1"
],
```

Ondersteunende script bestanden kunnen worden aangeroepen vanuit inline scripts en primaire script bestanden. Ondersteunende script bestanden hebben geen beperkingen voor de bestands extensie.

De ondersteunende bestanden worden naar `azscripts/azscriptinput` tijdens de runtime gekopieerd. Gebruik relatief pad om te verwijzen naar de ondersteunende bestanden van inline-scripts en primaire script bestanden.

## <a name="work-with-outputs-from-powershell-script"></a>Werken met uitvoer van Power shell-script

De volgende sjabloon laat zien hoe waarden worden door gegeven tussen twee `deploymentScripts` resources:

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-basic.json" range="1-68" highlight="30-31,50":::

In de eerste resource definieert u een variabele met de naam `$DeploymentScriptOutputs` , en gebruikt u deze om de uitvoer waarden op te slaan. Als u de uitvoer waarde van een andere resource in de sjabloon wilt openen, gebruikt u:

```json
reference('<ResourceName>').output.text
```

## <a name="work-with-outputs-from-cli-script"></a>Werken met uitvoer van CLI-script

Met de CLI/bash-ondersteuning van het Power shell-implementatie script wordt geen gemeen schappelijke variabele voor het opslaan van script uitvoer weer gegeven, maar er is een omgevings variabele `AZ_SCRIPTS_OUTPUT_PATH` met de naam die de locatie opslaat waar het bestand uitvoer van het script zich bevindt. Als een implementatie script wordt uitgevoerd vanuit een resource manager-sjabloon, wordt deze omgevings variabele automatisch voor u ingesteld door de bash-shell.

Uitvoer van het implementatie script moet worden opgeslagen op de `AZ_SCRIPTS_OUTPUT_PATH` locatie en de uitvoer moet een geldig JSON-teken reeks object zijn. De inhoud van het bestand moet worden opgeslagen als een sleutel/waarde-paar. Een matrix met teken reeksen wordt bijvoorbeeld opgeslagen als `{ "MyResult": [ "foo", "bar"] }` .  Alleen de matrix resultaten worden opgeslagen, bijvoorbeeld `[ "foo", "bar" ]` is ongeldig.

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-basic-cli.json" range="1-44" highlight="32":::

[JQ](https://stedolan.github.io/jq/) wordt in het vorige voor beeld gebruikt. Het wordt geleverd met de container installatie kopieën. Zie [ontwikkel omgeving configureren](#configure-development-environment).

## <a name="use-existing-storage-account"></a>Bestaand opslag account gebruiken

Een opslag account en een container exemplaar zijn nodig voor het uitvoeren van scripts en het oplossen van problemen. U hebt de opties om een bestaand opslag account op te geven, anders wordt het opslag account samen met het container exemplaar automatisch gemaakt door de script service. De vereisten voor het gebruik van een bestaand opslag account:

- De volgende typen opslag accounts worden ondersteund:

    | SKU             | Ondersteund soort     |
    |-----------------|--------------------|
    | Premium_LRS     | FileStorage        |
    | Premium_ZRS     | FileStorage        |
    | Standard_GRS    | Opslag, StorageV2 |
    | Standard_GZRS   | StorageV2          |
    | Standard_LRS    | Opslag, StorageV2 |
    | Standard_RAGRS  | Opslag, StorageV2 |
    | Standard_RAGZRS | StorageV2          |
    | Standard_ZRS    | StorageV2          |

    Deze combi Naties bieden ondersteuning voor bestands shares. Zie [een Azure-bestands share](../../storage/files/storage-how-to-create-file-share.md) en [typen opslag accounts](../../storage/common/storage-account-overview.md)maken voor meer informatie.

- De firewall regels van het opslag account worden nog niet ondersteund. Raadpleeg [Firewalls en virtuele netwerken voor Azure Storage configureren](../../storage/common/storage-network-security.md) voor meer informatie.
- Implementatie-Principal moet machtigingen hebben voor het beheren van het opslag account, waaronder lezen, maken, bestands shares verwijderen.

Als u een bestaand opslag account wilt opgeven, voegt u de volgende JSON toe aan het eigenschaps element van `Microsoft.Resources/deploymentScripts` :

```json
"storageAccountSettings": {
  "storageAccountName": "myStorageAccount",
  "storageAccountKey": "myKey"
},
```

- `storageAccountName`: Geef de naam op van het opslag account.
- `storageAccountKey`: Geef een van de sleutels voor het opslag account op. U kunt de functie [listkeys ophalen ()](./template-functions-resource.md#listkeys) gebruiken om de sleutel op te halen. Bijvoorbeeld:

    ```json
    "storageAccountSettings": {
        "storageAccountName": "[variables('storageAccountName')]",
        "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')), '2019-06-01').keys[0].value]"
    }
    ```

Zie [voorbeeld sjablonen](#sample-templates) voor een volledige `Microsoft.Resources/deploymentScripts` definitie-voor beeld.

Wanneer een bestaand opslag account wordt gebruikt, maakt de script service een bestands share met een unieke naam. Zie [implementatie script bronnen opschonen](#clean-up-deployment-script-resources) voor de manier waarop de script service de bestands share opschoont.

## <a name="develop-deployment-scripts"></a>Implementatie scripts ontwikkelen

### <a name="handle-non-terminating-errors"></a>Niet-afsluit fouten verwerken

U kunt bepalen hoe Power shell reageert op niet-afsluit fouten door de `$ErrorActionPreference` variabele in uw implementatie script te gebruiken. Als de variabele niet in uw implementatie script is ingesteld, gebruikt de script service de standaard waarde **door gaan**.

De script service stelt de inrichtings status van de resource in op **mislukt** wanneer het script een fout ondervindt ondanks de instelling van `$ErrorActionPreference` .

### <a name="pass-secured-strings-to-deployment-script"></a>Beveiligde teken reeksen door geven aan implementatie script

Als omgevings variabelen (EnvironmentVariable) in uw container instanties worden ingesteld, kunt u een dynamische configuratie opgeven van de toepassing of het script dat door de container wordt uitgevoerd. Met het implementatie script worden niet-beveiligde en beveiligde omgevings variabelen op dezelfde manier verwerkt als Azure container instance. Zie [omgevings variabelen instellen in container instanties](../../container-instances/container-instances-environment-variables.md#secure-values)voor meer informatie. Zie [voorbeeld sjablonen](#sample-templates)voor een voor beeld.

De Maxi maal toegestane grootte voor omgevings variabelen is 64 KB.

## <a name="monitor-and-troubleshoot-deployment-scripts"></a>Implementatie scripts bewaken en problemen oplossen

De script service maakt een [opslag account](../../storage/common/storage-account-overview.md) (tenzij u een bestaand opslag account opgeeft) en een [container exemplaar](../../container-instances/container-instances-overview.md) voor het uitvoeren van een script. Als deze resources automatisch worden gemaakt door de script service, hebben beide bronnen het `azscripts` achtervoegsel in de resource namen.

![Bron namen van Resource Manager-sjabloon implementatie script](./media/deployment-script-template/resource-manager-template-deployment-script-resources.png)

Het gebruikers script, de uitvoerings resultaten en het stdout-bestand worden opgeslagen in de bestands shares van het opslag account. Er is een map met de naam `azscripts` . In de map zijn er twee mappen voor de invoer en de uitvoer bestanden: `azscriptinput` en `azscriptoutput` .

De output-map bevat een _executionresult.json_ en het uitvoerbestand van het script. U kunt het fout bericht voor het uitvoeren van scripts zien in _executionresult.jsop_. Het uitvoer bestand wordt alleen gemaakt wanneer het script is uitgevoerd. De input-map bevat een scriptbestand voor het PowerShell-systeem en de scriptbestanden voor de gebruikersimplementatie. U kunt het script bestand voor de gebruikers implementatie vervangen door een herziene versie en het implementatie script opnieuw uitvoeren vanuit het Azure container-exemplaar.

### <a name="use-the-azure-portal"></a>De Azure-portal gebruiken

Nadat u een implementatie script bron hebt geïmplementeerd, wordt de resource vermeld onder de resource groep in de Azure Portal. Op de volgende scherm afbeelding ziet u de pagina **overzicht** van een implementatie script Bron:

![Overzicht van de portal voor de implementatie van Resource Manager-sjablonen](./media/deployment-script-template/resource-manager-deployment-script-portal.png)

Op de pagina overzicht vindt u enkele belang rijke informatie over de resource, zoals de **inrichtings status**, het **opslag account**, de **container instantie** en de **Logboeken**.

Vanuit het menu links kunt u de inhoud van het implementatie script bekijken, de argumenten die zijn door gegeven aan het script en de uitvoer. U kunt ook een sjabloon voor het implementatie script exporteren, inclusief het implementatie script.

### <a name="use-powershell"></a>PowerShell gebruiken

Met Azure PowerShell kunt u implementatie scripts beheren op basis van het abonnement of het bereik van de resource groep:

- [Get-AzDeploymentScript](/powershell/module/az.resources/get-azdeploymentscript): Hiermee worden de implementatie scripts opgehaald of weer gegeven.
- [Get-AzDeploymentScriptLog](/powershell/module/az.resources/get-azdeploymentscriptlog): Hiermee wordt het logboek van een uitvoering van een implementatie script opgehaald.
- [Remove-AzDeploymentScript](/powershell/module/az.resources/remove-azdeploymentscript): Hiermee verwijdert u een implementatie script en de bijbehorende resources.
- [Save-AzDeploymentScriptLog](/powershell/module/az.resources/save-azdeploymentscriptlog): slaat het logboek van een uitvoering van een implementatie script op schijf op.

De `Get-AzDeploymentScript` uitvoer is vergelijkbaar met:

```output
Name                : runPowerShellInlineWithOutput
Id                  : /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0618rg/providers/Microsoft.Resources/deploymentScripts/runPowerShellInlineWithOutput
ResourceGroupName   : myds0618rg
Location            : centralus
SubscriptionId      : 01234567-89AB-CDEF-0123-456789ABCDEF
ProvisioningState   : Succeeded
Identity            : /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/mydentity1008rg/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myuami
ScriptKind          : AzurePowerShell
AzPowerShellVersion : 3.0
StartTime           : 6/18/2020 7:46:45 PM
EndTime             : 6/18/2020 7:49:45 PM
ExpirationDate      : 6/19/2020 7:49:45 PM
CleanupPreference   : OnSuccess
StorageAccountId    : /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0618rg/providers/Microsoft.Storage/storageAccounts/ftnlvo6rlrvo2azscripts
ContainerInstanceId : /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0618rg/providers/Microsoft.ContainerInstance/containerGroups/ftnlvo6rlrvo2azscripts
Outputs             :
                      Key                 Value
                      ==================  ==================
                      text                Hello John Dole

RetentionInterval   : P1D
Timeout             : PT1H
```

### <a name="use-azure-cli"></a>Azure CLI gebruiken

Met Azure CLI kunt u implementatie scripts beheren op basis van het abonnement of het bereik van de resource groep:

- [AZ Deployment-scripts delete](/cli/azure/deployment-scripts#az-deployment-scripts-delete): een implementatie script verwijderen.
- [AZ Deployment-scripts lijst](/cli/azure/deployment-scripts#az-deployment-scripts-list): alle implementatie scripts weer geven.
- [AZ Deployment-scripts show](/cli/azure/deployment-scripts#az-deployment-scripts-show): een implementatie script ophalen.
- [AZ Deployment-scripts show-log](/cli/azure/deployment-scripts#az-deployment-scripts-show-log): script logboeken van de implementatie weer geven.

De uitvoer van de lijst opdracht ziet er ongeveer als volgt uit:

```json
[
  {
    "arguments": "-name \\\"John Dole\\\"",
    "azPowerShellVersion": "3.0",
    "cleanupPreference": "OnSuccess",
    "containerSettings": {
      "containerGroupName": null
    },
    "environmentVariables": null,
    "forceUpdateTag": "20200625T025902Z",
    "id": "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0624rg/providers/Microsoft.Resources/deploymentScripts/runPowerShellInlineWithOutput",
    "identity": {
      "tenantId": "01234567-89AB-CDEF-0123-456789ABCDEF",
      "type": "userAssigned",
      "userAssignedIdentities": {
        "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myidentity1008rg/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myuami": {
          "clientId": "01234567-89AB-CDEF-0123-456789ABCDEF",
          "principalId": "01234567-89AB-CDEF-0123-456789ABCDEF"
        }
      }
    },
    "kind": "AzurePowerShell",
    "location": "centralus",
    "name": "runPowerShellInlineWithOutput",
    "outputs": {
      "text": "Hello John Dole"
    },
    "primaryScriptUri": null,
    "provisioningState": "Succeeded",
    "resourceGroup": "myds0624rg",
    "retentionInterval": "1 day, 0:00:00",
    "scriptContent": "\r\n          param([string] $name)\r\n          $output = \"Hello {0}\" -f $name\r\n          Write-Output $output\r\n          $DeploymentScriptOutputs = @{}\r\n          $DeploymentScriptOutputs['text'] = $output\r\n        ",
    "status": {
      "containerInstanceId": "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0624rg/providers/Microsoft.ContainerInstance/containerGroups/64lxews2qfa5uazscripts",
      "endTime": "2020-06-25T03:00:16.796923+00:00",
      "error": null,
      "expirationTime": "2020-06-26T03:00:16.796923+00:00",
      "startTime": "2020-06-25T02:59:07.595140+00:00",
      "storageAccountId": "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0624rg/providers/Microsoft.Storage/storageAccounts/64lxews2qfa5uazscripts"
    },
    "storageAccountSettings": null,
    "supportingScriptUris": null,
    "systemData": {
      "createdAt": "2020-06-25T02:59:04.750195+00:00",
      "createdBy": "someone@contoso.com",
      "createdByType": "User",
      "lastModifiedAt": "2020-06-25T02:59:04.750195+00:00",
      "lastModifiedBy": "someone@contoso.com",
      "lastModifiedByType": "User"
    },
    "tags": null,
    "timeout": "1:00:00",
    "type": "Microsoft.Resources/deploymentScripts"
  }
]
```

### <a name="use-rest-api"></a>Rest API gebruiken

U kunt de informatie over de implementatie script bron implementeren op het niveau van de resource groep en het abonnements niveau door gebruik te maken van REST API:

```rest
/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/microsoft.resources/deploymentScripts/<DeploymentScriptResourceName>?api-version=2020-10-01
```

```rest
/subscriptions/<SubscriptionID>/providers/microsoft.resources/deploymentScripts?api-version=2020-10-01
```

In het volgende voor beeld wordt [ARMClient](https://github.com/projectkudu/ARMClient)gebruikt:

```azurepowershell
armclient login
armclient get /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourcegroups/myrg/providers/microsoft.resources/deploymentScripts/myDeployementScript?api-version=2020-10-01
```

De uitvoer is vergelijkbaar met:

```json
{
  "kind": "AzurePowerShell",
  "identity": {
    "type": "userAssigned",
    "tenantId": "01234567-89AB-CDEF-0123-456789ABCDEF",
    "userAssignedIdentities": {
      "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myidentity1008rg/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myuami": {
        "principalId": "01234567-89AB-CDEF-0123-456789ABCDEF",
        "clientId": "01234567-89AB-CDEF-0123-456789ABCDEF"
      }
    }
  },
  "location": "centralus",
  "systemData": {
    "createdBy": "someone@contoso.com",
    "createdByType": "User",
    "createdAt": "2020-06-25T02:59:04.7501955Z",
    "lastModifiedBy": "someone@contoso.com",
    "lastModifiedByType": "User",
    "lastModifiedAt": "2020-06-25T02:59:04.7501955Z"
  },
  "properties": {
    "provisioningState": "Succeeded",
    "forceUpdateTag": "20200625T025902Z",
    "azPowerShellVersion": "3.0",
    "scriptContent": "\r\n          param([string] $name)\r\n          $output = \"Hello {0}\" -f $name\r\n          Write-Output $output\r\n          $DeploymentScriptOutputs = @{}\r\n          $DeploymentScriptOutputs['text'] = $output\r\n        ",
    "arguments": "-name \\\"John Dole\\\"",
    "retentionInterval": "P1D",
    "timeout": "PT1H",
    "containerSettings": {},
    "status": {
      "containerInstanceId": "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0624rg/providers/Microsoft.ContainerInstance/containerGroups/64lxews2qfa5uazscripts",
      "storageAccountId": "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0624rg/providers/Microsoft.Storage/storageAccounts/64lxews2qfa5uazscripts",
      "startTime": "2020-06-25T02:59:07.5951401Z",
      "endTime": "2020-06-25T03:00:16.7969234Z",
      "expirationTime": "2020-06-26T03:00:16.7969234Z"
    },
    "outputs": {
      "text": "Hello John Dole"
    },
    "cleanupPreference": "OnSuccess"
  },
  "id": "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0624rg/providers/Microsoft.Resources/deploymentScripts/runPowerShellInlineWithOutput",
  "type": "Microsoft.Resources/deploymentScripts",
  "name": "runPowerShellInlineWithOutput"
}

```

De volgende REST API retourneert het logboek:

```rest
/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/microsoft.resources/deploymentScripts/<DeploymentScriptResourceName>/logs?api-version=2020-10-01
```

Het werkt alleen als de implementatie script bronnen worden verwijderd.

Als u de deploymentScripts-resource in de portal wilt bekijken, selecteert u **verborgen typen weer geven**:

![Implementatie script van Resource Manager-sjabloon, verborgen typen weer geven, portal](./media/deployment-script-template/resource-manager-deployment-script-portal-show-hidden-types.png)

## <a name="clean-up-deployment-script-resources"></a>Implementatie script resources opschonen

Een opslag account en een container exemplaar zijn nodig voor het uitvoeren van scripts en het oplossen van problemen. U hebt de mogelijkheid om een bestaand opslag account op te geven. anders wordt een opslag account samen met een container exemplaar automatisch door de script service gemaakt. De twee automatisch gemaakte resources worden verwijderd door de script service wanneer de uitvoering van het implementatie script wordt uitgevoerd in een Terminal status. U wordt gefactureerd voor de resources totdat de resources zijn verwijderd. Zie [container instances prijzen](https://azure.microsoft.com/pricing/details/container-instances/) en [Azure Storage prijzen](https://azure.microsoft.com/pricing/details/storage/)voor de prijs informatie.

De levens cyclus van deze resources wordt bepaald door de volgende eigenschappen in de sjabloon:

- `cleanupPreference`: De voor keur opschonen wanneer de uitvoering van het script in een Terminal status krijgt. De ondersteunde waarden zijn:

  - **Altijd**: de automatisch gemaakte resources verwijderen wanneer de uitvoering van het script in een Terminal status krijgt. Als er een bestaand opslag account wordt gebruikt, wordt de bestands share die is gemaakt in het opslag account door de script service verwijderd. Omdat de `deploymentScripts` bron nog steeds aanwezig is nadat de resources zijn opgeruimd, blijven de resultaten van de script-service, bijvoorbeeld stdout, uitvoer en retour waarde, voor de resources worden verwijderd.
  - **OnSuccess**: Verwijder de automatisch gemaakte resources alleen wanneer de uitvoering van het script is geslaagd. Als er een bestaand opslag account wordt gebruikt, wordt de bestands share alleen door de script service verwijderd wanneer de uitvoering van het script is geslaagd. U hebt nog steeds toegang tot de resources om de informatie over de fout opsporing te vinden.
  - **OnExpiration**: Verwijder de automatisch gemaakte resources alleen wanneer de `retentionInterval` instelling is verlopen. Als er een bestaand opslag account wordt gebruikt, wordt de bestands share door de script service verwijderd, maar blijft het opslag account behouden.

- `retentionInterval`: Geef het tijds interval op dat een script bron moet worden bewaard en waarna deze wordt verwijderd.

> [!NOTE]
> Het is niet raadzaam om het opslag account en het container exemplaar dat door de script service wordt gegenereerd voor andere doel einden te gebruiken. De twee resources kunnen worden verwijderd, afhankelijk van de levens cyclus van het script.

Het container exemplaar en het opslag account worden verwijderd volgens de `cleanupPreference` . Als het script mislukt en `cleanupPreference` niet is ingesteld op **altijd**, houdt het implementatie proces de container automatisch gedurende één uur actief. U kunt dit uur gebruiken om het script op te lossen. Als u wilt dat de container wordt uitgevoerd nadat de implementatie is voltooid, voegt u een slaapstandtoets toe aan het script. Voeg bijvoorbeeld [Start-slaap stand](/powershell/module/microsoft.powershell.utility/start-sleep) toe aan het einde van uw script. Als u de stap van de slaap stand niet toevoegt, wordt de container ingesteld op een Terminal status en is deze niet toegankelijk, zelfs niet als deze nog niet is verwijderd.

## <a name="run-script-more-than-once"></a>Script meer dan één keer uitvoeren

Uitvoering van het implementatie script is een idempotent-bewerking. Als geen van de `deploymentScripts` bron eigenschappen (met inbegrip van het inline-script) wordt gewijzigd, wordt het script niet uitgevoerd wanneer u de sjabloon opnieuw implementeert. De implementatie script service vergelijkt de resource namen in de sjabloon met de bestaande resources in dezelfde resource groep. Er zijn twee opties als u hetzelfde implementatie script meerdere keren wilt uitvoeren:

- Wijzig de naam van uw `deploymentScripts` resource. Gebruik bijvoorbeeld de sjabloon [utcNow](./template-functions-date.md#utcnow) als de naam van de resource of als onderdeel van de resource naam. Als u de resource naam wijzigt, wordt er een nieuwe `deploymentScripts` resource gemaakt. Het is handig om een geschiedenis van het uitvoeren van scripts te bewaren.

    > [!NOTE]
    > De `utcNow` functie kan alleen worden gebruikt in de standaard waarde voor een para meter.

- Geef een andere waarde op in de `forceUpdateTag` sjabloon eigenschap. Gebruik bijvoorbeeld `utcNow` als de-waarde.

> [!NOTE]
> Schrijf de implementatie scripts die idempotent zijn. Dit zorgt ervoor dat als ze per ongeluk worden uitgevoerd, er geen systeem wijzigingen optreden. Als het implementatie script bijvoorbeeld wordt gebruikt om een Azure-resource te maken, moet u controleren of de resource niet bestaat voordat u deze maakt. het script slaagt dus of u maakt de resource niet opnieuw.

## <a name="configure-development-environment"></a>De ontwikkelomgeving configureren

U kunt een vooraf geconfigureerde container installatie kopie gebruiken als uw ontwikkel omgeving voor implementatie scripts. Zie [ontwikkelings omgeving voor implementatie scripts configureren in sjablonen](./deployment-script-template-configure-dev.md)voor meer informatie.

Nadat het script is getest, kunt u dit als een implementatie script in uw sjablonen gebruiken.

## <a name="deployment-script-error-codes"></a>Fout codes voor implementatie scripts

| Foutcode | Beschrijving |
|------------|-------------|
| DeploymentScriptInvalidOperation | De resource definitie van het implementatie script in de sjabloon bevat ongeldige eigenschaps namen. |
| DeploymentScriptResourceConflict | Kan geen resource voor een implementatie script verwijderen die zich in de niet-Terminal status bevindt en de uitvoering niet langer dan 1 uur is overschreden. Het is ook niet mogelijk om hetzelfde implementatie script opnieuw uit te voeren met dezelfde resource-id (hetzelfde abonnement, dezelfde resource groepsnaam en resource naam), maar met verschillende script hoofdtekst op hetzelfde moment. |
| DeploymentScriptOperationFailed | De implementatie script bewerking is niet intern mislukt. Neem contact op met micro soft ondersteuning. |
| DeploymentScriptStorageAccountAccessKeyNotSpecified | De toegangs sleutel is niet opgegeven voor het bestaande opslag account.|
| DeploymentScriptContainerGroupContainsInvalidContainers | Een container groep die door de implementatie script service is gemaakt, is extern gewijzigd en er zijn ongeldige containers toegevoegd. |
| DeploymentScriptContainerGroupInNonterminalState | Twee of meer bronnen voor het implementatie script gebruiken dezelfde naam voor een Azure-container exemplaar in dezelfde resource groep en een van de resources heeft de uitvoering nog niet voltooid. |
| DeploymentScriptStorageAccountInvalidKind | Het bestaande opslag account van het type BlobBlobStorage of BlobStorage biedt geen ondersteuning voor bestands shares en kan niet worden gebruikt. |
| DeploymentScriptStorageAccountInvalidKindAndSku | Het bestaande opslag account biedt geen ondersteuning voor bestands shares. Zie [bestaand opslag account gebruiken](#use-existing-storage-account)voor een lijst met ondersteunde typen opslag accounts. |
| DeploymentScriptStorageAccountNotFound | Het opslag account bestaat niet of is verwijderd door een extern proces of hulp programma. |
| DeploymentScriptStorageAccountWithServiceEndpointEnabled | Het opgegeven opslag account heeft een service-eind punt. Een opslag account met een service-eind punt wordt niet ondersteund. |
| DeploymentScriptStorageAccountInvalidAccessKey | Er is een ongeldige toegangs sleutel opgegeven voor het bestaande opslag account. |
| DeploymentScriptStorageAccountInvalidAccessKeyFormat | Ongeldige sleutel indeling voor het opslag account. Zie [toegangs sleutels voor opslag accounts beheren](../../storage/common/storage-account-keys-manage.md). |
| DeploymentScriptExceededMaxAllowedTime | De uitvoerings tijd van het implementatie script overschrijdt de time-outwaarde die is opgegeven in de resource definitie voor het implementatie script. |
| DeploymentScriptInvalidOutputs | De uitvoer van het implementatie script is geen geldig JSON-object. |
| DeploymentScriptContainerInstancesServiceLoginFailure | De door de gebruiker toegewezen beheerde identiteit kan zich niet aanmelden na 10 pogingen met een interval van 1 minuut. |
| DeploymentScriptContainerGroupNotFound | Een container groep die door de implementatie script service is gemaakt, is verwijderd door een extern hulp programma of proces. |
| DeploymentScriptDownloadFailure | Kan geen ondersteunend script downloaden. Zie [ondersteund script gebruiken](#use-supporting-scripts).|
| DeploymentScriptError | Het gebruikers script heeft een fout gegenereerd. |
| DeploymentScriptBootstrapScriptExecutionFailed | Het Boots trap script heeft een fout gegenereerd. Boots trap script is het systeem script waarmee de uitvoering van het implementatie script wordt georganiseerd. |
| DeploymentScriptExecutionFailed | Onbekende fout tijdens het uitvoeren van het implementatie script. |
| DeploymentScriptContainerInstancesServiceUnavailable | Bij het maken van de Azure container instance (ACI) heeft de fout een service die niet beschikbaar is. |
| DeploymentScriptContainerGroupInNonterminalState | Bij het maken van het Azure container instance-exemplaar (ACI) gebruikt een ander implementatie script dezelfde ACI-naam in hetzelfde bereik (hetzelfde abonnement, naam van de resource groep en resource naam). |
| DeploymentScriptContainerGroupNameInvalid | De opgegeven naam voor het Azure-container exemplaar (ACI) voldoet niet aan de vereisten voor ACI. Zie [veelvoorkomende problemen in azure container instances oplossen](../../container-instances/container-instances-troubleshooting.md#issues-during-container-group-deployment).|

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u implementatie scripts gebruikt. Een zelf studie over het implementatie script door lopen:

> [!div class="nextstepaction"]
> [Zelf studie: implementatie scripts gebruiken in Azure Resource Manager sjablonen](./template-tutorial-deployment-script.md)

> [!div class="nextstepaction"]
> [Module leren: ARM-sjablonen uitbreiden met behulp van implementatie scripts](/learn/modules/extend-resource-manager-template-deployment-scripts/)