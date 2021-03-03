---
title: Informatie over het bijwerken van apparaten voor Azure IoT Hub-apparaatgroepen | Microsoft Docs
description: Begrijpen hoe apparaatgroepen worden gebruikt.
author: aysancag
ms.author: aysancag
ms.date: 2/09/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 18388f067ccb5b8a8876aeae685664694c207613
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101679227"
---
# <a name="device-groups"></a>Device groups

Een apparaatgroep is een verzameling apparaten. Apparaatgroepen bieden een manier om implementaties op veel apparaten te schalen. Elk apparaat behoort tot precies één apparaatgroep tegelijk.
U kunt ervoor kiezen om meerdere apparaatgroepen te maken om uw apparaten te organiseren. Contoso kan bijvoorbeeld de apparaatgroep ' Flighting ' gebruiken voor de apparaten in het test laboratorium en de apparaatgroep ' evaluatie ' voor de apparaten die het veld team in het Operations Center gebruikt. Daarnaast kan contoso ervoor kiezen om hun productie-apparaten te groeperen op basis van hun geografische regio's, zodat ze apparaten kunnen bijwerken volgens een schema dat wordt afgestemd op hun regionale tijd zones. 


## <a name="using-device-twin-tag-for-device-group-creation"></a>Apparaatbesturing-tag gebruiken voor het maken van een apparaatgroep

Met dubbele Tags voor apparaten kunnen gebruikers apparaten groeperen. Apparaten moeten beschikken over een ADUGroup-sleutel en een waarde op hun apparaat, waar ze kunnen worden gegroepeerd.

### <a name="device-twin-tag-format"></a>Dubbele label indeling voor apparaat

```markdown
"tags": {
  "ADUGroup": "<CustomTagValue>"
}
```


## <a name="uncategorized-device-group"></a>Niet-gecategoriseerde apparaatgroep

Niet-gecategoriseerd is een gereserveerd woord dat wordt gebruikt om apparaten te groeperen die:
- Geen dubbele tag van het ADUGroup-apparaat.
- Een ADUGroup-apparaat met dubbele tag, maar er is geen groep gemaakt met deze groeps naam.

Denk bijvoorbeeld aan de apparaten met de dubbele Tags van het apparaat hieronder:

```markdown
"deviceId": "Device1",
"tags": {
  "ADUGroup": "Group1"
}
```

```markdown
"deviceId": "Device2",
"tags": {
  "ADUGroup": "Group1"
}
```

```markdown
"deviceId": "Device3",
"tags": {
  "ADUGroup": "Group2"
}
```

```markdown
"deviceId": "Device4",
```

Hieronder vindt u de apparaten en de mogelijke groepen die voor hen kunnen worden gemaakt.

|Apparaat |Groep  |
|-----------|--------------|
|Device1    |Group1|
|Device2    |Group1|
|Device3    |Group2|
|Device4    |Niet-gecategoriseerd|



## <a name="next-steps"></a>Volgende stappen

[Apparaatgroep maken](./create-update-group.md)
