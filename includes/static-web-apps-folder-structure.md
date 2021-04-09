---
author: craigshoemaker
ms.service: static-web-apps
ms.topic: include
ms.date: 03/25/2021
ms.author: cshoe
ms.openlocfilehash: 806a30214e0307b8fa101c0de51ed2f16622d433
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105543551"
---
| Eigenschap | Beschrijving | Voorbeeld | Vereist |
|---|---|---|---|
| `app_location` | Locatie van de toepassings code. | Voer `/` in als de bron code van uw toepassing zich in de hoofdmap van de opslag plaats bevindt, of `/app` als uw toepassings code zich in een map bevindt met de naam `app` . | Yes |
| `api_location` | De locatie van uw Azure Functions-code. | Voer `/api` in als uw app-code zich in een map bevindt met de naam `api` . Als er geen Azure Functions-app wordt gedetecteerd in de map, mislukt de build, wordt ervan uitgegaan dat u geen API wilt. | No |
| `output_location` | Locatie van de map voor het build-uitvoer ten opzichte van de `app_location` . | Als de bron code van uw toepassing zich in bevindt `/app` en het build-script bestanden naar de `/app/build` map levert, wordt ingesteld `build` als de `output_location` waarde. | Nee |