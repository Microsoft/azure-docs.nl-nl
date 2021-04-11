---
title: CreateUiDefinition.jsin het deel venster bestand voor portal
description: Hierin wordt beschreven hoe u gebruikers interface definities maakt voor de Azure Portal. Wordt gebruikt bij het definiëren van Azure Managed Applications.
author: tfitzmac
ms.topic: conceptual
ms.date: 03/26/2021
ms.author: tomfitz
ms.openlocfilehash: 586237c6dd909312780163cf316220d2f3fddd8c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105641651"
---
# <a name="createuidefinitionjson-for-azure-managed-applications-create-experience"></a>CreateUiDefinition. json voor het maken van beheerde Azure-toepassingen

In dit document worden de belangrijkste concepten van de **createUiDefinition.jsin** het bestand geïntroduceerd. Het Azure Portal gebruikt dit bestand om de gebruikers interface te definiëren bij het maken van een beheerde toepassing.

De sjabloon is als volgt

```json
{
    "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
    "handler": "Microsoft.Azure.CreateUIDef",
    "version": "0.1.2-preview",
    "parameters": {
        "config": {
            "isWizard": false,
            "basics": { }
        },
        "basics": [ ],
        "steps": [ ],
        "outputs": { },
        "resourceTypes": [ ]
    }
}
```

A `CreateUiDefinition` bevat altijd drie eigenschappen:

* afhandelingsprocedure
* versie
* parameters

De handler moet altijd zijn `Microsoft.Azure.CreateUIDef` en de meest recente ondersteunde versie is `0.1.2-preview` .

Het schema van de eigenschap para meters is afhankelijk van de combi natie van de opgegeven handler en versie. Voor beheerde toepassingen zijn de ondersteunde eigenschappen `config` ,, `basics` `steps` en `outputs` . U gebruikt `config` deze alleen wanneer u het standaard gedrag van de stap wilt negeren `basics` . De eigenschappen van de basis beginselen en de stappen bevatten de [elementen](create-uidefinition-elements.md) , zoals tekst vakken en vervolg keuzelijsten, die moeten worden weer gegeven in de Azure Portal. De eigenschap outputs wordt gebruikt om de uitvoer waarden van de opgegeven elementen toe te wijzen aan de para meters van de Azure Resource Manager sjabloon.

Inclusief `$schema` wordt aanbevolen, maar is optioneel. Indien opgegeven, moet de waarde voor `version` overeenkomen met de versie in de `$schema` URI.

U kunt een JSON-editor gebruiken om uw createUiDefinition te maken en deze vervolgens te testen in de [createUiDefinition-sandbox](https://portal.azure.com/?feature.customPortal=false&#blade/Microsoft_Azure_CreateUIDef/SandboxBlade) om deze te bekijken. Zie [uw portal-interface testen voor Azure Managed Applications](test-createuidefinition.md)voor meer informatie over de sandbox.

## <a name="config"></a>Configureren

De `config` eigenschap is optioneel. Gebruik deze optie om het standaard gedrag van de stap basis beginselen te onderdrukken of om uw interface in te stellen als stapsgewijze wizard. Als `config` wordt gebruikt, is dit de eerste eigenschap in de sectie **createUiDefinition.jsin** het bestand `parameters` . In het volgende voor beeld ziet u de beschik bare eigenschappen.

```json
"config": {
    "isWizard": false,
    "basics": {
        "description": "Customized description with **markdown**, see [more](https://www.microsoft.com).",
        "subscription": {
            "constraints": {
                "validations": [
                    {
                        "isValid": "[not(contains(subscription().displayName, 'Test'))]",
                        "message": "Can't use test subscription."
                    },
                    {
                        "permission": "Microsoft.Compute/virtualmachines/write",
                        "message": "Must have write permission for the virtual machine."
                    },
                    {
                        "permission": "Microsoft.Compute/virtualMachines/extensions/write",
                        "message": "Must have write permission for the extension."
                    }
                ]
            },
            "resourceProviders": [
                "Microsoft.Compute"
            ]
        },
        "resourceGroup": {
            "constraints": {
                "validations": [
                    {
                        "isValid": "[not(contains(resourceGroup().name, 'test'))]",
                        "message": "Resource group name can't contain 'test'."
                    }
                ]
            },
            "allowExisting": true
        },
        "location": {
            "label": "Custom label for location",
            "toolTip": "provide a useful tooltip",
            "resourceTypes": [
                "Microsoft.Compute/virtualMachines"
            ],
            "allowedValues": [
                "eastus",
                "westus2"
            ],
            "visible": true
        }
    }
},
```

Voor de `isValid` eigenschap schrijft u een expressie die wordt omgezet in waar of onwaar. Geef voor de `permission` eigenschap een van de acties van de [resource provider](../../role-based-access-control/resource-provider-operations.md)op.

### <a name="wizard"></a>Wizard

Met de `isWizard` eigenschap kunt u een geslaagde validatie van elke stap vereisen voordat u verdergaat met de volgende stap. Als de `isWizard` eigenschap niet is opgegeven, is de standaard waarde **False** en is de stap-voor-stap validatie niet vereist.

Als `isWizard` is ingeschakeld, ingesteld op **True**, het tabblad **basissen** is beschikbaar en alle andere tabbladen zijn uitgeschakeld. Wanneer de knop **volgende** is geselecteerd, wordt het pictogram van het tabblad aangegeven als de validatie van een tabblad is geslaagd of mislukt. Nadat de vereiste velden van een tabblad zijn voltooid en gevalideerd, kunt u met de knop **volgende** naar het volgende tabblad navigeren. Wanneer alle tabbladen validatie geven, gaat u naar de pagina **controleren en maken** en selecteert u de knop **maken** om de implementatie te starten.

:::image type="content" source="./media/create-uidefinition-overview/tab-wizard.png" alt-text="Wizard tabblad":::

### <a name="override-basics"></a>Basis principes negeren

Met de basis principes van configuratie kunt u de stappen in de basis beginselen aanpassen.

`description`Geef voor een teken reeks met kortings functionaliteit op waarmee uw resource wordt beschreven. Opmaak van meerdere regels en koppelingen worden ondersteund.

`subscription`Met de `resourceGroup` elementen en kunt u meer validaties opgeven. De syntaxis voor het opgeven van validaties is identiek aan de aangepaste validatie voor het [tekstvak](microsoft-common-textbox.md). U kunt ook `permission` validaties opgeven voor het abonnement of de resource groep.  

Het besturings element voor abonnementen accepteert een lijst met naam ruimten van de resource provider. U kunt bijvoorbeeld **micro soft. Compute** opgeven. Er wordt een fout bericht weer gegeven wanneer de gebruiker een abonnement selecteert dat geen ondersteuning biedt voor de resource provider. De fout treedt op wanneer de resource provider niet is geregistreerd bij dat abonnement en de gebruiker geen machtiging heeft om de resource provider te registreren.  

Voor het besturings element voor de resource groep is een optie voor `allowExisting` . Wanneer `true` kunnen de gebruikers resource groepen selecteren die al resources hebben. Deze vlag is het meest van toepassing op oplossings sjablonen, waarbij gebruikers standaard gedrag verplichten om een nieuwe of lege resource groep te selecteren. In de meeste andere scenario's is het niet nodig om deze eigenschap op te geven.  

Geef voor de `location` Eigenschappen op voor het locatie besturings element dat u wilt overschrijven. Alle eigenschappen die niet worden overschreven, worden ingesteld op hun standaard waarden. `resourceTypes` Hiermee wordt een matrix met teken reeksen geaccepteerd die volledige namen van resource typen bevatten. De locatie opties zijn beperkt tot alleen regio's die ondersteuning bieden voor de resource typen.  `allowedValues`   Hiermee wordt een matrix met de teken reeksen van regio's geaccepteerd. In de vervolg keuzelijst worden alleen de regio's weer gegeven.U kunt zowel `allowedValues`   als instellen  `resourceTypes` . Het resultaat is het snij punt van beide lijsten. De `visible` eigenschap kan ten slotte worden gebruikt om de vervolg keuzelijst locatie voorwaardelijk of volledig uit te scha kelen.  

## <a name="basics"></a>Basisbeginselen

De stap **basis beginselen** is de eerste stap die wordt gegenereerd wanneer de Azure Portal het bestand parseert. Met de stappen in de basis beginselen kunnen gebruikers standaard het abonnement, de resource groep en de locatie voor de implementatie kiezen.

:::image type="content" source="./media/create-uidefinition-overview/basics.png" alt-text="Basis principes standaard":::

U kunt in deze sectie meer elementen toevoegen. Voeg, indien mogelijk, elementen toe die para meters voor implementatie query's uitvoeren, zoals de naam van een cluster-of beheerders referenties.

In het volgende voor beeld wordt een tekstvak weer gegeven dat is toegevoegd aan de standaard elementen.

```json
"basics": [
    {
        "name": "textBox1",
        "type": "Microsoft.Common.TextBox",
        "label": "Textbox on basics",
        "defaultValue": "my text value",
        "toolTip": "",
        "visible": true
    }
]
```

## <a name="steps"></a>Stappen

De eigenschap Steps bevat nul of meer stappen die na de basis beginselen worden weer gegeven. Elke stap bevat een of meer elementen. U kunt stappen toevoegen per rol of laag van de toepassing die wordt geïmplementeerd. Voeg bijvoorbeeld een stap toe voor de invoer van primaire knoop punten en een stap voor de werk knooppunten in een cluster.

```json
"steps": [
    {
        "name": "demoConfig",
        "label": "Configuration settings",
        "elements": [
          ui-elements-needed-to-create-the-instance
        ]
    }
]
```

## <a name="outputs"></a>Uitvoerwaarden

De Azure Portal gebruikt de `outputs` eigenschap om elementen van `basics` en `steps` toe te wijzen aan de para meters van de sjabloon Azure Resource Manager-implementatie. De sleutels van deze woorden lijst zijn de namen van de sjabloon parameters en de waarden zijn eigenschappen van de uitvoer objecten van de elementen waarnaar wordt verwezen.

Als u de resource naam voor een beheerde toepassing wilt instellen, moet u een waarde `applicationResourceName` met de naam opgeven in de eigenschap outputs. Als u deze waarde niet instelt, wijst de toepassing een GUID toe voor de naam. U kunt een tekstvak in de gebruikers interface toevoegen dat een naam aanvraagt bij de gebruiker.

```json
"outputs": {
    "vmName": "[steps('appSettings').vmName]",
    "trialOrProduction": "[steps('appSettings').trialOrProd]",
    "userName": "[steps('vmCredentials').adminUsername]",
    "pwd": "[steps('vmCredentials').vmPwd.password]",
    "applicationResourceName": "[steps('appSettings').vmName]"
}
```

## <a name="resource-types"></a>Resourcetypen

Als u de beschik bare locaties wilt filteren op alleen de locaties die ondersteuning bieden voor de resource typen die moeten worden geïmplementeerd, geeft u een matrix van de resource typen op. Als u meer dan één resource type opgeeft, worden alleen de locaties geretourneerd die alle resource typen ondersteunen. Deze eigenschap is optioneel.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
    "handler": "Microsoft.Azure.CreateUIDef",
    "version": "0.1.2-preview",
    "parameters": {
        "resourceTypes": ["Microsoft.Compute/disks"],
        "basics": [
          ...
```  

## <a name="functions"></a>Functions

CreateUiDefinition biedt [functies](create-uidefinition-functions.md) voor het werken met de invoer en uitvoer van elementen en functies, zoals voor waarden. Deze functies zijn vergelijkbaar in zowel de syntaxis als de functionaliteit voor het Azure Resource Manager van sjabloon functies.

## <a name="next-steps"></a>Volgende stappen

De createUiDefinition.jsin het bestand zelf heeft een eenvoudig schema. De reële diepte van het is afkomstig van alle ondersteunde elementen en functies. Deze items worden in meer detail beschreven op:

- [Elementen](create-uidefinition-elements.md)
- [Functies](create-uidefinition-functions.md)

Een huidig JSON-schema voor createUiDefinition is hier beschikbaar: `https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json` .

Zie [createUiDefinition.jsop](https://github.com/Azure/azure-managedapp-samples/blob/master/Managed%20Application%20Sample%20Packages/201-managed-app-using-existing-vnet/createUiDefinition.json)voor een gebruikers interface bestand.
