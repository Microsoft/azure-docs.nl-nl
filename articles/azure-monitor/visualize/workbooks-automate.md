---
title: Azure Monitor werkmappen en Azure Resource Manager sjablonen
description: Vereenvoudig complexe rapportage met vooraf opgebouwde en aangepaste werkmappen met para meters Azure Monitor die via Azure Resource Manager sjablonen zijn geïmplementeerd
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/30/2020
ms.openlocfilehash: 77190b85da08d09cf05a02dcc5787f0c24229948
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100611691"
---
# <a name="programmatically-manage-workbooks"></a>Werkmappen programmatisch beheren

Resource-eigen aren hebben de mogelijkheid om hun werkmappen programmatisch te maken en beheren via Resource Manager-sjablonen.

Dit kan handig zijn in scenario's zoals:
* Implementatie van organisatie-of domein-specifieke analyse rapporten samen met de implementaties van resources. U kunt bijvoorbeeld voor uw nieuwe apps of virtuele machines organisatie-specifieke prestaties en mislukte werkmappen implementeren.
* Het implementeren van standaard rapporten of-Dash boards met behulp van werkmappen voor bestaande resources.

De werkmap wordt gemaakt in de gewenste sub/resource-groep en met de inhoud die is opgegeven in de Resource Manager-sjablonen.

Er zijn twee soorten werkmap bronnen die programmatisch kunnen worden beheerd:
* [Werkmap sjablonen](#azure-resource-manager-template-for-deploying-a-workbook-template)
* [Werkmap exemplaren](#azure-resource-manager-template-for-deploying-a-workbook-instance)

## <a name="azure-resource-manager-template-for-deploying-a-workbook-template"></a>Azure Resource Manager sjabloon voor het implementeren van een werkmap sjabloon

1. Open een werkmap die u programmatisch wilt implementeren.
2. Schakel de werkmap over naar de bewerkings modus door te klikken op het item werkbalk opdracht _bewerken_ .
3. Open de _Geavanceerde editor_ met behulp _</>_ van de knop op de werk balk.
4. Zorg ervoor dat u zich op het tabblad _Galerie sjabloon_ bevindt.

    ![Tabblad Galerie sjabloon](./media/workbooks-automate/gallery-template.png)
1. Kopieer de JSON in de galerie sjabloon naar het klem bord.
2. Hieronder ziet u een voor beeld Azure Resource Manager sjabloon waarmee een werkmap sjabloon wordt geïmplementeerd voor Azure Monitor werkmap galerie. Plak de JSON die u hebt gekopieerd in plaats van `<PASTE-COPIED-WORKBOOK_TEMPLATE_HERE>` . Een referentie Azure Resource Manager sjabloon waarmee een werkmap sjabloon wordt gemaakt, vindt u [hier](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Documentation/ARM-template-for-creating-workbook-template).

    ```json
    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "resourceName": {
                "type": "string",
                "defaultValue": "my-workbook-template",
                "metadata": {
                    "description": "The unique name for this workbook template instance"
                }
            }
        },
        "resources": [
            {
                "name": "[parameters('resourceName')]",
                "type": "microsoft.insights/workbooktemplates",
                "location": "[resourceGroup().location]",
                "apiVersion": "2019-10-17-preview",
                "dependsOn": [],
                "properties": {
                    "galleries": [
                        {
                            "name": "A Workbook Template",
                            "category": "Deployed Templates",
                            "order": 100,
                            "type": "workbook",
                            "resourceType": "Azure Monitor"
                        }
                    ],
                    "templateData": <PASTE-COPIED-WORKBOOK_TEMPLATE_HERE>
                }
            }
        ]
    }
    ```
1. Vul in het `galleries` object de `name` en- `category` sleutels in met uw waarden. Meer informatie over [para meters](#parameters) vindt u in de volgende sectie.
2. Implementeer deze Azure Resource Manager sjabloon met behulp van de [Azure Portal](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template), de [opdracht regel interface](../../azure-resource-manager/templates/deploy-cli.md), [Power shell](../../azure-resource-manager/templates/deploy-powershell.md), enzovoort.
3. Open de Azure Portal en navigeer naar de galerie met werkmappen die u hebt gekozen in de sjabloon Azure Resource Manager. Navigeer in de voorbeeld sjabloon naar de Azure Monitor werkmap galerie:
    1. Open de Azure Portal en ga naar Azure Monitor
    2. Openen `Workbooks` vanuit de inhouds opgave
    3. Zoek uw sjabloon in de galerie onder categorie `Deployed Templates` (wordt een van de paarse items).

### <a name="parameters"></a>Parameters

|Parameters                |Uitleg                                                                                             |
|:-------------------------|:-------------------------------------------------------------------------------------------------------|
| `name`                   | De naam van de resource van de werkmap sjabloon in Azure Resource Manager.                                  |
|`type`                    | Altijd micro soft. Insights/workbooktemplates                                                            |
| `location`               | De Azure-locatie waarin de werkmap wordt gemaakt.                                               |
| `apiVersion`             | 2019-10-17 preview                                                                                     |
| `type`                   | Altijd micro soft. Insights/workbooktemplates                                                            |
| `galleries`              | De set galerieën waarin deze werkmap sjabloon wordt weer gegeven.                                                |
| `gallery.name`           | De beschrijvende naam van de werkmap sjabloon in de galerie.                                             |
| `gallery.category`       | De groep in de galerie waarin de sjabloon moet worden geplaatst.                                                     |
| `gallery.order`          | Een getal dat de volg orde van de sjabloon in een categorie in de galerie bepaalt. Een lagere volg orde betekent een hogere prioriteit. |
| `gallery.resourceType`   | Het resource type dat overeenkomt met de galerie. Dit is doorgaans de bron type teken reeks die overeenkomt met de resource (bijvoorbeeld micro soft. operationalinsights/Workspaces). |
|`gallery.type`            | Dit is een unieke sleutel die wordt onderscheiden van de galerie in een resource type. Application Insights bijvoorbeeld typen hebben `workbook` en `tsg` overeenkomen met verschillende werkmap galerieën. |

### <a name="galleries"></a>Galerieën

| Galerie                                        | Resourcetype                                      | Werkmap type |
| :--------------------------------------------- |:---------------------------------------------------|:--------------|
| Werkmappen in Azure Monitor                     | `Azure Monitor`                                    | `workbook`    |
| VM Insights in Azure Monitor                   | `Azure Monitor`                                    | `vm-insights` |
| Werkmappen in log Analytics-werk ruimte           | `microsoft.operationalinsights/workspaces`         | `workbook`    |
| Werkmappen in Application Insights              | `microsoft.insights/component`                     | `workbook`    |
| Probleemoplossings richtlijnen in Application Insights | `microsoft.insights/component`                     | `tsg`         |
| Gebruik in Application Insights                  | `microsoft.insights/component`                     | `usage`       |
| Werkmappen in Kubernetes-service                | `Microsoft.ContainerService/managedClusters`       | `workbook`    |
| Werkmappen in resource groepen                   | `microsoft.resources/subscriptions/resourcegroups` | `workbook`    |
| Werkmappen in Azure Active Directory            | `microsoft.aadiam/tenant`                          | `workbook`    |
| VM Insights in virtuele machines                | `microsoft.compute/virtualmachines`                | `insights`    |
| VM-inzichten in virtuele-machine schaal sets      | `microsoft.compute/virtualmachinescalesets`        | `insights`    |

## <a name="azure-resource-manager-template-for-deploying-a-workbook-instance"></a>Azure Resource Manager sjabloon voor het implementeren van een werkmap exemplaar

1. Open een werkmap die u programmatisch wilt implementeren.
2. Schakel de werkmap over naar de bewerkings modus door te klikken op het item werkbalk opdracht _bewerken_ .
3. Open de _Geavanceerde editor_ met behulp _</>_ van de knop op de werk balk.
4. Schakel in de editor van het _sjabloon type_ over naar de _Resource Manager-sjabloon_.
5. De Resource Manager-sjabloon voor het maken van wordt weer gegeven in de editor. Kopieer de inhoud en gebruik deze as-is of voeg deze samen met een grotere sjabloon waarmee ook de doel resource wordt geïmplementeerd.

    ![Afbeelding die laat zien hoe u de Resource Manager-sjabloon kunt ophalen in de gebruikers interface van de werkmap](./media/workbooks-automate/programmatic-template.png)

## <a name="sample-azure-resource-manager-template"></a>Voor beeld Azure Resource Manager sjabloon
In deze sjabloon ziet u hoe u een eenvoudige werkmap implementeert waarin een ' Hallo wereld! ' wordt weer gegeven.
```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workbookDisplayName":  {             
            "type":"string",
            "defaultValue": "My Workbook",
            "metadata": {
                "description": "The friendly name for the workbook that is used in the Gallery or Saved List. Needs to be unique in the scope of the resource group and source" 
            }
        },
        "workbookType":  {             
            "type":"string",
            "defaultValue": "tsg",
            "metadata": {
                "description": "The gallery that the workbook will been shown under. Supported values include workbook, `tsg`, Azure Monitor, etc." 
            }
        },
        "workbookSourceId":  {             
            "type":"string",
            "defaultValue": "<insert-your-resource-id-here>",
            "metadata": {
                "description": "The id of resource instance to which the workbook will be associated" 
            }
        },
        "workbookId": {
            "type":"string",
            "defaultValue": "[newGuid()]",
            "metadata": {
                "description": "The unique guid for this workbook instance" 
            }
        }
    },    
    "resources": [
        {
            "name": "[parameters('workbookId')]",
            "type": "Microsoft.Insights/workbooks",
            "location": "[resourceGroup().location]",
            "kind": "shared",
            "apiVersion": "2018-06-17-preview",
            "dependsOn": [],
            "properties": {
                "displayName": "[parameters('workbookDisplayName')]",
                "serializedData": "{\"version\":\"Notebook/1.0\",\"items\":[{\"type\":1,\"content\":\"{\\\"json\\\":\\\"Hello World!\\\"}\",\"conditionalVisibility\":null}],\"isLocked\":false}",
                "version": "1.0",
                "sourceId": "[parameters('workbookSourceId')]",
                "category": "[parameters('workbookType')]"
            }
        }
    ],
    "outputs": {
        "workbookId": {
            "type": "string",
            "value": "[resourceId( 'Microsoft.Insights/workbooks', parameters('workbookId'))]"
        }
    }
}
```

### <a name="template-parameters"></a>Sjabloonparameters

| Parameter | Uitleg |
| :------------- |:-------------|
| `workbookDisplayName` | De beschrijvende naam voor de werkmap die wordt gebruikt in de galerie of de opgeslagen lijst. Moet uniek zijn binnen het bereik van de resource groep en de bron |
| `workbookType` | De galerie waarin de werkmap wordt weer gegeven. Ondersteunde waarden zijn: werkmap, `tsg` , Azure monitor, enzovoort. |
| `workbookSourceId` | De ID van het resource-exemplaar waaraan de werkmap wordt gekoppeld. De nieuwe werkmap wordt weer gegeven met betrekking tot deze resource-instantie, bijvoorbeeld in de tabel met inhoud onder _werkmap_ van de resource. Als u wilt dat uw werkmap wordt weer gegeven in de galerie met werkmappen in Azure Monitor, gebruikt u de teken reeks _Azure monitor_ in plaats van een resource-id. |
| `workbookId` | De unieke GUID voor dit exemplaar van de werkmap. Gebruik _[newGuid ()]_ om automatisch een nieuwe GUID te maken. |
| `kind` | Wordt gebruikt om op te geven of de gemaakte werkmap gedeeld of privé is. Gebruik een waarde die wordt _gedeeld_ voor gedeelde werkmappen en de _gebruiker_ voor persoonlijk. |
| `location` | De Azure-locatie waar de werkmap wordt gemaakt. Gebruik _[resourceGroup (). locatie]_ om deze te maken op dezelfde locatie als de resource groep |
| `serializedData` | Bevat de inhoud of Payload die in de werkmap moet worden gebruikt. De Resource Manager-sjabloon uit de werkmappen-gebruikers interface gebruiken om de waarde op te halen |

### <a name="workbook-types"></a>Werkmap typen
Met werkmap typen geeft u op in welke werkmap galerie het nieuwe exemplaar van de werkmap wordt weer gegeven onder. Een aantal opties:

| Type | Galerie locatie |
| :------------- |:-------------|
| `workbook` | De standaard die wordt gebruikt in de meeste rapporten, met inbegrip van de galerie met werkmappen van Application Insights, Azure Monitor, enzovoort.  |
| `tsg` | De galerie met gidsen voor probleem oplossing in Application Insights |
| `usage` | De galerie _meer_ onder _gebruik_ in Application Insights |

### <a name="limitations"></a>Beperkingen
Dit mechanisme kan om een technische reden niet worden gebruikt voor het maken van werkmap exemplaren in de galerie met _werkmappen_ van Application Insights. We werken aan het adresseren van deze beperking. In de tussen tijd raden we u aan de galerie met probleemoplossings gids (workbookType: `tsg` ) te gebruiken om Application Insights gerelateerde werkmappen te implementeren.

## <a name="next-steps"></a>Volgende stappen

Ontdek hoe werkmappen worden gebruikt om de nieuwe [Azure monitor voor de opslag ervaring](../insights/storage-insights-overview.md)uit te scha kelen.
