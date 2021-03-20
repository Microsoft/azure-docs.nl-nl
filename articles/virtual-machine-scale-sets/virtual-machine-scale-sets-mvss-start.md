---
title: Meer informatie over sjablonen voor virtuele-machine schaal sets
description: Meer informatie over hoe u een basisschaalset-sjabloon maakt voor schaal sets van virtuele Azure-machines via een aantal eenvoudige stappen.
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: template
ms.date: 04/26/2019
ms.reviewer: jushiman
ms.custom: mimckitt
ms.openlocfilehash: 55c826b4baf38732684aaa0465aeaab6a45564db
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87831495"
---
# <a name="learn-about-virtual-machine-scale-set-templates"></a>Meer informatie over sjablonen voor virtuele-machine schaal sets
[Azure Resource Manager-sjablonen](../azure-resource-manager/templates/overview.md#template-deployment-process) zijn bijzonder handig om groepen gerelateerde resources te implementeren. In deze reeks zelf studies ziet u hoe u een basis sjabloon voor een schaalset maakt en hoe u deze sjabloon wijzigt in verschillende scenario's. Alle voor beelden zijn afkomstig uit deze [github-opslag plaats](https://github.com/gatneil/mvss).

Deze sjabloon is zo ontworpen dat deze eenvoudig is. Zie de [github-opslag plaats voor Azure Quick](https://github.com/Azure/azure-quickstart-templates) start-sjablonen en zoek naar mappen die de teken reeks bevatten voor meer gedetailleerde voor beelden van schaal sets sjablonen `vmss` .

Als u al bekend bent met het maken van sjablonen, kunt u door gaan naar de sectie volgende stappen om te zien hoe u deze sjabloon wijzigt.

## <a name="define-schema-and-contentversion"></a>$schema en contentVersion definiëren
Eerst definieert u `$schema` en `contentVersion` in de sjabloon. Het `$schema` element definieert de versie van de sjabloon taal en wordt gebruikt voor het markeren van de syntaxis van Visual Studio en vergelijk bare validatie functies. Het `contentVersion` element wordt niet gebruikt door Azure. In plaats daarvan kunt u de sjabloon versie volgen.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
```

## <a name="define-parameters"></a>Parameters definiëren
Vervolgens definieert u twee para meters `adminUsername` en `adminPassword` . Para meters zijn waarden die u opgeeft op het moment van de implementatie. De `adminUsername` para meter is gewoon een `string` type, maar omdat `adminPassword` het een geheim is, geeft u het type op `securestring` . Later worden deze para meters door gegeven aan de configuratie van de schaalset.

```json
  "parameters": {
    "adminUsername": {
      "type": "string"
    },
    "adminPassword": {
      "type": "securestring"
    }
  },
```
## <a name="define-variables"></a>Variabelen definiëren
Met Resource Manager-sjablonen kunt u ook variabelen definiëren die later in de sjabloon moeten worden gebruikt. In het voor beeld worden geen variabelen gebruikt, waardoor het JSON-object leeg is.

```json
  "variables": {},
```

## <a name="define-resources"></a>Resources definiëren
Vervolgens vindt u de sectie resources van de sjabloon. Hier kunt u opgeven wat u daad werkelijk wilt implementeren. In tegens telling tot `parameters` en `variables` (wat JSON-objecten zijn), `resources` is een JSON-lijst met JSON-objecten.

```json
   "resources": [
```

Alle resources vereisen `type` , `name` , en `apiVersion` `location` Eigenschappen. De eerste resource van dit voor beeld is van het type [micro soft. Network/virtualNetwork](/azure/templates/microsoft.network/virtualnetworks), name `myVnet` en apiVersion `2018-11-01` . (Als u de nieuwste API-versie voor een resource type wilt vinden, raadpleegt u de [Azure Resource Manager-sjabloon verwijzing](/azure/templates/).)

```json
     {
       "type": "Microsoft.Network/virtualNetworks",
       "name": "myVnet",
       "apiVersion": "2018-11-01",
```

## <a name="specify-location"></a>Locatie opgeven
Als u de locatie voor het virtuele netwerk wilt opgeven, gebruikt u een [Resource Manager-sjabloon functie](../azure-resource-manager/templates/template-functions.md). Deze functie moet tussen aanhalings tekens en vier Kante haakjes worden geplaatst, bijvoorbeeld: `"[<template-function>]"` . In dit geval gebruikt u de `resourceGroup` functie. Er worden geen argumenten gebruikt en er wordt een JSON-object geretourneerd met meta gegevens over de resource groep waarop deze implementatie wordt geïmplementeerd. De resource groep wordt ingesteld door de gebruiker op het moment van de implementatie. Deze waarde wordt vervolgens in dit JSON-object geïndexeerd met `.location` om de locatie van het JSON-object op te halen.

```json
       "location": "[resourceGroup().location]",
```

## <a name="specify-virtual-network-properties"></a>Eigenschappen van virtueel netwerk opgeven
Elke resource manager-resource heeft een eigen `properties` sectie voor configuraties die specifiek zijn voor de resource. In dit geval geeft u op dat het virtuele netwerk één subnet moet hebben met het privé-IP-adres bereik `10.0.0.0/16` . Een schaalset bevindt zich altijd in één subnet. Er kunnen geen subnetten worden gespannen.

```json
       "properties": {
         "addressSpace": {
           "addressPrefixes": [
             "10.0.0.0/16"
           ]
         },
         "subnets": [
           {
             "name": "mySubnet",
             "properties": {
               "addressPrefix": "10.0.0.0/16"
             }
           }
         ]
       }
     },
```

## <a name="add-dependson-list"></a>Lijst met dependsOn toevoegen
Naast de vereiste `type` Eigenschappen,, `name` `apiVersion` en, `location` kan elke resource een optionele `dependsOn` lijst met teken reeksen bevatten. In deze lijst wordt aangegeven welke andere resources van deze implementatie moeten worden voltooid voordat deze resource wordt geïmplementeerd.

In dit geval is er slechts één element in de lijst, het virtuele netwerk van het vorige voor beeld. U geeft deze afhankelijkheid op omdat voor de schaalset het netwerk moet bestaan voordat er virtuele machines worden gemaakt. Op deze manier kan de schaalset deze privé-IP-adressen van Vm's geven van het IP-adres bereik dat eerder is opgegeven in de netwerk eigenschappen. De indeling van elke teken reeks in de dependsOn-lijst is `<type>/<name>` . Gebruik hetzelfde `type` en `name` eerder in de resource definitie voor het virtuele netwerk.

```json
     {
       "type": "Microsoft.Compute/virtualMachineScaleSets",
       "name": "myScaleSet",
       "apiVersion": "2019-03-01",
       "location": "[resourceGroup().location]",
       "dependsOn": [
         "Microsoft.Network/virtualNetworks/myVnet"
       ],
```
## <a name="specify-scale-set-properties"></a>Eigenschappen van schaal sets opgeven
Schaal sets hebben veel eigenschappen voor het aanpassen van de virtuele machines in de schaalset. Zie de [sjabloon verwijzing](/azure/templates/microsoft.compute/virtualmachinescalesets)voor een volledige lijst met deze eigenschappen. Voor deze zelf studie zijn slechts enkele veelgebruikte eigenschappen ingesteld.
### <a name="supply-vm-size-and-capacity"></a>Grootte en capaciteit van de VM opgeven
De schaalset moet weten welke grootte van de virtuele machine moet worden gemaakt ("SKU-naam") en hoeveel virtuele machines er moeten worden gemaakt ("SKU-capaciteit"). Zie de [documentatie over VM-grootten](../virtual-machines/sizes.md)om te zien welke VM-grootten er beschikbaar zijn.

```json
       "sku": {
         "name": "Standard_A1",
         "capacity": 2
       },
```

### <a name="choose-type-of-updates"></a>Type updates kiezen
De schaalset moet ook weten hoe updates moeten worden verwerkt in de schaalset. Er zijn momenteel drie opties, `Manual` `Rolling` en `Automatic` . Zie de documentatie over het [upgraden van een schaalset](./virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model)voor meer informatie over de verschillen tussen de twee.

```json
       "properties": {
         "upgradePolicy": {
           "mode": "Manual"
         },
```

### <a name="choose-vm-operating-system"></a>VM-besturings systeem kiezen
De schaalset moet weten welk besturings systeem op de Vm's moet worden geplaatst. Maak hier de Vm's met een volledig patched Ubuntu 16,04-LTS-installatie kopie.

```json
         "virtualMachineProfile": {
           "storageProfile": {
             "imageReference": {
               "publisher": "Canonical",
               "offer": "UbuntuServer",
               "sku": "16.04-LTS",
               "version": "latest"
             }
           },
```

### <a name="specify-computernameprefix"></a>ComputerNamePrefix opgeven
De schaalset implementeert meerdere Vm's. Geef in plaats van elke VM-naam op te geven `computerNamePrefix` . De schaalset voegt een index toe aan het voor voegsel voor elke virtuele machine, zodat VM-namen het formulier hebben `<computerNamePrefix>_<auto-generated-index>` .

In het volgende code fragment gebruikt u de para meters van voordat u de gebruikers naam en het wacht woord voor de beheerder instelt voor alle virtuele machines in de schaalset. Dit proces maakt gebruik van de `parameters` sjabloon functie. Deze functie gebruikt een teken reeks die opgeeft naar welke para meter moet worden verwezen en die de waarde voor die para meter uitvoert.

```json
           "osProfile": {
             "computerNamePrefix": "vm",
             "adminUsername": "[parameters('adminUsername')]",
             "adminPassword": "[parameters('adminPassword')]"
           },
```

### <a name="specify-vm-network-configuration"></a>VM-netwerk configuratie opgeven
Specificeer ten slotte de netwerk configuratie voor de virtuele machines in de schaalset. In dit geval hoeft u alleen de ID op te geven van het subnet dat u eerder hebt gemaakt. Hiermee wordt de schaal ingesteld waarmee de netwerk interfaces in dit subnet worden geplaatst.

U kunt de ID van het virtuele netwerk met het subnet ophalen met behulp van de `resourceId` sjabloon functie. Deze functie neemt het type en de naam van een resource en retourneert de volledig gekwalificeerde id van die resource. Deze ID heeft de volgende notatie: `/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/<resourceProviderNamespace>/<resourceType>/<resourceName>`

De id van het virtuele netwerk is echter niet voldoende. Geef het specifieke subnet op waar de virtuele machines voor de schaalset zich bevinden. U kunt dit doen door samen `/subnets/mySubnet` te voegen met de id van het virtuele netwerk. Het resultaat is de volledig gekwalificeerde ID van het subnet. Doe dit samen met de `concat` functie, die in een reeks teken reeksen neemt en die de samen voeging retourneert.

```json
           "networkProfile": {
             "networkInterfaceConfigurations": [
               {
                 "name": "myNic",
                 "properties": {
                   "primary": "true",
                   "ipConfigurations": [
                     {
                       "name": "myIpConfig",
                       "properties": {
                         "subnet": {
                           "id": "[concat(resourceId('Microsoft.Network/virtualNetworks', 'myVnet'), '/subnets/mySubnet')]"
                         }
                       }
                     }
                   ]
                 }
               }
             ]
           }
         }
       }
     }
   ]
}

```

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]