---
title: Bron vergrendeling begrijpen
description: Meer informatie over de vergrendelings opties in azure blauw drukken om resources te beveiligen wanneer u een blauw druk toewijst.
ms.date: 01/27/2021
ms.topic: conceptual
ms.openlocfilehash: b2004ad294ae0eec1b4f2fc6f49308efd32d652e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98920187"
---
# <a name="understand-resource-locking-in-azure-blueprints"></a>Meer informatie over het vergren delen van resources in azure-blauw drukken

Het maken van consistente omgevingen op schaal is alleen echt waardevol als er een mechanisme is om die consistentie te hand haven. In dit artikel wordt uitgelegd hoe resource vergrendeling werkt in azure-blauw drukken. Zie de zelf studie [nieuwe resources beveiligen](../tutorials/protect-new-resources.md) voor een voor beeld van het vergren delen van resources en het Toep assen van _toewijzingen weigeren_.

> [!NOTE]
> Resource vergrendelingen die worden geïmplementeerd door Azure-blauw drukken worden alleen toegepast op resources die worden geïmplementeerd door de toewijzing van de blauw druk. Bestaande resources, zoals resources in resource groepen die al bestaan, hebben geen vergren delingen toegevoegd.

## <a name="locking-modes-and-states"></a>Vergrendelings modi en statussen

Vergrendelings modus is van toepassing op de blauw druk-toewijzing en er zijn drie opties: **niet vergren delen**, **alleen-lezen** of **niet verwijderen**. De vergrendelings modus wordt tijdens de toewijzing van een blauw druk geconfigureerd tijdens de implementatie van artefacten. Een andere vergrendelings modus kan worden ingesteld door de toewijzing van de blauw druk bij te werken.
Vergrendelings modi kunnen echter niet worden gewijzigd buiten Azure-blauw drukken.

Resources die zijn gemaakt door artefacten in een blauw druk-toewijzing, hebben vier statussen: **niet vergrendeld**, **alleen-lezen**, **niet bewerken/verwijderen** of **kan niet verwijderen**. Elk type artefact kan de status **niet vergrendeld** hebben. De volgende tabel kan worden gebruikt om de status van een resource te bepalen:

|Modus|Bron type voor artefacten|Staat|Beschrijving|
|-|-|-|-|
|Niet vergren delen|*|Niet vergrendeld|Resources worden niet beveiligd door Azure-blauw drukken. Deze status wordt ook gebruikt voor resources die worden toegevoegd aan een **alleen-lezen** -of **verwijderings** artefact van een resource buiten een blauw druk-toewijzing.|
|Alleen-lezen|Resourcegroep|Kan niet bewerken/verwijderen|De resource groep is alleen-lezen en tags op de resource groep kunnen niet worden gewijzigd. **Niet-vergrendelde** resources kunnen worden toegevoegd, verplaatst, gewijzigd of verwijderd uit deze resource groep.|
|Alleen-lezen|Niet-resource groep|Alleen-lezen|De resource kan op geen enkele manier worden gewijzigd. Geen wijzigingen en kan niet worden verwijderd.|
|Niet verwijderen|*|Kan niet verwijderen|De resources kunnen worden gewijzigd, maar kunnen niet worden verwijderd. **Niet-vergrendelde** resources kunnen worden toegevoegd, verplaatst, gewijzigd of verwijderd uit deze resource groep.|

## <a name="overriding-locking-states"></a>Vergrendelings status negeren

Het is doorgaans mogelijk dat iemand met de juiste op [rollen gebaseerd toegangs beheer van Azure (Azure RBAC)](../../../role-based-access-control/overview.md) voor het abonnement, zoals de rol ' eigenaar ', toestemming mag geven om resources te wijzigen of te verwijderen. Deze toegang is niet het geval wanneer Azure-blauw drukken wordt toegepast op vergren deling als onderdeel van een geïmplementeerde toewijzing. Als de toewijzing is ingesteld met de optie **alleen-lezen** of **niet verwijderen** , niet zelfs de eigenaar van het abonnement kan de geblokkeerde actie uitvoeren op de beveiligde bron.

Deze beveiligings maatregel beveiligt de consistentie van de gedefinieerde blauw druk en de omgeving die is ontworpen om te worden gemaakt op basis van per ongeluk of programmatische verwijdering of wijziging.

### <a name="assign-at-management-group"></a>Toewijzen aan beheer groep

De enige optie om te voor komen dat abonnements eigenaren een blauw druk-toewijzing verwijderen, is het toewijzen van de blauw druk aan een beheer groep. In dit scenario hebben alleen **eigen aars** van de beheer groep de benodigde machtigingen voor het verwijderen van de blauw druk-toewijzing.

Als u de blauw druk wilt toewijzen aan een beheer groep in plaats van een abonnement, verandert de REST API aanroepen als volgt:

```http
PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{assignmentMG}/providers/Microsoft.Blueprint/blueprintAssignments/{assignmentName}?api-version=2018-11-01-preview
```

De beheer groep die is gedefinieerd door, `{assignmentMG}` moet zich binnen de beheer groeps hiërarchie bevindt of dezelfde beheer groep zijn waar de blauw druk-definitie wordt opgeslagen.

De aanvraag tekst van de blauw druk-toewijzing ziet er als volgt uit:

```json
{
    "identity": {
        "type": "SystemAssigned"
    },
    "location": "eastus",
    "properties": {
        "description": "enforce pre-defined simpleBlueprint to this XXXXXXXX subscription.",
        "blueprintId": "/providers/Microsoft.Management/managementGroups/{blueprintMG}/providers/Microsoft.Blueprint/blueprints/simpleBlueprint",
        "scope": "/subscriptions/{targetSubscriptionId}",
        "parameters": {
            "storageAccountType": {
                "value": "Standard_LRS"
            },
            "costCenter": {
                "value": "Contoso/Online/Shopping/Production"
            },
            "owners": {
                "value": [
                    "johnDoe@contoso.com",
                    "johnsteam@contoso.com"
                ]
            }
        },
        "resourceGroups": {
            "storageRG": {
                "name": "defaultRG",
                "location": "eastus"
            }
        }
    }
}
```

Het belangrijkste verschil in deze aanvraag tekst en een wordt toegewezen aan een abonnement is de `properties.scope` eigenschap. Deze vereiste eigenschap moet worden ingesteld op het abonnement waarop de blauw druk-toewijzing van toepassing is. Het abonnement moet een direct onderliggend element zijn van de beheer groeps hiërarchie waarin de blauw druk-toewijzing wordt opgeslagen.

> [!NOTE]
> Een blauw druk die is toegewezen aan het beheer groeps bereik, blijft de toewijzing van een blauw druk op abonnements niveau. Het enige verschil is waar de blauw druk toewijzing wordt opgeslagen om te voor komen dat abonnements eigenaren de toewijzing en de bijbehorende vergren delingen verwijderen.

## <a name="removing-locking-states"></a>Vergrendelings status verwijderen

Als het nodig is om een resource die wordt beveiligd door een toewijzing, te wijzigen of te verwijderen, kunt u dit op twee manieren doen.

- De blauw druk-toewijzing bijwerken naar een vergrendelings modus **zonder vergren deling**
- De blauw druk-toewijzing verwijderen

Wanneer de toewijzing wordt verwijderd, worden de vergren delingen die zijn gemaakt door Azure blauw drukken verwijderd. De resource blijft echter achter en moet worden verwijderd via normale manier.

## <a name="how-blueprint-locks-work"></a>Hoe blauw drukken werkt

Een actie voor weigeren voor het weigeren van een [Azure RBAC-toewijzing wordt](../../../role-based-access-control/deny-assignments.md) toegepast op artefact resources tijdens de toewijzing van een blauw druk als de toewijzing de optie **alleen-lezen** of **niet verwijderen** is geselecteerd. De actie voor weigeren wordt toegevoegd door de beheerde identiteit van de blauw druk toewijzing en kan alleen worden verwijderd uit de artefact resources met dezelfde beheerde identiteit. Deze beveiligings meting dwingt het vergrendelings mechanisme af en voor komt het verwijderen van de blauw druk buiten Azure blauw drukken.

:::image type="content" source="../media/resource-locking/blueprint-deny-assignment.png" alt-text="Scherm afbeelding van de pagina toegangs beheer (I A M) en het tabblad Toewijzingen weigeren voor een resource groep." border="false":::

De [Eigenschappen](../../../role-based-access-control/deny-assignments.md#deny-assignment-properties) voor het weigeren van toewijzingen van elke modus zijn als volgt:

|Modus |Machtigingen. acties |Machtigingen. intact |Principals [i]. Voert |ExcludePrincipals [i]. Id | DoNotApplyToChildScopes |
|-|-|-|-|-|-|
|Alleen-lezen |**\** _ |_ *\* /Read **<br />** micro soft. Authorization/vergren delen/verwijderen/ **<br />** micro soft. netwerk/virtualNetwork/subnets/toevoegen/actie** |SystemDefined (iedereen) |blauw druk toewijzen en door de gebruiker gedefinieerd in **excludedPrincipals** |Resource groep- _True_; Resource- _False_ |
|Niet verwijderen |**\*/Delete** | **Micro soft. autorisatie/vergren delen/verwijderen**<br />**Micro soft. Network/virtualNetwork/subnetten/toevoegen/actie** |SystemDefined (iedereen) |blauw druk toewijzen en door de gebruiker gedefinieerd in **excludedPrincipals** |Resource groep- _True_; Resource- _False_ |

> [!IMPORTANT]
> Met Azure Resource Manager worden de gegevens van de roltoewijzing Maxi maal 30 minuten in de cache opgeslagen. Als gevolg hiervan is het weigeren van de toewijzingen voor het weigeren van een actie op blauw drukken-resources mogelijk niet onmiddellijk volledig van kracht. Tijdens deze periode is het mogelijk om een resource te verwijderen die is bedoeld om te worden beveiligd door de vergren delingen van blauw drukken.

## <a name="exclude-a-principal-from-a-deny-assignment"></a>Een principal uitsluiten van een weiger toewijzing

In sommige ontwerp-of beveiligings scenario's kan het nodig zijn om een principal uit te sluiten van de [toewijzing weigeren](../../../role-based-access-control/deny-assignments.md) die door de toewijzing van blauw drukken wordt gemaakt. Deze stap wordt uitgevoerd in REST API door Maxi maal vijf waarden toe te voegen aan de **excludedPrincipals** -matrix **bij het** [maken van de toewijzing](/rest/api/blueprints/assignments/createorupdate). De volgende toewijzings definitie is een voor beeld van een aanvraag tekst die **excludedPrincipals** bevat:

```json
{
  "identity": {
    "type": "SystemAssigned"
  },
  "location": "eastus",
  "properties": {
    "description": "enforce pre-defined simpleBlueprint to this XXXXXXXX subscription.",
    "blueprintId": "/providers/Microsoft.Management/managementGroups/{mgId}/providers/Microsoft.Blueprint/blueprints/simpleBlueprint",
    "locks": {
        "mode": "AllResourcesDoNotDelete",
        "excludedPrincipals": [
            "7be2f100-3af5-4c15-bcb7-27ee43784a1f",
            "38833b56-194d-420b-90ce-cff578296714"
        ]
    },
    "parameters": {
      "storageAccountType": {
        "value": "Standard_LRS"
      },
      "costCenter": {
        "value": "Contoso/Online/Shopping/Production"
      },
      "owners": {
        "value": [
          "johnDoe@contoso.com",
          "johnsteam@contoso.com"
        ]
      }
    },
    "resourceGroups": {
      "storageRG": {
        "name": "defaultRG",
        "location": "eastus"
      }
    }
  }
}
```

## <a name="exclude-an-action-from-a-deny-assignment"></a>Een actie uitsluiten van een weiger toewijzing

Net als bij het uitsluiten van [een principal](#exclude-a-principal-from-a-deny-assignment) bij een [toewijzing weigeren](../../../role-based-access-control/deny-assignments.md) in een blauw druk-toewijzing, kunt u specifieke bewerkingen van de [Azure-resource provider](../../../role-based-access-control/resource-provider-operations.md)uitsluiten. Binnen het blok **Properties. Locks** , op dezelfde locatie die **excludedPrincipals** is, kan een **excludedActions** worden toegevoegd:

```json
"locks": {
    "mode": "AllResourcesDoNotDelete",
    "excludedPrincipals": [
        "7be2f100-3af5-4c15-bcb7-27ee43784a1f",
        "38833b56-194d-420b-90ce-cff578296714"
    ],
    "excludedActions": [
        "Microsoft.ContainerRegistry/registries/push/write",
        "Microsoft.Authorization/*/read"
    ]
},
```

Hoewel **excludedPrincipals** moet expliciet zijn, kunnen **excludedActions** -vermeldingen gebruikmaken van `*` voor joker tekens die overeenkomen met de bewerkingen van de resource provider.

## <a name="next-steps"></a>Volgende stappen

- Volg de zelf studie [nieuwe resources beveiligen](../tutorials/protect-new-resources.md) .
- Meer informatie over de [levenscyclus van een blauwdruk](./lifecycle.md).
- Meer informatie over hoe u [statische en dynamische parameters](./parameters.md) gebruikt.
- Meer informatie over hoe u de [blauwdrukvolgorde](./sequencing-order.md) aanpast.
- Meer informatie over hoe u [bestaande toewijzingen bijwerkt](../how-to/update-existing-assignments.md).
- Problemen oplossen tijdens de toewijzing van een blauwdruk met [algemene probleemoplossing](../troubleshoot/general.md).
