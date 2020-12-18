---
title: Implementatie volgorde voor resources instellen
description: Hierin wordt beschreven hoe u een resource instelt als afhankelijk van een andere resource tijdens de implementatie om ervoor te zorgen dat de resources in de juiste volg orde worden geïmplementeerd.
ms.topic: conceptual
ms.date: 12/17/2020
ms.openlocfilehash: 933764f1930bd6c9e21d4ccffbde1bb93bbc9613
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/18/2020
ms.locfileid: "97672811"
---
# <a name="define-the-order-for-deploying-resources-in-arm-templates"></a>Definieer de volg orde voor het implementeren van resources in ARM-sjablonen

Wanneer u een resource implementeert, moet u er mogelijk voor zorgen dat er andere resources bestaan voordat deze wordt geïmplementeerd. U hebt bijvoorbeeld een logische SQL-Server nodig voordat u een Data Base implementeert. U definieert deze relatie door één resource als afhankelijk van de andere bron te markeren. U definieert een afhankelijkheid met het **dependsOn** -element of met behulp van de functie **Reference** .

Resource Manager evalueert de afhankelijkheden tussen resources en implementeert ze in de volgorde van afhankelijkheid. Als resources niet van elkaar afhankelijk zijn, worden deze door Resource Manager parallel geïmplementeerd. U hoeft alleen afhankelijkheden te definiëren voor resources die in dezelfde sjabloon zijn geïmplementeerd.

## <a name="dependson"></a>dependsOn

Binnen uw sjabloon kunt u met het dependsOn-element één resource definiëren als afhankelijk van een of meer resources. De waarde is een JSON-matrix met teken reeksen, die elk een resource naam zijn. De matrix kan resources bevatten die [voorwaardelijk worden geïmplementeerd](conditional-resource-deployment.md). Wanneer een voorwaardelijke resource niet is geïmplementeerd, wordt deze automatisch door Azure Resource Manager verwijderd uit de vereiste afhankelijkheden.

In het volgende voor beeld ziet u een schaalset voor virtuele machines die afhankelijk is van een load balancer, een virtueel netwerk en een lus waarmee meerdere opslag accounts worden gemaakt. Deze andere resources worden niet weer gegeven in het volgende voor beeld, maar ze moeten ergens anders in de sjabloon aanwezig zijn.

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "apiVersion": "2016-03-30",
  "name": "[variables('namingInfix')]",
  "location": "[variables('location')]",
  "tags": {
    "displayName": "VMScaleSet"
  },
  "dependsOn": [
    "[variables('loadBalancerName')]",
    "[variables('virtualNetworkName')]",
    "storageLoop",
  ],
  ...
}
```

In het vorige voor beeld is een afhankelijkheid opgenomen in de resources die worden gemaakt via een Kopieer-lus met de naam **storageLoop**. Zie [meerdere exemplaren van resources maken in azure Resource Manager](copy-resources.md)voor een voor beeld.

Bij het definiëren van afhankelijkheden kunt u de naam ruimte van de resource provider en het bron type toevoegen om ambiguïteit te voor komen. Als u bijvoorbeeld een load balancer en een virtueel netwerk wilt verduidelijken dat dezelfde namen kan hebben als andere resources, gebruikt u de volgende indeling:

```json
"dependsOn": [
  "[resourceId('Microsoft.Network/loadBalancers', variables('loadBalancerName'))]",
  "[resourceId('Microsoft.Network/virtualNetworks', variables('virtualNetworkName'))]"
]
```

Terwijl u mogelijk bent gedependsOnd om relaties tussen uw resources toe te wijzen, is het belang rijk om te begrijpen waarom u dit doet. Als u bijvoorbeeld wilt documenteren hoe resources zijn gekoppeld, is dependsOn niet de juiste benadering. U kunt geen query uitvoeren op de resources die na de implementatie zijn gedefinieerd in het dependsOn-element. Door dependsOn te gebruiken, is het mogelijk van invloed op de implementatie tijd, omdat Resource Manager niet in twee parallelle bronnen met een afhankelijkheid wordt geïmplementeerd.

## <a name="child-resources"></a>Onderliggende resources

Met de eigenschap resources kunt u onderliggende resources opgeven die zijn gerelateerd aan de resource die wordt gedefinieerd. Onderliggende resources kunnen alleen vijf niveaus diep worden gedefinieerd. Het is belang rijk te weten dat er geen impliciete implementatie afhankelijkheid tussen een onderliggende bron en de bovenliggende resource is gemaakt. Als u wilt dat de onderliggende resource wordt geïmplementeerd na de bovenliggende resource, moet u de afhankelijkheid expliciet aangeven met de eigenschap dependsOn.

Elke bovenliggende resource accepteert alleen bepaalde resource typen als onderliggende resources. De geaccepteerde resource typen worden opgegeven in het [sjabloon schema](https://github.com/Azure/azure-resource-manager-schemas) van de bovenliggende resource. De naam van het onderliggende bron type bevat de naam van het bovenliggende bron type, zoals **micro soft. web/sites/config** en **micro soft. web/sites/extensies** , zijn zowel onderliggende resources van **micro soft. web/sites**.

In het volgende voor beeld ziet u een logische SQL-Server en-data base. U ziet dat er een expliciete afhankelijkheid is gedefinieerd tussen de data base en de server, zelfs als de Data Base een onderliggend item van de server is.

```json
"resources": [
  {
    "name": "[variables('sqlserverName')]",
    "apiVersion": "2014-04-01-preview",
    "type": "Microsoft.Sql/servers",
    "location": "[resourceGroup().location]",
    "tags": {
      "displayName": "SqlServer"
    },
    "properties": {
      "administratorLogin": "[parameters('administratorLogin')]",
      "administratorLoginPassword": "[parameters('administratorLoginPassword')]"
    },
    "resources": [
      {
        "name": "[parameters('databaseName')]",
        "apiVersion": "2014-04-01-preview",
        "type": "databases",
        "location": "[resourceGroup().location]",
        "dependsOn": [
          "[variables('sqlserverName')]"
        ],
        "tags": {
          "displayName": "Database"
        },
        "properties": {
          "edition": "[parameters('edition')]",
          "collation": "[parameters('collation')]",
          "maxSizeBytes": "[parameters('maxSizeBytes')]",
          "requestedServiceObjectiveName": "[parameters('requestedServiceObjectiveName')]"
        }
      }
    ]
  }
]
```

## <a name="reference-and-list-functions"></a>Naslag informatie en functies

Met de [functie referentie](template-functions-resource.md#reference) kan een expressie de waarde ervan afleiden uit andere JSON-naam-en-waardeparen of runtime-resources. De [lijst * functies](template-functions-resource.md#list) retour neren waarden voor een resource vanuit een lijst bewerking.  Verwijzings-en lijst expressies declareren dat de ene resource afhankelijk is van een andere, wanneer de bron waarnaar wordt verwezen, wordt geïmplementeerd in dezelfde sjabloon en waarnaar wordt verwezen met de naam (geen Resource-ID). Als u de resource-ID doorgeeft in de functies verwijzing of lijst, wordt er geen impliciete verwijzing gemaakt.

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

U kunt dit element of het dependsOn-element gebruiken om afhankelijkheden op te geven, maar u hoeft niet beide voor dezelfde afhankelijke resource te gebruiken. Als dat mogelijk is, gebruikt u een impliciete verwijzing om te voor komen dat er overbodige afhankelijkheden worden toegevoegd.

Zie [naslag functie](template-functions-resource.md#reference)voor meer informatie.

## <a name="circular-dependencies"></a>Circulaire afhankelijkheden

Resource Manager identificeert circulaire afhankelijkheden tijdens de validatie van de sjabloon. Als er een fout bericht wordt weer gegeven dat er een circulaire afhankelijkheid bestaat, evalueert u uw sjabloon om te zien of er afhankelijkheden niet nodig zijn en kunnen ze worden verwijderd. Als het verwijderen van afhankelijkheden niet werkt, kunt u kring afhankelijkheden vermijden door enkele implementatie bewerkingen te verplaatsen naar onderliggende resources die worden geïmplementeerd na de bronnen die de circulaire afhankelijkheid hebben. Stel bijvoorbeeld dat u twee virtuele machines implementeert, maar u moet eigenschappen instellen voor elke machine die naar de andere verwijzen. U kunt deze in de volgende volg orde implementeren:

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

