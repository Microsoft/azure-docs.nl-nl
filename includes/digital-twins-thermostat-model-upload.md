---
author: baanders
description: bestand insluiten voor het uploaden van een model naar een Azure Digital Apparaatdubbels-exemplaar
ms.service: digital-twins
ms.topic: include
ms.date: 3/23/2021
ms.author: baanders
ms.openlocfilehash: 987409f070f34f0fd9ee212fab8cc57e889e0146
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2021
ms.locfileid: "104950588"
---
Het model ziet er als volgt uit:
:::code language="json" source="~/digital-twins-docs-samples/models/Thermostat.json":::

Als u **dit model wilt uploaden naar uw apparaatdubbels-exemplaar**, voert u de volgende Azure cli-opdracht uit, die het bovenstaande model uploadt als inline JSON. U kunt de opdracht uitvoeren in [Azure Cloud shell](../articles/cloud-shell/overview.md) in uw browser of op uw computer als u de CLI lokaal hebt [geïnstalleerd](/cli/azure/install-azure-cli).

```azurecli-interactive
az dt model create --models '{  "@id": "dtmi:contosocom:DigitalTwins:Thermostat;1",  "@type": "Interface",  "@context": "dtmi:dtdl:context;2",  "contents": [    {      "@type": "Property",      "name": "Temperature",      "schema": "double"    }  ]}' -n {digital_twins_instance_name}
```

> [!Note]
> Als u Cloud Shell in de Power shell-omgeving gebruikt, moet u mogelijk de aanhalings tekens in de inline JSON-velden voor hun waarden weglaten worden geparseerd. Hier volgt de opdracht voor het uploaden van het model met deze wijziging:
>
> Model uploaden:
> ```azurecli-interactive
> az dt model create --models '{  \"@id\": \"dtmi:contosocom:DigitalTwins:Thermostat;1\",  \"@type\": \"Interface\",  \"@context\": \"dtmi:dtdl:context;2\",  \"contents\": [    {      \"@type\": \"Property\",      \"name\": \"Temperature\",      \"schema\": \"double\"    }  ]}' -n {digital_twins_instance_name}
> ```