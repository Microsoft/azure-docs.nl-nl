---
title: PasswordBox UI-element
description: Hierin wordt het micro soft. common. PasswordBox UI-element voor Azure Portal beschreven. Hiermee kunnen gebruikers een geheime waarde opgeven bij het implementeren van beheerde toepassingen.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: 7902ea2e30dec20e57d88344dc9507aec3993600
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87064097"
---
# <a name="microsoftcommonpasswordbox-ui-element"></a>Micro soft. common. PasswordBox UI-element

Een besturings element dat kan worden gebruikt om een wacht woord op te geven en te bevestigen.

## <a name="ui-sample"></a>UI-voor beeld

![Microsoft.Common.PasswordBox](./media/managed-application-elements/microsoft-common-passwordbox.png)

## <a name="schema"></a>Schema

```json
{
  "name": "element1",
  "type": "Microsoft.Common.PasswordBox",
  "label": {
    "password": "Password",
    "confirmPassword": "Confirm password"
  },
  "toolTip": "",
  "constraints": {
    "required": true,
    "regex": "^[a-zA-Z0-9]{8,}$",
    "validationMessage": "Password must be at least 8 characters long, contain only numbers and letters"
  },
  "options": {
    "hideConfirmation": false
  },
  "visible": true
}
```

## <a name="sample-output"></a>Voorbeelduitvoer

```json
"p4ssw0rd"
```

## <a name="remarks"></a>Opmerkingen

- Dit element biedt geen ondersteuning voor de `defaultValue` eigenschap.
- `constraints`Zie [micro soft. common. TextBox](microsoft-common-textbox.md)voor meer informatie over de implementatie van.
- Als `options.hideConfirmation` is ingesteld op **True**, wordt het tweede tekstvak voor het bevestigen van het wacht woord van de gebruiker verborgen. De standaardwaarde is **onwaar**.

## <a name="next-steps"></a>Volgende stappen

* Zie aan de slag [met CreateUiDefinition](create-uidefinition-overview.md)voor een inleiding tot het maken van UI-definities.
* Zie [CreateUiDefinition-elementen](create-uidefinition-elements.md)voor een beschrijving van algemene eigenschappen in UI-elementen.
