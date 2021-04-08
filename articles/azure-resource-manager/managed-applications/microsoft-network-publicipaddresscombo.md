---
title: PublicIpAddressCombo UI-element
description: Hierin wordt het element micro soft. Network. PublicIpAddressCombo UI voor Azure Portal beschreven.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/28/2018
ms.author: tomfitz
ms.openlocfilehash: 5def6db9d551b3882204c9f997f164a0df7ac223
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87063284"
---
# <a name="microsoftnetworkpublicipaddresscombo-ui-element"></a>Gebruikers interface-element van micro soft. Network. PublicIpAddressCombo

Een groep besturings elementen voor het selecteren van een nieuw of bestaand openbaar IP-adres.

## <a name="ui-sample"></a>UI-voor beeld

![Microsoft.Network.PublicIpAddressCombo](./media/managed-application-elements/microsoft-network-publicipaddresscombo.png)

- Als de gebruiker geen selecteert voor het open bare IP-adres, wordt het tekstvak domeinnaam label verborgen.
- Als de gebruiker een bestaand openbaar IP-adres selecteert, wordt het tekstvak domeinnaam label uitgeschakeld. De waarde is het domein naam label van het geselecteerde IP-adres.
- Het achtervoegsel voor de domein naam (bijvoorbeeld westus.cloudapp.azure.com) wordt automatisch bijgewerkt op basis van de geselecteerde locatie.

## <a name="schema"></a>Schema

```json
{
  "name": "element1",
  "type": "Microsoft.Network.PublicIpAddressCombo",
  "label": {
    "publicIpAddress": "Public IP address",
    "domainNameLabel": "Domain name label"
  },
  "toolTip": {
    "publicIpAddress": "",
    "domainNameLabel": ""
  },
  "defaultValue": {
    "publicIpAddressName": "ip01",
    "domainNameLabel": "mydomain"
  },
  "constraints": {
    "required": {
      "domainNameLabel": true
    }
  },
  "options": {
    "hideNone": false,
    "hideDomainNameLabel": false,
    "hideExisting": false,
    "zone": 3
  },
  "visible": true
}
```

## <a name="sample-output"></a>Voorbeelduitvoer

Als de gebruiker geen openbaar IP-adres selecteert, retourneert het besturings element de volgende uitvoer:

```json
{
  "newOrExistingOrNone": "none"
}
```

Als de gebruiker een nieuw of bestaand IP-adres selecteert, retourneert het besturings element de volgende uitvoer:

```json
{
  "name": "ip01",
  "resourceGroup": "rg01",
  "domainNameLabel": "mydomain",
  "publicIPAllocationMethod": "Dynamic",
  "sku": "Basic",
  "newOrExistingOrNone": "new"
}
```

- Wanneer `options.hideNone` is opgegeven als **waar**, heeft `newOrExistingOrNone` alleen de waarde **Nieuw** of **bestaand**.
- Als `options.hideDomainNameLabel` is opgegeven als **waar**, `domainNameLabel` is niet-gedeclareerd.

## <a name="remarks"></a>Opmerkingen

- Als `constraints.required.domainNameLabel` is ingesteld op **waar**, moet de gebruiker een domein naam label opgeven bij het maken van een nieuw openbaar IP-adres. Bestaande open bare IP-adressen zonder label zijn niet beschikbaar voor selectie.
- Als `options.hideNone` is ingesteld op **True**, wordt de optie voor het selecteren van **geen** voor het open bare IP-adres verborgen. De standaardwaarde is **onwaar**.
- Als `options.hideDomainNameLabel` is ingesteld op **True**, wordt het tekstvak voor het domein naam label verborgen. De standaardwaarde is **onwaar**.
- Als `options.hideExisting` de waarde True is, kan de gebruiker geen bestaand openbaar IP-adres kiezen. De standaardwaarde is **onwaar**.
- Voor `zone` worden alleen open bare IP-adressen voor de opgegeven zone of zone met flexibele open bare IP-adressen beschikbaar.

## <a name="next-steps"></a>Volgende stappen

* Zie aan de slag [met CreateUiDefinition](create-uidefinition-overview.md)voor een inleiding tot het maken van UI-definities.
* Zie [CreateUiDefinition-elementen](create-uidefinition-elements.md)voor een beschrijving van algemene eigenschappen in UI-elementen.
