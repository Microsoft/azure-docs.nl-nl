---
title: Details van de structuur van de beleidstoewijzing
description: Beschrijft de beleidstoewijzingsdefinitie die door een Azure Policy beleidsdefinities en parameters te relateren aan resources voor evaluatie.
ms.date: 04/14/2021
ms.topic: conceptual
ms.openlocfilehash: 9de210b17264330e79ab5978a449e7a494054be2
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107535868"
---
# <a name="azure-policy-assignment-structure"></a>Azure Policy-toewijzingsstructuur

Beleidstoewijzingen worden door Azure Policy om te definiëren aan welke resources welk beleid of welke initiatieven worden toegewezen. De beleidstoewijzing kan de waarden van parameters voor die groep resources tijdens de toewijzing bepalen, waardoor beleidsdefinities die zijn gericht op dezelfde resource-eigenschappen met verschillende nalevingsbehoeften, opnieuw kunnen worden gebruikt.

U gebruikt JSON om een beleidstoewijzing te maken. De beleidstoewijzing bevat elementen voor:

- weergavenaam
- beschrijving
- metagegevens
- afdwingingsmodus
- uitgesloten scopes
- beleidsdefinitie
- niet-nalevingsberichten
- parameters

De volgende JSON toont bijvoorbeeld een beleidstoewijzing in _de DoNotEnforce-modus_ met dynamische parameters:

```json
{
    "properties": {
        "displayName": "Enforce resource naming rules",
        "description": "Force resource names to begin with DeptA and end with -LC",
        "metadata": {
            "assignedBy": "Cloud Center of Excellence"
        },
        "enforcementMode": "DoNotEnforce",
        "notScopes": [],
        "policyDefinitionId": "/subscriptions/{mySubscriptionID}/providers/Microsoft.Authorization/policyDefinitions/ResourceNaming",
        "nonComplianceMessages": [
            {
                "message": "Resource names must start with 'DeptA' and end with '-LC'."
            }
        ],
        "parameters": {
            "prefix": {
                "value": "DeptA"
            },
            "suffix": {
                "value": "-LC"
            }
        }
    }
}
```

Alle Azure Policy staan op [Azure Policy voorbeelden.](../samples/index.md)

## <a name="display-name-and-description"></a>Weergavenaam en beschrijving

U gebruikt **displayName** en **description om** de beleidstoewijzing te identificeren en context te bieden voor het gebruik ervan met de specifieke set resources. **displayName** heeft een maximale lengte _van 128_ tekens en **een beschrijving** met een maximale lengte _van 512_ tekens.

## <a name="metadata"></a>Metagegevens

De `metadata` optionele eigenschap slaat informatie over de beleidstoewijzing op. Klanten kunnen eigenschappen en waarden definiëren die nuttig zijn voor hun organisatie in `metadata` . Er worden echter enkele algemene _eigenschappen_ gebruikt door Azure Policy. Elke `metadata` eigenschap heeft een limiet van 1024 tekens.

### <a name="common-metadata-properties"></a>Algemene metagegevenseigenschappen

- `assignedBy` (tekenreeks): de gebruiksvriendelijke naam van de beveiligingsprincipaal die de toewijzing heeft gemaakt.
- `createdBy` (tekenreeks): de GUID van de beveiligingsprincipaal die de toewijzing heeft gemaakt.
- `createdOn` (tekenreeks): de Universele ISO 8601-datum/tijd-indeling van de aanmaaktijd van de toewijzing.
- `parameterScopes` (object): een verzameling sleutel-waardeparen waarbij de sleutel overeenkomt met een geconfigureerde parameternaam [strongType](./definition-structure.md#strongtype) en de waarde het resourcebereik definieert dat in portal wordt gebruikt om de lijst met beschikbare resources op te geven door te voldoen aan _strongType_. Portal stelt deze waarde in als het bereik anders is dan het toewijzingsbereik. Als deze optie is ingesteld, wordt met een bewerking van de beleidstoewijzing in portal het bereik voor de parameter automatisch ingesteld op deze waarde. Het bereik is echter niet vergrendeld voor de waarde en kan worden gewijzigd in een ander bereik.

  Het volgende voorbeeld van is voor een strongType-parameter met de naam `parameterScopes` **backupPolicyId** die een bereik voor resourceselectie in stelt wanneer de toewijzing wordt bewerkt in de portal. 

  ```json
  "metadata": {
      "parameterScopes": {
          "backupPolicyId": "/subscriptions/{SubscriptionID}/resourcegroups/{ResourceGroupName}"
      }
  }
  ```

- `updatedBy` (tekenreeks): de gebruiksvriendelijke naam van de beveiligingsprincipaal die de toewijzing heeft bijgewerkt, indien van u.
- `updatedOn` (tekenreeks): de Universele ISO 8601-datum/tijd-indeling van de updatetijd van de toewijzing, indien van deze.

## <a name="enforcement-mode"></a>Afdwingingsmodus

De **eigenschap enforcementMode** biedt klanten de mogelijkheid om het resultaat van een beleid op bestaande resources te testen zonder het beleidseffect te initiëren of vermeldingen in het [Azure-activiteitenlogboek te activeren.](../../../azure-monitor/essentials/platform-logs-overview.md) Dit scenario wordt vaak aangeduid als 'What If' en is afgestemd op veilige implementatiemethoden. **enforcementMode** verschilt van het [effect Uitgeschakeld,](./effects.md#disabled) omdat dit tot gevolg heeft dat de resource helemaal niet kan worden geëvalueerd.

Deze eigenschap heeft de volgende waarden:

|Modus |JSON-waarde |Type |Handmatig herstellen |Vermelding van activiteitenlogboek |Beschrijving |
|-|-|-|-|-|-|
|Ingeschakeld |Standaard |tekenreeks |Ja |Ja |Het beleidseffect wordt afgedwongen tijdens het maken of bijwerken van resources. |
|Uitgeschakeld |DoNotEnforce |tekenreeks |Ja |Nee | Het beleidseffect wordt niet afgedwongen tijdens het maken of bijwerken van resources. |

Als **enforcementMode** niet is opgegeven in een beleids- of initiatiefdefinitie, wordt de waarde _Standaard_ gebruikt. [Hersteltaken kunnen worden gestart](../how-to/remediate-resources.md) voor [deployIfNotExists-beleid,](./effects.md#deployifnotexists) zelfs wanneer **enforcementMode** is ingesteld _op DoNotEnforce._

## <a name="excluded-scopes"></a>Uitgesloten scopes

Het **bereik** van de toewijzing omvat alle onderliggende resourcecontainers en onderliggende resources. Als de definitie niet moet worden toegepast op een onderliggende resourcecontainer of onderliggende resource, kan elke container worden uitgesloten van evaluatie door  **notScopes in te stellen.** Deze eigenschap is een matrix om een of meer resourcecontainers of resources uit te sluiten van evaluatie. **notScopes kan** worden toegevoegd of bijgewerkt na het maken van de eerste toewijzing.

> [!NOTE]
> Een _uitgesloten_ resource wijkt af van een _uitgesloten_ resource. Zie Bereik begrijpen in Azure Policy voor [meer Azure Policy.](./scope.md)

## <a name="policy-definition-id"></a>Beleidsdefinitie-id

Dit veld moet de volledige padnaam van een beleidsdefinitie of een initiatiefdefinitie zijn.
`policyDefinitionId` is een tekenreeks en geen matrix. Het is raadzaam om in plaats daarvan een initiatief te gebruiken als er vaak meerdere beleidsregels tegelijk [worden](./initiative-definition-structure.md) toegewezen.

## <a name="non-compliance-messages"></a>Niet-nalevingsberichten

Als u een aangepast bericht wilt instellen waarin wordt beschreven waarom een resource niet compatibel is met het beleid of de initiatiefdefinitie, stelt u `nonComplianceMessages` in de toewijzingsdefinitie in. Dit knooppunt is een matrix `message` met vermeldingen. Dit aangepaste bericht is een aanvulling op het standaardfoutbericht voor niet-naleving en is optioneel.

> [!IMPORTANT]
> Aangepaste berichten voor niet-naleving worden alleen ondersteund voor definities of initiatieven met [Resource Manager modi.](./definition-structure.md#resource-manager-modes)

```json
"nonComplianceMessages": [
    {
        "message": "Default message"
    }
]
```

Als de toewijzing voor een initiatief is, kunnen verschillende berichten worden geconfigureerd voor elke beleidsdefinitie in het initiatief. De berichten gebruiken de `policyDefinitionReferenceId` waarde die is geconfigureerd in de initiatiefdefinitie. Zie eigenschappen van [beleidsdefinities voor meer informatie.](./initiative-definition-structure.md#policy-definition-properties)

```json
"nonComplianceMessages": [
    {
        "message": "Default message"
    },
    {
        "message": "Message for just this policy definition by reference ID",
        "policyDefinitionReferenceId": "10420126870854049575"
    }
]
```

## <a name="parameters"></a>Parameters

Dit segment van de beleidstoewijzing bevat de waarden voor de parameters die zijn gedefinieerd in de [beleidsdefinitie of initiatiefdefinitie](./definition-structure.md#parameters). Dit ontwerp maakt het mogelijk om een beleids- of initiatiefdefinitie opnieuw te gebruiken met verschillende resources, maar om te controleren op verschillende bedrijfswaarden of -resultaten.

```json
"parameters": {
    "prefix": {
        "value": "DeptA"
    },
    "suffix": {
        "value": "-LC"
    }
}
```

In dit voorbeeld zijn de parameters die eerder zijn gedefinieerd in de beleidsdefinitie `prefix` en `suffix` . Deze specifieke beleidstoewijzing stelt `prefix` in **op DeptA** en `suffix` op **-LC.** Dezelfde beleidsdefinitie kan opnieuw worden gebruikt met een andere set parameters voor een andere afdeling, waardoor de duplicatie en complexiteit van beleidsdefinities worden verkleind en tegelijkertijd flexibiliteit wordt bieden.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [structuur van beleidsdefinitie.](./definition-structure.md)
- Begrijpen hoe u [programmatisch beleid kunt maken.](../how-to/programmatically-create.md)
- Meer informatie over het [op halen van nalevingsgegevens.](../how-to/get-compliance-data.md)
- Ontdek hoe u [niet-compatibele resources kunt herstellen](../how-to/remediate-resources.md).
- Bekijk wat een beheergroep is met [Uw resources organiseren met Azure-beheergroepen.](../../management-groups/overview.md)
