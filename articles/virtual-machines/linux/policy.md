---
title: Beveiliging afdwingen met beleids regels op virtuele Linux-machines in azure
description: Een beleid Toep assen op een virtuele machine met Azure Resource Manager Linux
author: mimckitt
ms.service: virtual-machines
ms.collection: linux
ms.subservice: security
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 08/02/2017
ms.author: mimckitt
ms.openlocfilehash: c1fb80001f0669672e9f6e8a567685b799314df1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102549818"
---
# <a name="apply-policies-to-linux-vms-with-azure-resource-manager"></a>Beleids regels Toep assen op virtuele Linux-machines met Azure Resource Manager
Door gebruik te maken van beleids regels kan een organisatie verschillende conventies en regels voor de hele onderneming afdwingen. Afdwinging van het gewenste gedrag kan het risico verkleinen en bijdragen aan het succes van de organisatie. In dit artikel wordt beschreven hoe u Azure Resource Manager-beleid kunt gebruiken om het gewenste gedrag voor de Virtual Machines van uw organisatie te definiëren.

Zie [Wat is Azure Policy?](../../governance/policy/overview.md)voor een inleiding tot het beleid.

## <a name="permitted-virtual-machines"></a>Toegestaan Virtual Machines
Om ervoor te zorgen dat virtuele machines voor uw organisatie compatibel zijn met een toepassing, kunt u de toegestane besturings systemen beperken. In het volgende voor beeld is het mogelijk dat alleen Ubuntu 14.04.2-LTS-Virtual Machines worden gemaakt.

```json
{
  "if": {
    "allOf": [
      {
        "field": "type",
        "in": [
          "Microsoft.Compute/disks",
          "Microsoft.Compute/virtualMachines",
          "Microsoft.Compute/VirtualMachineScaleSets"
        ]
      },
      {
        "not": {
          "allOf": [
            {
              "field": "Microsoft.Compute/imagePublisher",
              "in": [
                "Canonical"
              ]
            },
            {
              "field": "Microsoft.Compute/imageOffer",
              "in": [
                "UbuntuServer"
              ]
            },
            {
              "field": "Microsoft.Compute/imageSku",
              "in": [
                "14.04.2-LTS"
              ]
            },
            {
              "field": "Microsoft.Compute/imageVersion",
              "in": [
                "latest"
              ]
            }
          ]
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

Gebruik een Joker teken voor het wijzigen van het voor gaande beleid om een Ubuntu LTS-installatie kopie toe te staan: 

```json
{
  "field": "Microsoft.Compute/virtualMachines/imageSku",
  "like": "*LTS"
}
```

Zie [beleids aliassen](../../governance/policy/concepts/definition-structure.md#aliases)voor informatie over beleids velden.

## <a name="managed-disks"></a>Managed Disks

Gebruik het volgende beleid om het gebruik van beheerde schijven te vereisen:

```json
{
  "if": {
    "anyOf": [
      {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.Compute/virtualMachines"
          },
          {
            "field": "Microsoft.Compute/virtualMachines/osDisk.uri",
            "exists": true
          }
        ]
      },
      {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.Compute/VirtualMachineScaleSets"
          },
          {
            "anyOf": [
              {
                "field": "Microsoft.Compute/VirtualMachineScaleSets/osDisk.vhdContainers",
                "exists": true
              },
              {
                "field": "Microsoft.Compute/VirtualMachineScaleSets/osdisk.imageUrl",
                "exists": true
              }
            ]
          }
        ]
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

## <a name="images-for-virtual-machines"></a>Installatie kopieën voor Virtual Machines

Uit veiligheids overwegingen kunt u vereisen dat alleen goedgekeurde aangepaste installatie kopieën in uw omgeving worden geïmplementeerd. U kunt de resource groep opgeven die de goedgekeurde installatie kopieën bevat of de specifieke goedgekeurde installatie kopieën.

In het volgende voor beeld zijn installatie kopieën van een goedgekeurde resource groep vereist:

```json
{
    "if": {
        "allOf": [
            {
                "field": "type",
                "in": [
                    "Microsoft.Compute/virtualMachines",
                    "Microsoft.Compute/VirtualMachineScaleSets"
                ]
            },
            {
                "not": {
                    "field": "Microsoft.Compute/imageId",
                    "contains": "resourceGroups/CustomImage"
                }
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
} 
```

In het volgende voor beeld worden de goedgekeurde afbeeldings-Id's opgegeven:

```json
{
    "field": "Microsoft.Compute/imageId",
    "in": ["{imageId1}","{imageId2}"]
}
```

## <a name="virtual-machine-extensions"></a>Extensies voor virtuele machines

Mogelijk wilt u het gebruik van bepaalde typen uitbrei dingen verbieden. Een extensie is bijvoorbeeld mogelijk niet compatibel met bepaalde installatie kopieën van virtuele machines. In het volgende voor beeld ziet u hoe u een specifieke uitbrei ding blokkeert. Hierbij wordt gebruikgemaakt van uitgever en type om te bepalen welke extensie moet worden geblokkeerd.

```json
{
    "if": {
        "allOf": [
            {
                "field": "type",
                "equals": "Microsoft.Compute/virtualMachines/extensions"
            },
            {
                "field": "Microsoft.Compute/virtualMachines/extensions/publisher",
                "equals": "Microsoft.Compute"
            },
            {
                "field": "Microsoft.Compute/virtualMachines/extensions/type",
                "equals": "{extension-type}"

      }
        ]
    },
    "then": {
        "effect": "deny"
    }
}
```


## <a name="next-steps"></a>Volgende stappen
* Nadat u een beleids regel hebt gedefinieerd (zoals weer gegeven in de voor gaande voor beelden), moet u de beleids definitie maken en deze toewijzen aan een bereik. Het bereik kan een abonnement, een resource groep of een resource zijn. Zie [Azure Portal gebruiken om](../../governance/policy/assign-policy-portal.md)beleids regels toe te wijzen en te beheren, [Power shell gebruiken om beleid toe te](../../governance/policy/assign-policy-powershell.md)wijzen of [Azure CLI gebruiken om beleid](../../governance/policy/assign-policy-azurecli.md)toe te wijzen.
* Zie [Wat is Azure Policy?](../../governance/policy/overview.md)voor een inleiding tot bron beleid.
* Voor begeleiding bij de manier waarop ondernemingen Resource Manager effectief kunnen gebruiken voor het beheer van abonnementen, gaat u naar [Azure enterprise-platform - Prescriptieve abonnementsgovernance](/azure/architecture/cloud-adoption-guide/subscription-governance).
