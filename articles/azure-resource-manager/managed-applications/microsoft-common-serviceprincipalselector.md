---
title: ServicePrincipalSelector UI-element
description: Hierin wordt het micro soft. common. ServicePrincipalSelector UI-element voor Azure Portal beschreven. Biedt een besturings element voor het kiezen van een toepassing en een tekstvak voor het invoeren van een wacht woord of vinger afdruk van een certificaat.
author: tfitzmac
ms.topic: conceptual
ms.date: 11/17/2020
ms.author: tomfitz
ms.openlocfilehash: 2fdbbad467d8c762db485fc7935e9cef78313fd0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96184447"
---
# <a name="microsoftcommonserviceprincipalselector-ui-element"></a>Micro soft. common. ServicePrincipalSelector UI-element

Een besturings element waarmee gebruikers een bestaande [Service-Principal](../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) kunnen selecteren of een nieuwe toepassing registreren. Wanneer u **Nieuw maken** selecteert, volgt u de stappen om een nieuwe toepassing te registreren. Wanneer u een bestaande toepassing selecteert, biedt het besturings element een tekstvak voor het invoeren van een wacht woord of vinger afdruk van het certificaat.

## <a name="ui-samples"></a>Voor beelden van gebruikers interface

U kunt een standaard toepassing gebruiken, een nieuwe toepassing maken of een bestaande toepassing gebruiken.

### <a name="use-default-application-or-create-new"></a>Standaard toepassing gebruiken of nieuwe maken

De standaard weergave wordt bepaald door de waarden in de `defaultValue` eigenschap en het **type service-principal** is ingesteld op **nieuwe maken**. Als de `principalId` eigenschap een geldig Globally Unique Identifier (GUID) bevat, zoekt het besturings element naar de toepassing `objectId` . De standaard waarde is van toepassing als de gebruiker geen selectie van het besturings element maakt.

Als u een nieuwe toepassing wilt registreren, selecteert u **Selectie wijzigen** en het dialoog venster **een toepassing registreren** wordt weer gegeven. Voer **naam** in, **ondersteund account type** en selecteer de knop **registreren** .

:::image type="content" source="./media/managed-application-elements/microsoft-common-serviceprincipal-default.png" alt-text="Initiële weer gave van micro soft. common. ServicePrincipalSelector.":::

Nadat u een nieuwe toepassing hebt geregistreerd, gebruikt u het **verificatie type** om een wacht woord of vinger afdruk van het certificaat in te voeren.

:::image type="content" source="./media/managed-application-elements/microsoft-common-serviceprincipal-authenticate.png" alt-text="Micro soft. common. ServicePrincipalSelector-verificatie.":::

### <a name="use-existing-application"></a>Bestaande toepassing gebruiken

Als u een bestaande toepassing wilt gebruiken, kiest u **bestaande selecteren** en selecteert **u vervolgens selectie maken**. In het dialoog venster **een toepassing selecteren** kunt u zoeken naar de naam van de toepassing. Selecteer in de resultaten de toepassing en vervolgens de knop **selecteren** . Nadat u een toepassing hebt geselecteerd, wordt het **verificatie type** voor het besturings element weer gegeven om een wacht woord of vinger afdruk van het certificaat in te voeren.

:::image type="content" source="./media/managed-application-elements/microsoft-common-serviceprincipal-existing.png" alt-text="Micro soft. common. ServicePrincipalSelector Selecteer bestaande toepassing.":::

## <a name="schema"></a>Schema

```json
{
  "name": "ServicePrincipal",
  "type": "Microsoft.Common.ServicePrincipalSelector",
  "label": {
    "password": "Password",
    "certificateThumbprint": "Certificate thumbprint",
    "authenticationType": "Authentication Type",
    "sectionHeader": "Service Principal"
  },
  "toolTip": {
    "password": "Password",
    "certificateThumbprint": "Certificate thumbprint",
    "authenticationType": "Authentication Type"
  },
  "defaultValue": {
    "principalId": "<default guid>",
    "name": "(New) default App Id"
  },
  "constraints": {
    "required": true,
    "regex": "^[a-zA-Z0-9]{8,}$",
    "validationMessage": "Password must be at least 8 characters long, contain only numbers and letters"
  },
  "options": {
    "hideCertificate": false
  },
  "visible": true
}
```

## <a name="remarks"></a>Opmerkingen

- De vereiste eigenschappen zijn als volgt:
  - `name`
  - `type`
  - `label`
  - `defaultValue`: Hiermee geeft u de standaard waarde `principalId` en op `name` .

- De optionele eigenschappen zijn als volgt:
  - `toolTip`: Hiermee wordt een knop Info `infoBalloon` aan elk label gekoppeld.
  - `visible`: Het besturings element verbergen of weer geven.
  - `options`: Hiermee geeft u op of de optie voor de vinger afdruk van het certificaat beschikbaar moet worden gemaakt.
  - `constraints`: Regex-beperkingen voor wachtwoord validatie.

## <a name="example"></a>Voorbeeld

Hier volgt een voor beeld van het `Microsoft.Common.ServicePrincipalSelector` besturings element. De `defaultValue` eigenschap wordt `principalId` ingesteld `<default guid>` als een tijdelijke aanduiding voor een standaard toepassings-id-GUID.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [],
    "steps": [
      {
        "name": "SPNcontrol",
        "label": "SPNcontrol",
        "elements": [
          {
            "name": "ServicePrincipal",
            "type": "Microsoft.Common.ServicePrincipalSelector",
            "label": {
              "password": "Password",
              "certificateThumbprint": "Certificate thumbprint",
              "authenticationType": "Authentication Type",
              "sectionHeader": "Service Principal"
            },
            "toolTip": {
              "password": "Password",
              "certificateThumbprint": "Certificate thumbprint",
              "authenticationType": "Authentication Type"
            },
            "defaultValue": {
              "principalId": "<default guid>",
              "name": "(New) default App Id"
            },
            "constraints": {
              "required": true,
              "regex": "^[a-zA-Z0-9]{8,}$",
              "validationMessage": "Password must be at least 8 characters long, contain only numbers and letters"
            },
            "options": {
              "hideCertificate": false
            },
            "visible": true
          }
        ]
      }
    ],
    "outputs": {
      "appId": "[steps('SPNcontrol').ServicePrincipal.appId]",
      "objectId": "[steps('SPNcontrol').ServicePrincipal.objectId]",
      "password": "[steps('SPNcontrol').ServicePrincipal.password]",
      "certificateThumbprint": "[steps('SPNcontrol').ServicePrincipal.certificateThumbprint]",
      "newOrExisting": "[steps('SPNcontrol').ServicePrincipal.newOrExisting]",
      "authenticationType": "[steps('SPNcontrol').ServicePrincipal.authenticationType]"
    }
  }
}
```

## <a name="example-output"></a>Voorbeelduitvoer

De `appId` is de id van de registratie van de toepassing die u hebt geselecteerd of gemaakt. De `objectId` is een matrix met object-id's voor de service-principals die zijn geconfigureerd voor de registratie van de geselecteerde toepassing.

Als er geen selectie wordt gemaakt vanuit het besturings element, is de waarde van de `newOrExisting` eigenschap **Nieuw**:

```json
{
  "appId": {
    "value": "<default guid>"
  },
  "objectId": {
    "value": ["<default guid>"]
  },
  "password": {
    "value": "<password>"
  },
  "certificateThumbprint": {
    "value": ""
  },
  "newOrExisting": {
    "value": "new"
  },
  "authenticationType": {
    "value": "password"
  }
}
```

Bij het maken van een **nieuwe** of een bestaande toepassing is geselecteerd in het besturings element, de waarde van de `newOrExisting` eigenschap is **bestaande**:

```json
{
  "appId": {
    "value": "<guid>"
  },
  "objectId": {
    "value": ["<guid>"]
  },
  "password": {
    "value": "<password>"
  },
  "certificateThumbprint": {
    "value": ""
  },
  "newOrExisting": {
    "value": "existing"
  },
  "authenticationType": {
    "value": "password"
  }
}
```

## <a name="next-steps"></a>Volgende stappen

- Zie aan de slag [met CreateUiDefinition](create-uidefinition-overview.md)voor een inleiding tot het maken van UI-definities.
- Zie [CreateUiDefinition-elementen](create-uidefinition-elements.md)voor een beschrijving van algemene eigenschappen in UI-elementen.