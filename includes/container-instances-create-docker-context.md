---
title: bestand opnemen
description: bestand opnemen
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: include
ms.date: 08/13/2020
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 3221c4f3e196cf1573bd7c0424fa3b4530c0b2ca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "90708251"
---
## <a name="create-azure-context"></a>Azure-context maken

Als u Docker-opdrachten wilt gebruiken voor het uitvoeren van containers in Azure Container Instances, meldt u zich eerst aan bij Azure:

```bash
docker login azure
```

Voer uw Azure-referenties in of selecteer deze wanneer dit wordt gevraagd.


Maak een ACI-context door `docker context create aci` uit te voeren. Deze context koppelt Docker aan een Azure-abonnement en een resourcegroep, zodat u containerinstanties kunt maken en beheren. Stel dat u een context wilt maken met de naam *myacicontext*:

```
docker context create aci myacicontext
```

Wanneer u hierom wordt gevraagd, selecteert u de id van uw Azure-abonnement en selecteert u een bestaande resourcegroep of **maakt u een nieuwe**. Als u een nieuwe resourcegroep maakt, krijgt deze een door het systeem gegenereerde naam. Azure Container Instances moeten, zoals alle Azure-resources, worden geïmplementeerd in een resourcegroep. Met resourcegroepen kunt u gerelateerde Azure-resources organiseren en beheren.


Voer `docker context ls` uit om te bevestigen dat u de ACI-context hebt toegevoegd aan uw Docker-contexten:

```
docker context ls
```