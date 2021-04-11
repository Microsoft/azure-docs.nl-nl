---
title: Fouten bij niet-gevonden resources
description: Hierin wordt beschreven hoe u fouten oplost wanneer een bron niet kan worden gevonden. De fout kan optreden bij het implementeren van een Azure Resource Manager sjabloon of bij het nemen van beheer acties.
ms.topic: troubleshooting
ms.date: 03/23/2021
ms.openlocfilehash: b80c32683190167d5c0d6e0a7f75acce8bbdb833
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104950872"
---
# <a name="resolve-resource-not-found-errors"></a>Fouten bij niet-gevonden resources oplossen

In dit artikel wordt de fout beschreven die wordt weer gegeven wanneer een resource niet kan worden gevonden tijdens een bewerking. Normaal gesp roken ziet u deze fout bij het implementeren van resources. U ziet deze fout ook wanneer u beheer taken uitvoert en Azure Resource Manager de vereiste resource niet kunt vinden. Als u bijvoorbeeld labels probeert toe te voegen aan een resource die niet bestaat, wordt deze fout weer gegeven.

## <a name="symptom"></a>Symptoom

Er zijn twee fout codes die aangeven dat de resource niet kan worden gevonden. De **NotFound** -fout retourneert een resultaat vergelijkbaar met het volgende:

```
Code=NotFound;
Message=Cannot find ServerFarm with name exampleplan.
```

De **ResourceNotFound** -fout retourneert een resultaat vergelijkbaar met het volgende:

```
Code=ResourceNotFound;
Message=The Resource 'Microsoft.Storage/storageAccounts/{storage name}' under resource
group {resource group name} was not found.
```

## <a name="cause"></a>Oorzaak

Resource Manager moet de eigenschappen van een resource ophalen, maar kan de resource niet vinden in uw abonnementen.

## <a name="solution-1---check-resource-properties"></a>Oplossing 1-Controleer de resource-eigenschappen

Wanneer u deze fout tijdens het uitvoeren van een beheer taak ontvangt, controleert u de waarden die u voor de resource opgeeft. De drie te controleren waarden zijn:

* Resourcenaam
* Naam van de resourcegroep
* Abonnement

Als u Power shell of Azure CLI gebruikt, controleert u of u de opdracht uitvoert in het abonnement dat de resource bevat. U kunt het abonnement wijzigen met [set-AzContext](/powershell/module/Az.Accounts/Set-AzContext) of [AZ account set](/cli/azure/account#az-account-set). Veel opdrachten bieden ook een abonnements parameter waarmee u een ander abonnement kunt opgeven dan de huidige context.

Als u problemen hebt met het controleren van de eigenschappen, meldt u zich aan bij de [Portal](https://portal.azure.com). Zoek de resource die u wilt gebruiken en controleer de resource naam, resource groep en het abonnement.

## <a name="solution-2---set-dependencies"></a>Oplossing 2: afhankelijkheden instellen

Als deze fout optreedt bij het implementeren van een sjabloon, moet u mogelijk een afhankelijkheid toevoegen. Resource Manager optimaliseert de implementatie door zo mogelijk bronnen parallel te maken. Als u een resource moet implementeren na een andere resource, moet u het element **dependsOn** in uw sjabloon gebruiken. Als u bijvoorbeeld een web-app implementeert, moet het App Service plan bestaan. Als u nog niet hebt opgegeven dat de web-app afhankelijk is van het App Service-abonnement, maakt Resource Manager beide resources tegelijk. Er wordt een fout bericht weer gegeven dat de resource van het App Service plan niet kan worden gevonden, omdat deze nog niet bestaat bij het instellen van een eigenschap in de web-app. U kunt deze fout voor komen door de afhankelijkheid in de web-app in te stellen.

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

Maar u wilt voor komen dat afhankelijkheden worden ingesteld die niet nodig zijn. Wanneer u onnodige afhankelijkheden hebt, kunt u de duur van de implementatie verlengen door te voor komen dat bronnen die niet afhankelijk zijn van elkaar, parallel worden geïmplementeerd. Daarnaast kunt u circulaire afhankelijkheden maken die de implementatie blok keren. Met de functies [referentie](template-functions-resource.md#reference) functie en [lijst *](template-functions-resource.md#list) wordt een impliciete afhankelijkheid gemaakt voor de resource waarnaar wordt verwezen, wanneer die resource wordt geïmplementeerd in dezelfde sjabloon en wordt verwezen door de naam (niet resource-id). Daarom kunt u meer afhankelijkheden hebben dan de afhankelijkheden die zijn opgegeven in de eigenschap **dependsOn** . De functie [resourceId](template-functions-resource.md#resourceid) maakt geen impliciete afhankelijkheid of valideert dat de resource bestaat. De functies [referentie](template-functions-resource.md#reference) functie en [lijst *](template-functions-resource.md#list) maken geen impliciete afhankelijkheid als de resource-id naar de resource wordt verwezen. Als u een impliciete afhankelijkheid wilt maken, geeft u de naam van de resource die is geïmplementeerd in dezelfde sjabloon door.

Wanneer u afhankelijkheids problemen ziet, moet u inzicht krijgen in de volg orde van de resource-implementatie. De volg orde van de implementatie bewerkingen bekijken:

1. Selecteer de implementatie geschiedenis voor uw resource groep.

   ![implementatie geschiedenis selecteren](./media/error-not-found/select-deployment.png)

2. Selecteer een implementatie in de geschiedenis en selecteer **gebeurtenissen**.

   ![implementatie gebeurtenissen selecteren](./media/error-not-found/select-deployment-events.png)

3. Bekijk de volg orde van gebeurtenissen voor elke resource. Let op de status van elke bewerking. De volgende afbeelding toont bijvoorbeeld drie opslag accounts die parallel zijn geïmplementeerd. U ziet dat de drie opslag accounts op hetzelfde moment worden gestart.

   ![parallelle implementatie](./media/error-not-found/deployment-events-parallel.png)

   De volgende afbeelding toont drie opslag accounts die niet parallel worden geïmplementeerd. Het tweede opslag account is afhankelijk van het eerste opslag account en het derde opslag account is afhankelijk van het tweede opslag account. Het eerste opslag account is gestart, geaccepteerd en voltooid voordat de volgende wordt gestart.

   ![sequentiële implementatie](./media/error-not-found/deployment-events-sequence.png)

## <a name="solution-3---get-external-resource"></a>Oplossing 3-externe bron ophalen

Als u een sjabloon implementeert en u een bron wilt ophalen die in een ander abonnement of een andere resource groep bestaat, gebruikt u de [functie resourceId](template-functions-resource.md#resourceid). Deze functie wordt geretourneerd om de volledig gekwalificeerde naam van de resource op te halen.

De para meters voor het abonnement en de resource groep in de functie resourceId zijn optioneel. Als u deze niet opgeeft, worden deze standaard ingesteld op het huidige abonnement en de resource groep. Wanneer u met een resource in een andere resource groep of een ander abonnement werkt, moet u deze waarden opgeven.

In het volgende voor beeld wordt de resource-ID opgehaald voor een resource die bestaat in een andere resource groep.

```json
"properties": {
  "name": "[parameters('siteName')]",
  "serverFarmId": "[resourceId('plangroup', 'Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
}
```

## <a name="solution-4---get-managed-identity-from-resource"></a>Oplossing 4: beheerde identiteit ophalen van resource

Als u een resource implementeert die impliciet een [beheerde identiteit](../../active-directory/managed-identities-azure-resources/overview.md)maakt, moet u wachten tot de resource is geïmplementeerd voordat u waarden ophaalt voor de beheerde identiteit. Als u de naam van de beheerde identiteit doorgeeft aan de [referentie](template-functions-resource.md#reference) functie, probeert Resource Manager de referentie op te lossen voordat de resource en de identiteit worden geïmplementeerd. In plaats daarvan geeft u de naam op van de resource waarmee de identiteit wordt toegepast. Deze aanpak zorgt ervoor dat de resource en de beheerde identiteit worden geïmplementeerd voordat Resource Manager de referentie functie verhelpt.

Gebruik in de functie Reference `Full` om alle eigenschappen op te halen, inclusief de beheerde identiteit.

Het patroon is:

`"[reference(resourceId(<resource-provider-namespace>, <resource-name>), <API-version>, 'Full').Identity.propertyName]"`

> [!IMPORTANT]
> Gebruik niet het patroon:
>
> `"[reference(concat(resourceId(<resource-provider-namespace>, <resource-name>),'/providers/Microsoft.ManagedIdentity/Identities/default'),<API-version>).principalId]"`
>
> De sjabloon kan niet worden uitgevoerd.

Als u bijvoorbeeld de principal-ID wilt ophalen voor een beheerde identiteit die wordt toegepast op een virtuele machine, gebruikt u:

```json
"[reference(resourceId('Microsoft.Compute/virtualMachines', variables('vmName')),'2019-12-01', 'Full').identity.principalId]",
```

Als u de Tenant-ID wilt ophalen voor een beheerde identiteit die wordt toegepast op een schaalset voor virtuele machines, gebruikt u:

```json
"[reference(resourceId('Microsoft.Compute/virtualMachineScaleSets',  variables('vmNodeType0Name')), 2019-12-01, 'Full').Identity.tenantId]"
```

## <a name="solution-5---check-functions"></a>Oplossing 5-functies controleren

Bij het implementeren van een sjabloon zoekt u naar expressies die gebruikmaken van de functies [Reference](template-functions-resource.md#reference) of [listkeys ophalen](template-functions-resource.md#listkeys) . De waarden die u opgeeft, variëren op basis van het feit of de resource zich in dezelfde sjabloon, resource groep en abonnement bevindt. Controleer of u de vereiste parameter waarden opgeeft voor uw scenario. Als de resource zich in een andere resource groep bevindt, geeft u de volledige Resource-ID op. Als u bijvoorbeeld wilt verwijzen naar een opslag account in een andere resource groep, gebruikt u:

```json
"[reference(resourceId('exampleResourceGroup', 'Microsoft.Storage/storageAccounts', 'myStorage'), '2017-06-01')]"
```

## <a name="solution-6---after-deleting-resource"></a>Oplossing 6-na het verwijderen van de resource

Wanneer u een resource verwijdert, kan het korte tijd duren wanneer de resource nog steeds wordt weer gegeven in de portal, maar niet daad werkelijk beschikbaar is. Als u de resource selecteert, krijgt u een fout melding waarin wordt aangegeven dat de resource niet is gevonden. Vernieuw de portal om de meest recente weer gave te krijgen.

[Neem contact op met de ondersteuning](https://azure.microsoft.com/support/options/)als het probleem zich blijft voordoen na een korte wacht tijd.
