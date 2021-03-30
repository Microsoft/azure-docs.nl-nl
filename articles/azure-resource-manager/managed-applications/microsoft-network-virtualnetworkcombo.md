---
title: VirtualNetworkCombo UI-element
description: Hierin wordt het element micro soft. Network. VirtualNetworkCombo UI voor Azure Portal beschreven.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/28/2018
ms.author: tomfitz
ms.openlocfilehash: 711f5293b205c1f500c6d9e08154342285ef959b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87033195"
---
# <a name="microsoftnetworkvirtualnetworkcombo-ui-element"></a>Gebruikers interface-element van micro soft. Network. VirtualNetworkCombo

Een groep besturings elementen voor het selecteren van een nieuw of bestaand virtueel netwerk.

## <a name="ui-sample"></a>UI-voor beeld

Wanneer de gebruiker een nieuw virtueel netwerk kiest, kan de gebruiker de naam en het adres voorvoegsel van elk subnet aanpassen. Het configureren van subnetten is optioneel.

![Micro soft. Network. VirtualNetworkCombo nieuw](./media/managed-application-elements/microsoft-network-virtualnetworkcombo-new.png)

Wanneer de gebruiker een bestaand virtueel netwerk kiest, moet de gebruiker elk subnet toewijzen de implementatie sjabloon vereist een bestaand subnet. In dit geval moeten subnetten worden geconfigureerd.

![Micro soft. Network. VirtualNetworkCombo bestaand](./media/managed-application-elements/microsoft-network-virtualnetworkcombo-existing.png)

## <a name="schema"></a>Schema

```json
{
  "name": "element1",
  "type": "Microsoft.Network.VirtualNetworkCombo",
  "label": {
    "virtualNetwork": "Virtual network",
    "subnets": "Subnets"
  },
  "toolTip": {
    "virtualNetwork": "",
    "subnets": ""
  },
  "defaultValue": {
    "name": "vnet01",
    "addressPrefixSize": "/16"
  },
  "constraints": {
    "minAddressPrefixSize": "/16"
  },
  "options": {
    "hideExisting": false
  },
  "subnets": {
    "subnet1": {
      "label": "First subnet",
      "defaultValue": {
        "name": "subnet-1",
        "addressPrefixSize": "/24"
      },
      "constraints": {
        "minAddressPrefixSize": "/24",
        "minAddressCount": 12,
        "requireContiguousAddresses": true
      }
    },
    "subnet2": {
      "label": "Second subnet",
      "defaultValue": {
        "name": "subnet-2",
        "addressPrefixSize": "/26"
      },
      "constraints": {
        "minAddressPrefixSize": "/26",
        "minAddressCount": 8,
        "requireContiguousAddresses": true
      }
    }
  },
  "visible": true
}
```

## <a name="sample-output"></a>Voorbeelduitvoer

```json
{
  "name": "vnet01",
  "resourceGroup": "rg01",
  "addressPrefixes": ["10.0.0.0/16"],
  "newOrExisting": "new",
  "subnets": {
    "subnet1": {
      "name": "subnet-1",
      "addressPrefix": "10.0.0.0/24",
      "startAddress": "10.0.0.1"
    },
    "subnet2": {
      "name": "subnet-2",
      "addressPrefix": "10.0.1.0/26",
      "startAddress": "10.0.1.1"
    }
  }
}
```

## <a name="remarks"></a>Opmerkingen

- Indien opgegeven, wordt het eerste niet-overlappende adres voorvoegsel van grootte `defaultValue.addressPrefixSize` automatisch bepaald op basis van de bestaande virtuele netwerken in het abonnement van de gebruiker.
- De standaard waarde voor `defaultValue.name` en `defaultValue.addressPrefixSize` is **Null**.
- `constraints.minAddressPrefixSize` moet worden opgegeven. Bestaande virtuele netwerken met een adres ruimte die kleiner is dan de opgegeven waarde, zijn niet beschikbaar voor selectie.
- `subnets` moet worden opgegeven en `constraints.minAddressPrefixSize` moet voor elk subnet worden opgegeven.
- Wanneer u een nieuw virtueel netwerk maakt, wordt het adres voorvoegsel van elk subnet automatisch berekend op basis van het adres voorvoegsel van het virtuele netwerk en de respectieve `addressPrefixSize` .
- Wanneer u een bestaand virtueel netwerk gebruikt, zijn alle subnetten die kleiner zijn dan de respectieve, `constraints.minAddressPrefixSize` niet beschikbaar voor selectie. Bovendien, indien opgegeven, subnetten die geen mini maal `minAddressCount` beschik bare adressen hebben, zijn niet beschikbaar voor selectie. De standaard waarde is **0**. Geef **waar** op als u wilt dat de beschik bare adressen aaneengesloten zijn `requireContiguousAddresses` . De standaard waarde is **True**.
- Het maken van subnetten in een bestaand virtueel netwerk wordt niet ondersteund.
- Als `options.hideExisting` de **waarde True** is, kan de gebruiker geen bestaand virtueel netwerk kiezen. De standaardwaarde is **onwaar**.

## <a name="next-steps"></a>Volgende stappen

* Zie aan de slag [met CreateUiDefinition](create-uidefinition-overview.md)voor een inleiding tot het maken van UI-definities.
* Zie [CreateUiDefinition-elementen](create-uidefinition-elements.md)voor een beschrijving van algemene eigenschappen in UI-elementen.
