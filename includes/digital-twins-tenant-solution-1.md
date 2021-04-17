---
author: baanders
description: bestand opnemen met een beschrijving van een tokenoplossing voor de beperking voor de Azure Digital Twins
ms.service: digital-twins
ms.topic: include
ms.date: 4/13/2021
ms.author: baanders
ms.openlocfilehash: 5bc7e8af356ebe5968fe8a74783edc215788e5b4
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589325"
---
Een manier om dit te doen is met de volgende CLI-opdracht, waarbij de id is van de Azure AD-tenant die de `<home-tenant-ID>` Azure Digital Twins bevat:

```azurecli-interactive
az account get-access-token --tenant <home-tenant-ID> --resource https://digitaltwins.azure.net
```

Nadat u dit hebt gevraagd, ontvangt de identiteit een token dat is uitgegeven voor de Azure AD-resource, die een overeenkomende tenant-id-claim heeft voor het *https://digitaltwins.azure.net* Azure Digital Twins exemplaar. Als u dit token gebruikt in API-aanvragen of met uw code, kan de federatiede identiteit toegang krijgen tot `Azure.Identity` de Azure Digital Twins resource.