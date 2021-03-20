---
title: CreateUiDefinition artefact
description: Laat zien hoe u het createUiDefinition-artefact maakt voor een door Azure beheerde toepassing. Het bestand heeft de naam createUiDefinition.jsop.
ms.topic: conceptual
ms.author: lazinnat
author: lazinnat
ms.date: 07/11/2019
ms.openlocfilehash: 9be1cb02c419314a33b9487bf183ef3659b2b549
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "75651486"
---
# <a name="reference-user-interface-elements-artifact"></a>Verwijzing: elementen artefact van gebruikers interface

Dit artikel bevat een verwijzing naar een *createUiDefinition.jsvoor* artefacten in azure Managed Applications. Zie elementen van de [gebruikers interface maken](create-uidefinition-elements.md)voor meer informatie over het ontwerpen van gebruikers interface-elementen.

## <a name="user-interface-elements"></a>Elementen van de gebruikersinterface

In de volgende JSON ziet u een voor beeld van *createUiDefinition.jsop* bestand voor Azure Managed Applications:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {}
    ],
    "steps": [
      {
        "name": "applicationSettings",
        "label": "Application Settings",
        "subLabel": {
          "preValidation": "Configure your application settings",
          "postValidation": "Done"
        },
        "bladeTitle": "Application Settings",
        "elements": [
          {
            "name": "funcname",
            "type": "Microsoft.Common.TextBox",
            "label": "Name of the function to be created",
            "toolTip": "Name of the function to be created",
            "visible": true,
            "constraints": {
              "required": true
            }
          },
          {
            "name": "storagename",
            "type": "Microsoft.Common.TextBox",
            "label": "Name of the storage to be created",
            "toolTip": "Name of the storage to be created",
            "visible": true,
            "constraints": {
              "required": true
            }
          },
          {
            "name": "zipFileBlobUri",
            "type": "Microsoft.Common.TextBox",
            "defaultValue": "https://github.com/Azure/azure-quickstart-templates/tree/master/101-custom-rp-with-function/artifacts/functionzip/functionpackage.zip",
            "label": "The Uri to the uploaded function zip file",
            "toolTip": "The Uri to the uploaded function zip file",
            "visible": true
          }
        ]
      }
    ],
    "outputs": {
      "funcname": "[steps('applicationSettings').funcname]",
      "storageName": "[steps('applicationSettings').storagename]",
      "zipFileBlobUri": "[steps('applicationSettings').zipFileBlobUri]"
    }
  }
}
```

## <a name="next-steps"></a>Volgende stappen

- [Zelfstudie: Een beheerde toepassing maken met aangepaste acties en resources](tutorial-create-managed-app-with-custom-provider.md)
- [Verwijzing: implementatie sjabloon artefact](reference-main-template-artifact.md)
- [Verwijzing: definitie-artefact weer geven](reference-view-definition-artifact.md)