---
title: Details van de structuur van de beleids definitie
description: Hierin wordt beschreven hoe beleids definities worden gebruikt om conventies voor Azure-resources in uw organisatie in te richten.
ms.date: 10/22/2020
ms.topic: conceptual
ms.openlocfilehash: 607d1d85dbb370305d0337cc311433c37e36c4c0
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/03/2021
ms.locfileid: "99493308"
---
# <a name="azure-policy-definition-structure"></a>Structuur van Azure-beleidsdefinities

Azure Policy legt conventies voor resources vast. Beleids definities beschrijven de nalevings [voorwaarden voor](#conditions) bronnen en het effect dat moet worden genomen als aan een voor waarde wordt voldaan. Met een voor waarde wordt een resource-eigenschaps [veld](#fields) of een [waarde](#value) naar een vereiste waarde vergeleken. Resource-eigenschaps velden worden geopend met behulp van [aliassen](#aliases). Wanneer een resource-eigenschaps veld een matrix is, kan een speciale [matrix alias](#understanding-the--alias) worden gebruikt om waarden van alle matrix leden te selecteren en een voor waarde toe te passen op elk van deze elementen.
Meer informatie over [voor waarden](#conditions).

Als u conventies definieert, kunt u kosten besparen en uw resources eenvoudiger beheren. U kunt bijvoorbeeld opgeven dat alleen bepaalde typen virtuele machines zijn toegestaan. Of u kunt vereisen dat resources een bepaalde tag hebben. Beleids toewijzingen worden overgenomen door onderliggende resources. Als een beleids toewijzing wordt toegepast op een resource groep, is deze van toepassing op alle resources in die resource groep.

Het schema voor de beleids definitie _policyRule_ wordt hier gevonden: [https://schema.management.azure.com/schemas/2019-09-01/policyDefinition.json](https://schema.management.azure.com/schemas/2019-09-01/policyDefinition.json)

U gebruikt JSON om een beleids definitie te maken. De beleids definitie bevat elementen voor:

- weergave naam
- beschrijving
- mode
- metagegevens
- parameters
- beleids regel
  - logische evaluatie
  - effect

In de volgende JSON wordt bijvoorbeeld een beleid weer gegeven dat de implementatie van resources beperkt:

```json
{
    "properties": {
        "displayName": "Allowed locations",
        "description": "This policy enables you to restrict the locations your organization can specify when deploying resources.",
        "mode": "Indexed",
        "metadata": {
            "version": "1.0.0",
            "category": "Locations"
        },
        "parameters": {
            "allowedLocations": {
                "type": "array",
                "metadata": {
                    "description": "The list of locations that can be specified when deploying resources",
                    "strongType": "location",
                    "displayName": "Allowed locations"
                },
                "defaultValue": [ "westus2" ]
            }
        },
        "policyRule": {
            "if": {
                "not": {
                    "field": "location",
                    "in": "[parameters('allowedLocations')]"
                }
            },
            "then": {
                "effect": "deny"
            }
        }
    }
}
```

Azure Policy ingebouwde toepassingen en patronen bevinden zich op voor [beelden van Azure Policy](../samples/index.md).

## <a name="display-name-and-description"></a>Weergave naam en beschrijving

U gebruikt **DisplayName** en **Beschrijving** om de beleids definitie te identificeren en context op te geven wanneer deze wordt gebruikt. **DisplayName** heeft een maximale lengte van _128_ tekens en een **Beschrijving** van Maxi maal _512_ tekens.

> [!NOTE]
> Tijdens het maken of bijwerken van een beleids definitie, de **id**, het **type** en de **naam** worden gedefinieerd door de Eigenschappen extern van de JSON en zijn deze niet nodig in het JSON-bestand. Het ophalen van de beleids definitie via SDK retourneert de eigenschappen **id**, **type** en **naam** als onderdeel van de JSON, maar elk is een alleen-lezen informatie met betrekking tot de beleids definitie.

## <a name="type"></a>Type

Hoewel de eigenschap **type** niet kan worden ingesteld, zijn er drie waarden die worden geretourneerd door SDK en zichtbaar zijn in de portal:

- `Builtin`: Deze beleids definities worden door micro soft verschaft en onderhouden.
- `Custom`: Alle beleids definities die zijn gemaakt door klanten hebben deze waarde.
- `Static`: Geeft een definitie van een [regelgevings nalevings](./regulatory-compliance.md) beleid aan bij micro soft- **eigendom**. De compliantie resultaten voor deze beleids definities zijn de resultaten van controles van derden op micro soft-infra structuur. In de Azure Portal wordt deze waarde soms weer gegeven als **beheerd door micro soft**. Zie [gedeelde verantwoordelijkheid in de Cloud](../../../security/fundamentals/shared-responsibility.md)voor meer informatie.

## <a name="mode"></a>Modus

De **modus** is geconfigureerd, afhankelijk van of het beleid is gericht op een Azure Resource Manager eigenschap of een bron provider eigenschap.

### <a name="resource-manager-modes"></a>Resource Manager-modi

De **modus** bepaalt welke resource typen worden geëvalueerd voor een beleids definitie. De ondersteunde modi zijn:

- `all`: resource groepen, abonnementen en alle resource typen evalueren
- `indexed`: alleen resource typen evalueren die ondersteuning bieden voor labels en locaties

Resource `Microsoft.Network/routeTables` ondersteunt bijvoorbeeld tags en locatie en wordt in beide modi geëvalueerd. De resource `Microsoft.Network/routeTables/routes` kan echter niet worden gelabeld en wordt niet geëvalueerd in de `Indexed` modus.

U wordt aangeraden de **modus** `all` in de meeste gevallen in te stellen. Alle beleids definities die via de portal zijn gemaakt, maken gebruik van de `all` modus. Als u Power shell of Azure CLI gebruikt, kunt u de para meter **mode** hand matig opgeven. Als de beleids definitie geen **modus** waarde bevat, wordt standaard `all` in azure PowerShell en naar `null` in azure cli. Een `null` modus is dezelfde als die gebruikt `indexed` om achterwaartse compatibiliteit te ondersteunen.

`indexed` moet worden gebruikt bij het maken van beleids regels voor het afdwingen van tags of locaties. Hoewel dit niet vereist is, voor komt u dat resources die tags en locaties ondersteunen, niet kunnen worden weer gegeven als niet-compatibel in de nalevings resultaten. De uitzonde ring is **resource groepen** en **abonnementen**. Met beleids definities die locatie of tags voor een resource groep of-abonnement afdwingen, moet u de **modus** instellen op `all` en het doel `Microsoft.Resources/subscriptions/resourceGroups` `Microsoft.Resources/subscriptions` opgeven. Zie voor een voor beeld [patroon: Labels-voor beeld #1](../samples/pattern-tags.md). Zie [tag-ondersteuning voor Azure-resources](../../../azure-resource-manager/management/tag-support.md)voor een lijst met resources die Tags ondersteunen.

### <a name="resource-provider-modes"></a>Resource provider modi

De volgende resource provider modus wordt volledig ondersteund:

- `Microsoft.Kubernetes.Data` voor het beheren van uw Kubernetes-clusters in of uit Azure. Definities die gebruikmaken van deze resource provider modus, gebruiken effecten _controleren_, _weigeren_ en _uitgeschakeld_. Het gebruik van het [EnforceOPAConstraint](./effects.md#enforceopaconstraint) -effect is _afgeschaft_.

De volgende resource provider modi worden momenteel ondersteund als een **Preview**:

- `Microsoft.ContainerService.Data` voor het beheren van regels voor toegangs beheer in de [Azure Kubernetes-service](../../../aks/intro-kubernetes.md). Definities die gebruikmaken van deze resource provider modus **moeten** het [EnforceRegoPolicy](./effects.md#enforceregopolicy) -effect gebruiken. Deze modus is _afgeschaft_.
- `Microsoft.KeyVault.Data` voor het beheren van kluizen en certificaten in [Azure Key Vault](../../../key-vault/general/overview.md). Zie [Azure Key Vault integreren met Azure Policy](../../../key-vault/general/azure-policy.md)voor meer informatie over deze beleids definities.

> [!NOTE]
> De modi van de resource provider ondersteunen alleen ingebouwde beleids definities en bieden geen ondersteuning voor [uitzonde ringen](./exemption-structure.md).

## <a name="metadata"></a>Metagegevens

De optionele `metadata` eigenschap bevat informatie over de beleids definitie. Klanten kunnen alle eigenschappen en waarden definiëren die van toepassing zijn op hun organisatie in `metadata` . Er zijn echter enkele _algemene_ eigenschappen die worden gebruikt door Azure Policy en in ingebouwde modules.

### <a name="common-metadata-properties"></a>Algemene eigenschappen van meta gegevens

- `version` (teken reeks): houdt informatie bij over de versie van de inhoud van een beleids definitie.
- `category` (teken reeks): bepaalt onder welke categorie in Azure Portal de beleids definitie wordt weer gegeven.
- `preview` (Booleaans): de vlag True of False voor als de beleids definitie _Preview_ is.
- `deprecated` (Booleaans): de vlag True of False voor als de beleids definitie is gemarkeerd als _afgeschaft_.

> [!NOTE]
> De Azure Policy-service gebruikt `version` , `preview` en `deprecated` Eigenschappen om het niveau van de wijziging in een ingebouwde beleids definitie of-initiatief en-status over te brengen. De indeling van `version` is: `{Major}.{Minor}.{Patch}` . Specifieke statussen, zoals _afgeschaft_ of _Preview_, worden toegevoegd aan de `version` eigenschap of een andere eigenschap als een **Booleaanse waarde**. Zie [ingebouwde versie beheer](https://github.com/Azure/azure-policy/blob/master/built-in-policies/README.md)voor meer informatie over de manier waarop Azure Policy ingebouwde versies.

## <a name="parameters"></a>Parameters

Met para meters kunt u het beleids beheer vereenvoudigen door het aantal beleids definities te verminderen. U kunt para meters zien zoals de velden in een formulier – `name` , `address` ,, `city` , `state` . Deze para meters blijven altijd hetzelfde, maar hun waarden worden gewijzigd op basis van de persoon die het formulier invult.
Para meters werken op dezelfde manier als wanneer u beleid bouwt. Door para meters in een beleids definitie op te nemen, kunt u het beleid voor verschillende scenario's gebruiken door verschillende waarden te gebruiken.

> [!NOTE]
> Para meters kunnen worden toegevoegd aan een bestaande en toegewezen definitie. De nieuwe para meter moet de eigenschap **DefaultValue** bevatten. Dit voor komt dat bestaande toewijzingen van het beleid of initiatief indirect ongeldig worden gemaakt.

### <a name="parameter-properties"></a>Parameter eigenschappen

Een para meter heeft de volgende eigenschappen die worden gebruikt in de beleids definitie:

- `name`: De naam van de para meter. Wordt gebruikt door de `parameters` implementatie functie binnen de beleids regel. Zie [een parameter waarde gebruiken](#using-a-parameter-value)voor meer informatie.
- `type`: Bepaalt of de para meter een **teken reeks**, een **matrix**, een **object**, een **Booleaanse waarde**, een **geheel getal**, een **float** of een **datum/tijd** is.
- `metadata`: Definieert subeigenschappen die voornamelijk worden gebruikt door de Azure Portal om gebruikers vriendelijke informatie weer te geven:
  - `description`: De uitleg van het gebruik van de para meter voor. Kan worden gebruikt om voor beelden te bieden van acceptabele waarden.
  - `displayName`: De beschrijvende naam die wordt weer gegeven in de portal voor de para meter.
  - `strongType`: (Optioneel) gebruikt bij het toewijzen van de beleids definitie via de portal. Biedt een context bewuste lijst. Zie [strongType](#strongtype)voor meer informatie.
  - `assignPermissions`: (Optioneel) Stel in op _waar_ als u wilt dat Azure Portal roltoewijzingen tijdens beleids toewijzing wilt maken. Deze eigenschap is handig voor het geval u machtigingen wilt toewijzen buiten het toewijzings bereik. Er is één roltoewijzing per roldefinitie in het beleid (of per functie definitie in alle beleids regels in het initiatief). De parameter waarde moet een geldige resource of een geldig bereik zijn.
- `defaultValue`: (Optioneel) Hiermee stelt u de waarde van de para meter in een toewijzing in als er geen waarde is opgegeven.
  Vereist bij het bijwerken van een bestaande beleids definitie die is toegewezen.
- `allowedValues`: (Optioneel) biedt een matrix van waarden die door de para meter worden geaccepteerd tijdens de toewijzing.

U kunt bijvoorbeeld een beleids definitie definiëren om de locaties te beperken waar resources kunnen worden geïmplementeerd. Een para meter voor die beleids definitie kan **allowedLocations** zijn. Deze para meter wordt gebruikt door elke toewijzing van de beleids definitie om de geaccepteerde waarden te beperken. Het gebruik van **strongType** biedt een verbeterde ervaring bij het volt ooien van de toewijzing via de portal:

```json
"parameters": {
    "allowedLocations": {
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

### <a name="using-a-parameter-value"></a>Een parameter waarde gebruiken

In de beleids regel verwijst u naar para meters met de volgende `parameters` functie syntaxis:

```json
{
    "field": "location",
    "in": "[parameters('allowedLocations')]"
}
```

Dit voor beeld verwijst naar de **allowedLocations** -para meter die is gedemonstreerd in [parameter eigenschappen](#parameter-properties).

### <a name="strongtype"></a>strongType

Binnen de `metadata` eigenschap kunt u **strongType** gebruiken om een multi-select lijst met opties in de Azure Portal op te geven. **strongType** kan een ondersteund _resource type_ of een toegestane waarde zijn. Als u wilt bepalen of een _resource type_ geldig is voor **strongType**, gebruikt u [Get-AzResourceProvider](/powershell/module/az.resources/get-azresourceprovider). De indeling voor een _resource type_ **strongType** is `<Resource Provider>/<Resource Type>` . Bijvoorbeeld `Microsoft.Network/virtualNetworks/subnets`.

Sommige _resource typen_ die niet worden geretourneerd door **Get-AzResourceProvider** worden ondersteund. Deze typen zijn:

- `Microsoft.RecoveryServices/vaults/backupPolicies`

Het niet- _bron type_ toegestane waarden voor **strongType** zijn:

- `location`
- `resourceTypes`
- `storageSkus`
- `vmSKUs`
- `existingResourceGroups`

## <a name="definition-location"></a>Locatie van definitie

Tijdens het maken van een initiatief of beleid is het nood zakelijk om de definitie locatie op te geven. De definitie locatie moet een beheer groep of een abonnement zijn. Deze locatie bepaalt het bereik waaraan het initiatief of beleid kan worden toegewezen. Resources moeten rechtstreekse leden zijn van of onderliggende items binnen de hiërarchie van de definitie locatie naar het doel voor toewijzing.

Als de definitie locatie een:

- Alleen voor **abonnements** resources binnen dit abonnement kan de beleids definitie worden toegewezen.
- **Beheer groep** -alleen resources binnen onderliggende beheer groepen en onderliggende abonnementen kunnen worden toegewezen aan de beleids definitie. Als u van plan bent de beleids definitie toe te passen op verschillende abonnementen, moet de locatie een beheer groep zijn die elk abonnement bevat.

Zie [bereik begrijpen in azure Policy](./scope.md#definition-location)voor meer informatie.

## <a name="policy-rule"></a>Beleidsregel

De beleids regel bestaat uit **als** en **vervolgens** blokken. In het **if** -blok definieert u een of meer voor waarden die aangeven wanneer het beleid wordt afgedwongen. U kunt logische Opera tors Toep assen op deze voor waarden om het scenario voor een beleid precies te definiëren.

In de **blok kering** definieert u het effect dat optreedt wanneer aan de voor waarden voor de **if** wordt voldaan.

```json
{
    "if": {
        <condition> | <logical operator>
    },
    "then": {
        "effect": "deny | audit | modify | append | auditIfNotExists | deployIfNotExists | disabled"
    }
}
```

### <a name="logical-operators"></a>Logische operators

Ondersteunde logische Opera tors zijn:

- `"not": {condition  or operator}`
- `"allOf": [{condition or operator},{condition or operator}]`
- `"anyOf": [{condition or operator},{condition or operator}]`

De syntaxis **not** keert het resultaat van de voor waarde. De **overzet** -syntaxis (vergelijkbaar met de logische **en** -bewerking) vereist dat alle voor waarden waar zijn. Voor de syntaxis van de **anyOf** (vergelijkbaar met de logische **of** bewerking) moeten een of meer voor waarden waar zijn.

U kunt logische Opera tors nesten. In het volgende voor beeld ziet u een **niet** -bewerking die is genest in een **overzet** -bewerking.

```json
"if": {
    "allOf": [{
            "not": {
                "field": "tags",
                "containsKey": "application"
            }
        },
        {
            "field": "type",
            "equals": "Microsoft.Storage/storageAccounts"
        }
    ]
},
```

### <a name="conditions"></a>Voorwaarden

Met een voor waarde wordt geëvalueerd of een waarde aan bepaalde criteria voldoet. De ondersteunde voor waarden zijn:

- `"equals": "stringValue"`
- `"notEquals": "stringValue"`
- `"like": "stringValue"`
- `"notLike": "stringValue"`
- `"match": "stringValue"`
- `"matchInsensitively": "stringValue"`
- `"notMatch": "stringValue"`
- `"notMatchInsensitively": "stringValue"`
- `"contains": "stringValue"`
- `"notContains": "stringValue"`
- `"in": ["stringValue1","stringValue2"]`
- `"notIn": ["stringValue1","stringValue2"]`
- `"containsKey": "keyName"`
- `"notContainsKey": "keyName"`
- `"less": "dateValue"` | `"less": "stringValue"` | `"less": intValue`
- `"lessOrEquals": "dateValue"` | `"lessOrEquals": "stringValue"` | `"lessOrEquals": intValue`
- `"greater": "dateValue"` | `"greater": "stringValue"` | `"greater": intValue`
- `"greaterOrEquals": "dateValue"` | `"greaterOrEquals": "stringValue"` |
  `"greaterOrEquals": intValue`
- `"exists": "bool"`

Voor **minder**, **lessOrEquals**, **meer** en **greaterOrEquals** als het eigenschaps type niet overeenkomt met het type voor waarde, wordt een fout gegenereerd. Teken reeks vergelijkingen worden gemaakt met `InvariantCultureIgnoreCase` .

Wanneer u de voor waarden **like** en **notLike** gebruikt, geeft u een Joker teken `*` op in de waarde.
De waarde mag niet meer dan één Joker teken bevatten `*` .

Wanneer u de voor waarden **match** en **notMatch** gebruikt, moet `#` u een cijfer, voor een letter, overeenkomen met een `?` `.` wille keurig teken en elk ander teken dat overeenkomt met het werkelijke teken. **Identieke** en **notMatch** zijn hoofdletter gevoelig. alle andere voor waarden die een _stringValue_ evalueren, zijn niet hoofdletter gevoelig. Hoofdletter gevoelige alternatieven zijn beschikbaar in **matchInsensitively** en **notMatchInsensitively**.

### <a name="fields"></a>Velden

Voor waarden die evalueren of de waarden van eigenschappen in de resource-aanvraag lading voldoen aan bepaalde criteria kunnen worden gevormd met behulp van een **veld** expressie.
De volgende velden worden ondersteund:

- `name`
- `fullName`
  - Retourneert de volledige naam van de resource. De volledige naam van een resource is de resource naam die wordt voorafgegaan door bovenliggende resource namen (bijvoorbeeld ' mijn server/myDatabase ').
- `kind`
- `type`
- `location`
  - Locatie velden worden genormaliseerd ter ondersteuning van verschillende indelingen. `East US 2`Wordt bijvoorbeeld beschouwd als gelijk aan `eastus2` .
  - Gebruik **Global** voor resources die de locatie neutraal.
- `id`
  - Retourneert de resource-ID van de resource die wordt geëvalueerd.
  - Voorbeeld: `/subscriptions/06be863d-0996-4d56-be22-384767287aa2/resourceGroups/myRG/providers/Microsoft.KeyVault/vaults/myVault`
- `identity.type`
  - Retourneert het type [beheerde identiteit](../../../active-directory/managed-identities-azure-resources/overview.md) dat is ingeschakeld voor de bron.
- `tags`
- `tags['<tagName>']`
  - De syntaxis van het haakje ondersteunt label namen met lees tekens zoals een afbreek streepje, een punt of een spatie.
  - Waar de **\<tagName\>** naam van de tag is voor het valideren van de voor waarde voor.
  - Voor beelden: `tags['Acct.CostCenter']` waarbij **acct. CostCenter** de naam van de tag is.
- `tags['''<tagName>''']`
  - De syntaxis van het haakje ondersteunt label namen met apostrofs in het teken met dubbele apostrofs.
  - Waarbij **' \<tagName\> '** de naam van het label is voor het valideren van de voor waarde voor.
  - Voor beeld: `tags['''My.Apostrophe.Tag''']` waarbij **' My. apostrof. tag '** de naam van de tag is.
- eigenschaps aliassen: Zie [aliassen](#aliases)voor een lijst.

> [!NOTE]
> `tags.<tagName>`, `tags[tagName]` en `tags[tag.with.dots]` zijn nog steeds acceptabele manieren om een label veld te declareren. De voorkeurs expressies zijn echter die in de bovenstaande lijst.

> [!NOTE]
> In **veld** expressies die verwijzen naar een **\[ \* \] alias**, wordt elk element in de matrix afzonderlijk geëvalueerd met logische **en** tussen elementen.
> Zie [verwijzing naar de bron eigenschappen](../how-to/author-policies-for-arrays.md#referencing-array-resource-properties)van een matrix voor meer informatie.

#### <a name="use-tags-with-parameters"></a>Tags gebruiken met para meters

Een parameter waarde kan worden door gegeven aan een label veld. Door een para meter door te geven aan een label veld, verhoogt u de flexibiliteit van de beleids definitie tijdens beleids toewijzing.

In het volgende voor beeld `concat` wordt gebruikt om een label veld te maken voor de tag met de naam de waarde van de para meter **tagName** . Als deze tag niet bestaat, wordt het **wijzigings** effect gebruikt om de tag toe te voegen met de waarde van dezelfde benoemde tag die is ingesteld voor de bovenliggende resource groep van de gecontroleerde resources met behulp van de `resourcegroup()` functie lookup.

```json
{
    "if": {
        "field": "[concat('tags[', parameters('tagName'), ']')]",
        "exists": "false"
    },
    "then": {
        "effect": "modify",
        "details": {
            "operations": [{
                "operation": "add",
                "field": "[concat('tags[', parameters('tagName'), ']')]",
                "value": "[resourcegroup().tags[parameters('tagName')]]"
            }],
            "roleDefinitionIds": [
                "/providers/microsoft.authorization/roleDefinitions/4a9ae827-6dc8-4573-8ac7-8239d42aa03f"
            ]
        }
    }
}
```

### <a name="value"></a>Waarde

Voor waarden die evalueren of een waarde aan bepaalde criteria voldoet, kunnen worden gevormd met behulp van een **waarde** -expressie. Waarden kunnen literals, de waarden van [para meters](#parameters)of de geretourneerde waarden van [ondersteunde sjabloon functies](#policy-functions)zijn.

> [!WARNING]
> Als het resultaat van een _sjabloonfunctie_ een fout is, mislukt de beleidsevaluatie. Een mislukte evaluatie is een impliciete **weigering**. Zie [sjabloonfouten vermijden](#avoiding-template-failures) voor meer informatie. Gebruik [enforcementMode](./assignment-structure.md#enforcement-mode) van **DoNotEnforce** om de impact van een mislukte evaluatie op nieuwe of bijgewerkte resources te voor komen tijdens het testen en valideren van een nieuwe beleids definitie.

#### <a name="value-examples"></a>Voor beelden van waarden

In dit voor beeld van een beleids regel wordt de **waarde** gebruikt om het resultaat van de `resourceGroup()` functie en de eigenschap geretourneerde **naam** te vergelijken met een **like** -voor waarde van `*netrg` . De regel weigert resources die niet van het `Microsoft.Network/*` **type** zijn in een resource groep waarvan de naam eindigt op `*netrg` .

```json
{
    "if": {
        "allOf": [{
                "value": "[resourceGroup().name]",
                "like": "*netrg"
            },
            {
                "field": "type",
                "notLike": "Microsoft.Network/*"
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
}
```

Dit voor beeld van een beleids regel gebruikt een **waarde** om te controleren of het resultaat van meerdere geneste functies **gelijk is aan** `true` . De regel weigert een resource die niet ten minste drie tags heeft.

```json
{
    "mode": "indexed",
    "policyRule": {
        "if": {
            "value": "[less(length(field('tags')), 3)]",
            "equals": "true"
        },
        "then": {
            "effect": "deny"
        }
    }
}
```

#### <a name="avoiding-template-failures"></a>Storingen in sjablonen voor komen

Het gebruik van _sjabloon functies_ in **waarde** biedt veel complexe geneste functies. Als het resultaat van een _sjabloonfunctie_ een fout is, mislukt de beleidsevaluatie. Een mislukte evaluatie is een impliciete **weigering**. Een voor beeld van een **waarde** die mislukt in bepaalde scenario's:

```json
{
    "policyRule": {
        "if": {
            "value": "[substring(field('name'), 0, 3)]",
            "equals": "abc"
        },
        "then": {
            "effect": "audit"
        }
    }
}
```

De voorbeeld beleidsregel hierboven maakt gebruik van [subtekenreeks ()](../../../azure-resource-manager/templates/template-functions-string.md#substring) om de eerste drie tekens van de **naam** te vergelijken met **ABC**. Als de **naam** korter is dan drie tekens, `substring()` resulteert de functie in een fout. Als gevolg van deze fout wordt het beleid **geweigerd** .

Gebruik in plaats daarvan de functie [als ()](../../../azure-resource-manager/templates/template-functions-logical.md#if) om te controleren of de eerste drie tekens van de **naam** gelijk zijn aan **ABC** zonder dat een **naam** die korter is dan drie tekens, een fout veroorzaakt:

```json
{
    "policyRule": {
        "if": {
            "value": "[if(greaterOrEquals(length(field('name')), 3), substring(field('name'), 0, 3), 'not starting with abc')]",
            "equals": "abc"
        },
        "then": {
            "effect": "audit"
        }
    }
}
```

Met de gereviseerde beleids regel `if()` controleert u de lengte van de **naam** voordat u probeert een `substring()` waarde op te halen die korter is dan drie tekens. Als de **naam** te kort is, wordt de waarde ' niet beginnend met ABC ' geretourneerd in plaats van **ABC**. Een resource met een korte naam die niet met **ABC** begint, mislukt nog steeds de beleids regel, maar veroorzaakt geen fout meer tijdens de evaluatie.

### <a name="count"></a>Count

Voor waarden die tellen hoeveel leden van een matrix voldoen aan bepaalde criteria, kunnen worden gevormd met behulp van een **Count** -expressie. Bij algemene scenario's wordt gecontroleerd of ten minste één van ', ' precies één van ', ' alle of ' geen van ' de matrix leden voldoen aan een voor waarde. Met **Count** wordt elk matrixlid geëvalueerd voor een voorwaarde expressie en worden de _werkelijke_ resultaten opgeteld, die vervolgens worden vergeleken met de expressie operator.

#### <a name="field-count"></a>Aantal velden

Tel hoeveel leden van een matrix in de aanvraag lading voldoen aan een voor waarde-expressie. De structuur van expressies voor **veld tellingen** is:

```json
{
    "count": {
        "field": "<[*] alias>",
        "where": {
            /* condition expression */
        }
    },
    "<condition>": "<compare the count of true condition expression array members to this value>"
}
```

De volgende eigenschappen worden gebruikt met het **aantal velden**:

- **Count. Field** (vereist): bevat het pad naar de matrix en moet een matrix alias zijn.
- **Count. where** (optioneel): de voor waarde-expressie die afzonderlijk moet worden geëvalueerd voor elk lid van de [ \[ \* \] alias](#understanding-the--alias) matrix `count.field` . Als deze eigenschap niet is ingevuld, worden alle matrix leden met het pad van ' Field ' geëvalueerd als _waar_. Elke [voor waarde](../concepts/definition-structure.md#conditions) kan worden gebruikt in deze eigenschap.
  [Logische Opera tors](#logical-operators) kunnen worden gebruikt in deze eigenschap om complexe evaluatie vereisten te maken.
- **\<condition\>** (vereist): de waarde wordt vergeleken met het aantal items dat aan het aantal is voldaan **. where** -voor waarde-expressie. Er moet een numerieke [voor waarde](../concepts/definition-structure.md#conditions) worden gebruikt.

Expressies voor **veld tellingen** kunnen dezelfde veld matrix Maxi maal drie keer in één **policyRule** definitie opsommen.

Zie [verwijzen naar eigenschappen van matrix bronnen](../how-to/author-policies-for-arrays.md#referencing-array-resource-properties)voor meer informatie over het werken met matrix eigenschappen in azure Policy, waaronder gedetailleerde uitleg over het evalueren van de expressie **aantal veld tellingen** .

#### <a name="value-count"></a>Aantal waarden
Tel hoeveel leden van een matrix aan een voor waarde voldoen. De matrix kan een letterlijke matrix of een [verwijzing naar een matrix parameter](#using-a-parameter-value)zijn. De structuur van expressies voor **aantal waarden** is:

```json
{
    "count": {
        "value": "<literal array | array parameter reference>",
        "name": "<index name>",
        "where": {
            /* condition expression */
        }
    },
    "<condition>": "<compare the count of true condition expression array members to this value>"
}
```

De volgende eigenschappen worden gebruikt met het **aantal waarden**:

- **aantal. Value** (vereist): de matrix die moet worden geëvalueerd.
- **Count.name** (vereist): de index naam, bestaande uit Engelse letters en cijfers. Hiermee wordt een naam gedefinieerd voor de waarde van het matrixlid geëvalueerd in de huidige iteratie. De naam wordt gebruikt voor het verwijzen naar de huidige waarde binnen de `count.where` voor waarde. Optioneel wanneer de **Count** -expressie zich niet in een onderliggend element van een andere **Count** -expressie bevindt. Indien niet opgegeven, wordt de index naam impliciet ingesteld op `"default"` .
- **Count. where** (optioneel): de voor waarde-expressie die afzonderlijk moet worden geëvalueerd voor elk lid van de matrix van `count.value` . Als deze eigenschap niet is ingesteld, worden alle matrix leden geëvalueerd als _waar_. Elke [voor waarde](../concepts/definition-structure.md#conditions) kan worden gebruikt in deze eigenschap. [Logische Opera tors](#logical-operators) kunnen worden gebruikt in deze eigenschap om complexe evaluatie vereisten te maken. De waarde van het momenteel genummerde matrixlid is toegankelijk door de [huidige](#the-current-function) functie aan te roepen.
- **\<condition\>** (vereist): de waarde wordt vergeleken met het aantal items dat voldoet aan de `count.where` voor waarde-expressie. Er moet een numerieke [voor waarde](../concepts/definition-structure.md#conditions) worden gebruikt.

De volgende limieten worden afgedwongen:
- Maxi maal tien **waarden** van expressies kunnen worden gebruikt in één **policyRule** definitie.
- Elke expressie voor **aantal waarden** kan maxi maal 100 iteraties uitvoeren. Dit nummer bevat het aantal iteraties dat wordt uitgevoerd door de expressies voor het **aantal bovenliggende waarden** .

#### <a name="the-current-function"></a>De huidige functie

De `current()` functie is alleen beschikbaar in de `count.where` voor waarde. Het retourneert de waarde van het matrixlid dat momenteel wordt opgesomd door de evaluatie van de **telling** -expressie.

**Gebruik van aantal waarden**

- `current(<index name defined in count.name>)`. Bijvoorbeeld: `current('arrayMember')`.
- `current()`. Alleen toegestaan als de expressie voor **aantal waarden** geen onderliggend item is van een andere **Count** -expressie. Retourneert dezelfde waarde als hierboven.

Als de waarde die door de aanroep wordt geretourneerd een object is, worden eigenschaps-toegangs rechten ondersteund. Bijvoorbeeld: `current('objectArrayMember').property`.

**Gebruik van veld aantal**

- `current(<the array alias defined in count.field>)`. Bijvoorbeeld `current('Microsoft.Test/resource/enumeratedArray[*]')`.
- `current()`. Alleen toegestaan als de expressie **aantal velden** is niet een onderliggend element van een andere **Count** -expressie. Retourneert dezelfde waarde als hierboven.
- `current(<alias of a property of the array member>)`. Bijvoorbeeld `current('Microsoft.Test/resource/enumeratedArray[*].property')`.

#### <a name="field-count-examples"></a>Voor beelden van veld tellingen

Voor beeld 1: controleren of een matrix leeg is

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]"
    },
    "equals": 0
}
```

Voor beeld 2: controleren op slechts één matrixlid om te voldoen aan de voor waarde-expressie

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]",
        "where": {
            "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].description",
            "equals": "My unique description"
        }
    },
    "equals": 1
}
```

Voor beeld 3: controleren of er ten minste één matrixlid is om te voldoen aan de voor waarde-expressie

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]",
        "where": {
            "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].description",
            "equals": "My common description"
        }
    },
    "greaterOrEquals": 1
}
```

Voor beeld 4: controleren of alle leden van een object Matrix voldoen aan de voor waarde-expressie

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]",
        "where": {
            "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].description",
            "equals": "description"
        }
    },
    "equals": "[length(field('Microsoft.Network/networkSecurityGroups/securityRules[*]'))]"
}
```

Voor beeld 5: Controleer of ten minste één matrixlid overeenkomt met meerdere eigenschappen in de voor waarde-expressie

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]",
        "where": {
            "allOf": [
                {
                    "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].direction",
                    "equals": "Inbound"
                },
                {
                    "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].access",
                    "equals": "Allow"
                },
                {
                    "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].destinationPortRange",
                    "equals": "3389"
                }
            ]
        }
    },
    "greater": 0
}
```

Voor beeld 6: gebruik de `current()` functie binnen de `where` voor waarden om toegang te krijgen tot de waarde van het momenteel genummerde matrixlid in een sjabloon functie. Met deze voor waarde wordt gecontroleerd of een virtueel netwerk een adres voorvoegsel bevat dat zich niet in het CIDR-bereik 10.0.0.0/24 bevindt.

```json
{
    "count": {
        "field": "Microsoft.Network/virtualNetworks/addressSpace.addressPrefixes[*]",
        "where": {
          "value": "[ipRangeContains('10.0.0.0/24', current('Microsoft.Network/virtualNetworks/addressSpace.addressPrefixes[*]'))]",
          "equals": false
        }
    },
    "greater": 0
}
```

Voor beeld 7: gebruik de `field()` functie binnen de `where` voor waarden om toegang te krijgen tot de waarde van het momenteel genummerde matrixlid. Met deze voor waarde wordt gecontroleerd of een virtueel netwerk een adres voorvoegsel bevat dat zich niet in het CIDR-bereik 10.0.0.0/24 bevindt.

```json
{
    "count": {
        "field": "Microsoft.Network/virtualNetworks/addressSpace.addressPrefixes[*]",
        "where": {
          "value": "[ipRangeContains('10.0.0.0/24', first(field(('Microsoft.Network/virtualNetworks/addressSpace.addressPrefixes[*]')))]",
          "equals": false
        }
    },
    "greater": 0
}
```

#### <a name="value-count-examples"></a>Voor beelden van aantal waarden

Voor beeld 1: controleren of de resource naam overeenkomt met een van de opgegeven naam patronen.

```json
{
    "count": {
        "value": [ "prefix1_*", "prefix2_*" ],
        "name": "pattern",
        "where": {
            "field": "name",
            "like": "[current('pattern')]"
        }
    },
    "greater": 0
}
```

Voor beeld 2: controleren of de resource naam overeenkomt met een van de opgegeven naam patronen. De `current()` functie geeft geen index naam op. De uitkomst is hetzelfde als in het vorige voor beeld.

```json
{
    "count": {
        "value": [ "prefix1_*", "prefix2_*" ],
        "where": {
            "field": "name",
            "like": "[current()]"
        }
    },
    "greater": 0
}
```

Voor beeld 3: controleren of de resource naam overeenkomt met een van de opgegeven naam patronen die door een matrix parameter worden geleverd.

```json
{
    "count": {
        "value": "[parameters('namePatterns')]",
        "name": "pattern",
        "where": {
            "field": "name",
            "like": "[current('pattern')]"
        }
    },
    "greater": 0
}
```

Voor beeld 4: Controleer of een van de adres voorvoegsels van het virtuele netwerk niet is opgenomen in de lijst met goedgekeurde voor voegsels.

```json
{
    "count": {
        "field": "Microsoft.Network/virtualNetworks/addressSpace.addressPrefixes[*]",
        "where": {
            "count": {
                "value": "[parameters('approvedPrefixes')]",
                "name": "approvedPrefix",
                "where": {
                    "value": "[ipRangeContains(current('approvedPrefix'), current('Microsoft.Network/virtualNetworks/addressSpace.addressPrefixes[*]'))]",
                    "equals": true
                },
            },
            "equals": 0
        }
    },
    "greater": 0
}
```

Voor beeld 5: Controleer of alle gereserveerde NSG-regels zijn gedefinieerd in een NSG. De eigenschappen van de gereserveerde NSG-regels worden gedefinieerd in een matrix parameter met objecten.

Parameter waarde:

```json
[
    {
        "priority": 101,
        "access": "deny",
        "direction": "inbound",
        "destinationPortRange": 22
    },
    {
        "priority": 102,
        "access": "deny",
        "direction": "inbound",
        "destinationPortRange": 3389
    }
]
```

Verslaggev
```json
{
    "count": {
        "value": "[parameters('reservedNsgRules')]",
        "name": "reservedNsgRule",
        "where": {
            "count": {
                "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]",
                "where": {
                    "allOf": [
                        {
                            "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].priority",
                            "equals": "[current('reservedNsgRule').priority]"
                        },
                        {
                            "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].access",
                            "equals": "[current('reservedNsgRule').access]"
                        },
                        {
                            "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].direction",
                            "equals": "[current('reservedNsgRule').direction]"
                        },
                        {
                            "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].destinationPortRange",
                            "equals": "[current('reservedNsgRule').destinationPortRange]"
                        }
                    ]
                }
            },
            "equals": 1
        }
    },
    "equals": "[length(parameters('reservedNsgRules'))]"
}
```

### <a name="effect"></a>Effect

Azure Policy ondersteunt de volgende typen effect:

- **Toevoegen**: Hiermee voegt u de gedefinieerde set velden toe aan de aanvraag
- **Controle**: er wordt een waarschuwings gebeurtenis gegenereerd in het activiteiten logboek, maar de aanvraag mislukt niet
- **AuditIfNotExists**: er wordt een waarschuwings gebeurtenis in het activiteiten logboek gegenereerd als een gerelateerde resource niet bestaat
- **Weigeren**: er wordt een gebeurtenis in het activiteiten logboek gegenereerd en de aanvraag mislukt
- **DeployIfNotExists**: implementeert een gerelateerde resource als deze nog niet bestaat
- **Uitgeschakeld**: resources worden niet geëvalueerd voor naleving van de beleids regel
- **Wijzigen**: de gedefinieerde labels worden toegevoegd, bijgewerkt of verwijderd uit een resource
- **EnforceOPAConstraint** (afgeschaft): Hiermee configureert u de open Policy Agent Admissions-controller met gate keeper v3 voor zelf-beheerde Kubernetes-clusters in azure
- **EnforceRegoPolicy** (afgeschaft): Hiermee configureert u de open Policy Agent Admissions-controller met gate keeper v2 in de Azure Kubernetes-service

Zie voor meer informatie over elk effect, de volg orde van evaluatie, eigenschappen en voor beelden [informatie over Azure Policy effecten](effects.md).

### <a name="policy-functions"></a>Beleids functies

Alle [Resource Manager-sjabloon functies](../../../azure-resource-manager/templates/template-functions.md) zijn beschikbaar voor gebruik in een beleids regel, met uitzonde ring van de volgende functies en door de gebruiker gedefinieerde functies:

- Functie copyindex ()
- implementatie ()
- orderverzamellijst
- newGuid()
- pickZones()
- providers ()
- verwijzing ()
- resourceId ()
- variabelen ()

> [!NOTE]
> Deze functies zijn nog steeds beschikbaar in het `details.deployment.properties.template` gedeelte van de sjabloon implementatie in een **deployIfNotExists** -beleids definitie.

De volgende functie is beschikbaar voor gebruik in een beleids regel, maar verschilt van gebruik in een Azure Resource Manager sjabloon (ARM-sjabloon):

- `utcNow()` -In tegens telling tot een ARM-sjabloon kan deze eigenschap buiten de _standaard waarde_ worden gebruikt.
  - Retourneert een teken reeks die is ingesteld op de huidige datum en tijd in de indeling van de Universal ISO 8601 DateTime `yyyy-MM-ddTHH:mm:ss.fffffffZ` .

De volgende functies zijn alleen beschikbaar in beleids regels:

- `addDays(dateTime, numberOfDaysToAdd)`
  - **DateTime**: [required] string-string in de indeling Universal ISO 8601 datetime ' jjjj-mm-ddTuu: mm: SS. FFFFFFFZ'
  - **numberOfDaysToAdd**: [vereist] geheel getal-aantal dagen dat moet worden toegevoegd
- `field(fieldName)`
  - **FieldName**: [required] string: naam van het [veld](#fields) dat moet worden opgehaald
  - Retourneert de waarde van het veld van de resource die wordt geëvalueerd door de if-voor waarde.
  - `field` wordt hoofd zakelijk gebruikt in combi natie met **AuditIfNotExists** en **DeployIfNotExists** om te verwijzen naar velden in de resource die worden geëvalueerd. Een voor beeld van dit gebruik is te zien in het [DeployIfNotExists-voor beeld](effects.md#deployifnotexists-example).
- `requestContext().apiVersion`
  - Retourneert de API-versie van de aanvraag waarvoor beleids evaluatie is geactiveerd (bijvoorbeeld: `2019-09-01` ).
    Deze waarde is de API-versie die is gebruikt in de PUT/PATCH-aanvraag voor evaluaties bij het maken of bijwerken van de resource. De nieuwste API-versie wordt altijd gebruikt tijdens de evaluatie van de naleving van bestaande resources.
- `policy()`
  - Retourneert de volgende informatie over het beleid dat wordt geëvalueerd. Eigenschappen zijn toegankelijk vanuit het geretourneerde object (bijvoorbeeld: `[policy().assignmentId]` ).
  
  ```json
  {
    "assignmentId": "/subscriptions/ad404ddd-36a5-4ea8-b3e3-681e77487a63/providers/Microsoft.Authorization/policyAssignments/myAssignment",
    "definitionId": "/providers/Microsoft.Authorization/policyDefinitions/34c877ad-507e-4c82-993e-3452a6e0ad3c",
    "setDefinitionId": "/providers/Microsoft.Authorization/policySetDefinitions/42a694ed-f65e-42b2-aa9e-8052e9740a92",
    "definitionReferenceId": "StorageAccountNetworkACLs"
  }
  ```

- `ipRangeContains(range, targetRange)`
    - **Range**: [required] string-teken reeks die een bereik van IP-adressen aangeeft.
    - **targetRange**: [vereist] teken reeks-teken reeks die een bereik van IP-adressen aangeeft.

    Retourneert of het opgegeven IP-adres bereik het doel-IP-adres bereik bevat. Lege bereiken of combi neren tussen IP-families is niet toegestaan en resulteert in een evaluatie fout.

    Ondersteunde indelingen:
    - Eén IP-adres (voor beelden: `10.0.0.0` , `2001:0DB8::3:FFFE` )
    - CIDR-bereik (voor beelden: `10.0.0.0/24` , `2001:0DB8::/110` )
    - Het bereik dat is gedefinieerd door de begin-en eind-IP-adressen (voor beelden: `192.168.0.1-192.168.0.9` , `2001:0DB8::-2001:0DB8::3:FFFF` )

- `current(indexName)`
    - Een speciale functie die alleen in [expressies in aantal](#count)kan worden gebruikt.

#### <a name="policy-function-example"></a>Voor beeld van beleids functie

In dit voor beeld van een beleids regel wordt de `resourceGroup` functie resource gebruikt voor het ophalen van de eigenschap **name** , gecombineerd met de `concat` functie Array en object, om een voor waarde op te bouwen `like` die de resource naam afdwingt om te beginnen met de naam van de resource groep.

```json
{
    "if": {
        "not": {
            "field": "name",
            "like": "[concat(resourceGroup().name,'*')]"
        }
    },
    "then": {
        "effect": "deny"
    }
}
```

## <a name="aliases"></a>Aliassen

U kunt eigenschaps aliassen gebruiken om toegang te krijgen tot specifieke eigenschappen voor een bron type. Met aliassen kunt u bepalen welke waarden of voor waarden zijn toegestaan voor een eigenschap van een resource. Elke alias wordt toegewezen aan paden in verschillende API-versies voor een bepaald bron type. Tijdens de beleids evaluatie haalt de beleids engine het pad naar de eigenschap voor die API-versie op.

De lijst met aliassen is altijd groeien. Als u wilt weten welke aliassen momenteel worden ondersteund door Azure Policy, gebruikt u een van de volgende methoden:

- Azure Policy-extensie voor Visual Studio code (aanbevolen)

  Gebruik de [extensie Azure Policy voor Visual Studio code](../how-to/extension-for-vscode.md) om aliassen voor resource-eigenschappen weer te geven en te detecteren.

  :::image type="content" source="../media/extension-for-vscode/extension-hover-shows-property-alias.png" alt-text="Scherm afbeelding van de uitbrei ding van de Azure Policy voor Visual Studio code die een eigenschap aanwijst om de alias namen weer te geven." border="false":::

- Azure PowerShell

  ```azurepowershell-interactive
  # Login first with Connect-AzAccount if not using Cloud Shell

  # Use Get-AzPolicyAlias to list available providers
  Get-AzPolicyAlias -ListAvailable

  # Use Get-AzPolicyAlias to list aliases for a Namespace (such as Azure Compute -- Microsoft.Compute)
  (Get-AzPolicyAlias -NamespaceMatch 'compute').Aliases
  ```

  > [!NOTE]
  > Als u aliassen wilt vinden die kunnen worden gebruikt met het [wijzigings](./effects.md#modify) effect, gebruikt u de volgende opdracht in azure PowerShell **4.6.0** of hoger:
  >
  > ```azurepowershell-interactive
  > Get-AzPolicyAlias | Select-Object -ExpandProperty 'Aliases' | Where-Object { $_.DefaultMetadata.Attributes -eq 'Modifiable' }
  > ```

- Azure CLI

  ```azurecli-interactive
  # Login first with az login if not using Cloud Shell

  # List namespaces
  az provider list --query [*].namespace

  # Get Azure Policy aliases for a specific Namespace (such as Azure Compute -- Microsoft.Compute)
  az provider show --namespace Microsoft.Compute --expand "resourceTypes/aliases" --query "resourceTypes[].aliases[].name"
  ```

- REST API-ARMClient

  ```http
  GET https://management.azure.com/providers/?api-version=2019-10-01&$expand=resourceTypes/aliases
  ```

### <a name="understanding-the--alias"></a>Informatie over de alias [*]

Verschillende van de aliassen die beschikbaar zijn, hebben een versie die wordt weer gegeven als een ' normale ' naam en een andere die hieraan is **\[\*\]** gekoppeld. Bijvoorbeeld:

- `Microsoft.Storage/storageAccounts/networkAcls.ipRules`
- `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]`

De ' normale ' alias vertegenwoordigt het veld als een enkele waarde. Dit veld is bedoeld voor vergelijkings scenario's die exact overeenkomen wanneer de volledige set waarden exact zo moet worden gedefinieerd, niet meer en niet minder.

De **\[\*\]** alias vertegenwoordigt een verzameling van waarden die zijn geselecteerd in de elementen van een matrix bron eigenschap. Bijvoorbeeld:

| Alias | Geselecteerde waarden |
|:---|:---|
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]` | De elementen van de `ipRules` matrix. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].action` | De waarden van de `action` eigenschap van elk element van de `ipRules` matrix. |

Bij gebruik in een [veld](#fields) voorwaarde maakt de matrix alias het mogelijk om elk afzonderlijk matrix element te vergelijken met een doel waarde. Als u with [Count](#count) -expressie gebruikt, is het volgende mogelijk:

- De grootte van een matrix controleren
- Controleren of de all\any\none van de matrix elementen voldoen aan een complexe voor waarde
- Controleren of precies ***n*** matrix elementen voldoen aan een complexe voor waarde

Zie [refererende matrix bron eigenschappen](../how-to/author-policies-for-arrays.md#referencing-array-resource-properties)voor meer informatie en voor beelden.

## <a name="next-steps"></a>Volgende stappen

- Zie de [definitie structuur](./initiative-definition-structure.md) van het initiatief
- Bekijk voor beelden op [Azure Policy voor beelden](../samples/index.md).
- Lees [Informatie over de effecten van het beleid](effects.md).
- Meer informatie over het [programmatisch maken van beleids regels](../how-to/programmatically-create.md).
- Meer informatie over het [ophalen van compatibiliteits gegevens](../how-to/get-compliance-data.md).
- Ontdek hoe u [niet-compatibele resources kunt herstellen](../how-to/remediate-resources.md).
- Bekijk wat een beheer groep is met [het organiseren van uw resources met Azure-beheer groepen](../../management-groups/overview.md).
