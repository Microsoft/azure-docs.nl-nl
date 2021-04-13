---
author: baanders
description: bestand insluiten voor het uploaden van een model naar een Azure Digital Apparaatdubbels-exemplaar
ms.service: digital-twins
ms.topic: include
ms.date: 4/1/2021
ms.author: baanders
ms.openlocfilehash: add49cabaece1187cb9bcda3a94c92feb08b2439
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107304291"
---
Het model ziet er als volgt uit:
:::code language="json" source="~/digital-twins-docs-samples/models/Thermostat.json":::

Als u **dit model wilt uploaden naar uw apparaatdubbels-exemplaar**, voert u de volgende Azure cli-opdracht uit, die het bovenstaande model uploadt als inline JSON. U kunt de opdracht uitvoeren in [Azure Cloud shell](../articles/cloud-shell/overview.md) in uw browser (gebruik de **bash** -omgeving) of op uw computer als u de CLI [lokaal hebt geïnstalleerd](/cli/azure/install-azure-cli).

```azurecli-interactive
az dt model create --models '{  "@id": "dtmi:contosocom:DigitalTwins:Thermostat;1",  "@type": "Interface",  "@context": "dtmi:dtdl:context;2",  "contents": [    {      "@type": "Property",      "name": "Temperature",      "schema": "double"    }  ]}' -n {digital_twins_instance_name}
```