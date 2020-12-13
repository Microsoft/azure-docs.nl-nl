---
title: Meer informatie over Azure Role-definities-Azure RBAC
description: Meer informatie over definities van Azure-rollen in azure op rollen gebaseerd toegangs beheer (Azure RBAC) voor een nauw keurig toegangs beheer van Azure-resources.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/08/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: ''
ms.openlocfilehash: bc3640fecbe1138e46fd0d36975691740bc669dd
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/13/2020
ms.locfileid: "97369256"
---
# <a name="understand-azure-role-definitions"></a>Informatie over Azure Role-definities

Als u wilt weten hoe een Azure-rol werkt of als u uw eigen [aangepaste Azure-rol](custom-roles.md)maakt, is het handig om te begrijpen hoe rollen worden gedefinieerd. In dit artikel worden de details van roldefinities beschreven en vindt u enkele voor beelden.

## <a name="role-definition"></a>Roldefinitie

Een *roldefinitie* is een verzameling machtigingen. Het wordt soms gewoon een *rol* genoemd. Een roldefinitie beschijft de bewerkingen die kunnen worden uitgevoerd, zoals lezen, schrijven en verwijderen. Het kan ook een overzicht geven van de bewerkingen die zijn uitgesloten van toegestane bewerkingen of bewerkingen met betrekking tot onderliggende gegevens.

Hieronder ziet u een voor beeld van de eigenschappen in een roldefinitie wanneer deze worden weer gegeven met Azure PowerShell:

```
Name
Id
IsCustom
Description
Actions []
NotActions []
DataActions []
NotDataActions []
AssignableScopes []
```

Hieronder ziet u een voor beeld van de eigenschappen in een roldefinitie wanneer deze worden weer gegeven met de Azure Portal, Azure CLI of de REST API:

```
roleName
name
type
description
actions []
notActions []
dataActions []
notDataActions []
assignableScopes []
```

In de volgende tabel wordt beschreven wat de functie-eigenschappen betekenen.

| Eigenschap | Beschrijving |
| --- | --- |
| `Name`</br>`roleName` | De weergave naam van de rol. |
| `Id`</br>`name` | De unieke ID van de rol. |
| `IsCustom`</br>`roleType` | Hiermee wordt aangegeven of dit een aangepaste rol is. Ingesteld op `true` of `CustomRole` voor aangepaste rollen. Ingesteld op `false` of `BuiltInRole` voor ingebouwde rollen. |
| `Description`</br>`description` | De beschrijving van de rol. |
| `Actions`</br>`actions` | Een matrix met teken reeksen die de beheer bewerkingen specificeert die de rol kan uitvoeren. |
| `NotActions`</br>`notActions` | Een matrix met teken reeksen die de beheer bewerkingen specificeert die zijn uitgesloten van de toegestane `Actions` . |
| `DataActions`</br>`dataActions` | Een matrix met teken reeksen waarmee de gegevens bewerkingen worden opgegeven die door de functie kunnen worden uitgevoerd op uw gegevens in dat object. |
| `NotDataActions`</br>`notDataActions` | Een matrix met teken reeksen die de gegevens bewerkingen specificeert die worden uitgesloten van de toegestane waarde `DataActions` . |
| `AssignableScopes`</br>`assignableScopes` | Een matrix met teken reeksen die de bereiken specificeert waarvan de rol beschikbaar is voor toewijzing. |

### <a name="operations-format"></a>Bewerkingen-indeling

Bewerkingen worden opgegeven met teken reeksen die de volgende indeling hebben:

- `{Company}.{ProviderName}/{resourceType}/{action}`

Het `{action}` gedeelte van een bewerkings reeks geeft u het type bewerkingen op dat u kunt uitvoeren op een resource type. U ziet bijvoorbeeld de volgende subtekenreeksen in `{action}` :

| Subtekenreeks van actie    | Description         |
| ------------------- | ------------------- |
| `*` | Het Joker teken verleent toegang tot alle bewerkingen die overeenkomen met de teken reeks. |
| `read` | Hiermee schakelt u lees bewerkingen in (GET). |
| `write` | Hiermee wordt schrijf bewerkingen (PUT of PATCH) ingeschakeld. |
| `action` | Hiermee schakelt u aangepaste bewerkingen in, zoals het opnieuw opstarten van virtuele machines (POST). |
| `delete` | Hiermee worden Verwijder bewerkingen (verwijderen) ingeschakeld. |

### <a name="role-definition-example"></a>Voor beeld van Role definition

Hier ziet u [de roldefinitie-rol zoals](built-in-roles.md#contributor) deze wordt weer gegeven in azure PowerShell en Azure cli. De jokerbewerking (`*`) onder `Actions` geeft aan dat de principal die aan deze rol is toegewezen alle acties kan uitvoeren, of met andere woorden, alles kan beheren. Dit omvat acties die in de toekomst zijn gedefinieerd, wanneer Azure nieuwe resourcetypen toevoegt. De bewerkingen onder `NotActions` worden afgetrokken van `Actions`. In het geval van de rol [Inzender](built-in-roles.md#contributor) `NotActions` verwijdert deze rol de mogelijkheid om de toegang tot resources te beheren en ook Azure Blueprint toewijzingen te beheren.

De rol Inzender zoals weer gegeven in Azure PowerShell:

```json
{
  "Name": "Contributor",
  "Id": "b24988ac-6180-42a0-ab88-20f7382dd24c",
  "IsCustom": false,
  "Description": "Lets you manage everything except access to resources.",
  "Actions": [
    "*"
  ],
  "NotActions": [
    "Microsoft.Authorization/*/Delete",
    "Microsoft.Authorization/*/Write",
    "Microsoft.Authorization/elevateAccess/Action",
    "Microsoft.Blueprint/blueprintAssignments/write",
    "Microsoft.Blueprint/blueprintAssignments/delete"
  ],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
    "/"
  ]
}
```

Rol Inzender zoals weer gegeven in azure CLI:

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage everything except access to resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
  "name": "b24988ac-6180-42a0-ab88-20f7382dd24c",
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "notActions": [
        "Microsoft.Authorization/*/Delete",
        "Microsoft.Authorization/*/Write",
        "Microsoft.Authorization/elevateAccess/Action",
        "Microsoft.Blueprint/blueprintAssignments/write",
        "Microsoft.Blueprint/blueprintAssignments/delete"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="management-and-data-operations"></a>Beheer-en gegevens bewerkingen

Toegangs beheer op basis van rollen voor beheer bewerkingen is opgegeven in `Actions` de `NotActions` Eigenschappen en van een roldefinitie. Hier volgen enkele voor beelden van beheer bewerkingen in Azure:

- Toegang tot een opslag account beheren
- Een BLOB-container maken, bijwerken of verwijderen
- Een resource groep en alle bijbehorende resources verwijderen

De toegang tot het beheer wordt niet overgenomen van uw gegevens omdat de container verificatie methode is ingesteld op ' Azure AD-gebruikers account ' en niet op ' toegangs sleutel '. Deze schei ding voor komt dat rollen met Joker tekens ( `*` ) onbeperkte toegang tot uw gegevens hebben. Als een gebruiker bijvoorbeeld een rol van [lezer](built-in-roles.md#reader) heeft voor een abonnement, kan deze het opslag account weer geven, maar standaard kunnen ze de onderliggende gegevens niet weer geven.

Voorheen werd op rollen gebaseerd toegangs beheer niet gebruikt voor gegevens bewerkingen. Autorisatie voor gegevens bewerkingen varieerden van alle resource providers. Hetzelfde op rollen gebaseerde toegangs beheer autorisatie model dat wordt gebruikt voor beheer bewerkingen, is uitgebreid naar gegevens bewerkingen.

Voor de ondersteuning van gegevens bewerkingen zijn nieuwe gegevens eigenschappen aan de roldefinitie toegevoegd. Gegevensbewerkingen worden opgegeven in de eigenschappen `DataActions` en `NotDataActions`. Door deze gegevens eigenschappen toe te voegen, wordt de schei ding tussen beheer en gegevens gehandhaafd. Hiermee voorkomt u dat huidige roltoewijzingen met jokertekens (`*`) plotseling toegang hebben tot gegevens. Hier volgen enkele gegevensbewerkingen die kunnen worden opgegeven in `DataActions` en `NotDataActions`:

- Een lijst met blobs in een container lezen
- Een opslag-blob in een container schrijven
- Een bericht in een wachtrij verwijderen

Hier ziet u de definitie van de rol van [BLOB-gegevens lezer](built-in-roles.md#storage-blob-data-reader) , die bewerkingen bevat in de `Actions` `DataActions` Eigenschappen en. Met deze rol kunt u de BLOB-container en ook de onderliggende BLOB-gegevens lezen.

Rol van gegevens lezer van opslag-BLOB zoals weer gegeven in Azure PowerShell:

```json
{
  "Name": "Storage Blob Data Reader",
  "Id": "2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
  "IsCustom": false,
  "Description": "Allows for read access to Azure Storage blob containers and data",
  "Actions": [
    "Microsoft.Storage/storageAccounts/blobServices/containers/read",
    "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
  ],
  "NotActions": [],
  "DataActions": [
    "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read"
  ],
  "NotDataActions": [],
  "AssignableScopes": [
    "/"
  ]
}
```

Rol van gegevens lezer van opslag-BLOB zoals weer gegeven in azure CLI:

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read access to Azure Storage blob containers and data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
  "name": "2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/read",
        "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Blob Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

Alleen gegevensbewerkingen kunnen worden toegevoegd aan de eigenschappen `DataActions` en `NotDataActions`. Resource providers bepalen welke bewerkingen gegevens bewerkingen zijn, door de `isDataAction` eigenschap in te stellen op `true` . Zie de bewerkingen van de `isDataAction` `true` [resource provider](resource-provider-operations.md)voor een overzicht van de bewerkingen waarbij dat is. Rollen zonder gegevens bewerkingen hoeven geen `DataActions` `NotDataActions` Eigenschappen in de roldefinitie te hebben.

Autorisatie voor alle beheer bewerkingen-API-aanroepen wordt afgehandeld door Azure Resource Manager. Autorisatie voor data Operation-API-aanroepen wordt verwerkt door een resource provider of Azure Resource Manager.

### <a name="data-operations-example"></a>Voor beeld van gegevens bewerkingen

Voor een beter inzicht in hoe beheer en gegevens bewerkingen werken, kunt u een specifiek voor beeld overwegen. Lisa is toegewezen aan de rol van [eigenaar](built-in-roles.md#owner) op het abonnements bereik. Bob is toegewezen aan de rol van de [blobgegevens van de gegevens opslag](built-in-roles.md#storage-blob-data-contributor) in een bereik van een opslag account. In het volgende diagram ziet u dit voor beeld.

![Toegangs beheer op basis van rollen is uitgebreid om zowel beheer-als gegevens bewerkingen te ondersteunen](./media/role-definitions/rbac-management-data.png)

De rol van [eigenaar](built-in-roles.md#owner) voor Alice en de rol [BLOB data contributor-gegevens](built-in-roles.md#storage-blob-data-contributor) voor Bob hebben de volgende acties:

Eigenaar

&nbsp;&nbsp;&nbsp;&nbsp;Regelen<br>
&nbsp;&nbsp;&nbsp;&nbsp;`*`

Inzender voor Storage Blob-gegevens

&nbsp;&nbsp;&nbsp;&nbsp;Regelen<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/delete`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/read`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/write`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action`<br>
&nbsp;&nbsp;&nbsp;&nbsp;DataActions<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/move/action`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write`

Aangezien Anne een actie Joker teken ( `*` ) heeft op een abonnements bereik, worden de machtigingen ervan overgenomen zodat ze alle beheer acties kunnen uitvoeren. Anja kan containers lezen, schrijven en verwijderen. Anja kan echter geen gegevens bewerkingen uitvoeren zonder extra stappen te nemen. Zo kan Anja de blobs in een container bijvoorbeeld standaard niet lezen. Om de blobs te lezen, zou Anne de toegangs sleutels voor opslag moeten ophalen en gebruiken om toegang te krijgen tot de blobs.

De machtigingen van Bob zijn beperkt tot alleen de `Actions` en `DataActions` opgegeven in de rol [BLOB data contributor](built-in-roles.md#storage-blob-data-contributor) van de opslag. Op basis van de rol kan Bob zowel beheer-als gegevens bewerkingen uitvoeren. Bob kan bijvoorbeeld containers in het opgegeven opslag account lezen, schrijven en verwijderen en kunnen ook de blobs lezen, schrijven en verwijderen.

Zie de [Azure Storage-beveiligings handleiding](../storage/blobs/security-recommendations.md)voor meer informatie over de beveiliging van beheer en gegevenslaag voor opslag.

### <a name="what-tools-support-using-azure-roles-for-data-operations"></a>Welke hulp middelen bieden ondersteuning voor het gebruik van Azure-functies voor gegevens bewerkingen?

Als u gegevens bewerkingen wilt bekijken en gebruiken, moet u beschikken over de juiste versies van de hulpprogram ma's of Sdk's:

| Hulpprogramma  | Versie  |
|---------|---------|
| [Azure PowerShell](/powershell/azure/install-az-ps) | 1.1.0 of hoger |
| [Azure CLI](/cli/azure/install-azure-cli) | 2.0.30 of hoger |
| [Azure voor .NET](/dotnet/azure/) | 2.8.0-Preview of hoger |
| [Azure SDK voor Go](/azure/go/azure-sdk-go-install) | 15.0.0 of hoger |
| [Azure voor Java](/java/azure/) | 1.9.0 of hoger |
| [Azure voor python](/azure/python/) | 0.40.0 of hoger |
| [Azure-SDK voor Ruby](https://rubygems.org/gems/azure_sdk) | 0.17.1 of hoger |

Als u de gegevens bewerkingen in het REST API wilt bekijken en gebruiken, moet u de para meter van de **API-versie** instellen op de volgende versie of hoger:

- 2018-07-01

## <a name="actions"></a>Acties

`Actions`Met de machtiging worden de beheer bewerkingen opgegeven die door de functie kunnen worden uitgevoerd. Het is een verzameling bewerkings reeksen waarmee Beveilig bare bewerkingen van Azure-resource providers worden geïdentificeerd. Hier volgen enkele voor beelden van beheer bewerkingen die kunnen worden gebruikt in `Actions` .

> [!div class="mx-tableFixed"]
> | Bewerkings reeks    | Description         |
> | ------------------- | ------------------- |
> | `*/read` | Verleent toegang tot Lees bewerkingen voor alle resource typen van alle Azure-resource providers.|
> | `Microsoft.Compute/*` | Verleent toegang tot alle bewerkingen voor alle resource typen in de micro soft. Compute-resource provider.|
> | `Microsoft.Network/*/read` | Hiermee wordt toegang verleend tot Lees bewerkingen voor alle resource typen in de resource provider micro soft. Network.|
> | `Microsoft.Compute/virtualMachines/*` | Verleent toegang tot alle bewerkingen van virtuele machines en de bijbehorende onderliggende resource typen.|
> | `microsoft.web/sites/restart/Action` | Hiermee wordt toegang verleend om een web-app opnieuw op te starten.|

## <a name="notactions"></a>NotActions

`NotActions`Met de machtiging worden de beheer bewerkingen opgegeven die worden uitgesloten van de toegestaan `Actions` . Gebruik de `NotActions` machtiging als de set bewerkingen die u wilt toestaan eenvoudiger is gedefinieerd door beperkte bewerkingen uit te sluiten. De toegang die wordt verleend door een rol (efficiënte machtigingen) wordt berekend door de `NotActions` bewerkingen van de bewerkingen af te trekken `Actions` .

> [!NOTE]
> Als aan een gebruiker een rol is toegewezen die een bewerking uitsluit in `NotActions` , en aan een tweede rol wordt toegewezen die toegang verleent aan dezelfde bewerking, mag de gebruiker die bewerking uitvoeren. `NotActions` is geen regel voor weigeren: het is een handige manier om een reeks toegestane bewerkingen te maken wanneer specifieke bewerkingen moeten worden uitgesloten.
>

## <a name="dataactions"></a>DataActions

`DataActions`Met de machtiging worden de gegevens bewerkingen opgegeven die door de functie kunnen worden uitgevoerd op uw gegevens in dat object. Als een gebruiker bijvoorbeeld BLOB-gegevens toegang tot een opslag account heeft gelezen, kunnen ze de blobs binnen dat opslag account lezen. Hier volgen enkele voor beelden van gegevens bewerkingen die kunnen worden gebruikt in `DataActions` .

> [!div class="mx-tableFixed"]
> | Bewerkings reeks    | Description         |
> | ------------------- | ------------------- |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read` | Retourneert een BLOB of een lijst met blobs. |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write` | Retourneert het resultaat van het schrijven van een blob. |
> | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/read` | Retourneert een bericht. |
> | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/*` | Retourneert een bericht of het resultaat van het schrijven of verwijderen van een bericht. |

## <a name="notdataactions"></a>NotDataActions

`NotDataActions`Met de machtiging worden de gegevens bewerkingen opgegeven die worden uitgesloten van de toegestaan `DataActions` . De toegang die wordt verleend door een rol (efficiënte machtigingen) wordt berekend door de `NotDataActions` bewerkingen van de bewerkingen af te trekken `DataActions` . Elke resource provider biedt de respectieve set Api's om te voldoen aan gegevens bewerkingen.

> [!NOTE]
> Als aan een gebruiker een rol is toegewezen die een gegevens bewerking uitsluit in en `NotDataActions` waaraan een tweede rol wordt toegewezen die toegang verleent aan dezelfde gegevens bewerking, mag de gebruiker die gegevens bewerking uitvoeren. `NotDataActions` is geen regel voor weigeren: het is een handige manier om een set toegestane gegevens bewerkingen te maken wanneer specifieke gegevens bewerkingen moeten worden uitgesloten.
>

## <a name="assignablescopes"></a>AssignableScopes

`AssignableScopes`Met de eigenschap geeft u de bereiken (beheer groepen, abonnementen of resource groepen) op waarvoor deze functie definitie beschikbaar is. U kunt de rol beschikbaar maken voor toewijzing in alleen de beheer groepen, abonnementen of resource groepen die deze nodig hebben. U moet ten minste één beheer groep, abonnement of resource groep gebruiken.

Ingebouwde rollen zijn `AssignableScopes` ingesteld op het hoofd bereik ( `"/"` ). Het hoofd bereik geeft aan dat de rol beschikbaar is voor toewijzing in alle bereiken. Voor beelden van geldige toewijs bare bereiken zijn:

> [!div class="mx-tableFixed"]
> | De rol is beschikbaar voor toewijzing | Voorbeeld |
> |----------|---------|
> | Eén abonnement | `"/subscriptions/{subscriptionId1}"` |
> | Twee abonnementen | `"/subscriptions/{subscriptionId1}", "/subscriptions/{subscriptionId2}"` |
> | Resourcegroep van netwerk | `"/subscriptions/{subscriptionId1}/resourceGroups/Network"` |
> | Eén beheer groep | `"/providers/Microsoft.Management/managementGroups/{groupId1}"` |
> | Beheer groep en een abonnement | `"/providers/Microsoft.Management/managementGroups/{groupId1}", /subscriptions/{subscriptionId1}",` |
> | Alle bereiken (alleen van toepassing op ingebouwde rollen) | `"/"` |

`AssignableScopes`Zie [aangepaste rollen voor Azure](custom-roles.md)voor meer informatie over aangepaste rollen.

## <a name="next-steps"></a>Volgende stappen

* [Ingebouwde Azure-rollen](built-in-roles.md)
* [Aangepaste Azure-rollen](custom-roles.md)
* [Azure-resource provider bewerkingen](resource-provider-operations.md)
