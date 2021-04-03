---
title: IdentitySelector UI-element
description: Hierin wordt het micro soft. ManagedIdentity. IdentitySelector UI-element voor Azure Portal beschreven. Gebruiken voor het toewijzen van beheerde identiteiten aan een resource.
author: tfitzmac
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: tomfitz
ms.openlocfilehash: 91e3cd0b99825fd72eb342ce7a8555b046455538
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87063353"
---
# <a name="microsoftmanagedidentityidentityselector-ui-element"></a>Micro soft. ManagedIdentity. IdentitySelector UI-element

Een besturings element voor het toewijzen van [beheerde identiteiten](../../active-directory/managed-identities-azure-resources/overview.md) voor een bron in een implementatie.

## <a name="ui-sample"></a>UI-voor beeld

Het besturings element bestaat uit de volgende elementen:

![Micro soft. ManagedIdentity. IdentitySelector eerste stap](./media/managed-application-elements/microsoft-managedidentity-identityselector-1.png)

Wanneer de gebruiker **toevoegen** selecteert, wordt het volgende formulier geopend. De gebruiker kan een of meer door de gebruiker toegewezen identiteiten voor de resource selecteren.

![Micro soft. ManagedIdentity. IdentitySelector tweede stap](./media/managed-application-elements/microsoft-managedidentity-identityselector-2.png)

De geselecteerde identiteiten worden weer gegeven in de tabel. De gebruiker kan items uit deze tabel toevoegen of verwijderen.

![Micro soft. ManagedIdentity. IdentitySelector derde stap](./media/managed-application-elements/microsoft-managedidentity-identityselector-3.png)

## <a name="schema"></a>Schema

```json
{
  "name": "identity",
  "type": "Microsoft.ManagedIdentity.IdentitySelector",
  "label": "Managed Identity Configuration",
  "toolTip": {
    "systemAssignedIdentity": "Enable system assigned identity to grant the resource access to other existing resources.",
    "userAssignedIdentity": "Add user assigned identities to grant the resource access to other existing resources."
  },
  "defaultValue": {
    "systemAssignedIdentity": "Off"
  },
  "options": {
    "hideSystemAssignedIdentity": false,
    "hideUserAssignedIdentity": false
  },
  "visible": true
}
```

## <a name="sample-output"></a>Voorbeelduitvoer

```json
{
  "identity": {
    "value": {
      "type": "UserAssigned",
      "userAssignedIdentities": {
        "/subscriptions/xxxx/resourceGroups/TestResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/TestUserIdentity1": {}
      }
    }
  }
}
```

## <a name="remarks"></a>Opmerkingen

- Gebruik **defaultValue.systemAssignedIdentity** om een begin waarde in te stellen voor het besturings element identiteits opties die door het systeem zijn toegewezen. De standaard waarde is **uitgeschakeld**. De volgende waarden zijn toegestaan:
  - **Aan** : een door het systeem toegewezen identiteit wordt toegewezen aan de resource.
  - **Uit** : een door het systeem toegewezen identiteit is niet toegewezen aan de resource.
  - **OnOnly** : een door het systeem toegewezen identiteit wordt toegewezen aan de resource. Gebruikers kunnen deze waarde niet bewerken tijdens de implementatie.
  - **OffOnly** : een door het systeem toegewezen identiteit is niet toegewezen aan de resource. Gebruikers kunnen deze waarde niet bewerken tijdens de implementatie.

- Als **Options. hideSystemAssignedIdentity** is ingesteld op **True**, wordt de gebruikers interface voor het configureren van de door het systeem toegewezen identiteit niet weer gegeven. De standaard waarde voor deze optie is **Onwaar**.
- Als **Options. hideUserAssignedIdentity** is ingesteld op **True**, wordt de gebruikers interface voor het configureren van de door de gebruiker toegewezen identiteit niet weer gegeven. Aan de resource is geen door de gebruiker toegewezen identiteit toegewezen. De standaard waarde voor deze optie is **Onwaar**.

## <a name="next-steps"></a>Volgende stappen

- Zie aan de slag [met CreateUiDefinition](create-uidefinition-overview.md)voor een inleiding tot het maken van UI-definities.
- Zie [CreateUiDefinition-elementen](create-uidefinition-elements.md)voor een beschrijving van algemene eigenschappen in UI-elementen.