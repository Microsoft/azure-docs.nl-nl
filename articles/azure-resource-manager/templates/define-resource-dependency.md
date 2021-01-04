---
title: Implementatie volgorde voor resources instellen
description: Hierin wordt beschreven hoe u een resource instelt als afhankelijk van een andere resource tijdens de implementatie. De afhankelijkheden zorgen ervoor dat resources in de juiste volg orde worden geïmplementeerd.
ms.topic: conceptual
ms.date: 12/21/2020
ms.openlocfilehash: a96dca0ab30d0baee2688427d78867ea128e673a
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/22/2020
ms.locfileid: "97722008"
---
# <a name="define-the-order-for-deploying-resources-in-arm-templates"></a>Definieer de volg orde voor het implementeren van resources in ARM-sjablonen

Bij het implementeren van resources moet u er mogelijk voor zorgen dat er resources bestaan voor andere resources. U hebt bijvoorbeeld een logische SQL-Server nodig voordat u een Data Base implementeert. U kunt deze relatie tot stand brengen door één resource als afhankelijk van de andere bron te markeren. Gebruik het element **dependsOn** om een expliciete afhankelijkheid te definiëren. Gebruik de functies **verwijzing** of **lijst** om een impliciete afhankelijkheid te definiëren.

Resource Manager evalueert de afhankelijkheden tussen resources en implementeert ze in de volgorde van afhankelijkheid. Als resources niet van elkaar afhankelijk zijn, worden deze door Resource Manager parallel geïmplementeerd. U hoeft alleen afhankelijkheden te definiëren voor resources die in dezelfde sjabloon zijn geïmplementeerd.

## <a name="dependson"></a>dependsOn

Binnen uw sjabloon kunt u met het dependsOn-element één resource definiëren als afhankelijk van een of meer resources. De waarde is een JSON-matrix met teken reeksen, die elk een resource naam of-ID zijn. De matrix kan resources bevatten die [voorwaardelijk worden geïmplementeerd](conditional-resource-deployment.md). Wanneer een voorwaardelijke resource niet is geïmplementeerd, wordt deze automatisch door Azure Resource Manager verwijderd uit de vereiste afhankelijkheden.

In het volgende voor beeld ziet u een netwerk interface die afhankelijk is van een virtueel netwerk, een netwerk beveiligings groep en een openbaar IP-adres. Voor de volledige sjabloon raadpleegt u [de Quick Start-sjabloon voor een virtuele Linux-machine](https://github.com/Azure/azure-quickstart-templates/blob/master/101-vm-simple-linux/azuredeploy.json).

```json
{
    "type": "Microsoft.Network/networkInterfaces",
    "apiVersion": "2020-06-01",
    "name": "[variables('networkInterfaceName')]",
    "location": "[parameters('location')]",
    "dependsOn": [
      "[resourceId('Microsoft.Network/networkSecurityGroups/', parameters('networkSecurityGroupName'))]",
      "[resourceId('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]",
      "[resourceId('Microsoft.Network/publicIpAddresses/', variables('publicIpAddressName'))]"
    ],
    ...
}
```

Terwijl u mogelijk bent gedependsOnd om relaties tussen uw resources toe te wijzen, is het belang rijk om te begrijpen waarom u dit doet. Als u bijvoorbeeld wilt documenteren hoe resources zijn gekoppeld, is dependsOn niet de juiste benadering. U kunt geen query uitvoeren op de resources die na de implementatie zijn gedefinieerd in het dependsOn-element. Het instellen van onnodige afhankelijkheden vertraagt de implementatie tijd omdat Resource Manager die bronnen niet parallel kan implementeren.

## <a name="child-resources"></a>Onderliggende resources

Een impliciete implementatie afhankelijkheid wordt niet automatisch gemaakt tussen een [onderliggende bron](child-resource-name-type.md) en de bovenliggende resource. Als u de onderliggende resource moet implementeren na de bovenliggende resource, stelt u de eigenschap dependsOn in.

In het volgende voor beeld ziet u een logische SQL-Server en-data base. U ziet dat er een expliciete afhankelijkheid is gedefinieerd tussen de data base en de server, zelfs als de Data Base een onderliggend item van de server is.

```json
"resources": [
  {
    "type": "Microsoft.Sql/servers",
    "apiVersion": "2020-02-02-preview",
    "name": "[parameters('serverName')]",
    "location": "[parameters('location')]",
    "properties": {
      "administratorLogin": "[parameters('administratorLogin')]",
      "administratorLoginPassword": "[parameters('administratorLoginPassword')]"
    },
    "resources": [
      {
        "type": "databases",
        "apiVersion": "2020-08-01-preview",
        "name": "[parameters('sqlDBName')]",
        "location": "[parameters('location')]",
        "sku": {
          "name": "Standard",
          "tier": "Standard"
          },
        "dependsOn": [
          "[resourceId('Microsoft.Sql/servers', concat(parameters('serverName')))]"
        ]
      }
    ]
  }
]
```

Voor de volledige sjabloon raadpleegt u [Quick start Temp late for Azure SQL database](https://github.com/Azure/azure-quickstart-templates/blob/master/101-sql-database/azuredeploy.json).

## <a name="reference-and-list-functions"></a>Naslag informatie en functies

Met de [functie referentie](template-functions-resource.md#reference) kan een expressie de waarde ervan afleiden uit andere JSON-naam-en-waardeparen of runtime-resources. De [lijst * functies](template-functions-resource.md#list) retour neren waarden voor een resource vanuit een lijst bewerking.

Verwijzings-en lijst expressies declareert impliciet dat de ene resource afhankelijk is van een andere. Als dat mogelijk is, gebruikt u een impliciete verwijzing om te voor komen dat er overbodige afhankelijkheden worden toegevoegd.

Als u een impliciete afhankelijkheid wilt afdwingen, verwijst u naar de resource op naam, niet op Resource-ID. Als u de resource-ID doorgeeft in de functies verwijzing of lijst, wordt er geen impliciete verwijzing gemaakt.

De algemene indeling van de referentie functie is:

```json
reference('resourceName').propertyPath
```

De algemene indeling van de functie Listkeys ophalen is:

```json
listKeys('resourceName', 'yyyy-mm-dd')
```

In het volgende voor beeld is een CDN-eind punt expliciet afhankelijk van het CDN-profiel en is de impliciete afhankelijk van een web-app.

```json
{
    "name": "[variables('endpointName')]",
    "apiVersion": "2016-04-02",
    "type": "endpoints",
    "location": "[resourceGroup().location]",
    "dependsOn": [
      "[variables('profileName')]"
    ],
    "properties": {
      "originHostHeader": "[reference(variables('webAppName')).hostNames[0]]",
      ...
    }
```

Zie [naslag functie](template-functions-resource.md#reference)voor meer informatie.

## <a name="depend-on-resources-in-a-loop"></a>Is afhankelijk van resources in een lus

Als u resources wilt implementeren die afhankelijk zijn van resources in een [copy-lus](copy-resources.md), hebt u twee opties. U kunt een afhankelijkheid instellen voor afzonderlijke resources in de lus of op de hele lus.

> [!NOTE]
> Voor de meeste scenario's moet u de afhankelijkheid instellen voor afzonderlijke resources binnen de copy-lus. Het is alleen afhankelijk van de gehele lus wanneer alle resources in de lus bestaan voordat de volgende resource wordt gemaakt. Als u de afhankelijkheid op de hele lus instelt, wordt de grafiek afhankelijkheden aanzienlijk uitgebreid, vooral als deze lussen bronnen afhankelijk zijn van andere resources. De uitgevouwen afhankelijkheden maken het moeilijk om de implementatie efficiënt uit te voeren.

In het volgende voor beeld ziet u hoe u meerdere virtuele machines implementeert. Met de sjabloon maakt u hetzelfde aantal netwerk interfaces. Elke virtuele machine is afhankelijk van één netwerk interface, in plaats van de hele lus.

```json
{
  "type": "Microsoft.Network/networkInterfaces",
  "apiVersion": "2020-05-01",
  "name": "[concat(variables('nicPrefix'),'-',copyIndex())]",
  "location": "[parameters('location')]",
  "copy": {
    "name": "nicCopy",
    "count": "[parameters('vmCount')]"
  },
  ...
},
{
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2020-06-01",
  "name": "[concat(variables('vmPrefix'),copyIndex())]",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[resourceId('Microsoft.Network/networkInterfaces',concat(variables('nicPrefix'),'-',copyIndex()))]"
  ],
  "copy": {
    "name": "vmCopy",
    "count": "[parameters('vmCount')]"
  },
  "properties": {
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "[resourceId('Microsoft.Network/networkInterfaces',concat(variables('nicPrefix'),'-',copyIndex()))]",
          "properties": {
            "primary": "true"
          }
        }
      ]
    },
    ...
  }
}
```

In het volgende voor beeld ziet u hoe u drie opslag accounts implementeert voordat u de virtuele machine implementeert. U ziet dat de naam van het element Copy is ingesteld op `storagecopy` en dat het element dependsOn voor de virtuele machine ook is ingesteld op `storagecopy` .

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "copy": {
        "name": "storagecopy",
        "count": 3
      },
      "properties": {}
    },
    {
      "type": "Microsoft.Compute/virtualMachines",
      "apiVersion": "2015-06-15",
      "name": "[concat('VM', uniqueString(resourceGroup().id))]",
      "dependsOn": ["storagecopy"],
      ...
    }
  ],
  "outputs": {}
}
```

## <a name="circular-dependencies"></a>Circulaire afhankelijkheden

Resource Manager identificeert circulaire afhankelijkheden tijdens de validatie van de sjabloon. Als u een fout melding voor een circulaire afhankelijkheid krijgt, evalueert u de sjabloon om te zien of er afhankelijkheden kunnen worden verwijderd. Als het verwijderen van afhankelijkheden niet werkt, kunt u kring afhankelijkheden vermijden door enkele implementatie bewerkingen naar onderliggende resources te verplaatsen. Implementeer de onderliggende resources na de resources die de circulaire afhankelijkheid hebben. Stel bijvoorbeeld dat u twee virtuele machines implementeert, maar u moet eigenschappen instellen voor elke machine die naar de andere verwijzen. U kunt deze in de volgende volg orde implementeren:

1. vm1
2. vm2
3. De uitbrei ding van VM1 is afhankelijk van VM1 en VM2. De uitbrei ding stelt waarden in voor VM1 die worden opgehaald uit VM2.
4. De uitbrei ding van VM2 is afhankelijk van VM1 en VM2. De uitbrei ding stelt waarden in voor VM2 die worden opgehaald uit VM1.

Zie [problemen met algemene Azure-implementaties met Azure Resource Manager oplossen](common-deployment-errors.md)voor meer informatie over het beoordelen van de implementatie volgorde en het oplossen van afhankelijkheids fouten.

## <a name="next-steps"></a>Volgende stappen

* Zie [zelf studie: Azure Resource Manager sjablonen met afhankelijke resources maken](template-tutorial-create-templates-with-dependent-resources.md)om een zelf studie te door lopen.
* Zie [complexe Cloud implementaties beheren met behulp van geavanceerde arm-sjabloon functies](/learn/modules/manage-deployments-advanced-arm-template-features/)voor een Microsoft Learn module die bron afhankelijkheden omvat.
* Zie [Azure Resource Manager aanbevolen procedures voor sjablonen](template-best-practices.md)voor aanbevelingen bij het instellen van afhankelijkheden.
* Zie [problemen met veelvoorkomende Azure-implementatie fouten oplossen met Azure Resource Manager](common-deployment-errors.md)voor meer informatie over het oplossen van problemen met afhankelijkheden tijdens de implementatie.
* Zie [ontwerp sjablonen](template-syntax.md)voor meer informatie over het maken van Azure Resource Manager sjablonen.
* Zie [sjabloon functies](template-functions.md)voor een lijst met de beschik bare functies in een sjabloon.

