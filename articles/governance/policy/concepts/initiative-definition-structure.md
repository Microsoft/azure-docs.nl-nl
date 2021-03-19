---
title: Details van de definitie structuur van het initiatief
description: Hierin wordt beschreven hoe beleids initiatieven worden gebruikt voor het groeperen van beleids definities voor implementatie naar Azure-resources in uw organisatie.
ms.date: 03/16/2021
ms.topic: conceptual
ms.openlocfilehash: edd3f25dd528d1a718c9287c9f30988b87fb73e2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104587216"
---
# <a name="azure-policy-initiative-definition-structure"></a>Definitie structuur van Azure Policy initiatief

Met initiatieven kunt u verschillende gerelateerde beleids definities groeperen om toewijzingen en beheer te vereenvoudigen omdat u met een groep werkt als één item. U kunt bijvoorbeeld gerelateerde coderings beleidsregels in één initiatief groeperen. In plaats van elk beleid afzonderlijk toe te wijzen, past u het initiatief toe.

U gebruikt JSON om een beleids initiatief definitie te maken. De beleids initiatief definitie bevat elementen voor:

- weergave naam
- beschrijving
- metagegevens
- parameters
- beleids definities
- beleids groepen (deze eigenschap maakt deel uit van de [functie voor naleving van regelgeving (preview-versie](./regulatory-compliance.md)))

In het volgende voor beeld ziet u hoe u een initiatief maakt voor het afhandelen van twee Tags: `costCenter` en `productName` . Er worden twee ingebouwde beleids regels gebruikt om de standaard label waarde toe te passen.

```json
{
    "properties": {
        "displayName": "Billing Tags Policy",
        "policyType": "Custom",
        "description": "Specify cost Center tag and product name tag",
        "metadata": {
            "version": "1.0.0",
            "category": "Tags"
        },
        "parameters": {
            "costCenterValue": {
                "type": "String",
                "metadata": {
                    "description": "required value for Cost Center tag"
                },
                "defaultValue": "DefaultCostCenter"
            },
            "productNameValue": {
                "type": "String",
                "metadata": {
                    "description": "required value for product Name tag"
                },
                "defaultValue": "DefaultProduct"
            }
        },
        "policyDefinitions": [{
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
                "parameters": {
                    "tagName": {
                        "value": "costCenter"
                    },
                    "tagValue": {
                        "value": "[parameters('costCenterValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
                "parameters": {
                    "tagName": {
                        "value": "costCenter"
                    },
                    "tagValue": {
                        "value": "[parameters('costCenterValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
                "parameters": {
                    "tagName": {
                        "value": "productName"
                    },
                    "tagValue": {
                        "value": "[parameters('productNameValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
                "parameters": {
                    "tagName": {
                        "value": "productName"
                    },
                    "tagValue": {
                        "value": "[parameters('productNameValue')]"
                    }
                }
            }
        ]
    }
}
```

Azure Policy ingebouwde toepassingen en patronen bevinden zich op voor [beelden van Azure Policy](../samples/index.md).

## <a name="metadata"></a>Metagegevens

De optionele `metadata` eigenschap bevat informatie over de beleids initiatief definitie.
Klanten kunnen alle eigenschappen en waarden definiëren die van toepassing zijn op hun organisatie in `metadata` . Er zijn echter enkele _algemene_ eigenschappen die worden gebruikt door Azure Policy en in ingebouwde modules.

### <a name="common-metadata-properties"></a>Algemene eigenschappen van meta gegevens

- `version` (teken reeks): houdt informatie bij over de versie van de inhoud van een beleids initiatief definitie.
- `category` (teken reeks): bepaalt onder welke categorie in Azure Portal de beleids definitie wordt weer gegeven.

  > [!NOTE]
  > Voor een [regelgevings nalevings](./regulatory-compliance.md) initiatief moet de naleving van de `category` **regelgeving** worden vastgesteld.

- `preview` (Booleaans): de vlag True of False voor als de beleids initiatief definitie _Preview_ is.
- `deprecated` (Booleaans): de vlag True of False voor als de beleids initiatief definitie is gemarkeerd als _afgeschaft_.

> [!NOTE]
> De Azure Policy-service gebruikt `version` , `preview` en `deprecated` Eigenschappen om het niveau van de wijziging in een ingebouwde beleids definitie of-initiatief en-status over te brengen. De indeling van `version` is: `{Major}.{Minor}.{Patch}` . Specifieke statussen, zoals _afgeschaft_ of _Preview_, worden toegevoegd aan de `version` eigenschap of een andere eigenschap als een **Booleaanse waarde**. Zie [ingebouwde versie beheer](https://github.com/Azure/azure-policy/blob/master/built-in-policies/README.md)voor meer informatie over de manier waarop Azure Policy ingebouwde versies.

## <a name="parameters"></a>Parameters

Met para meters kunt u het beleids beheer vereenvoudigen door het aantal beleids definities te verminderen. U kunt para meters zien zoals de velden in een formulier – `name` , `address` ,, `city` , `state` . Deze para meters blijven altijd hetzelfde, maar hun waarden worden gewijzigd op basis van de persoon die het formulier invult.
Para meters werken op dezelfde manier als bij het bouwen van beleids initiatieven. Door para meters op te nemen in een beleids initiatief definitie, kunt u die para meter opnieuw gebruiken in het opgenomen beleid.

> [!NOTE]
> Zodra een initiatief is toegewezen, kunnen Initative niveau parameters niet worden gewijzigd. Daarom is het aanbeveling om een **DefaultValue** in te stellen bij het definiëren van de para meter.

### <a name="parameter-properties"></a>Parameter eigenschappen

Een para meter heeft de volgende eigenschappen die worden gebruikt in de beleids initiatief definitie:

- `name`: De naam van de para meter. Wordt gebruikt door de `parameters` implementatie functie binnen de beleids regel. Zie [een parameter waarde gebruiken](#passing-a-parameter-value-to-a-policy-definition)voor meer informatie.
- `type`: Bepaalt of de para meter een **teken reeks**, een **matrix**, een **object**, een **Booleaanse waarde**, een **geheel getal**, een **float** of een **datum/tijd** is.
- `metadata`: Definieert subeigenschappen die voornamelijk worden gebruikt door de Azure Portal om gebruikers vriendelijke informatie weer te geven:
  - `description`: De uitleg van het gebruik van de para meter voor. Kan worden gebruikt om voor beelden te bieden van acceptabele waarden.
  - `displayName`: De beschrijvende naam die wordt weer gegeven in de portal voor de para meter.
  - `strongType`: (Optioneel) gebruikt bij het toewijzen van de beleids definitie via de portal. Biedt een context bewuste lijst. Zie [strongType](#strongtype)voor meer informatie.
- `defaultValue`: (Optioneel) Hiermee stelt u de waarde van de para meter in een toewijzing in als er geen waarde is opgegeven.
- `allowedValues`: (Optioneel) biedt een matrix van waarden die door de para meter worden geaccepteerd tijdens de toewijzing.

U kunt bijvoorbeeld een beleids initiatief definitie definiëren om de locatie van resources in de verschillende opgenomen beleids definities te beperken. Een para meter voor die beleids initiatief definitie kan worden **allowedLocations**. De para meter is vervolgens beschikbaar voor elke opgenomen beleids definitie en gedefinieerd tijdens de toewijzing van het beleid.

```json
"parameters": {
    "init_allowedLocations": {
        "type": "array",
        "metadata": {
            "description": "The list of allowed locations for resources.",
            "displayName": "Allowed locations",
            "strongType": "location"
        },
        "defaultValue": [ "westus2" ],
        "allowedValues": [
            "eastus2",
            "westus2",
            "westus"
        ]
    }
}
```

### <a name="passing-a-parameter-value-to-a-policy-definition"></a>Een parameter waarde door geven aan een beleids definitie

U declareert u aan de initiatief parameters die u doorgeeft aan de opgenomen beleids definities in de [policyDefinitions](#policy-definitions) -matrix van de initiatief definitie. De parameter naam kan hetzelfde zijn, waarbij verschillende namen worden gebruikt in de initiatieven dan in de beleids definities de Lees baarheid van code vereenvoudigt.

Zo kan de para meter **init_allowedLocations** initiatief die eerder is gedefinieerd, worden door gegeven aan verschillende opgenomen beleids definities en de bijbehorende para meters, **sql_locations** en **vm_locations**, zoals:

```json
"policyDefinitions": [
    {
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/0ec8fc28-d5b7-4603-8fec-39044f00a92b",
        "policyDefinitionReferenceId": "allowedLocationsSQL",
        "parameters": {
            "sql_locations": {
                "value": "[parameters('init_allowedLocations')]"
            }
        }
    },
    {
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/aa09bd0f-aa5f-4343-b6ab-a33a6a6304f3",
        "policyDefinitionReferenceId": "allowedLocationsVMs",
        "parameters": {
            "vm_locations": {
                "value": "[parameters('init_allowedLocations')]"
            }
        }
    }
]
```

Dit voor beeld verwijst naar de **init_allowedLocations** para meter die is gedemonstreerd in [parameter eigenschappen](#parameter-properties).

### <a name="strongtype"></a>strongType

Binnen de `metadata` eigenschap kunt u **strongType** gebruiken om een multi-select lijst met opties in de Azure Portal op te geven. **strongType** kan een ondersteund _resource type_ of een toegestane waarde zijn. Als u wilt bepalen of een _resource type_ geldig is voor **strongType**, gebruikt u [Get-AzResourceProvider](/powershell/module/az.resources/get-azresourceprovider).

Sommige resource typen die niet worden geretourneerd door **Get-AzResourceProvider** worden ondersteund. Deze resource typen zijn:

- `Microsoft.RecoveryServices/vaults/backupPolicies`

De toegestane waarden voor de niet-bron typen voor **strongType** zijn:

- `location`
- `resourceTypes`
- `storageSkus`
- `vmSKUs`
- `existingResourceGroups`

## <a name="policy-definitions"></a>Beleidsdefinities

Het `policyDefinitions` gedeelte van de initiatief definitie is een _matrix_ waarvan bestaande beleids definities in het initiatief zijn opgenomen. Zoals vermeld in het [door geven van een parameter waarde naar een beleids definitie](#passing-a-parameter-value-to-a-policy-definition), is deze eigenschap waar [Initiative-para meters](#parameters) worden door gegeven aan de beleids definitie.

### <a name="policy-definition-properties"></a>Eigenschappen van beleids definitie

Elk _matrix_ element dat een beleids definitie vertegenwoordigt, heeft de volgende eigenschappen:

- `policyDefinitionId` (teken reeks): de ID van de aangepaste of ingebouwde beleids definitie die moet worden opgenomen.
- `policyDefinitionReferenceId` (teken reeks): een korte naam voor de opgenomen beleids definitie.
- `parameters`: (Optioneel) de naam/waarde-paren voor het door geven van een initiatief parameter naar de opgenomen beleids definitie als een eigenschap in die beleids definitie. Zie [para meters](#parameters)voor meer informatie.
- `groupNames` (matrix met teken reeksen): (optioneel) de groep waarvan de beleids definitie lid is. Zie [beleids groepen](#policy-definition-groups)voor meer informatie.

Hier volgt een voor beeld van `policyDefinitions` met twee opgenomen beleids definities die elk zijn door gegeven aan dezelfde initiatief parameter:

```json
"policyDefinitions": [
    {
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/0ec8fc28-d5b7-4603-8fec-39044f00a92b",
        "policyDefinitionReferenceId": "allowedLocationsSQL",
        "parameters": {
            "sql_locations": {
                "value": "[parameters('init_allowedLocations')]"
            }
        }
    },
    {
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/aa09bd0f-aa5f-4343-b6ab-a33a6a6304f3",
        "policyDefinitionReferenceId": "allowedLocationsVMs",
        "parameters": {
            "vm_locations": {
                "value": "[parameters('init_allowedLocations')]"
            }
        }
    }
]
```

## <a name="policy-definition-groups"></a>Beleids definitie groepen

Beleids definities in een initiatief definitie kunnen worden gegroepeerd en gecategoriseerd. De functie [compliance Conformion](./regulatory-compliance.md) (preview) van Azure Policy gebruikt deze eigenschap om definities te groeperen in **besturings elementen** en **nalevings domeinen**. Deze informatie wordt gedefinieerd in de `policyDefinitionGroups` _matrix_ eigenschap. Meer informatie over de groepering vindt u in een **policyMetadata** -object dat door micro soft is gemaakt. Zie [meta gegevens objecten](#metadata-objects)voor meer informatie.

### <a name="policy-definition-groups-parameters"></a>Para meters voor beleids definitie groepen

Elk _matrix_ element in `policyDefinitionGroups` moet de volgende eigenschappen hebben:

- `name` (teken reeks) \[ vereist \] : de korte naam voor de **groep**. In naleving van regelgeving, het **besturings element**. De waarde van deze eigenschap wordt gebruikt door `groupNames` in `policyDefinitions` .
- `category` (teken reeks): de hiërarchie waartoe de groep behoort. In naleving van regelgeving, het **nalevings domein** van het besturings element.
- `displayName` (teken reeks): de beschrijvende naam voor de **groep** of het **besturings element**. Gebruikt door de portal.
- `description` (teken reeks): een beschrijving van wat betreft de **groep** of het **besturings element** .
- `additionalMetadataId` (teken reeks): de locatie van het [policyMetadata](#metadata-objects) -object met aanvullende details over het **besturings element** en het **nalevings domein**.

  > [!NOTE]
  > Klanten kunnen verwijzen naar een bestaand [policyMetadata](#metadata-objects) -object. Deze objecten zijn echter alleen _-lezen_ en worden alleen gemaakt door micro soft.

Een voor beeld van de `policyDefinitionGroups` eigenschap van de definitie van het ingebouwde NIST-initiatief ziet er als volgt uit:

```json
"policyDefinitionGroups": [
    {
        "name": "NIST_SP_800-53_R4_AC-1",
        "additionalMetadataId": "/providers/Microsoft.PolicyInsights/policyMetadata/NIST_SP_800-53_R4_AC-1"
    }
]
```

### <a name="metadata-objects"></a>Metagegevensobjecten

Door micro soft gemaakte invoeg toepassingen met reglementaire naleving hebben extra informatie over elk besturings element.
Deze informatie is:

- Wordt weer gegeven in de Azure Portal op het overzicht van een **besturings element** op een regelgevings nalevings initiatief.
- Beschikbaar via REST API. Bekijk de `Microsoft.PolicyInsights` resource provider en de [policyMetadata-bewerkings groep](/rest/api/policy/policymetadata/getresource).
- Beschikbaar via Azure CLI. Zie de opdracht [AZ Policy meta data](/cli/azure/policy/metadata) .

> [!IMPORTANT]
> Meta gegevens objecten voor naleving van de regelgeving zijn _alleen-lezen_ en kunnen niet door klanten worden gemaakt.

De meta gegevens voor een beleids groepering hebben de volgende informatie in het `properties` knoop punt:

- `metadataId`: De **besturings element-id** waarop de groepering betrekking heeft.
- `category` (vereist): het **nalevings domein** waartoe het **besturings element** behoort.
- `title` (vereist): de beschrijvende naam van de **besturings element-id**.
- `owner` (vereist): identificeert wie verantwoordelijk is voor het besturings element in Azure: _klant_, _micro soft_, _gedeeld_.
- `description`: Aanvullende informatie over het besturings element.
- `requirements`: Details over de verantwoordelijkheid van de implementatie van het besturings element.
- `additionalContentUrl`: Een koppeling naar meer informatie over het besturings element. Deze eigenschap is doorgaans een koppeling naar de documentatie sectie waarin dit besturings element wordt behandeld in de nalevings standaard.

Hieronder ziet u een voor beeld van het object **policyMetadata** . Dit voor beeld van meta gegevens behoort tot het _NIST SP 800-53 R4 AC-1-_ besturings element.

```json
{
  "properties": {
    "metadataId": "NIST SP 800-53 R4 AC-1",
    "category": "Access Control",
    "title": "Access Control Policy and Procedures",
    "owner": "Shared",
    "description": "**The organization:**    \na. Develops, documents, and disseminates to [Assignment: organization-defined personnel or roles]:  \n1. An access control policy that addresses purpose, scope, roles, responsibilities, management commitment, coordination among organizational entities, and compliance; and  \n2. Procedures to facilitate the implementation of the access control policy and associated access controls; and  \n  
\nb. Reviews and updates the current:  \n1. Access control policy [Assignment: organization-defined frequency]; and  \n2. Access control procedures [Assignment: organization-defined frequency].",
    "requirements": "**a.**  The customer is responsible for developing, documenting, and disseminating access control policies and procedures. The customer access control policies and procedures address access to all customer-deployed resources and customer system access (e.g., access to customer-deployed virtual machines, access to customer-built applications).  \n**b.**  The customer is responsible for reviewing and updating access control policies and procedures in accordance with FedRAMP requirements.",
    "additionalContentUrl": "https://nvd.nist.gov/800-53/Rev4/control/AC-1"
  },
  "id": "/providers/Microsoft.PolicyInsights/policyMetadata/NIST_SP_800-53_R4_AC-1",
  "name": "NIST_SP_800-53_R4_AC-1",
  "type": "Microsoft.PolicyInsights/policyMetadata"
}
```

## <a name="next-steps"></a>Volgende stappen

- De [definitie structuur](./definition-structure.md) bekijken
- Bekijk voor beelden op [Azure Policy voor beelden](../samples/index.md).
- Lees [Informatie over de effecten van het beleid](effects.md).
- Meer informatie over het [programmatisch maken van beleids regels](../how-to/programmatically-create.md).
- Meer informatie over het [ophalen van compatibiliteits gegevens](../how-to/get-compliance-data.md).
- Ontdek hoe u [niet-compatibele resources kunt herstellen](../how-to/remediate-resources.md).
- Bekijk wat een beheer groep is met [het organiseren van uw resources met Azure-beheer groepen](../../management-groups/overview.md).
