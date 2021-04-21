---
title: Fouten bij niet gevonden resource
description: Beschrijft hoe u fouten kunt oplossen wanneer een resource niet kan worden gevonden. De fout kan optreden bij het implementeren van een Azure Resource Manager sjabloon of bij het uitvoeren van beheeracties.
ms.topic: troubleshooting
ms.date: 03/23/2021
ms.openlocfilehash: 5e3a72eaad99721cec9500956179a3ae9d9cf8d2
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762133"
---
# <a name="resolve-resource-not-found-errors"></a>Fouten met resource niet gevonden oplossen

In dit artikel wordt de fout beschreven die u ziet wanneer een resource niet kan worden gevonden tijdens een bewerking. Normaal gesproken wordt deze fout weergegeven bij het implementeren van resources. U ziet deze fout ook wanneer u beheertaken uitvoert en Azure Resource Manager de vereiste resource niet kunt vinden. Als u bijvoorbeeld tags probeert toe te voegen aan een resource die niet bestaat, ontvangt u deze fout.

## <a name="symptom"></a>Symptoom

Er zijn twee foutcodes die aangeven dat de resource niet kan worden gevonden. De **fout NotFound** retourneert een resultaat dat lijkt op:

```
Code=NotFound;
Message=Cannot find ServerFarm with name exampleplan.
```

De **ResourceNotFound-fout** retourneert een resultaat dat lijkt op:

```
Code=ResourceNotFound;
Message=The Resource 'Microsoft.Storage/storageAccounts/{storage name}' under resource
group {resource group name} was not found.
```

## <a name="cause"></a>Oorzaak

Resource Manager moet de eigenschappen voor een resource ophalen, maar kan de resource niet vinden in uw abonnementen.

## <a name="solution-1---check-resource-properties"></a>Oplossing 1: resource-eigenschappen controleren

Wanneer u deze fout ontvangt tijdens het uitvoeren van een beheertaak, controleert u de waarden die u voor de resource op geeft. De drie waarden die u moet controleren, zijn:

* Resourcenaam
* Naam van de resourcegroep
* Abonnement

Als u PowerShell of Azure CLI gebruikt, controleert u of u de opdracht in het abonnement met de resource gebruikt. U kunt het abonnement wijzigen met [Set-AzContext](/powershell/module/Az.Accounts/Set-AzContext) of [az account set](/cli/azure/account#az_account_set). Veel opdrachten bieden ook een abonnementsparameter waarmee u een ander abonnement kunt opgeven dan de huidige context.

Als u problemen hebt met het verifiëren van de eigenschappen, meld u dan aan bij de [portal](https://portal.azure.com). Zoek de resource die u wilt gebruiken en bekijk de resourcenaam, de resourcegroep en het abonnement.

## <a name="solution-2---set-dependencies"></a>Oplossing 2: afhankelijkheden instellen

Als u deze fout krijgt bij het implementeren van een sjabloon, moet u mogelijk een afhankelijkheid toevoegen. Resource Manager optimaliseert de implementatie door resources parallel te maken, indien mogelijk. Als de ene resource na een andere resource moet worden geïmplementeerd, moet u het **element dependsOn** in uw sjabloon gebruiken. Wanneer u bijvoorbeeld een web-app implementeert, moet het App Service bestaan. Als u nog niet hebt opgegeven dat de web-app afhankelijk is van het App Service plan, maakt Resource Manager beide resources tegelijk. Er wordt een foutbericht weergegeven waarin staat dat de resource App Service plan niet kan worden gevonden, omdat deze nog niet bestaat wanneer u probeert een eigenschap in te stellen voor de web-app. U voorkomt deze fout door de afhankelijkheid in de web-app in te stellen.

```json
{
  "type": "Microsoft.Web/sites",
  "apiVersion": "2015-08-01",
  "dependsOn": [
    "[variables('hostingPlanName')]"
  ],
  ...
}
```

Maar u wilt het instellen van afhankelijkheden die niet nodig zijn, vermijden. Wanneer u onnodige afhankelijkheden hebt, kunt u de duur van de implementatie verlengen door te voorkomen dat resources die niet afhankelijk zijn van elkaar parallel worden geïmplementeerd. Daarnaast kunt u circulaire afhankelijkheden maken die de implementatie blokkeren. De [functie reference](template-functions-resource.md#reference) en [list*](template-functions-resource.md#list) maken een impliciete afhankelijkheid van de resource waarnaar wordt verwezen, wanneer die resource wordt geïmplementeerd in dezelfde sjabloon en wordt verwezen door de naam (niet de resource-id). Daarom hebt u mogelijk meer afhankelijkheden dan de afhankelijkheden die zijn opgegeven in de **eigenschap dependsOn.** De [functie resourceId](template-functions-resource.md#resourceid) maakt geen impliciete afhankelijkheid of controleert of de resource bestaat. De [referentiefunctie](template-functions-resource.md#reference) en [lijst*-functies](template-functions-resource.md#list) maken geen impliciete afhankelijkheid wanneer naar de resource wordt verwezen met de resource-id. Als u een impliciete afhankelijkheid wilt maken, moet u de naam doorgeven van de resource die in dezelfde sjabloon is geïmplementeerd.

Wanneer u afhankelijkheidsproblemen ziet, moet u inzicht krijgen in de volgorde van de resource-implementatie. De volgorde van implementatiebewerkingen weergeven:

1. Selecteer de implementatiegeschiedenis voor uw resourcegroep.

   ![implementatiegeschiedenis selecteren](./media/error-not-found/select-deployment.png)

2. Selecteer een implementatie uit de geschiedenis en selecteer **Gebeurtenissen**.

   ![implementatiegebeurtenissen selecteren](./media/error-not-found/select-deployment-events.png)

3. Bekijk de volgorde van gebeurtenissen voor elke resource. Let op de status van elke bewerking. In de volgende afbeelding ziet u bijvoorbeeld drie opslagaccounts die parallel zijn geïmplementeerd. U ziet dat de drie opslagaccounts tegelijkertijd worden gestart.

   ![parallelle implementatie](./media/error-not-found/deployment-events-parallel.png)

   In de volgende afbeelding ziet u drie opslagaccounts die niet parallel worden geïmplementeerd. Het tweede opslagaccount is afhankelijk van het eerste opslagaccount en het derde opslagaccount is afhankelijk van het tweede opslagaccount. Het eerste opslagaccount wordt gestart, geaccepteerd en voltooid voordat het volgende wordt gestart.

   ![sequentiële implementatie](./media/error-not-found/deployment-events-sequence.png)

## <a name="solution-3---get-external-resource"></a>Oplossing 3: externe resource op halen

Wanneer u een sjabloon implementeert en u een resource moet krijgen die zich in een ander abonnement of een andere resourcegroep bevindt, gebruikt u de [functie resourceId.](template-functions-resource.md#resourceid) Deze functie retourneert om de volledig gekwalificeerde naam van de resource op te halen.

De abonnements- en resourcegroepparameters in de functie resourceId zijn optioneel. Als u deze niet op geeft, worden deze standaard ingesteld op het huidige abonnement en de huidige resourcegroep. Wanneer u werkt met een resource in een andere resourcegroep of een ander abonnement, moet u deze waarden verstrekken.

In het volgende voorbeeld wordt de resource-id voor een resource die zich in een andere resourcegroep bevindt, opr.

```json
"properties": {
  "name": "[parameters('siteName')]",
  "serverFarmId": "[resourceId('plangroup', 'Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
}
```

## <a name="solution-4---get-managed-identity-from-resource"></a>Oplossing 4: beheerde identiteit uit resource halen

Als u een resource implementeert die impliciet een beheerde identiteit [maakt,](../../active-directory/managed-identities-azure-resources/overview.md)moet u wachten tot die resource is geïmplementeerd voordat u waarden voor de beheerde identiteit ophaalt. Als u de naam van [](template-functions-resource.md#reference) de beheerde identiteit doorheft aan de referentiefunctie, probeert Resource Manager om de verwijzing op te lossen voordat de resource en identiteit worden geïmplementeerd. Geef in plaats daarvan de naam door van de resource op wie de identiteit wordt toegepast. Deze aanpak zorgt ervoor dat de resource en de beheerde identiteit worden geïmplementeerd voordat Resource Manager de referentiefunctie om te zetten.

Gebruik in de referentiefunctie om `Full` alle eigenschappen op te halen, inclusief de beheerde identiteit.

Het patroon is:

`"[reference(resourceId(<resource-provider-namespace>, <resource-name>), <API-version>, 'Full').Identity.propertyName]"`

> [!IMPORTANT]
> Gebruik niet het patroon:
>
> `"[reference(concat(resourceId(<resource-provider-namespace>, <resource-name>),'/providers/Microsoft.ManagedIdentity/Identities/default'),<API-version>).principalId]"`
>
> Uw sjabloon mislukt.

Als u bijvoorbeeld de principal-id wilt op halen voor een beheerde identiteit die wordt toegepast op een virtuele machine, gebruikt u:

```json
"[reference(resourceId('Microsoft.Compute/virtualMachines', variables('vmName')),'2019-12-01', 'Full').identity.principalId]",
```

Of gebruik het volgende om de tenant-id op te halen voor een beheerde identiteit die wordt toegepast op een virtuele-machineschaalset:

```json
"[reference(resourceId('Microsoft.Compute/virtualMachineScaleSets',  variables('vmNodeType0Name')), 2019-12-01, 'Full').Identity.tenantId]"
```

## <a name="solution-5---check-functions"></a>Oplossing 5: functies controleren

Zoek bij het implementeren van een sjabloon naar expressies die gebruikmaken van de functies [reference](template-functions-resource.md#reference) of [listKeys.](template-functions-resource.md#listkeys) De waarden die u op geeft, variëren afhankelijk van of de resource zich in dezelfde sjabloon, resourcegroep en abonnement. Controleer of u de vereiste parameterwaarden voor uw scenario opgeeft. Als de resource zich in een andere resourcegroep heeft, geeft u de volledige resource-id op. Als u bijvoorbeeld wilt verwijzen naar een opslagaccount in een andere resourcegroep, gebruikt u:

```json
"[reference(resourceId('exampleResourceGroup', 'Microsoft.Storage/storageAccounts', 'myStorage'), '2017-06-01')]"
```

## <a name="solution-6---after-deleting-resource"></a>Oplossing 6: na het verwijderen van de resource

Wanneer u een resource verwijdert, kan het even duren wanneer de resource nog steeds wordt weergegeven in de portal, maar niet daadwerkelijk beschikbaar is. Als u de resource selecteert, wordt er een foutbericht weergegeven dat de resource niet is gevonden. Vernieuw de portal om de meest recente weergave te krijgen.

Als het probleem zich na een korte wachttijd blijft voor doen, neemt [u contact op met de ondersteuning](https://azure.microsoft.com/support/options/).
