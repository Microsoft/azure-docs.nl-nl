---
title: Aangepaste Azure-rollen - Azure RBAC
description: Meer informatie over het maken van aangepaste Azure-rollen met op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC) voor een fijner toegangsbeheer van Azure-resources.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: conceptual
ms.workload: identity
ms.date: 12/15/2020
ms.author: rolyon
ms.openlocfilehash: 9779c2a269902d856d1639ce78028d0e658656bb
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107479828"
---
# <a name="azure-custom-roles"></a>Aangepaste Azure-rollen

> [!IMPORTANT]
> Het toevoegen van een beheergroep aan `AssignableScopes` is momenteel in de preview-fase.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

Als de [ingebouwde rollen van Azure](built-in-roles.md) niet voldoen aan de specifieke behoeften van uw organisatie, kunt u uw eigen aangepaste rollen maken. Net als bij ingebouwde rollen kunt u aangepaste rollen toewijzen aan gebruikers, groepen en service-principals in beheergroepen (alleen in preview), abonnementen en resourcegroepbereiken.

Aangepaste rollen kunnen worden gedeeld tussen abonnementen die dezelfde Azure AD-directory vertrouwen. Er geldt een limiet **van 5000 aangepaste** rollen per directory. (Voor Azure Duitsland en Azure China 21Vianet is de limiet 2000 aangepaste rollen.) Aangepaste rollen kunnen worden gemaakt met behulp van de Azure Portal, Azure PowerShell, Azure CLI of de REST API.

## <a name="steps-to-create-a-custom-role"></a>Stappen voor het maken van een aangepaste rol

Hier volgen de basisstappen voor het maken van een aangepaste rol.

1. Bepaal de machtigingen die u nodig hebt.

    Wanneer u een aangepaste rol maakt, moet u weten welke bewerkingen beschikbaar zijn om uw machtigingen te definiëren. Normaal gesproken begint u met een bestaande ingebouwde rol en past u deze vervolgens aan uw behoeften aan. U voegt de bewerkingen toe aan de `Actions` eigenschappen of van de `NotActions` [roldefinitie](role-definitions.md). Als u gegevensbewerkingen hebt, voegt u deze toe aan de `DataActions` eigenschappen of `NotDataActions` .

    Zie de volgende sectie Bepalen welke machtigingen u nodig hebt voor [meer informatie.](#how-to-determine-the-permissions-you-need)

1. Bepaal hoe u de aangepaste rol wilt maken.

    U kunt aangepaste rollen maken met [behulp van Azure Portal,](custom-roles-portal.md) [Azure PowerShell,](custom-roles-powershell.md) [Azure CLI](custom-roles-cli.md)of [de REST API.](custom-roles-rest.md)

1. Maak de aangepaste rol.

    De eenvoudigste manier is het gebruik van de Azure Portal. Zie Aangepaste Azure-rollen maken of bijwerken met behulp van de Azure Portal voor stappen voor het maken van een [aangepaste Azure Portal.](custom-roles-portal.md)

1. Test de aangepaste rol.

    Zodra u uw aangepaste rol hebt, moet u deze testen om te controleren of deze werkt zoals verwacht. Als u later wijzigingen moet aanbrengen, kunt u de aangepaste rol bijwerken.

## <a name="how-to-determine-the-permissions-you-need"></a>Bepalen welke machtigingen u nodig hebt

Azure heeft duizenden machtigingen die u mogelijk kunt opnemen in uw aangepaste rol. Hier zijn enkele methoden die u kunnen helpen bepalen welke machtigingen u aan uw aangepaste rol wilt toevoegen:

- Bekijk bestaande [ingebouwde rollen.](built-in-roles.md)

    Mogelijk wilt u een bestaande rol wijzigen of machtigingen combineren die in meerdere rollen worden gebruikt.

- Geef de Azure-services weer die u toegang wilt verlenen.

- Bepaal de [resourceproviders die zijn toe te staan aan de Azure-services.](../azure-resource-manager/management/azure-services-resource-providers.md)

    Azure-services stellen hun functionaliteit en machtigingen beschikbaar via [resourceproviders.](../azure-resource-manager/management/overview.md) De resourceprovider Microsoft.Compute levert bijvoorbeeld resources voor virtuele machines en de resourceprovider Microsoft.Billing levert abonnements- en factureringsresources. Als u de resourceproviders kent, kunt u de machtigingen bepalen die u nodig hebt voor uw aangepaste rol.

    Wanneer u een aangepaste rol maakt met behulp van Azure Portal, kunt u ook de resourceproviders bepalen door te zoeken naar trefwoorden. Deze zoekfunctionaliteit wordt beschreven in Aangepaste Azure-rollen maken of bijwerken [met behulp van de Azure Portal](custom-roles-portal.md#step-4-permissions).

    ![Deelvenster Machtigingen toevoegen met resourceprovider](./media/custom-roles-portal/add-permissions-provider.png)

- Zoek in [de beschikbare machtigingen](resource-provider-operations.md) naar machtigingen die u wilt opnemen.

    Wanneer u een aangepaste rol maakt met behulp van Azure Portal, kunt u op sleutelwoord naar machtigingen zoeken. U kunt bijvoorbeeld zoeken naar virtuele *machine* of *factureringsmachtigingen.* U kunt ook alle machtigingen downloaden als een CSV-bestand en vervolgens in dit bestand zoeken. Deze zoekfunctionaliteit wordt beschreven in Aangepaste Azure-rollen maken of bijwerken [met behulp van de Azure Portal](custom-roles-portal.md#step-4-permissions).

    ![Lijst met machtigingen toevoegen](./media/custom-roles-portal/add-permissions-list.png)

## <a name="custom-role-example"></a>Voorbeeld van aangepaste rol

Hieronder ziet u hoe een aangepaste rol eruitziet zoals wordt weergegeven met Azure PowerShell in JSON-indeling. Deze aangepaste rol kan worden gebruikt voor het bewaken en opnieuw opstarten van virtuele machines.

```json
{
  "Name": "Virtual Machine Operator",
  "Id": "88888888-8888-8888-8888-888888888888",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.ResourceHealth/availabilityStatuses/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Insights/diagnosticSettings/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
    "/subscriptions/{subscriptionId1}",
    "/subscriptions/{subscriptionId2}",
    "/providers/Microsoft.Management/managementGroups/{groupId1}"
  ]
}
```

Hieronder ziet u dezelfde aangepaste rol als die wordt weergegeven met behulp van Azure CLI.

```json
[
  {
    "assignableScopes": [
      "/subscriptions/{subscriptionId1}",
      "/subscriptions/{subscriptionId2}",
      "/providers/Microsoft.Management/managementGroups/{groupId1}"
    ],
    "description": "Can monitor and restart virtual machines.",
    "id": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleDefinitions/88888888-8888-8888-8888-888888888888",
    "name": "88888888-8888-8888-8888-888888888888",
    "permissions": [
      {
        "actions": [
          "Microsoft.Storage/*/read",
          "Microsoft.Network/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action",
          "Microsoft.Authorization/*/read",
          "Microsoft.ResourceHealth/availabilityStatuses/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Insights/diagnosticSettings/*",
          "Microsoft.Support/*"
        ],
        "dataActions": [],
        "notActions": [],
        "notDataActions": []
      }
    ],
    "roleName": "Virtual Machine Operator",
    "roleType": "CustomRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

Wanneer u een aangepaste rol maakt, wordt deze weergegeven in de Azure Portal met een oranje resourcepictogram.

![Pictogram aangepaste rol](./media/custom-roles/roles-custom-role-icon.png)

## <a name="custom-role-properties"></a>Eigenschappen van aangepaste rollen

In de volgende tabel wordt beschreven wat de eigenschappen van de aangepaste rol betekenen.

| Eigenschap | Vereist | Type | Beschrijving |
| --- | --- | --- | --- |
| `Name`</br>`roleName` | Ja | Tekenreeks | De weergavenaam van de aangepaste rol. Hoewel een roldefinitie een beheergroep of resource op abonnementsniveau is, kan een roldefinitie worden gebruikt in meerdere abonnementen die dezelfde Azure AD-directory delen. Deze weergavenaam moet uniek zijn binnen het bereik van de Azure AD-directory. Kan letters, cijfers, spaties en speciale tekens bevatten. Het maximumaantal tekens is 128. |
| `Id`</br>`name` | Ja | Tekenreeks | De unieke id van de aangepaste rol. Voor Azure PowerShell en Azure CLI wordt deze id automatisch gegenereerd wanneer u een nieuwe rol maakt. |
| `IsCustom`</br>`roleType` | Ja | Tekenreeks | Geeft aan of dit een aangepaste rol is. Stel in `true` op of voor aangepaste `CustomRole` rollen. Stel in `false` op `BuiltInRole` of voor ingebouwde rollen. |
| `Description`</br>`description` | Ja | Tekenreeks | De beschrijving van de aangepaste rol. Kan letters, cijfers, spaties en speciale tekens bevatten. Het maximumaantal tekens is 1024. |
| `Actions`</br>`actions` | Ja | Tekenreeks[] | Een matrix met tekenreeksen die de beheerbewerkingen specificeert die de rol mag uitvoeren. Zie Acties voor [meer informatie.](role-definitions.md#actions) |
| `NotActions`</br>`notActions` | Nee | Tekenreeks[] | Een matrix met tekenreeksen die de beheerbewerkingen specificeert die zijn uitgesloten van de toegestane `Actions` . Zie [NotActions voor meer informatie.](role-definitions.md#notactions) |
| `DataActions`</br>`dataActions` | Nee | Tekenreeks[] | Een matrix met tekenreeksen die de gegevensbewerkingen specificeert die met de rol kunnen worden uitgevoerd voor uw gegevens in dat object. Als u een aangepaste rol maakt met , kan die rol `DataActions` niet worden toegewezen in het bereik van de beheergroep. Zie [DataActions voor meer informatie.](role-definitions.md#dataactions) |
| `NotDataActions`</br>`notDataActions` | Nee | Tekenreeks[] | Een matrix met tekenreeksen die de gegevensbewerkingen specificeert die zijn uitgesloten van de toegestane `DataActions` . Zie [NotDataActions voor meer informatie.](role-definitions.md#notdataactions) |
| `AssignableScopes`</br>`assignableScopes` | Ja | Tekenreeks[] | Een matrix met tekenreeksen die de scopes specificeert die de aangepaste rol beschikbaar maakt voor toewijzing. U kunt slechts één beheergroep definiëren in `AssignableScopes` van een aangepaste rol. Het toevoegen van een beheergroep aan `AssignableScopes` is momenteel in de preview-fase. Zie [AssignableScopes voor meer informatie.](role-definitions.md#assignablescopes) |

## <a name="wildcard-permissions"></a>Machtigingen voor jokertekens

`Actions`, `NotActions` , en ondersteunen `DataActions` `NotDataActions` jokertekens ( `*` ) om machtigingen te definiëren. Met een jokerteken ( `*` ) wordt een machtiging uitgebreid naar alles wat overeenkomt met de actietekenreeks die u opneemt. Stel bijvoorbeeld dat u alle machtigingen wilt toevoegen die betrekking hebben op Azure Cost Management export. U kunt al deze actiereeksen toevoegen:

```
Microsoft.CostManagement/exports/action
Microsoft.CostManagement/exports/read
Microsoft.CostManagement/exports/write
Microsoft.CostManagement/exports/delete
Microsoft.CostManagement/exports/run/action
```

In plaats van al deze tekenreeksen toe te voegen, kunt u gewoon een tekenreeks met jokertekens toevoegen. De volgende jokertekenreeks is bijvoorbeeld gelijk aan de vorige vijf tekenreeksen. Dit omvat ook eventuele toekomstige exportmachtigingen die kunnen worden toegevoegd.

```
Microsoft.CostManagement/exports/*
```

U kunt ook meerdere jokertekens in een tekenreeks hebben. De volgende tekenreeks vertegenwoordigt bijvoorbeeld alle querymachtigingen voor Cost Management.

```
Microsoft.CostManagement/*/query/*
```

## <a name="who-can-create-delete-update-or-view-a-custom-role"></a>Wie een aangepaste rol kan maken, verwijderen, bijwerken of weergeven

Net als ingebouwde rollen geeft de eigenschap de scopes aan `AssignableScopes` die beschikbaar zijn voor toewijzing. De eigenschap voor een aangepaste rol bepaalt ook wie de aangepaste rol kan `AssignableScopes` maken, verwijderen, bijwerken of weergeven.

| Taak | Bewerking | Beschrijving |
| --- | --- | --- |
| Een aangepaste rol maken/verwijderen | `Microsoft.Authorization/ roleDefinitions/write` | Gebruikers aan wie deze bewerking voor alle aangepaste rollen is verleend, kunnen aangepaste rollen maken `AssignableScopes` (of verwijderen) voor gebruik in deze scopes. Bijvoorbeeld eigenaars [en](built-in-roles.md#owner) [beheerders van gebruikerstoegang van](built-in-roles.md#user-access-administrator) beheergroepen, abonnementen en resourcegroepen. |
| Een aangepaste rol bijwerken | `Microsoft.Authorization/ roleDefinitions/write` | Gebruikers aan wie deze bewerking is verleend voor alle aangepaste rollen, kunnen `AssignableScopes` aangepaste rollen in deze scopes bijwerken. Bijvoorbeeld eigenaars [en](built-in-roles.md#owner) [beheerders van gebruikerstoegang van](built-in-roles.md#user-access-administrator) beheergroepen, abonnementen en resourcegroepen. |
| Een aangepaste rol weergeven | `Microsoft.Authorization/ roleDefinitions/read` | Gebruikers aan deze bewerking voor een bereik kunnen de aangepaste rollen bekijken die beschikbaar zijn voor toewijzing in dat bereik. Bij alle ingebouwde rollen is toegestaan dat aangepaste rollen beschikbaar zijn voor toewijzing. |

## <a name="custom-role-limits"></a>Aangepaste rollimieten

In de volgende lijst worden de limieten voor aangepaste rollen beschreven.

- Elke directory kan maximaal **5000 aangepaste** rollen hebben.
- Azure Duitsland en Azure China 21Vianet maximaal 2000 aangepaste rollen voor elke directory.
- U kunt niet `AssignableScopes` instellen op het hoofdbereik ( `"/"` ).
- U kunt geen jokertekens ( `*` ) gebruiken in `AssignableScopes` . Deze beperking met jokertekens zorgt ervoor dat een gebruiker mogelijk geen toegang kan krijgen tot een bereik door de roldefinitie bij te werken.
- U kunt slechts één beheergroep definiëren in `AssignableScopes` van een aangepaste rol. Het toevoegen van een beheergroep aan `AssignableScopes` is momenteel in de preview-fase.
- Aangepaste rollen met `DataActions` kunnen niet worden toegewezen in het bereik van de beheergroep.
- Azure Resource Manager controleert niet of de beheergroep bestaat in het toewijsbare bereik van de roldefinitie.

Zie Uw resources organiseren met Azure-beheergroepen voor meer informatie over [aangepaste rollen en beheergroepen.](../governance/management-groups/overview.md#azure-custom-role-definition-and-assignment)

## <a name="input-and-output-formats"></a>Invoer- en uitvoerindelingen

Als u een aangepaste rol wilt maken met behulp van de opdrachtregel, gebruikt u doorgaans JSON om de gepersonaliseerde eigenschappen voor de aangepaste rol op te geven. Afhankelijk van de hulpprogramma's die u gebruikt, zien de invoer- en uitvoerindelingen er iets anders uit. In deze sectie worden de invoer- en uitvoerindelingen vermeld, afhankelijk van het hulpprogramma.

### <a name="azure-powershell"></a>Azure PowerShell

Als u een aangepaste rol wilt maken met Azure PowerShell, moet u de volgende invoer invoeren.

```json
{
  "Name": "",
  "Description": "",
  "Actions": [],
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": []
}
```

Als u een aangepaste rol wilt bijwerken Azure PowerShell, moet u de volgende invoer invoeren. Houd er rekening mee `Id` dat de eigenschap is toegevoegd. 

```json
{
  "Name": "",
  "Id": "",
  "Description": "",
  "Actions": [],
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": []
}
```

Hieronder ziet u een voorbeeld van de uitvoer wanneer u een aangepaste rol vermeldt met behulp van Azure PowerShell en de [opdracht ConvertTo-Json.](/powershell/module/microsoft.powershell.utility/convertto-json) 

```json
{
  "Name": "",
  "Id": "",
  "IsCustom": true,
  "Description": "",
  "Actions": [],
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": []
}
```

### <a name="azure-cli"></a>Azure CLI

Als u een aangepaste rol wilt maken of bijwerken met behulp van Azure CLI, moet u de volgende invoer invoeren. Deze indeling heeft dezelfde indeling wanneer u een aangepaste rol maakt met behulp van Azure PowerShell.

```json
{
  "Name": "",
  "Description": "",
  "Actions": [],
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": []
}
```

Hieronder ziet u een voorbeeld van de uitvoer wanneer u een aangepaste rol vermeldt met behulp van Azure CLI.

```json
[
  {
    "assignableScopes": [],
    "description": "",
    "id": "",
    "name": "",
    "permissions": [
      {
        "actions": [],
        "dataActions": [],
        "notActions": [],
        "notDataActions": []
      }
    ],
    "roleName": "",
    "roleType": "CustomRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

### <a name="rest-api"></a>REST-API

Als u een aangepaste rol wilt maken of bijwerken met behulp van REST API, moet u de volgende invoer invoeren. Deze indeling is dezelfde indeling die wordt gegenereerd wanneer u een aangepaste rol maakt met behulp van de Azure Portal.

```json
{
  "properties": {
    "roleName": "",
    "description": "",
    "assignableScopes": [],
    "permissions": [
      {
        "actions": [],
        "notActions": [],
        "dataActions": [],
        "notDataActions": []
      }
    ]
  }
}
```

Hieronder ziet u een voorbeeld van de uitvoer wanneer u een aangepaste rol vermeldt met behulp van de REST API.

```json
{
    "properties": {
        "roleName": "",
        "type": "CustomRole",
        "description": "",
        "assignableScopes": [],
        "permissions": [
            {
                "actions": [],
                "notActions": [],
                "dataActions": [],
                "notDataActions": []
            }
        ],
        "createdOn": "",
        "updatedOn": "",
        "createdBy": "",
        "updatedBy": ""
    },
    "id": "",
    "type": "Microsoft.Authorization/roleDefinitions",
    "name": ""
}
```

## <a name="next-steps"></a>Volgende stappen

- [Zelfstudie: Een aangepaste Azure-rol maken met Azure PowerShell](tutorial-custom-role-powershell.md)
- [Zelfstudie: Een aangepaste Azure-rol maken met Azure CLI](tutorial-custom-role-cli.md)
- [Inzicht krijgen in Azure-roldefinities](role-definitions.md)
- [Problemen met Azure RBAC oplossen](troubleshooting.md)
