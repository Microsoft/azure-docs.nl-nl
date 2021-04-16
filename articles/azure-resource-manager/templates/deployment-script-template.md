---
title: Implementatiescripts gebruiken in sjablonen | Microsoft Docs
description: implementatiescripts in Azure Resource Manager gebruiken.
services: azure-resource-manager
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 04/15/2021
ms.author: jgao
ms.openlocfilehash: d35deb978b3b60b73ac393b241471cb528817d35
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107536962"
---
# <a name="use-deployment-scripts-in-arm-templates"></a>Implementatiescripts gebruiken in ARM-sjablonen

Meer informatie over het gebruik van implementatiescripts in Azure Resource-sjablonen (ARM-sjablonen). Met een nieuw resourcetype met de naam `Microsoft.Resources/deploymentScripts` kunnen gebruikers scripts uitvoeren in sjabloonimplementaties en de resultaten van de uitvoering bekijken. Deze scripts kunnen worden gebruikt voor het uitvoeren van aangepaste stappen, zoals:

- gebruikers toevoegen aan een directory
- gegevensvlakbewerkingen uitvoeren, bijvoorbeeld blobs kopiëren of een seed-database
- een licentiesleutel zoeken en valideren
- een zelf-ondertekend certificaat maken
- een object maken in Azure AD
- IP-adresblokken van aangepast systeem op zoeken

De voordelen van implementatiescript:

- Eenvoudig te coden, te gebruiken en fouten op te sporen. U kunt implementatiescripts ontwikkelen in uw favoriete ontwikkelomgevingen. De scripts kunnen worden ingesloten in sjablonen of in externe scriptbestanden.
- U kunt de scripttaal en het platform opgeven. Momenteel worden Azure PowerShell en Azure CLI-implementatiescripts in de Linux-omgeving ondersteund.
- Toestaan dat opdrachtregelargumenten worden doorgeven aan het script.
- Kan scriptuitvoer opgeven en deze doorgeven aan de implementatie.

De implementatiescriptresource is alleen beschikbaar in de regio's waar Azure Container Instance beschikbaar is.  Zie [Resourcebeschikbaarheid voor Azure Container Instances in Azure-regio's.](../../container-instances/container-instances-region-availability.md)

> [!IMPORTANT]
> Er zijn een opslagaccount en een container-exemplaar nodig om scripts uit te voeren en problemen op te lossen. U hebt de opties om een bestaand opslagaccount op te geven, anders wordt het opslagaccount samen met de container-instantie automatisch gemaakt door de scriptservice. De twee automatisch gemaakte resources worden meestal verwijderd door de scriptservice wanneer de uitvoering van het implementatiescript een terminale status krijgt. U wordt gefactureerd voor de resources totdat de resources zijn verwijderd. Zie Resources voor [implementatiescripts ops schonen voor meer informatie.](#clean-up-deployment-script-resources)

> [!IMPORTANT]
> De resource-API deploymentScripts versie 2020-10-01 ondersteunt [OnBehalfofTokens (OBO).](../../active-directory/develop/v2-oauth2-on-behalf-of-flow.md) Met behulp van OBO maakt de implementatiescriptservice gebruik van het token van de implementatie-principal om de onderliggende resources te maken voor het uitvoeren van implementatiescripts, waaronder Azure Container Instance, Azure Storage-account en roltoewijzingen voor de beheerde identiteit. In een oudere API-versie wordt de beheerde identiteit gebruikt om deze resources te maken.
> Logica voor opnieuw proberen voor aanmelden bij Azure is nu ingebouwd in het wrapperscript. Als u machtigingen verleent in dezelfde sjabloon waarin u implementatiescripts kunt uitvoeren. De implementatiescriptservice moet zich tien minuten lang opnieuw aanmelden met een interval van 10 seconden totdat de toewijzing van de beheerde identiteitsrol wordt gerepliceerd.

## <a name="configure-the-minimum-permissions"></a>De minimale machtigingen configureren

Voor api-versie 2020-10-01 of hoger van het implementatiescript wordt de implementatie-principal gebruikt om onderliggende resources te maken die nodig zijn om de implementatiescriptresource uit te voeren: een opslagaccount en een Azure-containerin exemplaar. Als het script moet worden geverifieerd bij Azure en specifieke Azure-acties moet uitvoeren, wordt u aangeraden het script te voorzien van een door de gebruiker toegewezen beheerde identiteit. De beheerde identiteit moet de vereiste toegang hebben om de bewerking in het script te voltooien.

Als u de machtigingen met de minste bevoegdheden wilt configureren, hebt u het volgende nodig:

- Wijs een aangepaste rol met de volgende eigenschappen toe aan de implementatie-principal:

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

  Als de Azure Storage en de Azure Container Instance-resourceproviders niet zijn geregistreerd, moet u ook en `Microsoft.Storage/register/action` `Microsoft.ContainerInstance/register/action` toevoegen.

- Als een beheerde identiteit wordt gebruikt,  moet voor de implementatie-principal de rol Operator voor beheerde identiteit (een ingebouwde rol) zijn toegewezen aan de beheerde identiteitsresource.

## <a name="sample-templates"></a>Voorbeeldsjablonen

De volgende JSON is een voorbeeld. Zie het meest recente sjabloonschema voor [meer informatie.](/azure/templates/microsoft.resources/deploymentscripts)

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
> Het voorbeeld is bedoeld voor demonstratiedoeleinden. De eigenschappen `scriptContent` en kunnen niet naast elkaar bestaan in een `primaryScriptUri` sjabloon.

> [!NOTE]
> De _scriptContent_ toont een script met meerdere regels.  De Azure Portal en Azure DevOps-pijplijn kunnen een implementatiescript met meerdere regels niet parseren. U kunt de PowerShell-opdrachten (met behulp van puntkomma's of _\\ r \\ n_ of _\\ n_) in één regel ketenen of de eigenschap gebruiken `primaryScriptUri` met een extern scriptbestand. Er zijn veel gratis JSON-tekenreekshulpprogramma's beschikbaar voor escape/unescape. Bijvoorbeeld [https://www.freeformatter.com/json-escape.html](https://www.freeformatter.com/json-escape.html) .

Details van eigenschapswaarde:

- `identity`: Voor api-versie 2020-10-01 of hoger van het implementatiescript is een door de gebruiker toegewezen beheerde identiteit optioneel, tenzij u azure-specifieke acties in het script moet uitvoeren.  Voor de API-versie 2019-10-01-preview is een beheerde identiteit vereist omdat de implementatiescriptservice deze gebruikt om de scripts uit te voeren. Wanneer de identiteits-eigenschap is opgegeven, roept de scriptservice aan `Connect-AzAccount -Identity` voordat het gebruikersscript wordt aanroept. Momenteel wordt alleen door de gebruiker toegewezen beheerde identiteit ondersteund. Als u zich wilt aanmelden met een andere identiteit, kunt u [Connect-AzAccount aanroepen](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount) in het script.
- `kind`: Geef het type script op. Momenteel worden Azure PowerShell en Azure CLI-scripts ondersteund. De waarden zijn **AzurePowerShell** en **AzureCLI.**
- `forceUpdateTag`: Als u deze waarde tussen sjabloonimplementaties verandert, wordt het implementatiescript opnieuw uitgevoerd. Als u de `newGuid()` functies of `utcNow()` gebruikt, kunnen beide functies alleen worden gebruikt in de standaardwaarde voor een parameter. Zie [Script meerdere keren uitvoeren](#run-script-more-than-once) voor meer informatie.
- `containerSettings`: Geef de instellingen op voor het aanpassen van Azure Container Instance. Voor het implementatiescript is een nieuwe Azure Container Instance vereist. U kunt geen bestaande Azure Container Instance opgeven. U kunt de naam van de containergroep echter aanpassen met behulp van `containerGroupName` . Als dit niet wordt opgegeven, wordt de groepsnaam automatisch gegenereerd.
- `storageAccountSettings`: Geef de instellingen op voor het gebruik van een bestaand opslagaccount. Als niet is opgegeven, wordt er automatisch `storageAccountName` een opslagaccount gemaakt. Zie [Een bestaand opslagaccount gebruiken.](#use-existing-storage-account)
- `azPowerShellVersion`/`azCliVersion`: Geef de moduleversie op die moet worden gebruikt. Bekijk een lijst met [ondersteunde Azure PowerShell versies](https://mcr.microsoft.com/v2/azuredeploymentscripts-powershell/tags/list). Bekijk een lijst met [ondersteunde Versies van Azure CLI.](https://mcr.microsoft.com/v2/azure-cli/tags/list)

  >[!IMPORTANT]
  > Het implementatiescript maakt gebruik van de beschikbare CLI-installatie Microsoft Container Registry (MCR). Het duurt ongeveer een maand om een CLI-installatier te certificeren voor het implementatiescript. Gebruik niet de CLI-versies die binnen 30 dagen zijn uitgebracht. Zie Opmerkingen bij de Release van Azure CLI voor meer informatie over de [releasedatums voor de afbeeldingen.](/cli/azure/release-notes-azure-cli) Als er een niet-ondersteunde versie wordt gebruikt, worden in het foutbericht de ondersteunde versies vermeld.

- `arguments`: Geef de parameterwaarden op. De waarden worden gescheiden door een spatie.

  Implementatiescripts splitsen de argumenten op in een matrix met tekenreeksen door de systeemoproep [CommandLineToArgvW aan te ](/windows/win32/api/shellapi/nf-shellapi-commandlinetoargvw) roepen. Deze stap is nodig omdat de [](/rest/api/container-instances/containergroups/createorupdate#containerexec) argumenten als een opdracht-eigenschap worden doorgegeven aan Azure Container Instance en de opdracht-eigenschap een matrix met tekenreeksen is.

  Als de argumenten escape-tekens bevatten, gebruikt [u JsonEscaper om](https://www.jsonescaper.com/) de tekens te double-escapen. Plak de oorspronkelijke escape-tekenreeks in het hulpprogramma en selecteer **escape.**  Met het hulpprogramma wordt een dubbele escape-tekenreeks uitgevoerd. In de vorige voorbeeldsjabloon is het argument `-name \"John Dole\"` bijvoorbeeld . De tekenreeks met escape-tekenreeks is `-name \\\"John Dole\\\"` .

  Als u een ARM-sjabloonparameter van het type object als argument wilt doorgeven, converteert u het object naar een tekenreeks met behulp van de functie [string()](./template-functions-string.md#string) en gebruikt u vervolgens de functie [replace()](./template-functions-string.md#replace) om een te vervangen `\"` in `\\\"` . Bijvoorbeeld:

  ```json
  replace(string(parameters('tables')), '\"', '\\\"')
  ```

  Zie de voorbeeldsjabloon [voor meer informatie.](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-jsonEscape.json)

- `environmentVariables`: Geef de omgevingsvariabelen op die moeten worden door geven aan het script. Zie Implementatiescripts ontwikkelen [voor meer informatie.](#develop-deployment-scripts)
- `scriptContent`: Geef de scriptinhoud op. Als u een extern script wilt uitvoeren, gebruikt u in plaats daarvan `primaryScriptUri`. Zie Use [inline script (Inlinescript gebruiken)](#use-inline-scripts) en [Use external script (Extern script gebruiken) voor voorbeelden.](#use-external-scripts)
- `primaryScriptUri`: Geef een openbaar toegankelijke URL op naar het primaire implementatiescript met ondersteunde bestandsextensies. Zie Externe scripts gebruiken [voor meer informatie.](#use-external-scripts)
- `supportingScriptUris`: Geef een matrix met openbaar toegankelijke URL's op voor ondersteunende bestanden die worden aangeroepen in `scriptContent` of `primaryScriptUri` . Zie Externe scripts gebruiken [voor meer informatie.](#use-external-scripts)
- `timeout`: Geef de maximale toegestane uitvoeringstijd voor het script op, opgegeven in de [ISO 8601-indeling](https://en.wikipedia.org/wiki/ISO_8601). Standaardwaarde is **P1D**.
- `cleanupPreference`. Geef de voorkeur op voor het opsschoonen van implementatieresources wanneer de uitvoering van het script een terminale status krijgt. De standaardinstelling is **Altijd,** wat betekent dat u de resources moet verwijderen ondanks de terminal status (Geslaagd, Mislukt, Geannuleerd). Zie [Resources van implementatiescripts opschonen](#clean-up-deployment-script-resources) voor meer informatie.
- `retentionInterval`: Geef het interval op waarvoor de service de resources van het implementatiescript behoudt nadat de uitvoering van het implementatiescript een terminaltoestand heeft bereikt. De resources van het implementatiescript worden verwijderd wanneer deze duur is verlopen. De duur is gebaseerd op het [ISO 8601-patroon](https://en.wikipedia.org/wiki/ISO_8601). De retentieperiode ligt tussen 1 en 26 uur (PT26H). Deze eigenschap wordt gebruikt wanneer `cleanupPreference` is ingesteld op **OnExpiration**. Zie [Resources van implementatiescripts opschonen](#clean-up-deployment-script-resources) voor meer informatie.

### <a name="additional-samples"></a>Aanvullende voorbeelden

- [Voorbeeld 1:](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault.json)maak een sleutelkluis en gebruik het implementatiescript om een certificaat toe te wijzen aan de sleutelkluis.
- [Voorbeeld 2:](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault-subscription.json)maak een resourcegroep op abonnementsniveau, maak een sleutelkluis in de resourcegroep en gebruik vervolgens het implementatiescript om een certificaat toe te wijzen aan de sleutelkluis.
- [Voorbeeld 3:](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault-mi.json)maak een door de gebruiker toegewezen beheerde identiteit, wijs de rol inzender toe aan de identiteit op het niveau van de resourcegroep, maak een sleutelkluis en gebruik vervolgens het implementatiescript om een certificaat toe te wijzen aan de sleutelkluis.

## <a name="use-inline-scripts"></a>Inlinescripts gebruiken

In de volgende sjabloon is één resource gedefinieerd met het `Microsoft.Resources/deploymentScripts` type . Het gemarkeerde deel is het inlinescript.

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-helloworld.json" range="1-44" highlight="24-30":::

> [!NOTE]
> Omdat de scripts voor inline-implementatie tussen dubbele aanhalingstekens staan, moeten de tekenreeksen in de implementatiescripts een escape-teken krijgen met behulp van een backslash **(&#92;)** of tussen enkele aanhalingstekens. U kunt ook het gebruik van tekenreeksvervanging overwegen, zoals wordt weergegeven in het vorige JSON-voorbeeld.

Het script gebruikt één parameter en voert de parameterwaarde uit. `DeploymentScriptOutputs` wordt gebruikt voor het opslaan van uitvoer. In de sectie outputs laat de `value` regel zien hoe u toegang krijgt tot de opgeslagen waarden. `Write-Output` wordt gebruikt voor het gebruik van debuggen. Zie Implementatiescripts bewaken en problemen oplossen voor meer informatie over het openen van [het uitvoerbestand.](#monitor-and-troubleshoot-deployment-scripts) Zie Voorbeeldsjablonen voor de beschrijvingen [van eigenschappen.](#sample-templates)

Als u het script wilt uitvoeren, **selecteert** u Proberen om de Cloud Shell openen en plakt u de volgende code in het shell-deelvenster.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the name of the resource group to be created"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location

New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.json"

Write-Host "Press [ENTER] to continue ..."
```

De uitvoer ziet er als volgt uit:

![Resource Manager voor sjabloonimplementatiescript Hallo wereld-uitvoer](./media/deployment-script-template/resource-manager-template-deployment-script-helloworld-output.png)

## <a name="use-external-scripts"></a>Externe scripts gebruiken

Naast inlinescripts kunt u ook externe scriptbestanden gebruiken. Alleen primaire PowerShell-scripts met _de bestandsextensie ps1_ worden ondersteund. Voor CLI-scripts kunnen de primaire scripts alle extensies (of zonder extensie) hebben, zolang de scripts geldige bash-scripts zijn. Als u externe scriptbestanden wilt gebruiken, vervangt u `scriptContent` door `primaryScriptUri` . Bijvoorbeeld:

```json
"primaryScriptUri": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.ps1",
```

Zie de voorbeeldsjabloon [voor meer informatie.](https://github.com/Azure/azure-docs-json-samples/blob/master/deployment-script/deploymentscript-helloworld-primaryscripturi.json)

De externe scriptbestanden moeten toegankelijk zijn. Als u uw scriptbestanden wilt beveiligen die zijn opgeslagen in Azure Storage-accounts, genereert u een SAS-token en neemt u dit op in de URI voor de sjabloon. Stel de vervaltijd zo in, dat er genoeg tijd is om de implementatie te voltooien. Zie Persoonlijke ARM-sjabloon implementeren met [SAS-token voor meer informatie.](./secure-template-with-sas-token.md)

U bent verantwoordelijk voor het waarborgen van de integriteit van de scripts waarnaar wordt verwezen door een implementatiescript, `primaryScriptUri` ofwel of `supportingScriptUris` . Verwijs naar alleen scripts die u vertrouwt.

## <a name="use-supporting-scripts"></a>Ondersteunende scripts gebruiken

U kunt gecompliceerde logica scheiden in een of meer ondersteunende scriptbestanden. Met `supportingScriptUris` de eigenschap kunt u indien nodig een matrix van URI's aan de ondersteunende scriptbestanden leveren:

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

Ondersteunende scriptbestanden kunnen worden aangeroepen vanuit zowel inline scripts als primaire scriptbestanden. Ondersteunende scriptbestanden hebben geen beperkingen voor de bestandsextensie.

De ondersteunende bestanden worden tijdens de `azscripts/azscriptinput` runtime naar gekopieerd. Gebruik een relatief pad om te verwijzen naar de ondersteunende bestanden van inline scripts en primaire scriptbestanden.

## <a name="work-with-outputs-from-powershell-script"></a>Werken met uitvoer van PowerShell-script

In de volgende sjabloon ziet u hoe u waarden kunt doorgeven tussen twee `deploymentScripts` resources:

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-basic.json" range="1-68" highlight="30-31,50":::

In de eerste resource definieert u een variabele met de naam `$DeploymentScriptOutputs` en gebruikt u deze om de uitvoerwaarden op te slaan. Als u toegang wilt krijgen tot de uitvoerwaarde van een andere resource in de sjabloon, gebruikt u:

```json
reference('<ResourceName>').outputs.text
```

## <a name="work-with-outputs-from-cli-script"></a>Werken met uitvoer van CLI-script

Anders dan bij het PowerShell-implementatiescript biedt DE CLI/bash-ondersteuning geen algemene variabele om scriptuitvoer op te slaan. In plaats daarvan is er een omgevingsvariabele met de naam waarin de locatie wordt opgeslagen waar het scriptuitvoerbestand zich `AZ_SCRIPTS_OUTPUT_PATH` bevindt. Als een implementatiescript wordt uitgevoerd vanuit een Resource Manager sjabloon, wordt deze omgevingsvariabele automatisch voor u ingesteld door de Bash-shell. De waarde van `AZ_SCRIPTS_OUTPUT_PATH` is */mnt/azscripts/azscriptoutput/scriptoutputs.jsop*.

Implementatiescriptuitvoer moet worden opgeslagen op de locatie en de uitvoer moet `AZ_SCRIPTS_OUTPUT_PATH` een geldig JSON-tekenreeksobject zijn. De inhoud van het bestand moet worden opgeslagen als een sleutel-waardepaar. Een matrix met tekenreeksen wordt bijvoorbeeld opgeslagen als `{ "MyResult": [ "foo", "bar"] }` .  Het opslaan van alleen de matrixresultaten, `[ "foo", "bar" ]` bijvoorbeeld , is ongeldig.

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-basic-cli.json" range="1-44" highlight="32":::

[jq](https://stedolan.github.io/jq/) wordt gebruikt in het vorige voorbeeld. Deze wordt geleverd met de containerafbeeldingen. Zie [Ontwikkelomgeving configureren.](#configure-development-environment)

## <a name="use-existing-storage-account"></a>Bestaand opslagaccount gebruiken

Er zijn een opslagaccount en een container-exemplaar nodig om scripts uit te voeren en problemen op te lossen. U hebt de opties om een bestaand opslagaccount op te geven, anders wordt het opslagaccount samen met de container-instantie automatisch gemaakt door de scriptservice. De vereisten voor het gebruik van een bestaand opslagaccount:

- Ondersteunde opslagaccounts zijn:

    | SKU             | Ondersteund type     |
    |-----------------|--------------------|
    | Premium_LRS     | FileStorage        |
    | Premium_ZRS     | FileStorage        |
    | Standard_GRS    | Storage, StorageV2 |
    | Standard_GZRS   | StorageV2          |
    | Standard_LRS    | Storage, StorageV2 |
    | Standard_RAGRS  | Storage, StorageV2 |
    | Standard_RAGZRS | StorageV2          |
    | Standard_ZRS    | StorageV2          |

    Deze combinaties ondersteunen bestands shares. Zie Een Azure-bestands [share maken en](../../storage/files/storage-how-to-create-file-share.md) Typen [opslagaccounts voor meer informatie.](../../storage/common/storage-account-overview.md)

- Firewallregels voor opslagaccounts worden nog niet ondersteund. Raadpleeg [Firewalls en virtuele netwerken voor Azure Storage configureren](../../storage/common/storage-network-security.md) voor meer informatie.
- De implementatie-principal moet machtigingen hebben voor het beheren van het opslagaccount, waaronder het lezen, maken en verwijderen van bestands shares.

Als u een bestaand opslagaccount wilt opgeven, voegt u de volgende JSON toe aan het eigenschapselement van `Microsoft.Resources/deploymentScripts` :

```json
"storageAccountSettings": {
  "storageAccountName": "myStorageAccount",
  "storageAccountKey": "myKey"
},
```

- `storageAccountName`: geef de naam van het opslagaccount op.
- `storageAccountKey`: geef een van de opslagaccountsleutels op. U kunt de [functie listKeys() gebruiken](./template-functions-resource.md#listkeys) om de sleutel op te halen. Bijvoorbeeld:

    ```json
    "storageAccountSettings": {
        "storageAccountName": "[variables('storageAccountName')]",
        "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')), '2019-06-01').keys[0].value]"
    }
    ```

Zie [Voorbeeldsjablonen voor](#sample-templates) een volledig `Microsoft.Resources/deploymentScripts` definitievoorbeeld.

Wanneer een bestaand opslagaccount wordt gebruikt, maakt de scriptservice een bestands share met een unieke naam. Zie [Resources voor implementatiescripts ops schonen](#clean-up-deployment-script-resources) voor informatie over hoe de scriptservice de bestands share opschoont.

## <a name="develop-deployment-scripts"></a>Implementatiescripts ontwikkelen

### <a name="handle-non-terminating-errors"></a>Niet-beëindigingsfouten verwerken

U kunt bepalen hoe PowerShell reageert op niet-beëindigingsfouten met behulp van `$ErrorActionPreference` de variabele in uw implementatiescript. Als de variabele niet is ingesteld in uw implementatiescript, gebruikt de scriptservice de standaardwaarde **Doorgaan.**

De scriptservice stelt de inrichtingstoestand van de resource in **op** Mislukt wanneer er een fout wordt aangetroffen in het script, ondanks de instelling van `$ErrorActionPreference` .

### <a name="use-environment-variables"></a>Omgevingsvariabelen gebruiken

Het implementatiescript maakt gebruik van deze omgevingsvariabelen:

|Omgevingsvariabele|Standaardwaarde|Gereserveerd systeem|
|--------------------|-------------|---------------|
|AZ_SCRIPTS_AZURE_ENVIRONMENT|AzureCloud|N|
|AZ_SCRIPTS_CLEANUP_PREFERENCE|OnExpiration|N|
|AZ_SCRIPTS_OUTPUT_PATH|<AZ_SCRIPTS_PATH_OUTPUT_DIRECTORY>/<AZ_SCRIPTS_PATH_SCRIPT_OUTPUT_FILE_NAME>|J|
|AZ_SCRIPTS_PATH_INPUT_DIRECTORY|/mnt/azscripts/azscriptinput|J|
|AZ_SCRIPTS_PATH_OUTPUT_DIRECTORY|/mnt/azscripts/azscriptoutput|J|
|AZ_SCRIPTS_PATH_USER_SCRIPT_FILE_NAME|Azure PowerShell: userscript.ps1; Azure CLI: userscript.sh|J|
|AZ_SCRIPTS_PATH_PRIMARY_SCRIPT_URI_FILE_NAME|primaryscripturi.config|J|
|AZ_SCRIPTS_PATH_SUPPORTING_SCRIPT_URI_FILE_NAME|supportingscripturi.config|J|
|AZ_SCRIPTS_PATH_SCRIPT_OUTPUT_FILE_NAME|scriptoutputs.jsop|J|
|AZ_SCRIPTS_PATH_EXECUTION_RESULTS_FILE_NAME|executionresult.jsop|J|
|AZ_SCRIPTS_USER_ASSIGNED_IDENTITY|/subscriptions/|N|

Zie Werken met uitvoer van `AZ_SCRIPTS_OUTPUT_PATH` [CLI-script](#work-with-outputs-from-cli-script)voor meer informatie over het gebruik van .

### <a name="pass-secured-strings-to-deployment-script"></a>Beveiligde tekenreeksen doorgeven aan implementatiescript

Door omgevingsvariabelen (EnvironmentVariable) in te stellen in uw container-exemplaren, kunt u dynamische configuratie bieden van de toepassing of het script dat door de container wordt uitgevoerd. Het implementatiescript verwerkt niet-beveiligde en beveiligde omgevingsvariabelen op dezelfde manier als Azure Container Instance. Zie Omgevingsvariabelen [instellen in container-exemplaren voor meer informatie.](../../container-instances/container-instances-environment-variables.md#secure-values) Zie Voorbeeldsjablonen voor [een voorbeeld.](#sample-templates)

De maximaal toegestane grootte voor omgevingsvariabelen is 64 kB.

## <a name="monitor-and-troubleshoot-deployment-scripts"></a>Implementatiescripts bewaken en problemen oplossen

De scriptservice maakt een [opslagaccount](../../storage/common/storage-account-overview.md) (tenzij u een bestaand opslagaccount opgeeft) en een [container-exemplaar](../../container-instances/container-instances-overview.md) voor het uitvoeren van scripts. Als deze resources automatisch door de scriptservice worden gemaakt, hebben beide resources het `azscripts` achtervoegsel in de resourcenamen.

![Resource Manager resourcenamen voor sjabloonimplementatiescripts](./media/deployment-script-template/resource-manager-template-deployment-script-resources.png)

Het gebruikersscript, de uitvoerresultaten en het stdout-bestand worden opgeslagen in de bestands shares van het opslagaccount. Er is een map met de naam `azscripts` . In de map staan nog twee mappen voor de invoer en de uitvoerbestanden: `azscriptinput` en `azscriptoutput` .

De output-map bevat een _executionresult.json_ en het uitvoerbestand van het script. U ziet het foutbericht over de uitvoering van het script in _executionresult.jsop_. Het uitvoerbestand wordt alleen gemaakt wanneer het script is uitgevoerd. De input-map bevat een scriptbestand voor het PowerShell-systeem en de scriptbestanden voor de gebruikersimplementatie. U kunt het scriptbestand voor de gebruikersimplementatie vervangen door een herzien script en het implementatiescript opnieuw uitvoeren vanuit de Azure-containerin instance.

### <a name="use-the-azure-portal"></a>De Azure-portal gebruiken

Nadat u een implementatiescriptresource hebt geïmplementeerd, wordt de resource vermeld onder de resourcegroep in de Azure Portal. In de volgende schermopname ziet u **de pagina** Overzicht van een implementatiescriptresource:

![Resource Manager portaloverzicht voor sjabloonimplementatiescripts](./media/deployment-script-template/resource-manager-deployment-script-portal.png)

Op de overzichtspagina wordt belangrijke informatie over de resource weergegeven, zoals Inrichtingstoestand, **Opslagaccount,** **Container-instantie** en **Logboeken.**

In het menu links kunt u de inhoud van het implementatiescript, de argumenten die zijn doorgegeven aan het script en de uitvoer bekijken. U kunt ook een sjabloon voor het implementatiescript exporteren, inclusief het implementatiescript.

### <a name="use-powershell"></a>PowerShell gebruiken

Met Azure PowerShell kunt u implementatiescripts beheren op abonnements- of resourcegroepbereik:

- [Get-AzDeploymentScript:](/powershell/module/az.resources/get-azdeploymentscript)haalt implementatiescripts op of vermeldt deze.
- [Get-AzDeploymentScriptLog: haalt](/powershell/module/az.resources/get-azdeploymentscriptlog)het logboek op van de uitvoering van een implementatiescript.
- [Remove-AzDeploymentScript:](/powershell/module/az.resources/remove-azdeploymentscript)hiermee verwijdert u een implementatiescript en de bijbehorende resources.
- [Save-AzDeploymentScriptLog: slaat](/powershell/module/az.resources/save-azdeploymentscriptlog)het logboek van de uitvoering van een implementatiescript op schijf op.

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

Met Behulp van Azure CLI kunt u implementatiescripts beheren op abonnements- of resourcegroepbereik:

- [az deployment-scripts delete:](/cli/azure/deployment-scripts#az-deployment-scripts-delete)Een implementatiescript verwijderen.
- [az deployment-scripts list:](/cli/azure/deployment-scripts#az-deployment-scripts-list)Alle implementatiescripts opsnooien.
- [az deployment-scripts show:](/cli/azure/deployment-scripts#az-deployment-scripts-show)Een implementatiescript ophalen.
- [az deployment-scripts show-log:](/cli/azure/deployment-scripts#az-deployment-scripts-show-log)Implementatiescriptlogboeken tonen.

De uitvoer van de lijstopdracht is vergelijkbaar met:

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

### <a name="use-rest-api"></a>REST API gebruiken

U kunt de implementatiegegevens van de implementatie van implementatiescripts op het niveau van de resourcegroep en het abonnement verkrijgen met behulp van REST API:

```rest
/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/microsoft.resources/deploymentScripts/<DeploymentScriptResourceName>?api-version=2020-10-01
```

```rest
/subscriptions/<SubscriptionID>/providers/microsoft.resources/deploymentScripts?api-version=2020-10-01
```

In het volgende voorbeeld wordt [ARMClient gebruikt:](https://github.com/projectkudu/ARMClient)

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

Het werkt alleen voordat de resources van het implementatiescript worden verwijderd.

Als u de deploymentScripts-resource in de portal wilt zien, selecteert **u Verborgen typen tonen:**

![Resource Manager implementatiescript voor sjablonen, verborgen typen tonen, portal](./media/deployment-script-template/resource-manager-deployment-script-portal-show-hidden-types.png)

## <a name="clean-up-deployment-script-resources"></a>Resources voor implementatiescripts ops schonen

Er zijn een opslagaccount en een container-instantie nodig om scripts uit te voeren en problemen op te lossen. U hebt de opties om een bestaand opslagaccount op te geven, anders wordt er automatisch een opslagaccount samen met een container-instantie gemaakt door de scriptservice. De twee automatisch gemaakte resources worden door de scriptservice verwijderd wanneer de uitvoering van het implementatiescript een terminale status krijgt. U wordt gefactureerd voor de resources totdat de resources zijn verwijderd. Zie prijzen en Container Instances voor [Azure Storage informatie](https://azure.microsoft.com/pricing/details/container-instances/) over [de prijs.](https://azure.microsoft.com/pricing/details/storage/)

De levenscyclus van deze resources wordt bepaald door de volgende eigenschappen in de sjabloon:

- `cleanupPreference`: De voorkeur voor het ops schonen wanneer de uitvoering van het script een terminale status krijgt. De ondersteunde waarden zijn:

  - **Altijd:** verwijder de automatisch gemaakte resources zodra de scriptuitvoering een terminale status heeft. Als er een bestaand opslagaccount wordt gebruikt, verwijdert de scriptservice de bestands share die in het opslagaccount is gemaakt. Omdat de resource nog steeds aanwezig kan zijn nadat de resources zijn opgeschoond, worden de resultaten van de scriptuitvoering door de scriptservice persistent gemaakt, bijvoorbeeld stdout, uitvoer en retourwaarde voordat de resources worden `deploymentScripts` verwijderd.
  - **OnSuccess:** verwijder de automatisch gemaakte resources alleen wanneer de uitvoering van het script is geslaagd. Als een bestaand opslagaccount wordt gebruikt, verwijdert de scriptservice de bestands share alleen wanneer het uitvoeren van het script is geslaagd. U hebt nog steeds toegang tot de resources om de foutopsporingsinformatie te vinden.
  - **OnExpiration:** verwijder de automatisch gemaakte resources alleen wanneer de `retentionInterval` instelling is verlopen. Als er een bestaand opslagaccount wordt gebruikt, verwijdert de scriptservice de bestands share, maar behoudt het opslagaccount.

- `retentionInterval`: Geef het tijdsinterval op dat een scriptresource wordt bewaard en waarna deze verloopt en wordt verwijderd.

> [!NOTE]
> Het wordt afgeraden om het opslagaccount en de container-instantie te gebruiken die door de scriptservice worden gegenereerd voor andere doeleinden. De twee resources kunnen worden verwijderd, afhankelijk van de levenscyclus van het script.

De container-instantie en het opslagaccount worden verwijderd volgens de `cleanupPreference` . Als het script echter mislukt en niet is ingesteld op Altijd, blijft de container tijdens het implementatieproces automatisch `cleanupPreference` één uur actief.  U kunt dit uur gebruiken om problemen met het script op te lossen. Als u de container actief wilt houden na geslaagde implementaties, voegt u een slaapstandstap toe aan uw script. Voeg bijvoorbeeld [Start-Sleep toe](/powershell/module/microsoft.powershell.utility/start-sleep) aan het einde van uw script. Als u de slaapstandstap niet toevoegt, wordt de container ingesteld op een terminaltoestand en is deze niet toegankelijk, zelfs niet als deze nog niet is verwijderd.

## <a name="run-script-more-than-once"></a>Script meer dan één keer uitvoeren

Het uitvoeren van implementatiescripts is een idempotente bewerking. Als geen van de `deploymentScripts` resource-eigenschappen (inclusief het inline script) wordt gewijzigd, wordt het script niet uitgevoerd wanneer u de sjabloon opnieuw wilt uitvoeren. De implementatiescriptservice vergelijkt de resourcenamen in de sjabloon met de bestaande resources in dezelfde resourcegroep. Er zijn twee opties als u hetzelfde implementatiescript meerdere keren wilt uitvoeren:

- Wijzig de naam van uw `deploymentScripts` resource. Gebruik bijvoorbeeld de [sjabloonfunctie utcNow](./template-functions-date.md#utcnow) als de resourcenaam of als onderdeel van de resourcenaam. Als u de resourcenaam verandert, wordt er een nieuwe `deploymentScripts` resource gemaakt. Het is goed voor het bewaren van een geschiedenis van scriptuitvoering.

    > [!NOTE]
    > De `utcNow` functie kan alleen worden gebruikt in de standaardwaarde voor een parameter.

- Geef een andere waarde op in de `forceUpdateTag` sjabloon-eigenschap. Gebruik bijvoorbeeld als `utcNow` waarde.

> [!NOTE]
> Schrijf de implementatiescripts die idempotent zijn. Dit zorgt ervoor dat als ze per ongeluk opnieuw worden uitgevoerd, dit geen systeemwijzigingen veroorzaakt. Als het implementatiescript bijvoorbeeld wordt gebruikt om een Azure-resource te maken, controleert u of de resource niet bestaat voordat u deze maakt, zodat het script slaagt of u de resource niet opnieuw maakt.

## <a name="configure-development-environment"></a>De ontwikkelomgeving configureren

U kunt een vooraf geconfigureerde containerafbeelding gebruiken als ontwikkelomgeving voor implementatiescripts. Zie Configure development [environment for deployment scripts in templates (Ontwikkelomgeving configureren voor implementatiescripts in sjablonen) voor meer informatie.](./deployment-script-template-configure-dev.md)

Nadat het script is getest, kunt u het gebruiken als een implementatiescript in uw sjablonen.

## <a name="deployment-script-error-codes"></a>Foutcodes voor implementatiescripts

| Foutcode | Beschrijving |
|------------|-------------|
| DeploymentScriptInvalidOperation | De resourcedefinitie van het implementatiescript in de sjabloon bevat ongeldige eigenschapsnamen. |
| DeploymentScriptResourceConflict | Kan een implementatiescriptresource die niet-terminal is, niet verwijderen en de uitvoering niet langer is dan 1 uur. Of kan hetzelfde implementatiescript niet opnieuw uitvoeren met dezelfde resource-id (hetzelfde abonnement, dezelfde resourcegroepnaam en resourcenaam), maar tegelijkertijd met verschillende inhoud van de scripttekst. |
| DeploymentScriptOperationFailed | De implementatiescriptbewerking is intern mislukt. Neem contact op met Microsoft Ondersteuning. |
| DeploymentScriptStorageAccountAccessKeyNotSpecified | De toegangssleutel is niet opgegeven voor het bestaande opslagaccount.|
| DeploymentScriptContainerGroupContainsInvalidContainers | Een containergroep die door de implementatiescriptservice is gemaakt, is extern gewijzigd en er zijn ongeldige containers toegevoegd. |
| DeploymentScriptContainerGroupInNonterminalState | Twee of meer implementatiescriptresources gebruiken dezelfde naam voor de Azure Container Instance in dezelfde resourcegroep en een van deze resources heeft de uitvoering nog niet voltooid. |
| DeploymentScriptStorageAccountInvalidKind | Het bestaande opslagaccount van het type BlobBlobStorage of BlobStorage biedt geen ondersteuning voor bestands shares en kan niet worden gebruikt. |
| DeploymentScriptStorageAccountInvalidKindAndSku | Het bestaande opslagaccount biedt geen ondersteuning voor bestands shares. Zie Bestaand opslagaccount gebruiken voor een lijst met [ondersteunde opslagaccounts.](#use-existing-storage-account) |
| DeploymentScriptStorageAccountNotFound | Het opslagaccount bestaat niet of is verwijderd door een extern proces of hulpprogramma. |
| DeploymentScriptStorageAccountWithServiceEndpointEnabled | Het opgegeven opslagaccount heeft een service-eindpunt. Een opslagaccount met een service-eindpunt wordt niet ondersteund. |
| DeploymentScriptStorageAccountInvalidAccessKey | Ongeldige toegangssleutel opgegeven voor het bestaande opslagaccount. |
| DeploymentScriptStorageAccountInvalidAccessKeyFormat | Ongeldige indeling van opslagaccountsleutel. Zie [Toegangssleutels voor opslagaccounts beheren.](../../storage/common/storage-account-keys-manage.md) |
| DeploymentScriptExceededMaxAllowedTime | De uitvoeringstijd van het implementatiescript heeft de time-outwaarde overschreden die is opgegeven in de resourcedefinitie van het implementatiescript. |
| DeploymentScriptInvalidOutputs | De uitvoer van het implementatiescript is geen geldig JSON-object. |
| DeploymentScriptContainerInstancesServiceLoginFailure | De door de gebruiker toegewezen beheerde identiteit kon zich na tien pogingen met een interval van 1 minuut niet aanmelden. |
| DeploymentScriptContainerGroupNotFound | Een containergroep die is gemaakt door de implementatiescriptservice, is verwijderd door een extern hulpprogramma of proces. |
| DeploymentScriptDownloadFailure | Downloaden van een ondersteunend script is mislukt. Zie [Ondersteunend script gebruiken.](#use-supporting-scripts)|
| DeploymentScriptError | Het gebruikersscript heeft een fout veroorzaakt. |
| DeploymentScriptBootstrapScriptExecutionFailed | Het bootstrapscript heeft een fout veroorzaakt. Bootstrapscript is het systeemscript dat de uitvoering van het implementatiescript ins orchestrateert. |
| DeploymentScriptExecutionFailed | Onbekende fout tijdens de uitvoering van het implementatiescript. |
| DeploymentScriptContainerInstancesServiceUnavailable | Bij het maken van de Azure Container Instance (ACI) heeft ACI een foutmelding over service niet beschikbaar gemaakt. |
| DeploymentScriptContainerGroupInNonterminalState | Bij het maken van de Azure Container Instance (ACI) gebruikt een ander implementatiescript dezelfde ACI-naam in hetzelfde bereik (hetzelfde abonnement, dezelfde resourcegroepnaam en resourcenaam). |
| DeploymentScriptContainerGroupNameInvalid | De opgegeven azure container instance name (ACI) voldoet niet aan de ACI-vereisten. Zie [Veelvoorkomende problemen oplossen in Azure Container Instances](../../container-instances/container-instances-troubleshooting.md#issues-during-container-group-deployment).|

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u implementatiescripts gebruikt. Ga als volgende te werk om een zelfstudie over een implementatiescript te volgen:

> [!div class="nextstepaction"]
> [Zelfstudie: Implementatiescripts gebruiken in Azure Resource Manager sjablonen](./template-tutorial-deployment-script.md)

> [!div class="nextstepaction"]
> [Learn-module: ARM-sjablonen uitbreiden met behulp van implementatiescripts](/learn/modules/extend-resource-manager-template-deployment-scripts/)
