---
title: Resources implementeren met Azure CLI en sjabloon
description: Gebruik Azure Resource Manager en Azure CLI om resources te implementeren in Azure. De resources worden gedefinieerd in een Resource Manager of een Bicep-bestand.
ms.topic: conceptual
ms.date: 03/25/2021
ms.openlocfilehash: f616a40f2683268f0cc26314fcc88ecca23bdbcf
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107782059"
---
# <a name="deploy-resources-with-arm-templates-and-azure-cli"></a>Resources implementeren met ARM-sjablonen en Azure CLI

In dit artikel wordt uitgelegd hoe u Azure CLI gebruikt met Azure Resource Manager-sjablonen (ARM-sjablonen) of Bicep-bestanden om uw resources in Azure te implementeren. Zie Overzicht van sjabloonimplementatie of Bicep-overzicht als [](overview.md) u niet bekend bent met de concepten van het implementeren en beheren van uw [Azure-oplossingen.](bicep-overview.md)

De implementatieopdrachten zijn gewijzigd in Azure CLI versie 2.2.0. Voor de voorbeelden in dit artikel is Azure CLI versie 2.2.0 of hoger vereist. Als u Bicep-bestanden wilt implementeren, hebt u [Azure CLI versie 2.20.0 of hoger nodig.](/cli/azure/install-azure-cli)

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

Als u Azure CLI niet hebt geïnstalleerd, kunt u deze Azure Cloud Shell. Zie ARM-sjablonen implementeren [vanuit Azure Cloud Shell voor meer Azure Cloud Shell.](deploy-cloud-shell.md)

## <a name="deployment-scope"></a>Implementatiebereik

U kunt uw implementatie richten op een resourcegroep, abonnement, beheergroep of tenant. Afhankelijk van het bereik van de implementatie gebruikt u verschillende opdrachten.

* Als u wilt implementeren in **een resourcegroep,** gebruikt [u az deployment group create](/cli/azure/deployment/group#az_deployment_group_create):

  ```azurecli-interactive
  az deployment group create --resource-group <resource-group-name> --template-file <path-to-template-or-bicep>
  ```

* Als u wilt implementeren in **een abonnement,** gebruikt [u az deployment sub create](/cli/azure/deployment/sub#az_deployment_sub_create):

  ```azurecli-interactive
  az deployment sub create --location <location> --template-file <path-to-template-or-bicep>
  ```

  Zie Resourcegroepen en resources maken op abonnementsniveau voor meer informatie over implementaties [op abonnementsniveau.](deploy-to-subscription.md)

* Als u wilt implementeren in **een beheergroep,** gebruikt [u az deployment mg create](/cli/azure/deployment/mg#az_deployment_mg_create):

  ```azurecli-interactive
  az deployment mg create --location <location> --template-file <path-to-template-or-bicep>
  ```

  Zie Resources maken op beheergroepsniveau voor meer informatie over implementaties [op beheergroepniveau.](deploy-to-management-group.md)

* Als u wilt implementeren in **een tenant,** gebruikt [u az deployment tenant create](/cli/azure/deployment/tenant#az_deployment_tenant_create):

  ```azurecli-interactive
  az deployment tenant create --location <location> --template-file <path-to-template-or-bicep>
  ```

  Zie Resources maken op tenantniveau voor meer informatie over [implementaties op tenantniveau.](deploy-to-tenant.md)

Voor elk bereik moet de gebruiker die de sjabloon of het Bicep-bestand implementeert, over de vereiste machtigingen beschikken om resources te maken.

## <a name="deploy-local-template-or-bicep-file"></a>Lokale sjabloon of Bicep-bestand implementeren

U kunt een sjabloon implementeren vanaf uw lokale computer of een sjabloon die extern is opgeslagen. In deze sectie wordt beschreven hoe u een lokale sjabloon implementeert.

Als u implementeert in een resourcegroep die niet bestaat, maakt u de resourcegroep. De naam van de resourcegroep kan alleen alfanumerieke tekens, punten, onderstrepingstekens, afbreekstreepingstekens en haakjes bevatten. Deze mag maximaal 90 tekens lang zijn. De naam mag niet eindigen op een punt.

```azurecli-interactive
az group create --name ExampleGroup --location "Central US"
```

Als u een lokale sjabloon of Bicep-bestand wilt implementeren, gebruikt u `--template-file` de parameter in de implementatieopdracht. In het volgende voorbeeld ziet u ook hoe u een parameterwaarde in kunt stellen.

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file <path-to-template-or-bicep> \
  --parameters storageAccountType=Standard_GRS
```

De implementatie kan enkele minuten duren. Wanneer dit is bereikt, ziet u een bericht met het resultaat:

```output
"provisioningState": "Succeeded",
```

## <a name="deploy-remote-template"></a>Externe sjabloon implementeren

> [!NOTE]
> Momenteel biedt Azure CLI geen ondersteuning voor het implementeren van externe Bicep-bestanden. Gebruik [Bicep CLI om](./bicep-install.md#development-environment) het Bicep-bestand te compileren naar een JSON-sjabloon en laad het JSON-bestand vervolgens op de externe locatie.

In plaats van ARM-sjablonen op te slaan op uw lokale computer, kunt u deze het liefst opslaan op een externe locatie. U kunt sjablonen opslaan in een opslagplaats voor broncodebeheer (zoals GitHub). U kunt de sjablonen ook opslaan in een Azure-opslagaccount voor gedeelde toegang in uw organisatie.

[!INCLUDE [Deploy templates in private GitHub repo](../../../includes/resource-manager-private-github-repo-templates.md)]

Als u implementeert in een resourcegroep die niet bestaat, maakt u de resourcegroep. De naam van de resourcegroep kan alleen alfanumerieke tekens, punten, onderstrepingstekens, afbreekstreepingstekens en haakjes bevatten. Deze mag maximaal 90 tekens lang zijn. De naam mag niet eindigen op een punt.

```azurecli-interactive
az group create --name ExampleGroup --location "Central US"
```

Als u een externe sjabloon wilt implementeren, gebruikt u de `template-uri`-parameter.

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json" \
  --parameters storageAccountType=Standard_GRS
```

In het voorgaande voorbeeld is een openbaar toegankelijke URI vereist voor de sjabloon. Dit werkt voor de meeste scenario's omdat uw sjabloon geen gevoelige gegevens mag bevatten. Als u gevoelige gegevens moet opgeven (zoals een beheerderswachtwoord), geeft u die waarde door als een beveiligde parameter. Als u echter de toegang tot de sjabloon wilt beheren, kunt u [sjabloonspecificaties gebruiken.](#deploy-template-spec)

Als u externe gekoppelde sjablonen met een relatief pad wilt implementeren die zijn opgeslagen in een opslagaccount, gebruikt u `query-string` om het SAS-token op te geven:

```azurecli-interactive
az deployment group create \
  --name linkedTemplateWithRelativePath \
  --resource-group myResourceGroup \
  --template-uri "https://stage20210126.blob.core.windows.net/template-staging/mainTemplate.json" \
  --query-string $sasToken
```

Zie Relatief pad gebruiken voor [gekoppelde sjablonen voor meer informatie.](./linked-templates.md#linked-template)

## <a name="deployment-name"></a>Naam van implementatie

Wanneer u een ARM-sjabloon implementeert, kunt u de implementatie een naam geven. Met deze naam kunt u de implementatie ophalen uit de implementatiegeschiedenis. Als u geen naam op geeft voor de implementatie, wordt de naam van het sjabloonbestand gebruikt. Als u bijvoorbeeld een sjabloon met de naam implementeert en geen implementatienaam `azuredeploy.json` opgeeft, heeft de implementatie de naam `azuredeploy` .

Telkens wanneer u een implementatie uitgevoerd, wordt er een vermelding toegevoegd aan de implementatiegeschiedenis van de resourcegroep met de naam van de implementatie. Als u een andere implementatie hebt uitgevoerd en deze dezelfde naam geeft, wordt de eerdere vermelding vervangen door de huidige implementatie. Als u unieke vermeldingen in de implementatiegeschiedenis wilt behouden, geeft u elke implementatie een unieke naam.

Als u een unieke naam wilt maken, kunt u een willekeurig getal toewijzen.

```azurecli-interactive
deploymentName='ExampleDeployment'$RANDOM
```

Of voeg een datumwaarde toe.

```azurecli-interactive
deploymentName='ExampleDeployment'$(date +"%d-%b-%Y")
```

Als u gelijktijdige implementaties naar dezelfde resourcegroep met dezelfde implementatienaam hebt uitgevoerd, wordt alleen de laatste implementatie voltooid. Implementaties met dezelfde naam die nog niet zijn voltooid, worden vervangen door de laatste implementatie. Als u bijvoorbeeld een implementatie met de naam hebt uitgevoerd die een opslagaccount met de naam implementeert en tegelijkertijd een andere implementatie met de naam die een opslagaccount met de naam implementeert, implementeert u slechts één `newStorage` `storage1` `newStorage` `storage2` opslagaccount. Het resulterende opslagaccount heeft de naam `storage2` .

Als u echter een implementatie met de naam hebt uitgevoerd die een opslagaccount met de naam implementeert en onmiddellijk nadat het is voltooid, hebt u een andere implementatie met de naam die een opslagaccount met de naam implementeert, en hebt u twee `newStorage` `storage1` `newStorage` `storage2` opslagaccounts. De ene heeft `storage1` de naam en de andere heeft de naam `storage2` . Maar u hebt slechts één vermelding in de implementatiegeschiedenis.

Wanneer u voor elke implementatie een unieke naam opgeeft, kunt u deze gelijktijdig zonder conflicten uitvoeren. Als u een implementatie met de naam hebt uitgevoerd die een opslagaccount met de naam implementeert en tegelijkertijd een andere implementatie met de naam die een opslagaccount met de naam implementeert, hebt u twee opslagaccounts en twee vermeldingen in de `newStorage1` `storage1` `newStorage2` `storage2` implementatiegeschiedenis.

Geef elke implementatie een unieke naam om conflicten met gelijktijdige implementaties te voorkomen en unieke vermeldingen in de implementatiegeschiedenis te garanderen.

## <a name="deploy-template-spec"></a>Sjabloonspecificatie implementeren

> [!NOTE]
> Momenteel biedt Azure CLI geen ondersteuning voor het maken van sjabloonspecificaties door Bicep-bestanden op te geven. U kunt echter een Bicep-bestand maken met de resource [Microsoft.Resources/templateSpecs](/azure/templates/microsoft.resources/templatespecs) om een sjabloonspecificatie te implementeren. Hier is een [voorbeeld.](https://github.com/Azure/azure-docs-json-samples/blob/master/create-template-spec-using-template/azuredeploy.bicep)

In plaats van een lokale of externe sjabloon te implementeren, kunt u een [sjabloonspecificatie maken.](template-specs.md) De sjabloonspecificatie is een resource in uw Azure-abonnement die een ARM-sjabloon bevat. Zo kunt u de sjabloon eenvoudig veilig delen met gebruikers in uw organisatie. U gebruikt op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC) om toegang te verlenen tot de sjabloonspecificatie. Deze functie is momenteel beschikbaar als preview-versie.

De volgende voorbeelden laten zien hoe u een sjabloonspecificatie maakt en implementeert.

Maak eerst de sjabloonspecificatie door de ARM-sjabloon op te geven.

```azurecli
az ts create \
  --name storageSpec \
  --version "1.0" \
  --resource-group templateSpecRG \
  --location "westus2" \
  --template-file "./mainTemplate.json"
```

Haal vervolgens de id voor de sjabloonspecificatie op en implementeer deze.

```azurecli
id = $(az ts show --name storageSpec --resource-group templateSpecRG --version "1.0" --query "id")

az deployment group create \
  --resource-group demoRG \
  --template-spec $id
```

Zie sjabloonspecificaties voor [Azure Resource Manager (preview) voor meer informatie.](template-specs.md)

## <a name="preview-changes"></a>Voorbeeld van wijzigingen bekijken

Voordat u de sjabloon implementeert, kunt u een voorbeeld bekijken van de wijzigingen die de sjabloon aan uw omgeving aan zal brengen. Gebruik de [what-if-bewerking om](template-deploy-what-if.md) te controleren of de sjabloon de wijzigingen aan de hand heeft die u verwacht. Met Wat-als wordt de sjabloon ook gevalideerd op fouten.

## <a name="parameters"></a>Parameters

Als u parameterwaarden wilt doorgeven, kunt u inlineparameters of een parameterbestand gebruiken.

### <a name="inline-parameters"></a>Inlineparameters

Als u inlineparameters wilt doorgeven, geeft u de waarden op in `parameters` . Als u bijvoorbeeld een tekenreeks en matrix wilt doorgeven aan een sjabloon in een Bash-shell, gebruikt u:

```azurecli-interactive
az deployment group create \
  --resource-group testgroup \
  --template-file <path-to-template-or-bicep> \
  --parameters exampleString='inline string' exampleArray='("value1", "value2")'
```

Als u Azure CLI gebruikt met Windows-opdrachtprompt (CMD) of PowerShell, geeft u de matrix door in de volgende indeling: `exampleArray="['value1','value2']"` .

U kunt ook de inhoud van het bestand op halen en die inhoud opgeven als een inlineparameter.

```azurecli-interactive
az deployment group create \
  --resource-group testgroup \
  --template-file <path-to-template-or-bicep> \
  --parameters exampleString=@stringContent.txt exampleArray=@arrayContent.json
```

Het is handig om een parameterwaarde op te geven uit een bestand wanneer u configuratiewaarden moet opgeven. U kunt bijvoorbeeld [cloud-init-waarden voor een virtuele Linux-machine leveren.](../../virtual-machines/linux/using-cloud-init.md)

De _arrayContent.jsvoor de_ indeling is:

```json
[
    "value1",
    "value2"
]
```

Als u bijvoorbeeld een -object wilt doorgeven om tags in te stellen, gebruikt u JSON. Uw sjabloon kan bijvoorbeeld een parameter bevatten zoals deze:

```json
    "resourceTags": {
      "type": "object",
      "defaultValue": {
        "Cost Center": "IT Department"
      }
    }
```

In dit geval kunt u een JSON-tekenreeks doorgeven om de parameter in te stellen, zoals wordt weergegeven in het volgende Bash-script:

```bash
tags='{"Owner":"Contoso","Cost Center":"2345-324"}'
az deployment group create --name addstorage  --resource-group myResourceGroup \
--template-file $templateFile \
--parameters resourceName=abcdef4556 resourceTags="$tags"
```

Gebruik dubbele aanhalingstekens rond de JSON die u wilt doorgeven aan het -object.

### <a name="parameter-files"></a>Parameterbestanden

In plaats van parameters als inline waarden door te geven in uw script, is het wellicht eenvoudiger een JSON-bestand te gebruiken dat de parameterwaarden bevat. Het parameterbestand moet een lokaal bestand zijn. Externe parameterbestanden worden niet ondersteund met Azure CLI. Zowel de ARM-sjabloon als het Bicep-bestand gebruiken JSON-parameterbestanden.

Zie [Een Resource Manager-parameterbestand maken](parameter-files.md) voor meer informatie over het parameterbestand.

Als u een lokaal parameterbestand wilt doorgeven, gebruikt u om een lokaal bestand met de naam `@`storage.parameters.js _opgeven op_.

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters @storage.parameters.json
```

## <a name="handle-extended-json-format"></a>Uitgebreide JSON-indeling verwerken

Als u een sjabloon met meerdere regelreeksen of opmerkingen wilt implementeren met behulp van Azure CLI met versie 2.3.0 of ouder, moet u de `--handle-extended-json-format` -switch gebruiken.  Bijvoorbeeld:

```json
{
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2018-10-01",
  "name": "[variables('vmName')]", // to customize name, change it in variables
  "location": "[
    parameters('location')
    ]", //defaults to resource group location
  /*
    storage account and network interface
    must be deployed first
  */
  "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
  ],
```

## <a name="next-steps"></a>Volgende stappen

* Zie Terugdraaien bij fout naar geslaagde implementatie als u wilt terugdraaien naar een geslaagde implementatie wanneer er een [foutmelding wordt weergegeven.](rollback-on-error.md)
* Zie implementatiemodi voor meer informatie over het afhandelen van resources die bestaan in de [resourcegroep,](deployment-modes.md)maar die niet zijn gedefinieerd in Azure Resource Manager sjabloon.
* Zie Inzicht in de structuur en syntaxis van ARM-sjablonen voor meer informatie over het definiëren van [parameters in uw sjabloon.](template-syntax.md)
* Zie Veelvoorkomende implementatiefouten in Azure oplossen met Azure Resource Manager voor tips [over het oplossen van veelvoorkomende implementatiefouten.](common-deployment-errors.md)
