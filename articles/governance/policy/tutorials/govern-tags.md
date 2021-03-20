---
title: 'Zelfstudie: Tag governance beheren'
description: In deze zelfstudie gebruikt u het effect Modify van Azure Policy om een model voor tag-governance model te maken en af te dwingen voor nieuwe en bestaande resources.
ms.date: 10/05/2020
ms.topic: tutorial
ms.openlocfilehash: 9efeb27151cd3a32741f1bdb6d1d90d3304c5874
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91876274"
---
# <a name="tutorial-manage-tag-governance-with-azure-policy"></a>Zelfstudie: Tag-governance beheren met Azure Policy

[Tags](../../../azure-resource-manager/management/tag-resources.md) zijn een belangrijk onderdeel bij het organiseren van uw Azure-resources in een taxonomie. Wanneer u de [best practices voor tagbeheer](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#naming-and-tagging-resources) volgt, kunnen tags de basis vormen voor het toepassen van uw bedrijfsbeleid met Azure Policy of het [bijhouden van kosten met Cost Management](../../../cost-management-billing/costs/cost-mgt-best-practices.md#tag-shared-resources).
Ongeacht hoe of waarom u tags gebruikt, is het belangrijk dat u tags snel kunt toevoegen, wijzigen en verwijderen voor Azure-resources. Zie [Tagondersteuning](../../../azure-resource-manager/management/tag-support.md) als u wilt weten of een Azure-resource ondersteuning biedt voor tags.

Het effect [Modify](../concepts/effects.md#modify) van Azure Policy is ontworpen om u te helpen bij de governance van tags, ongeacht de fase van de resource-governance waarin u zich bevindt. **Modify** is handig in deze situaties:

- U hebt nog geen ervaring met de cloud en beschikt niet over tag-governance
- U hebt al duizenden resources zonder tag-governance
- U hebt al een bestaande taxonomie die u moet wijzigen

In deze zelfstudie voert u de volgende taken uit:

> [!div class="checklist"]
> - Identificeer de vereisten van uw bedrijf
> - Elke vereiste toewijzen aan een beleidsdefinitie
> - De tagbeleidsregels groeperen in een initiatief

## <a name="prerequisites"></a>Vereisten

U hebt een Azure-abonnement nodig om deze zelfstudie te voltooien. Als u nog geen abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="identify-requirements"></a>Vereisten identificeren

Net als bij een goede implementatie van beheeropties voor governance, moeten de vereisten zijn te herleiden naar uw bedrijfsbehoeften en moeten ze volledig duidelijk zijn voordat u technische beheeropties gaat maken. Voor het scenario in deze zelfstudie zijn de volgende items de vereisten van ons bedrijf:

- Twee vereiste tags voor alle resources: _CostCenter_ en _Env_
- _CostCenter_ moet bestaan voor alle containers en afzonderlijke resources
  - Resources nemen kenmerken over van de container waarin ze zich bevinden, maar deze kunnen afzonderlijk worden overschreven
- _Env_ moet bestaan voor alle containers en afzonderlijke resources
  - Resources bepalen de omgeving aan de hand van het naamgevingsschema voor containers en mogen niet worden overschreven
  - Alle resources in een container maken deel uit van dezelfde omgeving

## <a name="configure-the-costcenter-tag"></a>De CostCenter-tag configureren

In terminologie die specifiek is voor een Azure-omgeving die wordt beheerd met Azure Policy, gelden de volgende uitkomsten voor de tag _CostCenter_:

- Resourcegroepen weigeren die geen tag _CostCenter_ hebben
- Resources wijzigen om de tag _CostCenter_ toe te voegen vanuit de bovenliggende resourcegroep wanneer de tag ontbreekt

### <a name="deny-resource-groups-missing-the-costcenter-tag"></a>Resourcegroepen weigeren die geen tag CostCenter hebben

Aangezien de _CostCenter_ (kostenplaats) voor een resourcegroep niet kan worden bepaald door de naam van de resourcegroep, moet de tag zijn gedefinieerd in de aanvraag om de resourcegroep te maken. Met de volgende beleidsregel met het effect [Deny](../concepts/effects.md#deny) wordt voorkomen dat er resourcegroepen worden gemaakt of bijgewerkt die geen tag _CostCenter_ hebben:

```json
"if": {
    "allOf": [{
            "field": "type",
            "equals": "Microsoft.Resources/subscriptions/resourceGroups"
        },
        {
            "field": "tags['CostCenter']",
            "exists": false
        }
    ]
},
"then": {
    "effect": "deny"
}
```

> [!NOTE]
> Omdat deze beleidsregel gericht is op een resourcegroep, moet de _mode_ in de beleidsdefinitie 'All' zijn in plaats van 'Indexed'.

### <a name="modify-resources-to-inherit-the-costcenter-tag-when-missing"></a>Resources wijzigen om de CostCenter-tag over te nemen als deze ontbreekt

De tweede vereiste voor _CostCenter_ is dat resources de tag van de bovenliggende resourcegroep moeten overnemen wanneer deze ontbreekt. Als de tag al is gedefinieerd voor de resource, moet deze ongewijzigd blijven, zelfs als de tag afwijkt van die van de bovenliggende resourcegroep. In de volgende beleidsregel wordt het effect [Modify](../concepts/effects.md#modify) gebruikt:

```json
"policyRule": {
    "if": {
        "field": "tags['CostCenter']",
        "exists": "false"
    },
    "then": {
        "effect": "modify",
        "details": {
            "roleDefinitionIds": [
                "/providers/microsoft.authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
            ],
            "operations": [{
                "operation": "add",
                "field": "tags['CostCenter']",
                "value": "[resourcegroup().tags['CostCenter']]"
            }]
        }
    }
}
```

In deze beleidsregel wordt de bewerking **add** gebruikt in plaats van **addOrReplace**, aangezien we de waarde van de tag niet willen wijzigen als deze aanwezig is wanneer we bestaande resources gaan [herstellen](../how-to/remediate-resources.md). In de regel wordt ook de sjabloonfunctie `[resourcegroup()]` gebruikt om de tagwaarde van de bovenliggende resourcegroep op te vragen.

> [!NOTE]
> Omdat deze beleidsregel gericht is op resources die tags ondersteunen, moet de _mode_ in de beleidsdefinitie 'Indexed' zijn. Deze configuratie zorgt er ook voor resourcegroepen worden overgeslagen door dit beleid.

## <a name="configure-the-env-tag"></a>De Env-tag configureren

In terminologie die specifiek is voor een Azure-omgeving die wordt beheerd met Azure Policy, gelden de volgende uitkomsten voor de tag _Env_:

- De tag _Env_ wijzigen voor de resourcegroep op basis van het naamgevingsschema van de resourcegroep
- De tag _Env_ voor alle resources in de resourcegroep wijzigen in de tag van de bovenliggende resourcegroep

### <a name="modify-resource-groups-env-tag-based-on-name"></a>De Env-tag van resourcegroepen wijzigen op basis van de naam

Er is een [Modify](../concepts/effects.md#modify)-beleid vereist voor elke omgeving die in uw Azure-omgeving bestaat. Het beleid Modify voor elke omgeving ziet er ongeveer uit zoals deze beleidsdefinitie:

```json
"policyRule": {
    "if": {
        "allOf": [{
            "field": "type",
            "equals": "Microsoft.Resources/subscriptions/resourceGroups"
        },
        {
            "field": "name",
            "like": "prd-*"
        },
        {
            "field": "tags['Env']",
            "notEquals": "Production"
        }

    ]
    },
    "then": {
        "effect": "modify",
        "details": {
            "roleDefinitionIds": [
                "/providers/microsoft.authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
            ],
            "operations": [{
                "operation": "addOrReplace",
                "field": "tags['Env']",
                "value": "Production"
            }]
        }
    }
}
```

> [!NOTE]
> Omdat deze beleidsregel gericht is op een resourcegroep, moet de _mode_ in de beleidsdefinitie 'All' zijn in plaats van 'Indexed'.

Dit beleid komt alleen overeen met resourcegroepen met het voorbeeldnaamgevingsschema dat wordt gebruikt voor productieresources `prd-`. Er kan een complexer naamgevingsschema worden gehanteerd met verschillende **match**-voorwaarden in plaats van slechts één **like** zoals in dit voorbeeld.

### <a name="modify-resources-to-inherit-the-env-tag"></a>Resources wijzigen om de Env-tag over te nemen

Om aan de bedrijfsvereiste te voldoen, moeten alle resources dezelfde tag _Env_ hebben als de bovenliggende resourcegroep. Deze tag kan niet worden overschreven en daarom gebruiken we de bewerking **addOrReplace** met het effect [Modify](../concepts/effects.md#modify). Het voorbeeld van het beleid Modify ziet eruit zoals de volgende regel:

```json
"policyRule": {
    "if": {
        "anyOf": [{
            "field": "tags['Env']",
            "notEquals": "[resourcegroup().tags['Env']]"
        },
        {
            "field": "tags['Env']",
            "exists": false
        }
    ]
    },
    "then": {
        "effect": "modify",
        "details": {
            "roleDefinitionIds": [
                "/providers/microsoft.authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
            ],
            "operations": [{
                "operation": "addOrReplace",
                "field": "tags['Env']",
                "value": "[resourcegroup().tags['Env']]"
            }]
        }
    }
}
```

> [!NOTE]
> Omdat deze beleidsregel gericht is op resources die tags ondersteunen, moet de _mode_ in de beleidsdefinitie 'Indexed' zijn. Deze configuratie zorgt er ook voor resourcegroepen worden overgeslagen door dit beleid.

Deze beleidsregel zoekt naar resources waarvan de waarde voor de tag _Env_ niet overeenkomt met de waarde voor de tag _Env_ van de bovenliggende resourcegroepen of waar de tag ontbreekt. Voor overeenkomende resources wordt de tag _Env_ ingesteld op de waarde van de tag van de bovenliggende resourcegroep, zelfs als de tag al in de resource aanwezig is maar een andere waarde heeft.

## <a name="assign-the-initiative-and-remediate-resources"></a>Het initiatief toewijzen en resources herstellen

Zodra de bovenstaande beleidsregels voor tags zijn gemaakt, voegt u ze samen in één initiatief voor tag-governance en wijst u ze toe aan een beheergroep of abonnement. Het initiatief en de daarin opgenomen beleidsregels evalueren vervolgens de naleving van bestaande resources en wijzigt aanvragen voor nieuwe of bijgewerkte resources die overeenkomen met de eigenschap **if** in de beleidsregel. Het is echter niet zo dat met het beleid bestaande, niet-conforme resources automatisch worden bijgewerkt met de gedefinieerde tagwijzigingen.

Net als bij een [deployIfNotExists](../concepts/effects.md#deployifnotexists)-beleid, gebruikt het beleid **Modify** hersteltaken om bestaande, niet-conforme resources te wijzigen. Volg de instructies in [Resources herstellen](../how-to/remediate-resources.md) om uw niet-conforme **Modify**-resources te identificeren en de tags voor uw gedefinieerde taxonomie te corrigeren.

## <a name="clean-up-resources"></a>Resources opschonen

Als u niet wilt doorgaan, gebruikt u de volgende stappen om alle bovenstaande toewijzingen of definities te verwijderen:

1. Selecteer **Definities** (of **Toewijzingen** als u een toewijzing wilt verwijderen) onder **Ontwerpen** in het linkerdeelvenster van de Azure Policy-pagina.

1. Zoek naar de nieuwe initiatief- of beleidsdefinitie (of toewijzing) die u zojuist hebt gemaakt.

1. Klik met de rechtermuisknop op de rij of selecteer de weglatingstekens aan het einde van de definitie (of de toewijzing) en selecteer **Definitie verwijderen** (of **Toewijzing verwijderen**).

## <a name="review"></a>Beoordelen

In deze zelfstudie is aandacht besteed aan de volgende taken:

> [!div class="checklist"]
> - De vereisten van uw bedrijf geïdentificeerd
> - Elke vereiste toewijzen aan een beleidsdefinitie
> - De tagbeleidsregels groeperen in een initiatief

## <a name="next-steps"></a>Volgende stappen

Lees het volgende artikel voor meer informatie over de structuur van beleidsdefinities:

> [!div class="nextstepaction"]
> [Structuur van Azure Policy-definities](../concepts/definition-structure.md)