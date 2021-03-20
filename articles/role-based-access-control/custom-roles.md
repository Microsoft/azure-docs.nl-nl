---
title: Aangepaste Azure-rollen-Azure RBAC
description: Meer informatie over het maken van aangepaste Azure-rollen met Azure op rollen gebaseerd toegangs beheer (Azure RBAC) voor een nauw keurig toegangs beheer van Azure-resources.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: conceptual
ms.workload: identity
ms.date: 12/15/2020
ms.author: rolyon
ms.openlocfilehash: 79aaeee942a6d46243ee1c72d5904484b8698ebe
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97617320"
---
# <a name="azure-custom-roles"></a>Aangepaste Azure-rollen

> [!IMPORTANT]
> Het toevoegen van een beheergroep aan `AssignableScopes` is momenteel in de preview-fase.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

Als de [ingebouwde rollen van Azure](built-in-roles.md) niet voldoen aan de specifieke behoeften van uw organisatie, kunt u uw eigen aangepaste rollen maken. Net als bij ingebouwde rollen kunt u aangepaste rollen toewijzen aan gebruikers, groepen en service-principals in de beheer groep, het abonnement en het bereik van de resource groep.

Aangepaste rollen kunnen worden gedeeld tussen abonnementen die dezelfde Azure AD-Directory vertrouwen. Er geldt een limiet van **5.000** aangepaste rollen per map. (Voor Azure Duitsland en Azure China 21Vianet is de limiet 2.000 aangepaste rollen.) Aangepaste rollen kunnen worden gemaakt met behulp van de Azure Portal, Azure PowerShell, Azure CLI of de REST API.

## <a name="steps-to-create-a-custom-role"></a>Stappen voor het maken van een aangepaste rol

Hier volgen de basis stappen voor het maken van een aangepaste rol.

1. Bepaal de benodigde machtigingen.

    Wanneer u een aangepaste rol maakt, moet u weten welke bewerkingen beschikbaar zijn voor het definiëren van uw machtigingen. Normaal gesp roken begint u met een bestaande ingebouwde rol en wijzigt u deze voor uw behoeften. U voegt de bewerkingen toe aan de `Actions` `NotActions` Eigenschappen of van de [roldefinitie](role-definitions.md). Als u gegevens bewerkingen hebt, voegt u deze toe aan de `DataActions` `NotDataActions` Eigenschappen of.

    Zie de volgende sectie [hoe u de benodigde machtigingen kunt bepalen](#how-to-determine-the-permissions-you-need)voor meer informatie.

1. Bepaal hoe u de aangepaste rol wilt maken.

    U kunt aangepaste rollen maken met behulp van [Azure Portal](custom-roles-portal.md), [Azure POWERSHELL](custom-roles-powershell.md), [Azure cli](custom-roles-cli.md)of de [rest API](custom-roles-rest.md).

1. Maak de aangepaste rol.

    De eenvoudigste manier is om de Azure Portal te gebruiken. Zie [aangepaste Azure-rollen maken of bijwerken met behulp](custom-roles-portal.md)van de Azure portal voor stappen voor het maken van een aangepaste rol met behulp van de Azure Portal.

1. De aangepaste rol testen.

    Wanneer u uw aangepaste rol hebt, moet u deze testen om te controleren of deze werkt zoals verwacht. Als u later aanpassingen wilt aanbrengen, kunt u de aangepaste rol bijwerken.

## <a name="how-to-determine-the-permissions-you-need"></a>De machtigingen bepalen die u nodig hebt

Azure heeft duizenden machtigingen die u mogelijk in uw aangepaste rol kunt gebruiken. Hier volgen enkele methoden die u kunnen helpen bij het bepalen van de machtigingen die u wilt toevoegen aan uw aangepaste rol:

- Bekijk de bestaande [ingebouwde rollen](built-in-roles.md).

    Mogelijk wilt u een bestaande rol wijzigen of de machtigingen combi neren die worden gebruikt in meerdere rollen.

- Vermeld de Azure-Services waaraan u toegang wilt verlenen.

- Bepaal de [resource providers die zijn toegewezen aan de Azure-Services](../azure-resource-manager/management/azure-services-resource-providers.md).

    De functionaliteit en machtigingen van Azure-Services zijn beschikbaar via [resource providers](../azure-resource-manager/management/overview.md). De resource provider micro soft. Compute levert bijvoorbeeld virtuele-machine bronnen en de resource provider micro soft. Bill levert abonnements-en facturerings bronnen op. Als u de resource providers kent, kunt u de benodigde machtigingen voor uw aangepaste rol beperken en bepalen.

    Wanneer u een aangepaste rol maakt met behulp van de Azure Portal, kunt u ook de resource providers bepalen door te zoeken naar tref woorden. Deze zoek functie wordt beschreven in [aangepaste Azure-rollen maken of bijwerken met behulp van de Azure Portal](custom-roles-portal.md#step-4-permissions).

    ![Deel venster machtigingen toevoegen met resource provider](./media/custom-roles-portal/add-permissions-provider.png)

- Zoek de [beschik bare machtigingen](resource-provider-operations.md) om de machtigingen te vinden die u wilt toevoegen.

    Wanneer u een aangepaste rol maakt met behulp van de Azure Portal, kunt u zoeken naar machtigingen op sleutel woord. U kunt bijvoorbeeld zoeken naar machtigingen voor de *virtuele machine* of *facturering* . U kunt ook alle machtigingen downloaden als een CSV-bestand en dit bestand vervolgens doorzoeken. Deze zoek functie wordt beschreven in [aangepaste Azure-rollen maken of bijwerken met behulp van de Azure Portal](custom-roles-portal.md#step-4-permissions).

    ![Lijst met machtigingen toevoegen](./media/custom-roles-portal/add-permissions-list.png)

## <a name="custom-role-example"></a>Voor beeld van aangepaste rol

Hieronder ziet u hoe een aangepaste rol eruitziet zoals wordt weer gegeven met behulp van Azure PowerShell in JSON-indeling. Deze aangepaste rol kan worden gebruikt voor het bewaken en opnieuw starten van virtuele machines.

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

Hieronder ziet u dezelfde aangepaste rol die wordt weer gegeven met behulp van Azure CLI.

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

Wanneer u een aangepaste rol maakt, wordt deze weer gegeven in de Azure Portal met een oranje resource pictogram.

![Pictogram aangepaste rol](./media/custom-roles/roles-custom-role-icon.png)

## <a name="custom-role-properties"></a>Eigenschappen van aangepaste rol

In de volgende tabel wordt beschreven wat de eigenschappen van de aangepaste rol betekenen.

| Eigenschap | Vereist | Type | Beschrijving |
| --- | --- | --- | --- |
| `Name`</br>`roleName` | Ja | Tekenreeks | De weergave naam van de aangepaste rol. Hoewel een roldefinitie een resource voor een beheer groep of op abonnements niveau is, kan een roldefinitie worden gebruikt in meerdere abonnementen die dezelfde Azure AD-directory delen. Deze weergave naam moet uniek zijn binnen het bereik van de Azure AD-adres lijst. Kan letters, cijfers, spaties en speciale tekens bevatten. Het maximum aantal tekens is 128. |
| `Id`</br>`name` | Ja | Tekenreeks | De unieke ID van de aangepaste rol. Voor Azure PowerShell en Azure CLI wordt deze ID automatisch gegenereerd wanneer u een nieuwe rol maakt. |
| `IsCustom`</br>`roleType` | Ja | Tekenreeks | Hiermee wordt aangegeven of dit een aangepaste rol is. Ingesteld op `true` of `CustomRole` voor aangepaste rollen. Ingesteld op `false` of `BuiltInRole` voor ingebouwde rollen. |
| `Description`</br>`description` | Ja | Tekenreeks | De beschrijving van de aangepaste rol. Kan letters, cijfers, spaties en speciale tekens bevatten. Het maximum aantal tekens is 1024. |
| `Actions`</br>`actions` | Ja | Teken reeks [] | Een matrix met teken reeksen die de beheer bewerkingen specificeert die de rol kan uitvoeren. Zie [acties](role-definitions.md#actions)voor meer informatie. |
| `NotActions`</br>`notActions` | Nee | Teken reeks [] | Een matrix met teken reeksen die de beheer bewerkingen specificeert die zijn uitgesloten van de toegestane `Actions` . Zie voor meer informatie [intact](role-definitions.md#notactions). |
| `DataActions`</br>`dataActions` | Nee | Teken reeks [] | Een matrix met teken reeksen waarmee de gegevens bewerkingen worden opgegeven die door de functie kunnen worden uitgevoerd op uw gegevens in dat object. Als u een aangepaste rol maakt met `DataActions` , kan deze rol niet worden toegewezen in het bereik van de beheer groep. Zie [DataActions](role-definitions.md#dataactions)voor meer informatie. |
| `NotDataActions`</br>`notDataActions` | Nee | Teken reeks [] | Een matrix met teken reeksen die de gegevens bewerkingen specificeert die worden uitgesloten van de toegestane waarde `DataActions` . Zie [NotDataActions](role-definitions.md#notdataactions)voor meer informatie. |
| `AssignableScopes`</br>`assignableScopes` | Ja | Teken reeks [] | Een matrix met teken reeksen die de bereiken aangeeft waarvoor de aangepaste rol beschikbaar is voor toewijzing. U kunt slechts één beheer groep definiëren in `AssignableScopes` een aangepaste rol. Het toevoegen van een beheergroep aan `AssignableScopes` is momenteel in de preview-fase. Zie [AssignableScopes](role-definitions.md#assignablescopes)voor meer informatie. |

## <a name="wildcard-permissions"></a>Machtigingen voor joker tekens

`Actions`, `NotActions` , `DataActions` en `NotDataActions` ondersteunen joker tekens ( `*` ) om machtigingen te definiëren. Een Joker teken ( `*` ) breidt een machtiging uit voor alles die overeenkomt met de actie teken reeks die u opgeeft. Stel bijvoorbeeld dat u alle machtigingen wilt toevoegen met betrekking tot Azure Cost Management en exports. U kunt al deze actie teken reeksen toevoegen:

```
Microsoft.CostManagement/exports/action
Microsoft.CostManagement/exports/read
Microsoft.CostManagement/exports/write
Microsoft.CostManagement/exports/delete
Microsoft.CostManagement/exports/run/action
```

In plaats van al deze teken reeksen toe te voegen, kunt u gewoon een Joker teken reeks toevoegen. De volgende Joker teken reeks is bijvoorbeeld gelijk aan de vorige vijf teken reeksen. Dit omvat ook toekomstige export machtigingen die kunnen worden toegevoegd.

```
Microsoft.CostManagement/exports/*
```

U kunt ook meerdere joker tekens in een teken reeks hebben. De volgende teken reeks vertegenwoordigt bijvoorbeeld alle query machtigingen voor Cost Management.

```
Microsoft.CostManagement/*/query/*
```

## <a name="who-can-create-delete-update-or-view-a-custom-role"></a>Wie kan een aangepaste rol maken, verwijderen, bijwerken of weer geven

Net als bij ingebouwde rollen `AssignableScopes` geeft de eigenschap de bereiken op die de rol beschikbaar is voor toewijzing. De `AssignableScopes` eigenschap voor een aangepaste rol bepaalt ook wie de aangepaste rol kan maken, verwijderen, bijwerken of weer geven.

| Taak | Bewerking | Beschrijving |
| --- | --- | --- |
| Een aangepaste rol maken/verwijderen | `Microsoft.Authorization/ roleDefinitions/write` | Gebruikers aan wie deze bewerking is toegewezen voor alle `AssignableScopes` aangepaste rollen, kunnen aangepaste rollen maken (of verwijderen) voor gebruik in deze bereiken. Bijvoorbeeld [eigen aren](built-in-roles.md#owner) en [beheerders van gebruikers toegang](built-in-roles.md#user-access-administrator) van beheer groepen, abonnementen en resource groepen. |
| Een aangepaste rol bijwerken | `Microsoft.Authorization/ roleDefinitions/write` | Gebruikers aan wie deze bewerking is toegewezen voor alle `AssignableScopes` van de aangepaste rol, kunnen aangepaste rollen in deze bereiken bijwerken. Bijvoorbeeld [eigen aren](built-in-roles.md#owner) en [beheerders van gebruikers toegang](built-in-roles.md#user-access-administrator) van beheer groepen, abonnementen en resource groepen. |
| Een aangepaste rol weer geven | `Microsoft.Authorization/ roleDefinitions/read` | Gebruikers aan wie deze bewerking in een bereik is verleend, kunnen de aangepaste rollen bekijken die beschikbaar zijn voor toewijzing in dat bereik. Bij alle ingebouwde rollen is toegestaan dat aangepaste rollen beschikbaar zijn voor toewijzing. |

## <a name="custom-role-limits"></a>Limieten voor aangepaste rollen

In de volgende lijst worden de limieten voor aangepaste rollen beschreven.

- Elke directory kan Maxi maal **5000** aangepaste rollen hebben.
- Azure Duitsland en Azure China 21Vianet kunnen Maxi maal 2000 aangepaste rollen hebben voor elke directory.
- U kunt niet instellen `AssignableScopes` op het hoofd bereik ( `"/"` ).
- U kunt geen joker tekens ( `*` ) gebruiken in `AssignableScopes` . Deze beperking voor joker tekens helpt ervoor te zorgen dat een gebruiker geen toegang kan krijgen tot een bereik door de roldefinitie bij te werken.
- U kunt slechts één beheer groep definiëren in `AssignableScopes` een aangepaste rol. Het toevoegen van een beheergroep aan `AssignableScopes` is momenteel in de preview-fase.
- Aangepaste rollen met `DataActions` kunnen niet worden toegewezen in het bereik van de beheer groep.
- Azure Resource Manager valideert niet het bestaan van de beheer groep in het toewijs bare bereik van de roldefinitie.

Zie [uw resources organiseren met Azure-beheer groepen](../governance/management-groups/overview.md#azure-custom-role-definition-and-assignment)voor meer informatie over aangepaste rollen en beheer groepen.

## <a name="input-and-output-formats"></a>Invoer-en uitvoer indelingen

Als u een aangepaste rol wilt maken met behulp van de opdracht regel, gebruikt u meestal JSON om de eigenschappen op te geven die u wilt gebruiken voor de aangepaste rol. Afhankelijk van de hulpprogram ma's die u gebruikt, ziet de invoer-en uitvoer opmaak er iets anders uit. In deze sectie worden de invoer-en uitvoer indelingen weer gegeven, afhankelijk van het hulp programma.

### <a name="azure-powershell"></a>Azure PowerShell

Als u een aangepaste rol wilt maken met behulp van Azure PowerShell, moet u de volgende invoer opgeven.

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

Als u een aangepaste rol wilt bijwerken met behulp van Azure PowerShell, moet u de volgende invoer opgeven. Houd er rekening mee dat de `Id` eigenschap is toegevoegd. 

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

Hieronder ziet u een voor beeld van de uitvoer wanneer u een aangepaste rol vermeldt met behulp van Azure PowerShell en de [ConvertTo-JSON-](/powershell/module/microsoft.powershell.utility/convertto-json) opdracht. 

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

Als u een aangepaste rol wilt maken of bijwerken met behulp van Azure CLI, moet u de volgende invoer opgeven. Deze indeling is dezelfde indeling wanneer u een aangepaste rol maakt met behulp van Azure PowerShell.

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

Hieronder ziet u een voor beeld van de uitvoer wanneer u een aangepaste rol vermeldt met behulp van Azure CLI.

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

Als u een aangepaste rol wilt maken of bijwerken met behulp van de REST API, moet u de volgende invoer opgeven. Deze indeling is dezelfde indeling die wordt gegenereerd wanneer u een aangepaste rol maakt met behulp van de Azure Portal.

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

Hieronder ziet u een voor beeld van de uitvoer wanneer u een aangepaste rol vermeldt met behulp van de REST API.

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
- [Informatie over Azure Role-definities](role-definitions.md)
- [Problemen met Azure RBAC oplossen](troubleshooting.md)
