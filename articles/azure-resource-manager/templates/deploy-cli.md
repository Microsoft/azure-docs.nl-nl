---
title: Resources implementeren met Azure CLI en sjabloon
description: Gebruik Azure Resource Manager en Azure CLI om resources te implementeren in Azure. De resources zijn gedefinieerd in een Resource Manager-sjabloon.
ms.topic: conceptual
ms.date: 01/15/2021
ms.openlocfilehash: d79a5c93bf79c5851beabbba2eb3a663c6ab5999
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/16/2021
ms.locfileid: "98251077"
---
# <a name="deploy-resources-with-arm-templates-and-azure-cli"></a>Resources implementeren met ARM-sjablonen en Azure CLI

In dit artikel wordt uitgelegd hoe u Azure CLI gebruikt met Azure Resource Manager sjablonen (ARM-sjablonen) om uw resources te implementeren in Azure. Als u niet bekend bent met de concepten van het implementeren en beheren van uw Azure-oplossingen, raadpleegt u [overzicht van sjabloon implementatie](overview.md).

De implementatie-opdrachten zijn gewijzigd in azure CLI-versie 2.2.0. Voor de voor beelden in dit artikel is Azure CLI-versie 2.2.0 of hoger vereist.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

Als Azure CLI niet is geïnstalleerd, kunt u Azure Cloud Shell gebruiken. Zie [arm-sjablonen implementeren vanaf Azure Cloud shell](deploy-cloud-shell.md)voor meer informatie.

## <a name="deployment-scope"></a>Implementatie bereik

U kunt uw implementatie richten op een resource groep, een abonnement, een beheer groep of een Tenant. Afhankelijk van het bereik van de implementatie, gebruikt u verschillende opdrachten.

* Gebruik [AZ Deployment Group Create](/cli/azure/deployment/group#az-deployment-group-create)om te implementeren in een **resource groep**:

  ```azurecli-interactive
  az deployment group create --resource-group <resource-group-name> --template-file <path-to-template>
  ```

* Gebruik [AZ Deployment sub Create](/cli/azure/deployment/sub#az-deployment-sub-create)om te implementeren in een **abonnement**:

  ```azurecli-interactive
  az deployment sub create --location <location> --template-file <path-to-template>
  ```

  Zie [resource groepen en-resources op abonnements niveau maken](deploy-to-subscription.md)voor meer informatie over implementaties op abonnements niveau.

* Gebruik [AZ Deployment mg Create](/cli/azure/deployment/mg#az-deployment-mg-create)om te implementeren in een **beheer groep**:

  ```azurecli-interactive
  az deployment mg create --location <location> --template-file <path-to-template>
  ```

  Zie [resources maken op het niveau van de beheer groep](deploy-to-management-group.md)voor meer informatie over implementaties op het niveau van beheer groepen.

* Gebruik [AZ Deployment Tenant Create](/cli/azure/deployment/tenant#az-deployment-tenant-create)om te implementeren naar een **Tenant**:

  ```azurecli-interactive
  az deployment tenant create --location <location> --template-file <path-to-template>
  ```

  Zie [resources maken op Tenant niveau](deploy-to-tenant.md)voor meer informatie over implementaties op Tenant niveau.

Voor elk bereik moet de gebruiker die de sjabloon implementeert, over de vereiste machtigingen beschikken om resources te maken.

## <a name="deploy-local-template"></a>Een lokale sjabloon implementeren

U kunt een sjabloon implementeren vanaf uw lokale computer of een die extern is opgeslagen. In deze sectie wordt het implementeren van een lokale sjabloon beschreven.

Als u implementeert in een resource groep die niet bestaat, maakt u de resource groep. De naam van de resource groep mag alleen alfanumerieke tekens, punten, onderstrepings teken, afbreek streepjes en haakjes bevatten. Het kan Maxi maal 90 tekens lang zijn. De naam kan niet eindigen met een punt.

```azurecli-interactive
az group create --name ExampleGroup --location "Central US"
```

Als u een lokale sjabloon wilt implementeren, gebruikt u de `--template-file` para meter in de implementatie opdracht. In het volgende voor beeld ziet u ook hoe u een parameter waarde instelt die afkomstig is uit de sjabloon.

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file azuredeploy.json \
  --parameters storageAccountType=Standard_GRS
```

De implementatie kan enkele minuten duren. Wanneer het is voltooid, ziet u een bericht met het volgende resultaat:

```output
"provisioningState": "Succeeded",
```

## <a name="deploy-remote-template"></a>Externe sjabloon implementeren

In plaats van ARM-sjablonen op uw lokale computer op te slaan, kunt u ze beter opslaan op een externe locatie. U kunt sjablonen opslaan in een opslagplaats voor broncodebeheer (zoals GitHub). U kunt de sjablonen ook opslaan in een Azure-opslagaccount voor gedeelde toegang in uw organisatie.

Als u implementeert in een resource groep die niet bestaat, maakt u de resource groep. De naam van de resource groep mag alleen alfanumerieke tekens, punten, onderstrepings teken, afbreek streepjes en haakjes bevatten. Het kan Maxi maal 90 tekens lang zijn. De naam kan niet eindigen met een punt.

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

In het voor gaande voor beeld is een openbaar toegankelijke URI vereist voor de sjabloon, die voor de meeste scenario's werkt, omdat uw sjabloon geen gevoelige gegevens mag bevatten. Als u gevoelige gegevens (zoals een beheerders wachtwoord) moet opgeven, geeft u die waarde als een beveiligde para meter door. Als u echter de toegang tot de sjabloon wilt beheren, kunt u de [sjabloon specificaties](#deploy-template-spec)gebruiken.

## <a name="deployment-name"></a>Naam van implementatie

Bij het implementeren van een ARM-sjabloon kunt u een naam opgeven voor de implementatie. Deze naam kan u helpen de implementatie op te halen uit de implementatie geschiedenis. Als u geen naam opgeeft voor de implementatie, wordt de naam van het sjabloon bestand gebruikt. Als u bijvoorbeeld een sjabloon implementeert `azuredeploy.json` met de naam en u geen implementatie naam opgeeft, wordt de implementatie een naam genoemd `azuredeploy` .

Telkens wanneer u een implementatie uitvoert, wordt een item toegevoegd aan de implementatie geschiedenis van de resource groep met de naam van de implementatie. Als u een andere implementatie uitvoert en deze dezelfde naam geeft, wordt de vorige vermelding vervangen door de huidige implementatie. Als u de unieke vermeldingen in de implementatie geschiedenis wilt behouden, geeft u elke implementatie een unieke naam.

Als u een unieke naam wilt maken, kunt u een wille keurig getal toewijzen.

```azurecli-interactive
deploymentName='ExampleDeployment'$RANDOM
```

U kunt ook een datum waarde toevoegen.

```azurecli-interactive
deploymentName='ExampleDeployment'$(date +"%d-%b-%Y")
```

Als u gelijktijdige implementaties uitvoert naar dezelfde resource groep met dezelfde implementatie naam, wordt alleen de laatste implementatie voltooid. Implementaties met dezelfde naam die nog niet zijn voltooid, worden vervangen door de laatste implementatie. Als u bijvoorbeeld een implementatie uitvoert met de naam `newStorage` die een opslag account implementeert `storage1` en tegelijkertijd een andere implementatie uitvoert met de naam `newStorage` die een opslag account implementeert `storage2` , implementeert u slechts één opslag account. De naam van het resulterende opslag account is `storage2` .

Als u echter een implementatie uitvoert met de naam `newStorage` die een opslag account implementeert `storage1` en direct nadat de implementatie is voltooid, voert u `newStorage` `storage2` twee opslag accounts uit met een naam die een opslag account implementeert. Een heeft `storage1` de naam en de andere heet `storage2` . Maar u hebt slechts één vermelding in de implementatie geschiedenis.

Wanneer u een unieke naam voor elke implementatie opgeeft, kunt u deze gelijktijdig zonder conflict uitvoeren. Als u een implementatie uitvoert met de naam `newStorage1` die een opslag account implementeert `storage1` en tegelijkertijd een andere implementatie uitvoert met de naam `newStorage2` die een opslag account implementeert `storage2` , hebt u twee opslag accounts en twee vermeldingen in de implementatie geschiedenis.

Geef elke implementatie een unieke naam om conflicten met gelijktijdige implementaties te voor komen en te zorgen voor unieke vermeldingen in de implementatie geschiedenis.

## <a name="deploy-template-spec"></a>Sjabloonspecificatie implementeren

In plaats van een lokale of externe sjabloon te implementeren, kunt u een [sjabloon specificatie](template-specs.md)maken. De sjabloon specificatie is een resource in uw Azure-abonnement die een ARM-sjabloon bevat. Het is eenvoudig om de sjabloon veilig te delen met gebruikers in uw organisatie. U gebruikt op rollen gebaseerd toegangs beheer van Azure (Azure RBAC) om toegang te verlenen tot de sjabloon specificatie. Deze functie is momenteel beschikbaar als preview-versie.

In de volgende voor beelden ziet u hoe u een sjabloon specificatie maakt en implementeert.

Maak eerst de sjabloon specificatie door de ARM-sjabloon op te geven.

```azurecli
az ts create \
  --name storageSpec \
  --version "1.0" \
  --resource-group templateSpecRG \
  --location "westus2" \
  --template-file "./mainTemplate.json"
```

Vervolgens haalt u de ID op voor de sjabloon specificatie en implementeert u deze.

```azurecli
id = $(az ts show --name storageSpec --resource-group templateSpecRG --version "1.0" --query "id")

az deployment group create \
  --resource-group demoRG \
  --template-spec $id
```

Zie [Azure Resource Manager-sjabloon specificaties (preview-versie)](template-specs.md)voor meer informatie.

## <a name="preview-changes"></a>Preview-wijzigingen

Voordat u uw sjabloon implementeert, kunt u een voor beeld bekijken van de wijzigingen die door de sjabloon in uw omgeving worden aangebracht. Gebruik de [bewerking What-if](template-deploy-what-if.md) om te controleren of de sjabloon de wijzigingen aanbrengt die u verwacht. Wat-als ook de sjabloon voor fouten valideert.

## <a name="parameters"></a>Parameters

Als u parameter waarden wilt door geven, kunt u inline-para meters of een parameter bestand gebruiken.

### <a name="inline-parameters"></a>Inline-para meters

Geef de waarden op in om inline-para meters door te geven `parameters` . Als u bijvoorbeeld een teken reeks en een matrix wilt door geven aan een sjabloon in een bash-shell, gebruikt u:

```azurecli-interactive
az deployment group create \
  --resource-group testgroup \
  --template-file demotemplate.json \
  --parameters exampleString='inline string' exampleArray='("value1", "value2")'
```

Als u Azure CLI gebruikt met Windows-opdracht prompt (CMD) of Power shell, geeft u de matrix de volgende notatie: `exampleArray="['value1','value2']"` .

U kunt ook de inhoud van het bestand ophalen en deze inhoud als een inline-para meter opgeven.

```azurecli-interactive
az deployment group create \
  --resource-group testgroup \
  --template-file demotemplate.json \
  --parameters exampleString=@stringContent.txt exampleArray=@arrayContent.json
```

Het ophalen van een parameter waarde uit een bestand is handig wanneer u configuratie waarden moet opgeven. U kunt bijvoorbeeld [Cloud-init-waarden opgeven voor een virtuele Linux-machine](../../virtual-machines/linux/using-cloud-init.md).

De _arrayContent.jsin_ de indeling is:

```json
[
    "value1",
    "value2"
]
```

Gebruik JSON als u een object wilt door geven, bijvoorbeeld om labels in te stellen. Uw sjabloon kan bijvoorbeeld een para meter bevatten zoals deze:

```json
    "resourceTags": {
      "type": "object",
      "defaultValue": {
        "Cost Center": "IT Department"
      }
    }
```

In dit geval kunt u een JSON-teken reeks door geven om de para meter in te stellen, zoals wordt weer gegeven in het volgende bash-script:

```bash
tags='{"Owner":"Contoso","Cost Center":"2345-324"}'
az deployment group create --name addstorage  --resource-group myResourceGroup \
--template-file $templateFile \
--parameters resourceName=abcdef4556 resourceTags="$tags"
```

Gebruik dubbele aanhalings tekens rond de JSON die u wilt door geven aan het object.

### <a name="parameter-files"></a>Parameter bestanden

In plaats van parameters als inline waarden door te geven in uw script, is het wellicht eenvoudiger een JSON-bestand te gebruiken dat de parameterwaarden bevat. Het parameter bestand moet een lokaal bestand zijn. Externe parameter bestanden worden niet ondersteund met Azure CLI.

Zie [Een Resource Manager-parameterbestand maken](parameter-files.md) voor meer informatie over het parameterbestand.

Als u een lokaal parameter bestand wilt door geven, gebruikt `@` u om een lokaal bestand met de naam _storage.parameters.jsop_ te geven.

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters @storage.parameters.json
```

## <a name="handle-extended-json-format"></a>Uitgebreide JSON-indeling verwerken

Als u een sjabloon wilt implementeren met reeksen met meerdere regels of opmerkingen met behulp van Azure CLI met versie 2.3.0 of ouder, moet u de `--handle-extended-json-format` Schakel optie gebruiken.  Bijvoorbeeld:

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

- Als u wilt terugkeren naar een geslaagde implementatie wanneer u een fout krijgt, raadpleegt u [herstellen bij fout naar geslaagde implementatie](rollback-on-error.md).
- Zie [Azure Resource Manager implementatie modi](deployment-modes.md)om op te geven hoe u resources wilt afhandelen die in de resource groep aanwezig zijn, maar die niet zijn gedefinieerd in de sjabloon.
- Zie [inzicht in de structuur en syntaxis van arm-sjablonen](template-syntax.md)voor informatie over het definiëren van para meters in uw sjabloon.
- Zie [problemen met algemene Azure-implementatie fouten oplossen met Azure Resource Manager](common-deployment-errors.md)voor tips over het oplossen van veelvoorkomende implementatie fouten.
